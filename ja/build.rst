.. _ja_build:

システムの構築
========================

ここではLS4クラスタを構築する方法について述べます。

.. contents::
   :backlinks: none
   :local:

1台のホスト上で動かす
----------------------

.. TODO: ls4-standalone command

LS4を1台のホスト上で動かしてテストすることができます：

::

    # 1. MDS（Tokyo Tyrant）を起動する
    #    ここでは単一ノード構成で起動します
    [localhost]$ ttserver mds.tct &

::

    # 2. CSを起動する
    #    --mds (MDSのアドレス) と -s (設定ディレクトリ) 引数が必要です
    [localhost]$ mkdir data-cs
    [localhost]$ ls4-cs --mds 127.0.0.1 -s ./data-cs &

::

    # 3. DSを起動する
    #    次の引数が必要です：
    #      --cs (CSのアドレス)
    #      --address (このノードのアドレス)
    #      --nid (一意なノードID)
    #      --rsid (参加するレプリケーション･セットのID)
    #      --name (分かりやすいノード名)
    #      --store (ストレージへのパス)
    [localhost]$ mkdir data-ds0
    [localhost]$ ls4-ds --cs 127.0.0.1 --address 127.0.0.1:18900 --nid 0 --rsid 0 \
                           --name ds0 --store ./data-ds0 &

::

    # 4. さらにDSを起動していく...
    [localhost]$ mkdir data-ds1
    [localhost]$ ls4-ds --cs 127.0.0.1 --address 127.0.0.1:18901 --nid 1 --rsid 0 \
                           --name ds1 --store ./data-ds1 &

    [localhost]$ mkdir data-ds2
    [localhost]$ ls4-ds --cs 127.0.0.1 --address 127.0.0.1:18902 --nid 2 --rsid 1 \
                           --name ds2 --store ./data-ds2 &

    [localhost]$ mkdir data-ds3
    [localhost]$ ls4-ds --cs 127.0.0.1 --address 127.0.0.1:18903 --nid 3 --rsid 1 \
                           --name ds3 --store ./data-ds3 --http 18080 &
    
    # HTTPクライアントを受け入れるために --http (port) 引数を指定します

*ls4ctl* コマンドを使ってクラスタの状態を確認してください。

::

    [localhost] $ ls4ctl localhost nodes
    nid            name                 address                location    rsid      state
      0             ds0         127.0.0.1:18900      subnet-127.000.000       0     active
      1             ds1         127.0.0.1:18901      subnet-127.000.000       0     active
      2             ds2         127.0.0.1:18902      subnet-127.000.000       1     active
      3             ds3         127.0.0.1:18903      subnet-127.000.000       1     active

これでHTTPクライアントを使ってLS4を利用する準備ができました。

::

    [localhost]$ curl -X POST -d 'data=value1&attrs={"test":"attr"}' http://localhost:18080/data/key1
    
    [localhost]$ curl -X GET http://localhost:18080/data/key1
    value1
    
    [localhost]$ curl -X GET http://localhost:18080/attrs/key1
    {"test":"attr"}
    
    [localhost]$ curl -X GET -d 'format=tsv' http://localhost:18080/attrs/key1
    test	attr


関連： :ref:`ja_api`

関連： :ref:`ja_command`


クラスタ構成
----------------------

以下の例では、次のような6台のノードからなるクラスタを構成します：

::

         node01               node02
        +------+             +------+
        |  MDS --------------- MDS  |
        |      |      \      +------+
        |  CS  |       \
        +------+        --- デュアルマスタ構成
    
     +- - - - - - +       +- - - - - - +
     |   node03   |       |   node05   |
        +------+             +------+
     |  |  DS  |  |       |  |  DS  |  |
        +------+             +------+   
     |            |       |            |
         node04               node06    
     |  +------+  |       |  +------+  |
        |  DS  |             |  DS  |   
     |  +------+  |       |  +------+  |
     +------------+       +------------+
    replication-set 0    replication-set 1

::

    # node01, node02: Tokyo Tyrantをデュアルマスタ構成で起動します
    [on node01]$ mkdir /var/ls4/mds1
    [on node01]$ ttserver /var/ls4/mds1/db.tct -ulog /var/ls4/mds1/ulog -sid 1 \
                          -mhost node02 -rts /var/ls4/mds1/node02.rts
    
    [on node02]$ mkdir /var/ls4/mds2
    [on node02]$ ttserver /var/ls4/mds2/db.tct -ulog /var/ls4/mds2/ulog -sid 2 \
                          -mhost node01 -rts /var/ls4/mds2/node01.rts
    
    # node01: CSを起動します
    [on node01]$ mkdir /var/ls4/cs
    [on node01]$ ls4-cs --mds tt:node01--node02 -s /var/ls4/cs
    
    # node03: DSを起動します（レプリケーション･セット 0）
    [on node03]$ mkdir /var/ls4/node03
    [on node03]$ ls4-ds --cs node01 --address node03 --nid 0 --rsid 0 \
                           --name node03 -s /var/ls4/node03
    
    # node04: DSを起動します（レプリケーション･セット 0）
    [on node04]$ mkdir /var/ls4/node04
    [on node04]$ ls4-ds --cs node01 --address node04 --nid 1 --rsid 0 \
                           --name node04 -s /var/ls4/node04
    
    # node05: DSを起動します（レプリケーション･セット 1）
    [on node05]$ mkdir /var/ls4/node05
    [on node05]$ ls4-ds --cs node01 --address node05 --nid 2 --rsid 1 \
                           --name node05 -s /var/ls4/node05
    
    # node06: DSを起動します（レプリケーション･セット 1）
    [on node06]$ mkdir /var/ls4/node06
    [on node06]$ ls4-ds --cs node01 --address node06 --nid 3 --rsid 1 \
                           --name node06 -s /var/ls4/node06
    
    # アプリケーションサーバ: GWを起動します
    [on app-svr]$ ls4-gw --cs node01 --port 18800 --http 18080

*ls4ctl* コマンドを使ってクラスタの状態を確認してください。

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active

これで準備が整いました。HTTPクライアントを使うか、 *ls4cmd* コマンドを使って動作を確認してみてください。

::

    [on app-svr]$ echo val1 | ls4cmd localhost add key1 - '{"type":"png"}'
    
    [on app-svr]$ ls4cmd localhost get "key1"
    0.002117 sec.
    {"type":"png"}
    val1

CS (Configuration Server) のIPアドレスは後から変更できないことに注意してください。そのIPアドレスがクラスタの識別子になるとも言えます。

CSに専用のIPエイリアスを割り当てておくのは非常に良いアイディアです：

::

    [on node01]$ ifconfig eth0:0 192.168.0.254
    [on node01]$ ls4-cs --mds tt:node01--node02 -s /var/ls4/cs \
                           -l 192.168.0.254

次のステップ： :ref:`ja_operation`

