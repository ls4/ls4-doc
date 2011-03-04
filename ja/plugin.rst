.. _ja_plugin:

プラグイン
==================================

.. contents::
   :backlinks: none
   :local:

ストレージプラグイン
----------------------

DS (Data Server) の *--store* 引数にスキーマを指定することで、ストレージの実装を選択することができます。デフォルトはDirectory Storageです。

Directory Storage (dir:)
^^^^^^^^^^^^^^^^^^^^^^

ディレクトリをストレージとして使用します。

スキーマは **dir:<path>** です。


MDSプラグイン
----------------------

CS (Config Server) の *--mds* 引数にスキーマを指定することで、MDS (Metadata Server) の実装を選択することができます。デフォルトはTokyo Tyrantです。

Tokyo Tyrant (tt:)
^^^^^^^^^^^^^^^^^^^^^^

`Tokyo Tyrant <http://fallabs.com/tokyotyrant/>`_ のテーブルデータベースをMDSとして使用します。
バージョニングをサポートしています。

スキーマは **tt:<servers>[;<weights>]** です。


Memcache (mc:)
^^^^^^^^^^^^^^^^^^^^^^

memcachedプロトコルをMDSとして使用します。
バージョニングはサポートしていません。

このプラグインはmemcachedプロトコルをサポートした永続的なストレージを使用することを意図しています。例えば `Kumofs <http://kumofs.sourceforge.net/>`_, `Flare <http://labs.gree.jp/Top/OpenSource/Flare-en.html>`_, `Membase <http://www.membase.org/>`_ などです。memcachedは使わないでください。

スキーマは **mc:<servers>[;<weights>]** です。


MDSキャッシュプラグイン
----------------------

GW (Gateway) または DS (Data Server) に *--mds-cache* 引数を指定することで、メタデータのキャッシュを有効にすることができます。

Memcached (mc:)
^^^^^^^^^^^^^^^^^^^^^^

`memcached <http://memcached.org/>`_ をMDSキャッシュとして使用します。memcachedの代わりに `Kyoto Tycoon <http://fallabs.com/kyototycoon/>`_ を使用することもできます。

スキーマは **mc:<servers>[;<expire>]** です。


Local memory (lcoal:)
^^^^^^^^^^^^^^^^^^^^^^

ローカルメモリをMDSキャッシュとして使用します。
キャッシュは共有されないので、更新系のAPIは一貫性の問題を引き起こすかもしれません。

スキーマは **local:<size>** です。


次のステップ： :ref:`ja_devel`

