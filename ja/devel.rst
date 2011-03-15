.. _ja_devel:

改善とデバッグ
========================

LS4はオープンソースの分散ストレージシステムです。ここではソフトウェアの改善やデバッグ、運用ノウハウを共有について述べます。

.. contents::
   :backlinks: none
   :local:

.. 知識を共有しよう
.. ----------------------
.. 
.. ？や！を共有しよう
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. HowToを共有しよう
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. 改善点を共有しよう
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. 

LS4を修正する
----------------------

最新のソースコードの入手
^^^^^^^^^^^^^^^^^^^^^^

LS4の開発リポジトリはgithubにあります。 http://github.com/ls4/ls4 から入手できます。

このドキュメントもgithubで管理されています。最新のソースは http://github.com/ls4/ls4-doc から入手でき、最新の整形済みドキュメントは http://ls4.sourceforge.net/doc/ で閲覧できます。


MDSプラグインを追加する
^^^^^^^^^^^^^^^^^^^^^^

MDSプラグインの基底クラスは ``MDS`` *(service/mds.rb)* クラスです。MDSプラグインで耐障害性をサポートするには、 ``BasicHADB`` *(service/mds_ha.rb)* クラスが有用かもしれません。

バージョニングをサポートしないシンプルなMDSプラグインを実装するには、 ``MemcacheMDS`` *(service/mds_memcache.rb)* クラスが参考になります。
バージョニングをサポートしたMDSプラグインを実装するには、 ``TokyoTyrantMDS`` *(service/mds_tt.rb)* クラスが参考になります。

次の手順でMDSプラグインを追加することができます：

#. *service/mds_<name>.rb* ファイルを追加し、MDSクラスを継承したクラスを記述する
#. クラス定義の先頭に ``MDSSelector.register(:<SCHEME>, self)`` と記述する
#. command/cs.rb, command/ds.rb, command/gw.rb に、 ``require 'service/mds_<name>.rb'`` を追加する
#. *ls4-cs* コマンドに *--mds <SCHEME>:<EXPR>* と指定する

関連： :ref:`ja_plugin_mds`


MDSキャッシュプラグインを追加する
^^^^^^^^^^^^^^^^^^^^^^

MDSキャッシュプラグインの基底クラスは ``MDSCache`` *(service/mds_cache.rb)* クラスです。

MDSキャッシュプラグインを実装するには、 ``LocalMemoryMDSCache`` *(service/mds_cache_mem.rb)* クラスが参考になります。

#. *service/mds_cache_<name>.rb* ファイルを追加し、MDSCacheクラスを継承したクラスを記述する
#. クラス定義の先頭に ``MDSCacheSelector.register(:<SCHEME>, self)`` と記述する
#. command/cs.rb, command/ds.rb, command/gw.rb に、 ``require 'service/mds_cache_<name>.rb'`` を追加する
#. *ls4-cs* コマンドに *--mds-cache <SCHEME>:<EXPR>* と指定する

関連： :ref:`ja_plugin_mds_cache`


負荷分散プラグインを追加する
^^^^^^^^^^^^^^^^^^^^^^

新しいデータを保存するレプリカセットを選択するアルゴリズムは、負荷分散プラグインを差し替えることで切り替えることができます。

負荷分散プラグインは、 ``BalanceBus`` *(service/balance.rb)* で定義されます。


マスターセレクタを追加する
^^^^^^^^^^^^^^^^^^^^^^

レプリカセットの中で、実際にデータを読み書きするDSを1台選択するアルゴリズムは、マスターセレクタを差し替えることで切り替えることができます。

マスターセレクタは、 ``MasterSelectBus`` *(service/master_select.rb)* で定義されます。


取得できる統計情報を追加する
^^^^^^^^^^^^^^^^^^^^^^

取得できる統計情報を追加するには、 ``StatService`` *(service/stat.rb)* 、 ``CSStatService`` *(service/stat_cs.rb)* 、 ``DSStatService`` *(service/stat_ds.rb)* または ``GWStatService`` *(service/stat_gw.rb)* にメソッドを追加します。

メソッド名は *stat_<name>* という命名規則に従ってください。このメソッドで返される値をRPCを使って取得することができます。

実際に値を取得するには、 *ls4stat* *(command/stat.rb)* コマンドや *ls4ctl* *(command/ctl.rb)* コマンドにコードを追加してください。


MessagePack-RPC APIを追加する
^^^^^^^^^^^^^^^^^^^^^^

次の手順でMessagePack-RPC APIを追加することができます：

#. ``GWRPCBus`` *(service/rpc_gw.rb)* クラスと ``GWRPCService`` *(service/rpc_gw.rg)* クラスにコードを追加する
#. ``GatewayService`` *(service/gateway.rb)* クラスに *rpc_<name>* という命名規則でメソッドを追加する

これらのコードを追加することで、 *ls4-gw* と *ls4-ds* の両方にMessagePack-RPC APIが追加できます。

関連： :ref:`ja_api_rpc`


HTTP APIを追加する
^^^^^^^^^^^^^^^^^^^^^^

``HTTPGatewayService`` *(service/gw_http.rb)* にコードを追加することで、HTTP APIを追加することができます。

HTTP APIはMessagePack-RPC APIの基本的にはラッパとして実装されます。新たな機能を追加するには、まずMessagePack-RPC APIを追加することで容易に実装できるかもしれません。

関連： :ref:`ja_api_http`


.. ソースコード
.. ----------------------
.. 
.. MessagePack-RPCと非同期通信
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. EventBus
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. ProcessBus
.. ^^^^^^^^^^^^^^^^^^^^^^

ソースツリー
----------------------

::

    lib/ls4
    |
    +-- lib/                    基本的なライブラリ群
    |   |
    |   +-- ebus.rb             EventBus：プログラム全体を駆動するイベント管理ライブラリ
    |   +-- cclog.rb            ログライブラリ
    |   +-- vbcode.rb           Variable Byte Codeの実装
    |
    +-- logic/
    |   |
    |   +-- node.rb             Nodeクラスの定義
    |   +-- okey.rb             ObjectKeyクラスの定義
    |   +-- tsv_data.rb         キャッシュ/永続化されるクラスタ情報データの基底クラス
    |   +-- fault_detector.rb   障害情報ファイルの実装・障害検出アルゴリズムの実装
    |   +-- membership.rb       メンバシップファイルの実装
    |   +-- weight.rb           重み情報ファイルの実装
    |
    +-- service/
    |   |
    |   +-- base.rb             すべてのサービスの基底クラス
    |   +-- bus.rb              すべてのバスの基底クラス
    |   +-- log.rb              ログサービス
    |   |
    |   +-- process.rb          プログラムの起動や終了、タイマーなどに関するサービス
    |   |
    |   +-- heartbeat.rb        heartbeatサービス
    |   +-- sync.rb             クラスタ情報データの同期サービス
    |   +-- time_check.rb       システム時刻のずれをチェックするサービス
    |   |
    |   +-- membership.rb       ノード一覧とレプリカセットを管理するサービス
    |   +-- master_select.rb    マスターセレクタ
    |   +-- balance.rb          負荷分散
    |   +-- weight.rb           重み情報を設定/同期する実装
    |   |
    |   +-- data_client.rb      DSのクライアントサービス
    |   +-- data_server.rb      DSのサーバサービス
    |   +-- data_server_url.rb  Direct Data Transferの実装
    |   +-- slave.rb            DSのレプリケーションの実装
    |   |
    |   +-- gateway.rb          GWの実装
    |   +-- gateway_ro.rb       GWの読み取り専用モードの実装
    |   +-- gw_http.rb          GWのHTTP APIの実装
    |   |
    |   +-- config.rb           コマンドライン引数に関する実装
    |   +-- config_cs.rb        コマンドライン引数に関する実装
    |   +-- config_ds.rb        コマンドライン引数に関する実装
    |   +-- config_gw.rb        コマンドライン引数に関する実装
    |   |
    |   +-- stat.rb             統計情報に関する実装
    |   +-- stat_cs.rb          統計情報に関する実装
    |   +-- stat_ds.rb          統計情報に関する実装
    |   +-- stat_gw.rb          統計情報に関する実装
    |   |
    |   +-- rpc.rb              MessagePack-RPC APIのインタフェース定義
    |   +-- rpc_cs.rb           MessagePack-RPC APIのインタフェース定義
    |   +-- rpc_ds.rb           MessagePack-RPC APIのインタフェース定義
    |   +-- rpc_gw.rb           MessagePack-RPC APIのインタフェース定義
    |   |
    |   +-- rts.rb              リレータイムスタンププラグインの実装
    |   +-- rts_file.rb         リレータイムスタンププラグインの実装
    |   +-- rts_memory.rb       リレータイムスタンププラグインの実装
    |   |
    |   +-- ulog.rb             更新ログプラグインの実装
    |   +-- ulog_file.rb        更新ログプラグインの実装
    |   +-- ulog_memory.rb      更新ログプラグインの実装
    |   |
    |   +-- mds.rb              MDSプラグインの実装
    |   +-- mds_ha.rb           MDSプラグインの実装
    |   +-- mds_tt.rb           MDSプラグインの実装
    |   +-- mds_memcache.rb     MDSプラグインの実装
    |   +-- mds_tc.rb           MDSプラグインの実装（Standaloneサーバ用）
    |   |
    |   +-- mds_cache.rb            MDSキャッシュプラグインの実装
    |   +-- mds_cache_mem.rb        MDSキャッシュプラグインの実装
    |   +-- mds_cache_memcached.rb  MDSキャッシュプラグインの実装
    |   |
    |   +-- storage.rb          ストレージプラグインの実装
    |   +-- storage_dir.rb      ストレージプラグインの実装
    |
    +-- command/
    |   |
    |   +-- cs.rb               ls4-csコマンド
    |   +-- ds.rb               ls4-dsコマンド
    |   +-- gw.rb               ls4-gwコマンド
    |   +-- standalone.rb       ls4-standaloneコマンド
    |   +-- ctl.rb              ls4ctlコマンド
    |   +-- cmd.rb              ls4cmdコマンド
    |   +-- rpc.rb              ls4rpcコマンド
    |   +-- stat.rb             ls4statコマンド
    |   +-- top.rb              ls4topコマンド
    |
    +-- default.rb              デフォルトのポート番号の定義
    |
    +-- version.rb

