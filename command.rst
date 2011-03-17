.. _command:

Command-line reference
=================================

.. TODO descrption

.. contents::
   :backlinks: none
   :local:

.. _command_cs:

ls4-cs: configuration server
----------------------

::

    Usage: ls4-cs [options]
        -p, --port PORT                  listen port
        -l, --listen HOST                listen address
        -m, --mds EXPR                   address of metadata servers (required)
        -M, --mds-cache EXPR             address of metadata cache servers
        -s, --store PATH                 path to default directory to store various data (required for production use)
            --fault_store PATH           path to fault status file
            --membership_store PATH      path to membership status file
            --weight_store PATH          path to weight status file
        -o, --log PATH
        -v, --verbose                    show debug messages
            --trace                      show debug and trace messages
            --color-log                  force to enable color log


-p, --port PORT
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The port number to listen on. Default is 18700.

-l, --listen HOST
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The address to listen on. Default is 0.0.0.0 (all address).

-m, --mds EXPR
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The address of the metadata server. See :ref:`plugin_mds`

.. TODO --mds

-M, --mds-cache EXPR
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The address of the metadata cache servers. Default is no-cache. See :ref:`plugin_mds_cache`

-s, --store PATH
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The path to store the status of the fault servers (fault store), the membership information (membership store) and weights of the replica-sets (weight store).

If it is omitted, these data are not saved. They are not required for casual-use but save for production use.

This option can be overwritten by *--fault_store*, *--membership* and *--weight_store* option.


.. _command_gw:

ls4-gw: gateway
----------------------

::

    Usage: ls4-gw [options]
        -c, --cs ADDRESS                 address of config server (required)
        -p, --port PORT                  listen port
        -l, --listen HOST                listen address
        -t, --http PORT                  http listen port
            --http-error-page PATH       path to eRuby template file
        -R, --read-only                  read-only mode
        -T, --read-only-time TIME        read-only mode using the time
        -N, --read-only-name NAME        read-only mode using the version name
        -L, --location STRING            enable location-aware master selection
        -s, --store PATH                 path to base directory
            --fault_store PATH           path to fault status file
            --membership_store PATH      path to membership status file
            --weight_store PATH          path to weight status file
        -o, --log PATH
        -v, --verbose                    show debug messages
            --trace                      show debug and trace messages
            --color-log                  force to enable color log


-c, --cs ADDRESS
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The address of the configuration server (CS).

-p, --port PORT
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The port number to listen on. Default is 18800.

-l, --listen HOST
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The address to listen on. Default is 0.0.0.0 (all address).

-t, --http PORT
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The port number for accepting HTTP client. Default is disabled.

--http-error-page PATH
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

To customize the error pages displayed when server error, specify a eRuby template file.

-R, --read-only
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Make the update oprations from clients error.

-T, --read-only-time TIME
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Make the update oprations from clients error.
And serves data created before the specified time.

TIME is integer of UNIX time (UTC). You can calculate the value as follows:

::

    $ ruby -r time -e 'p Time.at("2011-07-29 11:00:00").utc.to_i'
    1311904800

-N, --read-only-name NAME
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Make the update oprations from clients error.
And serves data created with the specified version name.

-L, --location STRING
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

-> :ref:`howto_location`

--http-redirect-port PORT
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

-> :ref:`howto_offload`

--http-redirect-path FORMAT
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

-> :ref:`howto_offload`

-s, --store PATH
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The path to cache the status of the fault servers (fault store), the membership information (membership store) and weights of the replica-sets (weight store).

If it is omitted, these data are cached only on memory, and deleted when the process is shutdown.

This option can be overwritten by *--fault_store*, *--membership* and *--weight_store* option.


.. _command_ds:

ls4-ds: data server
----------------------

DS has same features with GW, it supports same options.

::

    Usage: ls4-ds [options]
        -c, --cs ADDRESS                 address of config server (required)
        -i, --nid ID                     unieque node id (required)
        -n, --name NAME                  human-readable node name (required)
        -a, --address ADDRESS[:PORT]     address of this node (required)
        -l, --listen HOST[:PORT]         listen address
        -g, --rsid IDs                   replica-set IDs to join (required)
        -L, --location STRING            location of this node
        -s, --store PATH                 path to storage directory (required)
        -u, --ulog PATH                  path to update log directory
        -r, --rts PATH                   path to relay timestamp directory
        -t, --http PORT                  http listen port
            --http-error-page PATH       path to eRuby template file
            --http-redirect-port PORT
            --http-redirect-path FORMAT
        -R, --read-only                  read-only mode
        -N, --read-only-name NAME        read-only mode using the version name
        -T, --read-only-time TIME        read-only mode using the time
            --fault_store PATH           path to fault status file
            --membership_store PATH      path to membership status file
        -o, --log PATH
        -v, --verbose                    show debug messages
            --trace                      show debug and trace messages
            --color-log                  force to enable color log


-c, --cs ADDRESS
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The address of the configuration server (CS).

-i, --nid ID
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Unique node ID in integer.

-n, --name NAME
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The name of this server. This name is displayed in control tools.

-a, --address ADDRESS[:PORT]
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The address of this server. This server is accessed using this address.

The default port number is 18900.

-g, --rsid RSIDs
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The ID of the replica-set to join in.

-s, --store PATH
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The path to store the data.

The directory is used to cache the status of the fault servers (fault store), the membership information (membership store) and weights of the replica-sets (weight store).

This option can be overwritten by *--fault_store*, *--membership* and *--weight_store* option.


.. _command_standalone:

ls4-standalone: stand-alone server
----------------------

ls4-standalone is a program that provides all server functions in a single process. It is useful for the verification of the LS4.

::

    Usage: ls4-standalone [options]
        -p, --port PORT                  listen port
        -l, --listen HOST                listen address
        -m, --mds EXPR                   address of metadata servers
        -M, --mds-cache EXPR             address of metadata cache servers
        -s, --store PATH                 path to storage directory (required)
        -u, --ulog PATH                  path to update log directory
        -r, --rts PATH                   path to relay timestamp directory
        -t, --http PORT                  http listen port
            --http-error-page PATH       path to eRuby template file
            --http-redirect-port PORT
            --http-redirect-path FORMAT
        -R, --read-only                  read-only mode
        -N, --read-only-name NAME        read-only mode using the version name
        -T, --read-only-time TIME        read-only mode using the time
            --fault_store PATH           path to fault status file
            --membership_store PATH      path to membership status file
        -o, --log PATH
        -v, --verbose                    show debug messages
            --trace                      show debug and trace messages
            --color-log                  force to enable color log


.. TODO ls4-standalone


.. _command_ctl:

ls4ctl: control tool
----------------------

::

    Usage: ls4ctl <cs address[:port]> <command> [options]
    command:
       nodes                        show list of nodes
       stat                         show statistics of nodes
       remove_node <nid>            remove a node from a replica-set
       locate <key>                 show which servers store the key
       weight                       show list of replica-sets
       set_weight <rsid> <weight>   change a weight of a replica-set
       mds                          show MDS uri
       set_mds <expr>               set MDS uri
       mds_cache                    show MDS cache uri
       set_mds_cache <expr>         set MDS cache uri
       items                        show stored number of objects
       version                      show software version of nodes

Related: :ref:`plugin_mds`

Related: :ref:`plugin_mds_cache`


.. _command_cmd:

ls4cmd: command line client
----------------------

::

    Usage: ls4cmd <cs address[:port]> <command> [options]
    command:
       get <key>                           get data and attributes
       gett <time> <key>                   get data and attributes using the time
       getv <vname> <key>                  get data and attributes using the version name
       get_data <key>                      get data
       gett_data <time> <key>              get data using the time
       getv_data <vname> <key>             get data using the version name
       get_attrs <key>                     get attributes
       gett_attrs <time> <key>             get attributes using the time
       getv_attrs <vname> <key>            get attributes using the version name
       read <key> <offset> <size>          get data with the offset and the size
       readt <time> <key> <offset> <size>  get data with the offset and the size using version time
       readv <vname> <key> <offset> <size> get data with the offset and the size using version name
       add <key> <data> <json>             set data and attributes
       addv <vname> <key> <data> <json>    set data and attributes with version name
       add_data <key> <data>               set data
       addv_data <vname> <key> <data>      set data with version name
       update_attrs <key> <json>           update attributes
       delete <key>                        delete the data and attributes
       deletet <time> <key>                delete the data and attributes using the time
       deletev <vname> <key>               delete the data and attributes using the version name
       remove <key>                        remove the data and attributes

.. TODO ls4cmd


.. _command_rpc

ls4rpc: RPC command-line client
----------------------

::

    Usage: ls4rpc <host>:<port> [method [args ...]]

It runes interactive shell (IRB; interactive Ruby) when you run the command with the host and port number.
It shows supported methods when you type 'show' command in the shell.

When you run the command with method name and arguments, it issues a RPC and closes. Each arguments are in YAML format, and the return value is showed in YAML format.

The port number must be specified. Default port numbers of the servers are as follows:

  CS
    18700
  DS
    18900
  GW
    18800


.. _command_top:

ls4top: monitoring tool like 'top'
----------------------

::

    Usage: ls4top [options] <cs address[:port]>

.. TODO ls4top


.. _command_stat:

ls4stat: status gathering tool for monitoring/visualization systems
----------------------

::

    Usage: ls4stat <cs address[:port]> [options] params...
    params:
        nid     address    name      rsid    location
        state   time       uptime    pid     version
        read    write      delete    items
    default params:
        nid address name read write delete time
    options:
        -a, --array                      print as arrays instead of a maps
        -o, --only NID_OR_NAMES          get status of these servers only
        -t, --tsv                        use Tab-Separated-Values format (default)
        -j, --json                       use JSON format
        -m, --msgpack                    use MessagePack format
        -y, --yaml                       use YAML format

.. TODO ls4stat


::

    $ ls4stat <cs address[:port]> --tsv nid address name read write delete time

