.. _ja_plugin:

プラグイン
==================================

LS4はプログラム全体でプラグイン型のアーキテクチャを採用しています。ここではそれらのプラグインを切り替えて使用する方法について述べます。

.. contents::
   :backlinks: none
   :local:

.. ストレージプラグイン
.. ----------------------
.. 
.. DS (Data Server) の *--store* 引数にスキーマを指定することで、ストレージの実装を選択することができます。デフォルトはDirectory Storageです。
.. 
.. Directory Storage (dir:)
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. ディレクトリをストレージとして使用します。
.. 
.. スキーマは **dir:<path>** です。


.. _ja_plugin_mds:

MDSプラグイン
----------------------

CS (Config Server) の *--mds* 引数に *スキーマ:式* の形式で指定することで、MDS (Metadata Server) の実装を選択することができます。デフォルトはTokyo Tyrant（tt:）です。

Tokyo Tyrant (tt:)
^^^^^^^^^^^^^^^^^^^^^^

MDSとして `Tokyo Tyrant <http://fallabs.com/tokyotyrant/>`_ のテーブルデータベースを使用します。バージョニングをサポートしています。

単一サーバ構成、マスタ･スレーブ構成、またはデュアルマスタ構成を選択することができます。

単一サーバ構成の場合は、 **--mds tt:<server[:port]>** の形式で指定します。

マスタ･スレーブ構成の場合は、 **host1[:port],host2[:port],...** のように、アドレスを **,** 区切りで指定します。さらに **host1:port,host2:port,...;weight1,weight2,...** のように **;** に続いて整数を **,** 区切りで指定することで、参照に重みを指定することができます。
例えば、マスタサーバをhost1、スレーブをhost2とhost3で動作させ、参照はスレーブのみに2:1の割合で割り振るには、 **--mds tt:host1,host2,host3;0,2,1** と指定します。

デュアルマスタ構成の場合は、 **host1[:port]--host2[:port]** のように、2台のサーバのアドレスを **--** で区切って指定します。
例えば、host1とhost2の2台でデュアルマスタ構成とするには、 **--mds tt:host1--host2** と指定します。


Memcache (mc:)
^^^^^^^^^^^^^^^^^^^^^^

MDSとしてmemcachedプロトコルを使用します。バージョニングはサポートしていません。

このプラグインはmemcachedプロトコルをサポートした永続的なデータストアを使用することを意図しています。例えば `Kumofs <http://kumofs.sourceforge.net/>`_, `Flare <http://labs.gree.jp/Top/OpenSource/Flare-en.html>`_, `Membase <http://www.membase.org/>`_ などです。memcachedは使用できません。

スキーマは **mc:host[:port]** です。デフォルトのポート番号は11211です。


.. _ja_plugin_mds_cache:

MDSキャッシュプラグイン
----------------------

GW (Gateway) または DS (Data Server) に *--mds-cache* 引数を指定することで、メタデータのキャッシュを有効にすることができます。

Memcached (mc:)
^^^^^^^^^^^^^^^^^^^^^^

MDSキャッシュとして `memcached <http://memcached.org/>`_ を使用します。memcachedの代わりに `Kyoto Tycoon <http://fallabs.com/kyototycoon/>`_ を使用することもできます。

サーバの一覧は、 **mc:<servers>[;<expire>]** の形式で指定します。サーバの一覧は **,** 区切りで指定し、キャッシュの有効期間（秒）を **;** に続いて整数で指定します。
デフォルトのキャッシュの有効期間は1日（86400秒）です。

例えばhost1,host2,host3の3台を使用し、キャッシュの有効期間を1時間とするには、 **--mds-cache mc:host1,host2,host3;3600** と指定します。


Local memory (lcoal:)
^^^^^^^^^^^^^^^^^^^^^^

ローカルメモリをMDSキャッシュとして使用します。
キャッシュは共有されないので、更新系のAPIは一貫性の問題を引き起こすかもしれません。

スキーマは **local:<size>** です。 *size* には "k"、"m"、"g" などの接尾辞を指定することができます。

例えば32MBのローカルメモリをMDSキャッシュとして使用するには、 **--mds-cache local:32m** と指定します。


次のステップ： :ref:`ja_devel`

