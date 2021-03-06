.. _howto_ddt:

Direct Data Transfer using Nginx's X-Accel-Redirect
===================================================

Readers of this HowTo
----------------------

Photo storage system of web services is one of the most suitable usages of LS4. In these web sites, you probably use HTTP reverse proxies in front of the application servers.
If you are using `nginx <http://wiki.nginx.org/Main>`_ as the HTTP reverse proxy, you can reduce CPU load and network traffic of the application servers using nginx's "X-Accel-Redirect" feature.

It assumes following web backend system in this document:

::

                               Internet
                                  |
                             load balancer
                              /
                       reverse proxy
                       (1) /
                      (5) /
                        App
           (3)       (2) |
            ----------- GW
           /            |
    +-------------+  (4)|
    |             |     |
    |             |   +-|--+   +----+   +----+
    |     MDS     |   | DS |   | DS |   | DS |
    |             |   |    |   |    |   |    |
    |             |   | DS |   | DS |   | DS |
    +-------------+   |    |   |    |   |    |
                      | DS |   | DS |   | DS |
                      +----+   +----+   +----+


1. Reverse proxy relays requests to the application server.
2. Application sends a request to gateway (GW) to get data.
3. GW sends a query to metadata server (MDS).
4. GW gets large data from appropriate DS.
5. Application returns large data to reverse proxy.

You can reduce CPU load and network traffic by setting up following architecture described in this document:

::

                               Internet
                                  |
                             load balancer
                              /
                       reverse proxy (nginx)
                       (1) / |
                      (5) /  |
                        App  |
           (3)       (2) |   | (6)
            ----------- GW   |
           /            |   /
    +-------------+  (4)|  /
    |             |     | /
    |             |   +-||-+   +----+   +----+
    |     MDS     |   | DS |   | DS |   | DS |
    |             |   |    |   |    |   |    |
    |             |   | DS |   | DS |   | DS |
    +-------------+   |    |   |    |   |    |
                      | DS |   | DS |   | DS |
                      +----+   +----+   +----+

1. Reverse proxy (nginx) relays requests to the application server.
2. Application sends a request to gateway (GW) to get actual URL of the data on a DS.
3. GW sends a query to metadata server (MDS).
4. Application returns the URL to nginx with X-Accel-Redirect header.
5. Nginx gets large data from the DS.


Nginx setting
----------------------

Add following setting to nginx.conf:

::

    # location setting for real application servers
    location / {
      proxy_pass  http://real.server.host;
    
      # the application should return:
      #      X-Accel-Redirect: /reproxy
      #      X-Reproxy-URL: http://host:port/url/got/from/ls4-gw
      #      Content-Type: actual/content-type
    }
    
    location = /reproxy {
      # make this location internal-use only
      internal;
    
      # set $reproxy variable to the value of X-Reproxy-URL header
      set $reproxy $upstream_http_x_reproxy_url;

      # pass to the URL
      proxy_pass $reproxy;

      # inherits Content-Type header
      proxy_hide_header Content-Type
    }


Application code
----------------------

Application should return a HTTP response that includes "X-Accel-Redirect","X-Reproxy-URL" and "Content-Type" headers.

"X-Accel-Redirect" header describes internal location setting of nginx. In this example, set "X-Accel-Redirect: /reproxy".

"X-Reproxy-URL" header describes the actual URL of the data taken from a GW.

"Content-Type" header describes the actual content-type of the data.

::

    require 'sinatra'
    require 'net/http'
    
    get '/get_my_image' do
      # Gets actual URL of the data from ls4-gw
      url = nil
      Net::HTTP.start("gateway01", 8088) do |http|
        res = http.get("/api/uri?key=my_image")
        url = res.body
      end
      
      # Sets response headers
      headers "X-Accel-Redirect" => "/reproxy"
      headers "X-Reproxy-URL" => url
      headers "Content-Type" => "image/png"
      
      # Returns empty body
      return ""
    end


DS setting
----------------------

HTTP interface must be enabled on all DSs by *--http PORT* option.

::

    [on node04]$ ls4-ds --cs cs.node --address node04 --nid N --rsid R --name N \
                           -s /var/ls4/node04 \
                           --http 19800

Or you can use *--http-redirect-port PORT* option to return actual data using another HTTP server.

Related: :ref:`howto_offload`


References
----------------------

* `Re: Can I use lighttpd/nginx for webdav but have updated disk usage statistics for mogile? <http://www.mail-archive.com/mogilefs@lists.danga.com/msg00366.html>`_

