.. _ja_build:

システムの構築
========================

ここではLS4クラスタを構築する方法について述べます。

.. contents::
   :backlinks: none
   :local:

ls4-standaloneを動かす
----------------------

**ls4-standalone** コマンドを使うと、単一のプロセスでサーバを立ち上げることができます。 *-s* 引数にデータを保存するパスを指定します。 *-t* 引数にポート番号を指定すると、HTTPクライアントを使ってデータの読み書きができます。

::

    $ ls4-standalone -s ./data -t 18080

これで単一サーバのLS4を利用する準備ができました。 *ls4cmd* コマンドを使って動作を確認してみてください。

::

    $ ls4cmd add_data key1 val1

    $ ls4cmd get_data key1
    val1

データの読み書きにはHTTPを使うこともできます。

::

    $ curl -X POST -d 'data=value1&attrs={"test":"attr"}' http://localhost:18080/data/key1
    
    $ curl -X GET http://localhost:18080/data/key1
    value1
    
    $ curl -X GET http://localhost:18080/attrs/key1
    {"test":"attr"}
    
    $ curl -X GET -d 'format=tsv' http://localhost:18080/attrs/key1
    test	attr

関連： :ref:`ja_api`

関連： :ref:`ja_command`


クラスタ構成
----------------------

複数のサーバでクラスタを構築するには、 **spread-cs**, **spread-ds** そして **spread-gw** コマンドを使います。

ここでは次のような6台のノードからなるクラスタを構築します。MDSにはTokyo Tyrantを使い、デュアルマスタ構成とします。

::

        node01                node02
        +----------+          +----------+
        | ttserver ------------ ttserver |
        |          |          +----------+
        |  ls4-cs  |
        +----------+

     +- - - - - - - - +    +- - - - - - - - +
        node01                node02         
     |  +----------+  |    |  +----------+  |
        |  ls4-ds  |          |  ls4-cs  |   
     |  +----------+  |    |  +----------+  |
                                             
     |  node01        |    |  node02        |    application server
        +----------+          +----------+       +----------+
     |  |  ls4-ds  |  |    |  |  ls4-cs  |  |    |  ls4-gw  |
        +----------+          +----------+       +----------+
     + - - - - - - - -+    + - - - - - - - -+
     replication-set 1     replication-set 2


MDSの起動
^^^^^^^^^^^^^^^^^^^^^^

まずTokyo Tyrantをデュアルマスタ構成で起動します。

::

    # node01, node02: Tokyo Tyrantをデュアルマスタ構成で起動
    [on node01]$ mkdir /var/ls4/mds1
    [on node01]$ ttserver /var/ls4/mds1/db.tct -ulog /var/ls4/mds1/ulog -sid 1 \
                          -mhost node02 -rts /var/ls4/mds1/node02.rts
    
    [on node02]$ mkdir /var/ls4/mds2
    [on node02]$ ttserver /var/ls4/mds2/db.tct -ulog /var/ls4/mds2/ulog -sid 2 \
                          -mhost node01 -rts /var/ls4/mds2/node01.rts

CSの起動
^^^^^^^^^^^^^^^^^^^^^^

次にCSを起動します。

::

    # node01: CSを起動
    [on node01]$ mkdir /var/ls4/cs
    [on node01]$ ls4-cs --mds tt:node01--node02 -s /var/ls4/cs

DSの起動
^^^^^^^^^^^^^^^^^^^^^^

DSを起動していきます。複数のDSで1つのレプリケーション･セットを構成します。

::

    # node03, node04: レプリケーション･セット1を構成
    [on node03]$ mkdir /var/ls4/node03
    [on node03]$ ls4-ds --cs node01 --address node03 --nid 1 --rsid 1 \
                           --name node03 -s /var/ls4/node03
    
    [on node04]$ mkdir /var/ls4/node04
    [on node04]$ ls4-ds --cs node01 --address node04 --nid 1 --rsid 1 \
                           --name node04 -s /var/ls4/node04

::

    # node05, node06: レプリケーション･セット2を構成
    [on node05]$ mkdir /var/ls4/node05
    [on node05]$ ls4-ds --cs node01 --address node05 --nid 2 --rsid 2 \
                           --name node05 -s /var/ls4/node05
    
    [on node06]$ mkdir /var/ls4/node06
    [on node06]$ ls4-ds --cs node01 --address node06 --nid 3 --rsid 2 \
                           --name node06 -s /var/ls4/node06


GWの起動（オプショナル）
^^^^^^^^^^^^^^^^^^^^^^

最後にGWを起動してます。DSもGWとして使うことができるので、GWの起動はオプショナルです。

::

    # アプリケーションサーバ: GWを起動
    [on app-svr]$ ls4-gw --cs node01 --port 18800 --http 18080

状態の確認
^^^^^^^^^^^^^^^^^^^^^^

クラスタを構築したら、 *ls4ctl* コマンドを使って状態を確認してください。

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       1     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       1     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       2     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       2     active

これでLS4を利用する準備が整いました。 *ls4cmd* コマンドかHTTPクライアントを使って、動作を確認してみてください。

::

    [on app-svr]$ echo val1 | ls4cmd localhost add key1 - '{"type":"png"}'
    
    [on app-svr]$ ls4cmd localhost get "key1"
    0.002117 sec.
    {"type":"png"}
    val1

CSのIPアドレス
----------------------

CS (Configuration Server) のIPアドレスは後から変更できないことに注意してください。そのIPアドレスがクラスタの識別子になるとも言えます。

サーバやNICが故障した場合に備えて、CSに専用のIPエイリアスを割り当てておくのは非常に良いアイディアです：

::

    [on node01]$ ifconfig eth0:0 192.168.0.254
    [on node01]$ ls4-cs --mds tt:node01--node02 -s /var/ls4/cs \
                           -l 192.168.0.254

次のステップ： :ref:`ja_operation`

