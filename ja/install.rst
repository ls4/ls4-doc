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


.. _ja_install_step1:

ステップ1. Rubyのインストール
-----------------------------------------

もし既に Ruby >= 1.9.2 がインストールされていたら、 :ref:`ja_install_step2` に進んでください。

最近のOSでは、パッケージ管理システムを使ってインストールすることができます：

::

    ## Ubuntu/Debian
    $ sudo aptitude install ruby1.9
    $ ruby1.9 --version
    $ gem1.9 install ls4
    
    ## Mac OS X:
    $ sudo port install ruby19
    $ ruby1.9 --version
    $ gem1.9 install ls4

ソースからコンパイルしてインストールするには、次のようにコマンドを実行してください：

::

    $ wget http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.2-p180.tar.gz
    $ tar zxvf ruby-1.9.2-p180.tar.gz
    $ cd ruby-1.9.2-p180
    
    $ ./configure
    $ make -j3
    $ sudo make install

Rubyをコンパイルするためには、次のパッケージをインストールしておく必要があるかもしれません：

  - openssl-devel (or libssl-dev)
  - zlib-devel (or zlib1g-dev)
  - readline-devel (or libreadline6-dev)


.. _ja_install_step2:

ステップ2. LS4のインストール
-----------------------------------------

次のようにgemコマンドを使ってインストールすることができます：

::

    $ gem install ls4

もしリポジトリから最新のバージョンをダウンロードして利用したい場合は、次のようにgemパッケージを作成してください：

::

    $ git clone http://github.com/ls4/ls4.git
    $ cd ls4
    $ rake
    $ gem install pkg/ls4-<version>.gem


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


.. _ja_install_step3:

ステップ3. Tokyo Tyrantのインストール
-----------------------------------------

`Tokyo Tyrant <http://fallabs.com/tokyotyrant/>`_ は、MDS (Metadata Server) として利用します。

いくつかのOSでは、パッケージ管理システムを使ってインストールすることができます：

::

    ## Ubuntu/Debian:
    $ sudo aptitude install tokyotyrant
    
    ## Mac OS X:
    $ sudo port install tokyotyrant

ソースからコンパイルしてインストールするには、次のようにコマンドを実行してください：

::

    ## Tokyo Cabinet のインストール (database manager)
    $ wget http://fallabs.com/tokyocabinet/tokyocabinet-1.4.47.tar.gz
    $ tar zxvf tokyocabinet-1.4.47.targz
    $ cd tokyocabinet-1.4.47
    
    $ ./configure
    $ make -j3
    $ sudo make install

    ## Tokyo Tyrant のインストール
    $ wget http://fallabs.com/tokyotyrant/tokyotyrant-1.1.41.tar.gz
    $ tar zxvf tokyotyrant-1.1.41.targz
    $ cd tokyotyrant
    
    $ ./configure
    $ make -j3
    $ sudo make install


次のステップ： :ref:`ja_build`

