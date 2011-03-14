.. _ja_operation:

管理と運用
==============

.. TODO descrption

.. contents::
   :backlinks: none
   :local:

DSの追加
----------------------

新しいレプリカセットを作成する
^^^^^^^^^^^^^^^^^^^^^^

レプリカセットを追加することで、ストレージの容量とI/O性能を向上させることができます。

まずはじめに、現在のクラスタの状態を確認してください：

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active

新しいレプリカセット用に2台以上のサーバを用意して、ls4-dsコマンドを実行します。
ここでは、ID=2のレプリカセットを **node07** と **node08** の２台のサーバを使って構成します：

::

    [on node07]$ mkdir /var/ls4/node07
    [on node07]$ ls4-ds --cs node01 --address node07 --nid 4 --rsid 2 \
                           --name node07 -s /var/ls4/node07
    
    [on node08]$ mkdir /var/ls4/node08
    [on node08]$ ls4-ds --cs node01 --address node08 --nid 5 --rsid 2 \
                           --name node08 -s /var/ls4/node08

最後に **ls4ctl** コマンドを使ってクラスタの状態を確認してください。

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active
      4          node07       192.168.0.17:18900      subnet-192.168.000       2     active
      5          node08       192.168.0.18:18900      subnet-192.168.000       2     active

関連： :ref:`ja_operation_weight`


既存のレプリカセットにサーバを追加する
^^^^^^^^^^^^^^^^^^^^^^

既存のレプリカセットにサーバを追加することで、耐障害性と読み出し性能を向上させることができます。

まずはじめに、現在のクラスタの状態を確認してください：

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active

ここではID=0のレプリカセットにサーバを追加します。

新しいサーバを用意し、レプリカセット内の別のサーバからデータをコピーしてきます。コピーにはrsyncコマンドを使います：

::

    [on node07]$ mkdir /var/ls4/node07
    
    # まずリレータイムスタンプをコピーする
    [on node07]$ scp node03:/var/ls4/node03/rts-* /var/ls4/node07/
    
    # データをrsyncを使ってコピーする
    #   rsyncのオプション:
    #     -a  アーカイブモード
    #     -v  verboseモード
    #     -e  暗号化アルゴリズムを指定する
    #         arcfour128アルゴリズムは高速ですが脆弱なアルゴリズムです
    #         もし安全なネットワークでない場合には "blowfish" アルゴリズムが良いでしょう
    #     --bwlimit 帯域を制限する（単位はKB/s）
    [on node07]$ rsync -av -e 'ssh -c arcfour128' --bwlimit 32768 \
                       node03:/var/ls4/node03/data /var/ls4/node07/

データをコピーし終わったら、ls4-dsコマンドを実行してください。

::

    [on node07]$ ls4-ds --cs node01 --address node07 --nid 4 --rsid 0 \
                           --name node07 -s /var/ls4/node07

最後にクラスタの状態を確認してください。

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active
      4          node07       192.168.0.17:18900      subnet-192.168.000       0     active

.. TODO: See HowTo Geo-redundancy


DSの離脱
----------------------

レプリカセットからデータサーバを離脱させることができます。ただし、レプリカセットを取り除くことはできないことに注意してください。

まずはじめに、現在のクラスタの状態を確認してください：

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active

DSのプロセスを終了させます：

::

    [on node04]$ kill `pidof ls4-ds`

クラスタの状態は次のようになります：

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     FAULT
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active

**ls4ctl** **remove_node** コマンドを実行します：

::

    $ ls4ctl node01 remove_node 1

最後にクラスタの状態を確認してください。

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active


.. _ja_operation_weight:

重みの設定
----------------------

新しいデータを保存するレプリカセットは、 **重み** に基づいて選択されます。デフォルトの重みは10です。

例えば、レプリカセット0の重みが 5 で、レプリカセット1の重みが 5 のとき、新しいデータは 5/(10+5) の割合でレプリカセット0に、10/(10+5) の割合でレプリカセット1に保存されます。

重みを確認するには **ls4ctl** **weight** コマンドを使用し、重みを変更するには **ls4ctl** **set_weight** コマンドを使用します：

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


負荷の監視
----------------------

.. TODO

::

    $ ls4top node01

Type 's' to toggle short mode.


.. バックアップ
.. ----------------------
.. 
.. TODO
.. 
.. バックアップするべき項目
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. TODO
.. 
.. クラスタ情報のバックアップ
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. TODO
.. 
.. データのバックアップ
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. TODO
.. 
.. メタデータのバックアップ
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. TODO


次のステップ： :ref:`ja_fault`

