.. _arch:

Architecture
================

LS4 is a distributed storage system that provides high scalability and availability.
It describes the architecture of the LS4 in this document.

.. contents::
   :backlinks: none
   :local:

Data model
----------------------

LS4 stores a set of *objects* identified by a **key** (a string). Each object has **data** (an sequence of bytes) and **attributes** (an associative array).

And each object can have multiple **versions**. You can get back old version of the object unless you surely delete it.
Each version is identified by name or creation time (UNIX time at UTC).

::

      key                        object
                        data                  attributes
                 +-----------------+---------------------------------+       ---+
    "image1" =>  |  "HTJ PNG ..."  |  { type:png, model:NEX-5 }      |--+       | each object can
                 +-----------------+---------------------------------+  |--+    | have multiple
                   +-----------------+----------------------------------+  |    | versoins
                      +----------------+-----------------------------------+    |
                                                                             ---+
                 +-----------------+---------------------------------+
      key    =>  |  bytes .......  |  { key:value, key:value, ... }  |--+
                 +-----------------+---------------------------------+  |--+
                   +-----------------+----------------------------------+  |
                      +----------------+-----------------------------------+
    
      ...    =>  ...


Kind of servers
----------------------

LS4 consists of 4 kind of servers:

  DS (data server)
    DS stores and replicates contents on the disk.
  MDS (metadata server)
    MDS stores metadata of the contents. Metadata includes information about "where the content is stored." LS4 uses `Tokyo Tyrant <http://fallabs.com/tokyotyrant/>`_ or other systems for the MDS (See :ref:`plugin`).
  GW (gateway)
    GW receives requests from applications and relays it to appropriate DS. You can use DS as a GW because DS has features of GW.
  CS (config server)
    CS manages cluster configuration. It also watches status of DSs and detaches crashed DSs automatically.

DSs belong to **Replica Sets**.


.. _arch_replica_set:

Replica Sets
----------------------

Multiple DSs compose a group whose member stores same data. The group is called **Replica Set**.

::

                        App     App     App
                         |       |       |  HTTP or MessagePack-RPC protocol
            ----------- GW      GW      GW or DS
           /            /
    +-------------+    |  GW relays requests from apps to DSs.
    |             |    |
    |             |  +----+   +----+   +----+
    |     MDS     |  | DS |   | DS |   | DS |
    |             |  |    |   |    |   |    | Multiple DSs compose a replica-set.
    |             |  | DS |   | DS |   | DS | DSs in a replica-set store same data set.
    +-------------+  |    |   |    |   |    |
    MDS store        | DS |   | DS |   | DS | ... You can add replica-sets at any time.
    metadata         +----+   +----+   +----+
                         \       |       /
                          -----  |  ----- CS manages cluster configuration.
                               \ | /
                                CS


All DSs in a replica-set are on an equal relationship. Generally, a master server in the replica-set processes all reading and writing requests. Different master servers are selected by each keys.

When :ref:`Location-aware master selection <howto_location>` is enabled, one of the nearlest DSs are selected with priority.


Operations
----------------------

Adding data
^^^^^^^^^^^^^^^^^^^^^^

Gateway (or aata server) relays requests from applications to metadata servers and data servers.
Metadata servers store "which replica-set stores the data", and data servers store the data.

::

                        App     App     App
           (2)       (1) |       |       |
            ----------- GW      GW      GW
           /            /
    +-------------+    |
    |             |    | (3)
    |             |  +----+   +----+   +----+
    |     MDS     |  | DS |   | DS |   | DS |
    |             |  | | (4)  |    |   |    |
    |             |  | DS |   | DS |   | DS |
    +-------------+  | | (4)  |    |   |    |
                     | DS |   | DS |   | DS |
                     +----+   +----+   +----+

1. Application sends add request to a GW (gateway) or DS (data server). Any of GW or DS can respond to the requests.
2. GW (or DS) selects a replica-set that stores the data and insert its ID to MDS (metadata server). Weighted round-robin algorithm is used to select the replica-set.
3. GW (or DS) sends add request to a DS in the replica-set.
4. Other DSs in the replica-set replicate the stored data.

Related: :ref:`api`


Geting data
^^^^^^^^^^^^^^^^^^^^^^

Metadata servers know which replica-set stores the data. So gateway (or data server) sends query to metadata server first, and then get data from the data server.

::

                        App     App     App
           (2)       (1) |       |       |
            ----------- GW      GW      GW
           /            /
    +-------------+    |
    |             |    | (3)
    |             |  +----+   +----+   +----+
    |     MDS     |  | DS |   | DS |   | DS |
    |             |  |    |   |    |   |    |
    |             |  | DS |   | DS |   | DS |
    +-------------+  |    |   |    |   |    |
                     | DS |   | DS |   | DS |
                     +----+   +----+   +----+

1. Application sends get request to a GW or DS. Any of GW or DS can respond to the requests.
2. GW (or DS) sends search query to MDS. MDS returns ID of replica-set that has the requested data if it's found.
3. GW (or DS) selects a DS from the replica-set, and sends get request to the DS. The DS is selected using location-aware algorithm

Related: :ref:`api`

Related: :ref: `howto_location`


Updating and geting attributes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Attributes are stored on metadata servers.

::

                        App     App     App
           (2)       (1) |       |       |
            ----------- GW      GW      GW
           /
    +-------------+
    |             |
    |             |  +----+   +----+   +----+
    |     MDS     |  | DS |   | DS |   | DS |
    |             |  |    |   |    |   |    |
    |             |  | DS |   | DS |   | DS |
    +-------------+  |    |   |    |   |    |
                     | DS |   | DS |   | DS |
                     +----+   +----+   +----+

1. Application sends update or get request to a GW or DS. Any of GW or DS can respond to the requests.
2. GW (or DS) sends a query to MDS.

Related: :ref:`api`


Controling and Monitoring
--------------------------

All data servers are registered on configuration server. Controling and monitoring tools deal with all data servers all together by changing settings on configuration server or taking server list from the configuration server.

::

                     (1)      (2)
      Administrator --> Tools --> CS
                         / \
    +-------------+     |   -------------  (3)
    |             |     |       |        \
    |             |  +----+   +----+   +----+
    |     MDS     |  | DS |   | DS |   | DS |
    |             |  |    |   |    |   |    |
    |             |  | DS |   | DS |   | DS |
    +-------------+  |    |   |    |   |    |
                     | DS |   | DS |   | DS |
                     +----+   +----+   +----+

1. Administrator (you) runs a control tool with some arguments.
2. The control tool takes cluster information from CS (configuration server).
3. The control tool takes status or statistics from DSs and show them.


Next step: :ref:`build`

