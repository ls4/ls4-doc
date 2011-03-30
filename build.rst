.. _build:

Cluster construction
================================

It describes how to construct the LS4 cluster in this document.

.. contents::
   :backlinks: none
   :local:

Running on single host (stand-alone)
------------------------------------

You can try LS4 on single host using **ls4-standalone** command. It starts stand-alone server.

Set *-s* option for the path to store data. You can set *-t* option to use HTTP clients for reading/wrting objects:

::

    $ ls4-standalone -s ./data -t 18080

It is prepared to use LS4. Use the command-line client *ls4cmd* to confirm the workings of the server:

::

    $ ls4cmd localhost add_data key1 val1
    $ ls4cmd localhost get_data key1
    val1

You can use HTTP clients to read/write data:

::

    $ curl -X POST -d 'data=value1&attrs={"test":"attr"}' http://localhost:18080/data/key1
    
    $ curl -X GET http://localhost:18080/data/key1
    value1
    
    $ curl -X GET http://localhost:18080/attrs/key1
    {"test":"attr"}
    
    $ curl -X GET -d 'format=tsv' http://localhost:18080/attrs/key1
    test	attr

Related: :ref:`api`

Related: :ref:`command`


Running a cluster
----------------------

Use **ls4-cs**, **ls4-ds** and **ls4-gw** commands to build cluster with multiple servers.

It runs runs 6-node cluster in this tutorial. It uses Tokyo Tyrant for the metadata server (MDS) as a dual-master composition.

::

        node01                node02
        +----------+          +----------+
        | ttserver ------------ ttserver |
        |          |          |          |
        |  ls4-cs  |          |          |
        +----------+          +----------+

     + - - - - - - - -+    + - - - - - - - -+
     |                |    |                |
        node01                node02         
     |  +----------+  |    |  +----------+  |
        |  ls4-ds  |          |  ls4-ds  |   
     |  +----------+  |    |  +----------+  |
                                             
     |  node01        |    |  node02        |    application server
        +----------+          +----------+       +----------+
     |  |  ls4-ds  |  |    |  |  ls4-ds  |  |    |  ls4-gw  |
        +----------+          +----------+       +----------+
     |                |    |                |
     + - - - - - - - -+    + - - - - - - - -+
     replica-set 1         replica-set 2

Related: :ref:`arch`


Running the MDS
^^^^^^^^^^^^^^^^^^^^^^

First, run Tokyo Tyrant as a dual-msater composition. Tokyo Tyrant stores metadata.

Use *.tct* (means table database) for the suffix of the database file.

::

    # node01 and node02 run Tokyo Tyrant as a dual-master composition
    [on node01]$ mkdir /var/ls4/mds1
    [on node01]$ ttserver /var/ls4/mds1/db.tct -ulog /var/ls4/mds1/ulog -sid 1 \
                          -mhost node02 -rts /var/ls4/mds1/node02.rts
    
    [on node02]$ mkdir /var/ls4/mds2
    [on node02]$ ttserver /var/ls4/mds2/db.tct -ulog /var/ls4/mds2/ulog -sid 2 \
                          -mhost node01 -rts /var/ls4/mds2/node01.rts


Running a CS
^^^^^^^^^^^^^^^^^^^^^^

Second, run a configuration server (CS). CS requires the address of the MDS and the path to the directry to store state of the cluster.

::

    # node01 runs a CS
    [on node01]$ mkdir /var/ls4/cs
    [on node01]$ ls4-cs --mds tt:node01--node02 -s /var/ls4/cs

Related: :ref:`plugin`


Running DSs
^^^^^^^^^^^^^^^^^^^^^^

Next, run data servers (DS).

It build two replica-sets: ID 1 (rsid=1) and ID 2 (rsid=2). Each sets consists of two servers ([node03,node04], [node05,node06]).

DS requires the address of the CS, an unique node ID, human-readable node name, ID of the replica-set and path to the directry to store data:

::

    # node03 and node04 compose replica-set 1
    [on node03]$ mkdir /var/ls4/node03
    [on node03]$ ls4-ds --cs node01 --address node03 --nid 1 --name node03 \
                           --rsid 1 -s /var/ls4/node03
    
    [on node04]$ mkdir /var/ls4/node04
    [on node04]$ ls4-ds --cs node01 --address node04 --nid 2 --name node04 \
                           --rsid 1 -s /var/ls4/node04

::

    # node05 and node06 compose replica-set 2
    [on node05]$ mkdir /var/ls4/node05
    [on node05]$ ls4-ds --cs node01 --address node05 --nid 3 --name node05 \
                           --rsid 2 -s /var/ls4/node05
    
    [on node06]$ mkdir /var/ls4/node06
    [on node06]$ ls4-ds --cs node01 --address node06 --nid 4 --name node06 \
                           --rsid 2 -s /var/ls4/node06

Related: :ref:`command`


Running GWs (Optional)
^^^^^^^^^^^^^^^^^^^^^^

Finally, run gateways (GW). You can use DS as the GW.

::

    # application server runs a GW
    [on app-svr]$ ls4-gw --cs node01 --port 18800 --http 18080


Ronfirming the status
^^^^^^^^^^^^^^^^^^^^^^

Now the cluster is running and prepared to use. Use *ls4ctl* command to confirm the status:

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       1     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       1     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       2     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       2     active

Try to write and read data using http client, or *ls4cmd* command as follows:

::

    [on app-svr]$ echo val1 | ls4cmd localhost add key1 - '{"type":"png"}'
    
    [on app-svr]$ ls4cmd localhost get "key1"
    0.002117 sec.
    {"type":"png"}
    val1

Next step: :ref:`operation`


Advanced Techniques
----------------------

.. _build_ipalias:

Assigning exclusive IP alias for the CS
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can't change the IP address of the configuration server. In other words, the address becomes identifier of the cluster.

It is good idea to assign exclusive IP alias for the CS to make it easy for the substitute server to take over the address:

::

    [on node01]$ ifconfig eth0:0 192.168.0.254
    [on node01]$ ls4-cs --mds tt:node01--node02 -s /var/ls4/cs \
                        -l 192.168.0.254

Traffic Offloading
^^^^^^^^^^^^^^^^^^^^^^

-> :ref:`howto_offload`

Direct Data Transfer
^^^^^^^^^^^^^^^^^^^^^^

-> :ref:`howto_ddt`

