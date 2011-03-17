.. _ja_operation:

運用と障害対応
==============

LS4は高い拡張性と耐障害性を備えた分散ストレージシステムです。ここではアプリケーションに影響を及ぼさずにシステムを拡張・復旧する方法について述べます。

.. contents::
   :backlinks: none
   :local:

DSの追加
----------------------

新しいレプリカセットを作成する
^^^^^^^^^^^^^^^^^^^^^^

レプリカセットを追加することで、ストレージの容量とI/O性能を向上させることができます。

まずはじめに、 **ls4ctl** コマンドを使って現在のクラスタの状態を確認してください：

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

これで新しいレプリカセットの追加が完了しました。最後にクラスタの状態を確認してください。

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


.. _ja_operation_add_server:

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

データをコピーし終わったら、 **ls4-ds** コマンドを実行してください。

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

この後、データのコピー中に行われた更新操作の同期が自動的に行われ、レプリカセットへのサーバの追加が完了します。

更新操作の同期の進行状況を確認するには、 **ls4ctl items** コマンドを使用してください：

::

    $ ls4ctl node01 items
     nid            name       rsid     #items
       0          node03          0       5123
       1          node04          0       5123
       2          node05          1       4907
       3          node06          1       4907
       4          node07          0       5123
    total: 0

関連： :ref:`ja_howto_location`


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

新しいデータを保存するレプリカセットは、通常は **重み** に基づいて選択されます。デフォルトの重みは10です。

新しいデータを保存するとき、あるレプリカが選択される割合は、そのレプリカセットの重みをすべての重みの総和で割った値になります。例えば、レプリカセット0の重みが 5 で、レプリカセット1の重みが 10 のとき、新しいデータは 5/(5+10) の割合でレプリカセット0に、10/(5+10) の割合でレプリカセット1に保存されます。

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

関連： :ref:`ja_howto_location`


DSの復旧
----------------------

DS (Data Server) が故障すると、その状態が "FAULT" になります：

::

    $ ls4ctl node01 nodes
    nid            name                 address                location    rsid      state
      0          node03       192.168.0.13:18900      subnet-192.168.000       0     active
      1          node04       192.168.0.14:18900      subnet-192.168.000       0     FAULT
      2          node05       192.168.0.15:18900      subnet-192.168.000       1     active
      3          node06       192.168.0.16:18900      subnet-192.168.000       1     active

DSを復旧する手順は、データが失われた（HDDが故障した）か、失われていない（プロセスがダウンした）かによって異なります。

データが失われていない場合
^^^^^^^^^^^^^^^^^^^^^^

**--nid** 引数と **--rsid** 引数を変更せずに、サーバのプロセスを再起動してください。プロセスを再起動すると、プロセスがダウンしていた間に行われた更新操作の同期が自動的に始まります。

故障したサーバと新しいサーバでは、異なるIPアドレスを使うことができます。ただし、その場合でもすべてのデータ（リレータイムスタンプファイル *rts-*\* と更新ログファイル *ulog-*\* を含む）を引き継いでください。

データが失われた場合
^^^^^^^^^^^^^^^^^^^^^^

データが失われた場合は、いったんそのサーバを取り除き、新たなサーバを追加します。

サーバを取り除くには、 *ls4ctl remove_node* コマンドを使用します：

::

    $ ls4ctl node01 remove_node 1

新しいサーバを追加する方法は、 :ref:`ja_operation_add_server` を参照してください。

関連： :ref:`ja_command_ctl`


CSの復旧
----------------------

CSのIPアドレスは変更することができないので、新しいサーバには故障したサーバと同じIPアドレスを振る必要がある点に注意してください（関連： :ref:`ja_build_ipalias` ）。

CSを復旧する手順は、クラスタの状態ファイル（障害状況ファイル、メンバシップファイル、重み情報ファイル）が失われたか失われていないかによって異なります。

関連： :ref:`ja_command_cs`

データが失われていない場合
^^^^^^^^^^^^^^^^^^^^^^

ls4-csプロセスを再起動してください。

データが失われた場合
^^^^^^^^^^^^^^^^^^^^^^

CSが管理している情報は、他のノードにもキャッシュされています。
このため、それらのファイルをDSやGWからコピーすることで、CSの状態を復元できます：

::

    [on node01]$ mkdir /var/ls4/cs
    [on node01]$ scp node03:/var/ls4/node03/membership node03:/var/ls4/node03/fault /var/ls4/cs/

コピーが終わったら、 **ls4-cs** プロセスを再起動してください。


GWの復旧
----------------------

GW (Gateway) は *ステートレス* なプロセスなので、単にプロセスを再起動するだけで復旧することができます。


負荷の監視
----------------------

コマンドラインでリアルタイムに負荷を監視するには、 :ref:`ls4top <ja_command_top>` コマンドを使用します。

NagiosやMUNINなどの監視/可視化システム、負荷の変化をグラフ化して可視化するには、 :ref:`ls4stat <ja_command_stat>` コマンドを使用します。

→ :ref:`ja_command_top`

→ :ref:`ja_command_stat`


.. バックアップ
.. ----------------------
.. 
.. TODO backup
.. 
.. バックアップするべき項目
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. クラスタ情報のバックアップ
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. データのバックアップ
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. メタデータのバックアップ
.. ^^^^^^^^^^^^^^^^^^^^^^


次のステップ： :ref:`ja_plugin`

