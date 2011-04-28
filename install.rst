.. _install:

Installation
========================

LS4 is a distributed storage system implemed in Ruby.
You can install using `RubyGems <http://rubygems.org/>`_.

.. contents::
   :backlinks: none
   :local:

Requirements
----------------------

Following softwares are required to run LS4:

  - `Ruby <http://www.ruby-lang.org/>`_ >= 1.9.2
  - `Tokyo Tyrant <http://fallabs.com/tokyotyrant/>`_ >= 1.1.40

..  - `MessagePack-RPC for Ruby gem <http://msgpack.org/>`_ >= 0.4.3
..  - `Rack gem <http://rack.rubyforge.org/>`_ >= 1.2.1
..  - `Tokyo Cabinet gem <http://rubygems.org/gems/tokyocabinet>`_ >= 1.29
..  - `Tokyo Tyrant gem <http://rubygems.org/gems/tokyotyrant>`_ >= 1.13
..  - `memcache-client gem <http://rubygems.org/gems/memcache-client>`_ >= 1.8.5


.. _install_step1:

1. Installing Ruby
--------------------------------

If Ruby >= 1.9.2 is already installed, skip to :ref:`install_step2`.

On moderen OS, it is provided by the package systems:

::

    ## Ubuntu/Debian
    $ sudo aptitude install ruby1.9
    $ ruby1.9 --version
    $ gem1.9 install ls4
    
    ## Mac OS X:
    $ sudo port install ruby19
    $ ruby1.9 --version
    $ gem1.9 install ls4

To install Ruby from source, do following commands:

::

    $ wget http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.2-p180.tar.gz
    $ tar zxvf ruby-1.9.2-p180.tar.gz
    $ cd ruby-1.9.2-p180
    
    $ ./configure
    $ make -j3
    $ sudo make install

You may be required to install following packages to build Ruby.

  - openssl-devel (or libssl-dev)
  - zlib-devel (or zlib1g-dev)
  - readline-devel (or libreadline6-dev)


.. _install_step2:

2. Installing LS4
------------------------------------

Use gem command to install LS4:

::

    $ gem install ls4

Following commands are installed:

  - :ref:`ja_command_ctl`
  - :ref:`ja_command_cmd`
  - :ref:`ja_command_rpc`
  - :ref:`ja_command_top`
  - :ref:`ja_command_stat`
  - :ref:`ja_command_cs`
  - :ref:`ja_command_ds`
  - :ref:`ja_command_gw`
  - :ref:`ja_command_standalone`


.. _install_step3:

3. Installing Tokyo Tyrant
-----------------------------------------------

`Tokyo Tyrant <http://fallabs.com/tokyotyrant/>`_ is used as a metadata server (MDS) in LS4.

On some OS, it is provided by the package systems:

::

    ## Ubuntu/Debian:
    $ sudo aptitude install tokyotyrant
    
    ## Mac OS X:
    $ sudo port install tokyotyrant

To install Tokyo Tyrant from source, do following commands:

::

    ## Install Tokyo Cabinet (database manager)
    $ wget http://fallabs.com/tokyocabinet/tokyocabinet-1.4.47.tar.gz
    $ tar zxvf tokyocabinet-1.4.47.targz
    $ cd tokyocabinet-1.4.47
    
    $ ./configure
    $ make -j3
    $ sudo make install

    ## Install Tokyo Tyrant
    $ wget http://fallabs.com/tokyotyrant/tokyotyrant-1.1.41.tar.gz
    $ tar zxvf tokyotyrant-1.1.41.targz
    $ cd tokyotyrant
    
    $ ./configure
    $ make -j3
    $ sudo make install


Next step: :ref:`build`

