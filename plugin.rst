.. _plugin:

Plug-ins
============================

LS4 uses of plug-in archtecture.
It describes how to switch and use the plug-ins in this document.

.. contents::
   :backlinks: none
   :local:

.. Storage plug-in
.. ----------------------
.. 
.. You can choose a storage implementation by specifing scheme to the *--store* option on the data server (DS). The default is Directory Storage.
.. 
.. Directory Storage (dir:)
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. This uses local directory for the storage.
.. 
.. Scheme is **dir:<path>**


.. _plugin_mds:

MDS plug-in
----------------------

You can choose the implementation of metadata server (MDS) by specifing scheme to the *--mds* option on config server (CS). The default is Tokyo Tyrant (tt:).

Tokyo Tyrant (tt:)
^^^^^^^^^^^^^^^^^^^^^^

This uses `Tokyo Tyrant <http://fallabs.com/tokyotyrant/>`_'s table database for the MDS.
It supports versioning.

You cna use single-server mode, master-slave mode and dual-master mode.

In single-server mode, use **--mds tt:<server[:port]>** format.

In master-slave mode, specify addresses of the servers in comma-separated form like **host1[:port],host2[:port],...**. In addition, you can specify the weight of the reference as in comma-separated form followed by **;** like **--mds tt:host:port,host:port,...;weight1,weight2,...**. For example, if you use host1 as a master server, and host2 and host2 as slave servers, and divide reference loads in 2:1 raito on slave servers, specify: **--mds tt:host1,host2,host3;0,2,1**

In dual-master mode, speficy the two addresses of the servers with **--** separator like **host1[:port]--host2[:port]**. For example, if you use host1 and host2 in dual-master mode, specify: **--mds tt:host1--host2**.


Memcache (mc:)
^^^^^^^^^^^^^^^^^^^^^^

This uses memcached protocol for the MDS.
It does NOT support versioning.

This plug-in is intended to use persistent storage system that supports memcached protocol like `Kumofs <http://kumofs.sourceforge.net/>`_, `Flare <http://labs.gree.jp/Top/OpenSource/Flare-en.html>`_, `Membase <http://www.membase.org/>`_, etc.
Don't use memcached.

Scheme is **mc:host[:port]**. The default port number is 11211.


.. _plugin_mds_cache:

MDS cache plug-in
----------------------

You can enable metadata cache by adding *--mds-cache* option on gateway (or data server).

Memcached (mc:)
^^^^^^^^^^^^^^^^^^^^^^

This uses `memcached <http://memcached.org/>`_ for the the MDS cache. You can use `Kyoto Tycoon <http://fallabs.com/kyototycoon/>`_ instead of memcached.

.. TODO expire

Scheme is **mc:<servers>[;<expire>]**


Local memory (lcoal:)
^^^^^^^^^^^^^^^^^^^^^^

This uses local memory for the MDS cache.
Because cached data are not shared, updating APIs operations may occur consistency problem.

Schem is **local:<size>**. You can use size suffixes like "k", "m" or "g".

For example, if you use 32MB of the local memory for the MDS cache, specify: **--mds-cache local:32m**.


Next step: :ref:`devel`

