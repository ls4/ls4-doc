.. _operation:

Operation and Fault management
==============================

LS4 is a distributed storage system that provides high scalability and availability. It describes how to scale and recover servers without any impacts for the applications in this document.

.. contents::
   :backlinks: none
   :local:

Adding data servers
----------------------

Creating a new replica-set
^^^^^^^^^^^^^^^^^^^^^^^^^^

Both storage capacity and I/O performance grow by creating new replica-set

First of all, confirm current status of the cluster using **ls4ctl** command:

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active

Prepare 2 or more servers for the new replica-set and run ls4-ds command on the servers.
In this process, we'll create replica-set (ID=2) with 2 servers named **node07** and **node08**:

::

    [on node07]$ mkdir /var/ls4/node07
    [on node07]$ ls4-ds --cs node01 --address node07 --nid 4 --rsid 2 \
                           --name node07 -s /var/ls4/node07
    
    [on node08]$ mkdir /var/ls4/node08
    [on node08]$ ls4-ds --cs node01 --address node08 --nid 5 --rsid 2 \
                           --name node08 -s /var/ls4/node08

Finally, confirm the status of the cluster:

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active
      4          node07       192.168.0.17:18900      subnet-192.168.000       2     active
      5          node08       192.168.0.18:18900      subnet-192.168.000       2     active

Related: :ref:`operation_weight`


.. _operation_add_server:

Adding servers to existing replica-sets
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can raise availability and read performance of the replica-set by adding a server to existing a replica-set

First of all, confirm current status of the cluster:

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active

In this process, we'll add a server to replica-set whose ID is 0.

Prepare new server and copy existing data from other server on the replica-set using rsync.

::

    [on node07]$ mkdir /var/ls4/node07
    
    # Copy relay time stamps first
    [on node07]$ scp node03:/var/ls4/node03/rts-* /var/ls4/node07/
    
    # Copy data using rsync.
    #   rsync option:
    #     -a  Archive mode
    #     -v  Verbose mode
    #     -e  Set cipher algorithm.
    #         Note that arcfour128 is fast but weak algorithm only for secure network.
    #         "blowfish" is good choice if the network is insecure.
    #     --bwlimit limits bandwidth in KB/s
    [on node07]$ rsync -av -e 'ssh -c arcfour128' --bwlimit 32768 \
                       node03:/var/ls4/node03/data /var/ls4/node07/

After data is copied, run a DS process.

::

    [on node07]$ ls4-ds --cs node01 --address node07 --nid 4 --rsid 0 \
                           --name node07 -s /var/ls4/node07

The DS automatically synchronizes update operations proceeded in the copy of data after this.

Finally, confirm the status of the cluster:

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active
      4          node07       192.168.0.17:18900      subnet-192.168.000       0     active

Use **ls4ctl items** command to confirm the progress of the synchronization:

::

    $ ls4ctl node01 items
     nid            name       rsid     #items
       0          node03          0       5123
       1          node04          0       5123
       2          node05          1       4907
       3          node06          1       4907
       4          node07          0       5123
    total: 0

Related :ref:`howto_location`


Removing a data server
----------------------

You can remove data servers from a replica-set. Note that you can't remove replica-sets.

First of all, confirm current status of the cluster:

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active

Terminate a DS process:

::

    [on node04]$ kill `pidof ls4-ds`

Status of the cluster becomes as follows:

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     FAULT
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active

Then, run **ls4ctl** **remove_node** command:

::

    $ ls4ctl node01 remove_node 1

Finally, confirm the status of the cluster:

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active


.. _operation_weight:

Changing weight of replica-sets
-------------------------------

To store new data, a replica-set is selected based on the **weights**. The default weight is 10.

.. TODO weights

Use **ls4ctl** **weight** command to confirm the weights of the replica-sets, and **ls4ctl** **set_weight** command to change the weights:

::

    $ ls4ctl node01 weight
    rsid   weight       nids   names
       0       10        0,1   node3,node4
       1       10        2,3   node5,node6

    $ ls4ctl node01 set_weight 0 5

    $ ls4ctl node01 weight
    rsid   weight       nids   names
       0        5        0,1   node3,node4
       1       10        2,3   node5,node6

Related: :ref:`howto_location`


Recovering data server
----------------------

If a data server is crashed, its state becomes "FAULT" as follows:

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     FAULT
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active

Recovering operation of the data servers is different depending on which data is lost (HDD is crashed) or not (process is down).

If data is not lost
^^^^^^^^^^^^^^^^^^^^^^

Restart the server process without changing **--nid** option and **--rsid** option.

You can use different IP address (--address option) from the crashed server on the substitute server. But be sure to take over all data including relay timestamp (*rts-*\* files) and update log (*ulog-*\* files).

If data is lost
^^^^^^^^^^^^^^^^^^^^^^

If data is lost, the server must be removed first.

::

    $ ls4ctl node01 remove_node 1

Then add new node. See :ref:`operation_add_server`.

Related: :ref:`command_ctl`


Recovering configuration server
-------------------------------

Since IP address of the configuration server can't be change, you must use same IP address of the crashed server on a substitute server. Or if exclusive IP alias is set for the address, set it to the substitute server (See :ref:`build_ipalias`).

Recovering operation of the configuration server is different depending on which data is lost or not.

Related: :ref:`command_cs`

If data is not lost
^^^^^^^^^^^^^^^^^^^^^^

Restart the ls4-cs process.

If data is lost
^^^^^^^^^^^^^^^^^^^^^^

Configuration server stores cluster information (*membership* and *fault* files), and actually other nodes cache them.
So copy the cached information from another data server or gateway:

::

    [on node01]$ mkdir /var/ls4/cs
    [on node01]$ scp node03:/var/ls4/node03/membership node03:/var/ls4/node03/fault /var/ls4/cs/

Then restart the server process.


Recovering gateway
----------------------

Just restart it, since gateway is a *stateless* server.


Monitoring load
----------------------

To monitor load of the servers in command-line, use :ref:`ls4top <command_top>` command.

To visualize the load using monitoring systems like Nagios or MUNIN, use :ref:`ls4stat <command_stat>` command.

-> :ref:`command_top`

-> :ref:`command_stat`


.. Backup
.. ----------------------
.. 
.. TODO backup
.. 
.. Items to backup
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. Backup cluster information
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. Backup data
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. Backup metadata
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 


Next step: :ref:`plugin`

