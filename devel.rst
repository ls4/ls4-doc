.. _devel:

Debugging and Improvement
=====================================

.. TODO descrption

.. Share your knowledge
.. ----------------------
.. 
.. Share your questions and discoveries
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. Share your HOWTOs
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. Share your improvements
.. ^^^^^^^^^^^^^^^^^^^^^^


Getting the latest source codes
-------------------------------

Development repository of the LS4 is on github. You can checkout it from http://github.com/ls4/ls4.

This document is also on github. You can checkout it from http://github.com/ls4/ls4-doc and get formatted document at http://ls4.sourceforge.net/doc/. It uses `Sphinx <http://Sphinx.pocoo.org/>`_ to format this document.


.. Adding MDS plug-in
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. Adding MDS cache plug-in
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. Adding load-balancing plug-in
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. Adding master selector
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. Adding statics item
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. Adding MessagePack-RPC API
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. Adding HTTP API
.. ^^^^^^^^^^^^^^^^^^^^^^


.. Source codes
.. ----------------------
.. 
.. Asynchronous communication using MessagePack-RPC
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. EventBus
.. ^^^^^^^^^^^^^^^^^^^^^^
.. 
.. ProcessBus
.. ^^^^^^^^^^^^^^^^^^^^^^

Source tree
----------------------

::

    lib/ls4
    |
    +-- lib/
    |   |
    |   +-- ebus.rb
    |   +-- cclog.rb
    |   +-- vbcode.rb
    |
    +-- logic/
    |   |
    |   +-- node.rb
    |   +-- okey.rb
    |   +-- tsv_data.rb
    |   +-- fault_detector.rb
    |   +-- membership.rb
    |   +-- weight.rb
    |
    +-- service/
    |   |
    |   +-- base.rb
    |   +-- bus.rb
    |   +-- log.rb
    |   |
    |   +-- process.rb
    |   |
    |   +-- heartbeat.rb
    |   +-- sync.rb
    |   +-- time_check.rb
    |   |
    |   +-- membership.rb
    |   +-- master_select.rb
    |   +-- balance.rb
    |   +-- weight.rb
    |   |
    |   +-- data_client.rb
    |   +-- data_server.rb
    |   +-- data_server_url.rb
    |   +-- slave.rb
    |   |
    |   +-- gateway.rb
    |   +-- gateway_ro.rb
    |   +-- gw_http.rb
    |   |
    |   +-- config.rb
    |   +-- config_cs.rb
    |   +-- config_ds.rb
    |   +-- config_gw.rb
    |   |
    |   +-- stat.rb
    |   +-- stat_cs.rb
    |   +-- stat_ds.rb
    |   +-- stat_gw.rb
    |   |
    |   +-- rpc.rb
    |   +-- rpc_cs.rb
    |   +-- rpc_ds.rb
    |   +-- rpc_gw.rb
    |   |
    |   +-- rts.rb
    |   +-- rts_file.rb
    |   +-- rts_memory.rb
    |   |
    |   +-- ulog.rb
    |   +-- ulog_file.rb
    |   +-- ulog_memory.rb
    |   |
    |   +-- mds.rb
    |   +-- mds_ha.rb
    |   +-- mds_tt.rb
    |   +-- mds_memcache.rb
    |   +-- mds_tc.rb
    |   |
    |   +-- mds_cache.rb
    |   +-- mds_cache_mem.rb
    |   +-- mds_cache_memcached.rb
    |   |
    |   +-- storage.rb
    |   +-- storage_dir.rb
    |
    +-- command/
    |   |
    |   +-- cs.rb
    |   +-- ds.rb
    |   +-- gw.rb
    |   +-- standalone.rb
    |   +-- ctl.rb
    |   +-- cmd.rb
    |   +-- rpc.rb
    |   +-- stat.rb
    |   +-- top.rb
    |
    +-- default.rb
    |
    +-- version.rb


