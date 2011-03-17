.. _ja_install:

インストール
======================

LS4はRubyで実装された分散ストレージシステムです。
`RubyGems <http://rubygems.org/>`_ を使ってインストールすることができます。

.. contents::
   :backlinks: none
   :local:

依存ライブラリ
----------------------

LS4を実行するには次のソフトウェアが必要です：

  - `Ruby <http://www.ruby-lang.org/>`_ >= 1.9.2
  - `Tokyo Tyrant <http://fallabs.com/tokyotyrant/>`_ >= 1.1.40
  - `MessagePack-RPC for Ruby gem <http://msgpack.org/>`_ >= 0.4.3
  - `Rack gem <http://rack.rubyforge.org/>`_ >= 1.2.1
  - `Tokyo Cabinet gem <http://rubygems.org/gems/tokyocabinet>`_ >= 1.29
  - `Tokyo Tyrant gem <http://rubygems.org/gems/tokyotyrant>`_ >= 1.13
  - `memcache-client gem <http://rubygems.org/gems/memcache-client>`_ >= 1.8.5


方法1：RubyGemsを使ったインストール
----------------------

あなたがRubyを使っているのであれば、もっとも簡単な方法はRubyGemsを使ってインストールする方法です。

::

    $ gem install pkg/ls4-<version>.gem

もしリポジトリから最新のバージョンをダウンロードして利用したい場合は、rakeを使ってgemを作成してください。

::

    $ git clone http://github.com/ls4/ls4.git
    $ cd ls4
    $ rake
    $ gem install pkg/ls4-<version>.gem

方法2：make installを使ったインストール
----------------------

多くUNIXコマンドのように、./configure && make install を使ってインストールすることもできます。

::

    $ ./bootstrap  # 必要な場合
    $ ./configure RUBY=/usr/local/bin/ruby --prefix=/opt/local/ls4
    $ make
    $ sudo make install

依存ライブラリは手動でインストールする必要があります。

::

    $ sudo gem install msgpack-rpc
    $ sudo gem install tokyocabinet
    $ sudo gem install tokyotyrant
    $ sudo gem install memcache-client
    $ sudo gem install rack

以下のコマンドがインストールされます：

  - :ref:`ja_command_ctl`
  - :ref:`ja_command_cmd`
  - :ref:`ja_command_rpc`
  - :ref:`ja_command_top`
  - :ref:`ja_command_stat`
  - :ref:`ja_command_cs`
  - :ref:`ja_command_ds`
  - :ref:`ja_command_gw`
  - :ref:`ja_command_standalone`


方法3：専用のRuby 1.9をインストールする
----------------------

/opt/local/ls4 ディレクトリに全システムをコンパイルしてインストールします。

まず、以下のパッケージをパッケージ管理ツールを使ってインストールしてください：

  - gcc-g++ >= 4.1
  - openssl-devel (libssl-dev) （rubyのビルドに必要）
  - zlib-devel (zlib1g-dev) （rubyのビルドに必要）
  - readline-devel (libreadline6-dev) （rubyのビルドに必要）
  - tokyocabinet (libtokyocabinet-dev) （Tokyo Tyrantのビルドに必要）

以下の手順でRubyとLS4をインストールします：

::

    # ruby-1.9 を /opt/local/ls4 にインストールする
    $ wget ftp://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.2-p136.tar.bz2
    $ tar jxvf ruby-1.9.2-p136.tar.bz2
    $ cd ruby-1.9.2-p136
    $ ./configure --prefix=/opt/local/ls4
    $ make
    $ sudo make install

::

    # RubyGems を使って依存ライブラリとLS4をインストールする
    $ sudo /opt/local/ls4/bin/gem install ls4

::

    # Tokyo Tyrant into /opt/local/ls4 にインストールする
    $ wget http://fallabs.com/tokyotyrant/tokyotyrant-1.1.41.tar.gz
    $ tar zxvf tokyotyrant-1.1.41.tar.gz
    $ cd tokyotyrant-1.1.41
    $ ./configure --prefix=/opt/local/ls4
    $ make
    $ sudo make install


次のステップ： :ref:`ja_build`

