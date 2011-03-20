.. _howto_location:

Location-aware master selection
=========================================================================

.. contents::
   :backlinks: none
   :local:

Readers of this HowTo
----------------------

To prevent data loss from disasters or power failures of the datacenter, you have to create a copy of the data on a geographically distant location.

You can construct such replication formation by adding servers on distant location to replica-sets. And GW gets/adds data from/to the nearest DSs by priority when you supply information for the servers about "where the server is located".

::

    Tokyo (Primary)  --location=tokyo
    +- - - - - - - - - - - - - - - - - - - - - - - - - - +
    |                                                    |
       +----------+  +----------+  +----------+  +----+   
    |  |  App/GW  |  |  App/GW  |  |  App/GW  |  | CS |  |   
       +----------+  +----------+  +----------+  +----+   
    |                                                    |
       +------+    +------+    +------+       +-------+   
    |  |  DS  |    |  DS  |    |  DS  |       |  MDS  |  |
       +--|---+    +--|---+    +--|---+       +---|---+   
    |     |           |           |               |      |
       +--|---+    +--|---+    +--|---+       +---|---+   
    |  |  DS  |    |  DS  |    |  DS  |       |  MDS  |  |
       +--|---+    +--|---+    +--|---+       +---|---+   
    |     |           |           |               |      |
    +- - -|- - - - - -|- - - - - -|- - - - - - - -|- - - +
          |           |           |               |       
          |           |           |               |       
    +- - -|- - - - - -|- - - - - -|- - - - - - - -|- - - +
    |     |           |           |               |      |
       +--|---+    +--|---+    +--|---+       +---|---+   
    |  |  DS  |    |  DS  |    |  DS  |       |  MDS  |  |
       +------+    +------+    +------+       +-------+   
    |                                                    |
    +- - - - - - - - - - - - - - - - - - - - - - - - - - +
    California (Backup)  --location=california

Related: :ref:`arch_replica_set`


Setting up DS and GW
----------------------

Specify *--location* option on command-line arguments of *ls4-ds* and *ls4-gw*.

DS and GW read and write data by priority to the DS for which same *--location* option is specified.

Related: :ref:`command`

