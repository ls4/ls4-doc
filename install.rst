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
  - `MessagePack-RPC for Ruby gem <http://msgpack.org/>`_ >= 0.4.3
  - `Rack gem <http://rack.rubyforge.org/>`_ >= 1.2.1
  - `Tokyo Cabinet gem <http://rubygems.org/gems/tokyocabinet>`_ >= 1.29
  - `Tokyo Tyrant gem <http://rubygems.org/gems/tokyotyrant>`_ >= 1.13
  - `memcache-client gem <http://rubygems.org/gems/memcache-client>`_ >= 1.8.5


Choice 1: Install using RubyGems
--------------------------------

The easiest way to install LS4 will be to use RubyGems, if you're using Ruby.

::

    $ gem install ls4

Use rake comamnd to make latest gem package from the repository.

::

    $ git clone http://github.com/ls4/ls4.git
    $ cd ls4
    $ rake
    $ gem install pkg/ls4-<version>.gem

Choice 2: Install using make-install
------------------------------------

The other way is to use ./configure && make install:

::

    $ ./bootstrap  # if needed
    $ ./configure RUBY=/usr/local/bin/ruby
    $ make
    $ sudo make install

Install required libraries manually.

::

    $ gem install msgpack-rpc
    $ gem install tokyocabinet
    $ gem install tokyotyrant
    $ gem install memcache-client
    $ gem install rack

Following commands will be installed:

  - ls4ctl: Management tool
  - ls4cli: Command line client
  - ls4rpc: Command line RPC client
  - ls4top: Monitoring tool like 'top'
  - ls4-cs: CS server program
  - ls4-ds: DS server program
  - ls4-gw: GW server program
  - ls4-standalone: Stand-alone server program

Chocie 3: Installing Ruby 1.9 for exclusive use
-----------------------------------------------

In this guide, you will install all systems on /opt/local/ls4 directory.

First, install folowing packages using the package management system:

  - gcc-g++ >= 4.1
  - openssl-devel (or libssl-dev) to build Ruby
  - zlib-devel (or zlib1g-dev) to build Ruby
  - readline-devel (or libreadline6-dev) to build Ruby
  - tokyocabinet (or libtokyocabinet-dev) to build Tokyo Tyrant

Following procedure installs Ruby and LS4:

::

    # Installs ruby-1.9 into /opt/local/ls4
    $ wget ftp://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.2-p136.tar.bz2
    $ tar jxvf ruby-1.9.2-p136.tar.bz2
    $ cd ruby-1.9.2-p136
    $ ./configure --prefix=/opt/local/ls4
    $ make
    $ sudo make install

::

    # Installs required gems and LS4
    $ sudo /opt/local/ls4/bin/gem install ls4

::

    # Installs Tokyo Tyrant into /opt/local/ls4
    $ wget http://fallabs.com/tokyotyrant/tokyotyrant-1.1.41.tar.gz
    $ tar zxvf tokyotyrant-1.1.41.tar.gz
    $ cd tokyotyrant-1.1.41
    $ ./configure --prefix=/opt/local/ls4
    $ make
    $ sudo make install


Next step: :ref:`build`

