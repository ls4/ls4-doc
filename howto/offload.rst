.. _howto_offload:

Traffic offloading improves performance of the DSs
=========================================================================

.. contents::
   :backlinks: none
   :local:

Readers of this HowTo
----------------------

DS is written in Ruby and relatively slow. You can accelerate performance by using other HTTP servers (like nginx, lighttpd or thttpd) to acceleration to GET requests.

::

                        App
           (2)       (1) | \
            ----------- GW  \ (5)
           /            /    \
    +-------------+    |(3)   \
    |             |    |(4)    |
    |             |  +-|-------|----------------+
    |     MDS     |  | DS    Native HTTP Server |
    |             |  +--------------------------+
    |             |  +--------------------------+
    +-------------+  | DS    Native HTTP Server |
                     +--------------------------+
                     +--------------------------+
                     | DS    Native HTTP Server |
                     +--------------------------+

1. Applications send GW a request to get the URL of the data.
2. GW sends MDS a request to get the ID of the replica-set that stores the data.
3. GW selects a DS from the replica-set and queries the URL of the data.
4. DS returns GW the URL to get the data from the HTTP server.
5. Application gets the data from the HTTP server.

You can use it with :ref:`Direct Data Transfer <howto_ddt>` and make the data transfer more efficient.


Setting Up HTTP Server
----------------------

Using thttpd
^^^^^^^^^^^^^^^

::

    [on node04]$ thttpd -p 19800 -d /var/ls4/node04/data


Using nginx
^^^^^^^^^^^^^^^

::

    server {
      listen 19800;
      server_name localhost;
      sendfile on;
      location / {
        root /var/ls4/node04/data;
      }
    }

Setting DS
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Specify the port number of the HTTP server on *--http-redirect-port PORT* argument:

::

    [on node04]$ ls4-ds --cs cs.node --address node04 --nid N --rsid R --name N \
                           -s /var/ls4/node04 \
                           --http-redirect-port 19800

.. TODO

.. TODO http-redirect-path

