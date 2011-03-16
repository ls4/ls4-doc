.. _ja_howto_offload:

Traffic Offloading: HTTPサーバ機能を別プロセスに委譲してDSの性能を向上する
=========================================================================

.. contents::
   :backlinks: none
   :local:

目的
----------------------

DSはRubyで書かれており、動作は低速です。別のHTTPサーバ（nginxやlighttpd、thttpdなど）を使ってGETリクエストを処理することで、性能を向上させることができます。

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

1. アプリケーションはGWに対して、データを取得するためのURLを問い合わせます
2. GWはMDSに対して、データを保存しているレプリカセットのIDを問い合わせます
3. GWはレプリカセットの中からDSを1台選択し、データを取得するためのURLを問い合わせます
4. DSはHTTPサーバからデータを取得するためのURLをGWに返します
5. アプリケーションはHTTPサーバからデータを取得します

:ref:`Direct Data Transfer <ja_howto_ddt>` と組み合わせることで、さらに効率よくデータを転送することも可能です。


HTTPサーバの設定
----------------------

thttpdを使う
^^^^^^^^^^^^^^^^^^^^^^

::

    [on node04]$ thttpd -p 19800 -d /var/ls4/node04/data


nginxを使う
^^^^^^^^^^^^^^^^^^^^^^

::

    server {
      listen 19800;
      server_name localhost;
      sendfile on;
      location / {
        root /var/ls4/node04/data;
      }
    }


DSの設定
----------------------

HTTPサーバのポート番号を *--http-redirect-port PORT* 引数に指定して、GETリクエストを転送するようにします：

::

    [on node04]$ ls4-ds --cs cs.node --address node04 --nid N --rsid R --name N \
                           -s /var/ls4/node04 \
                           --http-redirect-port 19800

.. TODO

.. TODO http-redirect-path

