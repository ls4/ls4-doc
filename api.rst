.. _api:

API Reference
=================

.. contents::
   :backlinks: none
   :local:

HTTP API
--------

/data/<key>
^^^^^^^^^^^^^^^^^^^^^^

GET /data/<key>
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Gets data of the object.

If the object is found, it returns status code *200 OK* and *application/octet-stream* data.
If the object is not found, it returns status code *404 Not Found*.

Following parameters are acceptable:

  vtime=<integer>
    Specify version of the object by UNIX time at UTC. It returns the latest version created before the time. This parameter can't be used with *vname=*.

  vname=<string>
    Specify version of the object by name. This parameter can't be used with *vtime=*.


POST /data/<key>
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Adds a object.

If it succeeded, it returns status code *200 OK*.

Following parameters are acceptable:

  data=<bytes>
    Sets the data. This parameter is required.
  vname=<string>
    Sets the version name of the object.
  attrs=<format>
    Sets the attributes of the object. Encode this parameter using the format specified with *format=* parameter.
  format=<string>
    Sets the format of the *attrs=* option. This parameter is valid only when *attrs=* parameter is specified. You can use json (JSON), msgpack (MessagePack) or tsv (Tab-separated values). The default value is json.


PUT /data/<key>
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Adds a object.

This is same as POST excluding this returns status code *200* when it succeeded.


/attrs/<key>
^^^^^^^^^^^^^^^^^^^^^^

GET /attrs/<key>
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Gets attributes of the object.

If the object is found, it returns status code *200 OK* and attributes encoded by format specified with *format=* parameter.
If the object is not found, it returns status code *404 Not Found*.

Following parameters are acceptable:

  vtime=<integer>
    Specify version of the object by UNIX time at UTC. It returns the latest version created before the time. This parameter can't be used with *vname=*.
  vname=<string>
    Specify version of the object by name. This parameter can't be used with *vtime=*.
  format=<string>
    Specify the format of the attributes to be returned. You can use json (JSON; application/json), msgpack (MessagePack; application/x-msgpack) or tsv (Tab-separated values; text/tab-separated-values). The default value is json.


POST /attrs/<key>
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Overwrites attributes of the object.

If the object is found, it returns status code *200 OK* and *application/octet-stream* data.
If the object is not found, it returns status code *404 Not Found*.

Following parameters are acceptable:

  attrs=<format>
      Sets the attributes. This parameter is required.
  format=<string>
      Sets the format of the *attrs=* option. You can use json (JSON), msgpack (MessagePack) or tsv (Tab-separated values). The default value is json.


/api/<command>
^^^^^^^^^^^^^^^^^^^^^^

GET /api/get_data
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Gets data of the object.

If the object is found, it returns status code *200 OK* and *application/octet-stream* data.
If the object is not found, it returns status code *404 Not Found*.

Following parameters are acceptable:

  key=<string>
      Specify the key of the object. This parameters is required.
  vtime=<integer>
      Specify version of the object by UNIX time at UTC. It returns the latest version created before the time. This parameter can't be used with *vname=*.
  vname=<string>
      Specify version of the object by name. This parameter can't be used with *vtime=*.


GET /api/get_attrs
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Gets attributes of the object.

If the object is found, it returns status code *200 OK* and attributes encoded by format specified with *format=* parameter.
If the object is not found, it returns status code *404 Not Found*.

Following parameters are acceptable:

  key=<string>
      Specify the key of the object. This parameters is required.
  vtime=<integer>
      Specify version of the object by UNIX time at UTC. It returns the latest version created before the time. This parameter can't be used with *vname=*.
  vname=<string>
      Specify version of the object by name. This parameter can't be used with *vtime=*.
  format=<string>
      Specify the format of the attributes to be returned. You can use json (JSON; application/json), msgpack (MessagePack; application/x-msgpack) or tsv (Tab-separated values; text/tab-separated-values). The default value is json.


POST /api/add
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Adds a object.

If it succeeded, it returns status code *200 OK*.

Following parameters are acceptable:

  key=<string>
      Specify the key of the object. This parameters is required.
  data=<bytes>
      Sets the data. This parameter is required.
  vname=<string>
      Sets the version name of the object.
  attrs=<format>
      Sets the attributes of the object. Encode this parameter using the format specified with *format=* parameter.
  format=<string>
      Sets the format of the *attrs=* option. This parameter is valid only when *attrs=* parameter is specified. You can use json (JSON), msgpack (MessagePack) or tsv (Tab-separated values). The default value is json.


POST /api/update_attrs
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Overwrites attributes of the object.

If the object is found, it returns status code *200 OK* and *application/octet-stream* data.
If the object is not found, it returns status code *404 Not Found*.

Following parameters are acceptable:

  key=<string>
      Specify the key of the object. This parameters is required.
  attrs=<format>
      Sets the attributes. This parameter is required.
  format=<string>
      Sets the format of the *attrs=* option. You can use json (JSON), msgpack (MessagePack) or tsv (Tab-separated values). The default value is json.


POST /api/delete
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Deletes the object.

If it succeeded, it returns status code *200 OK*.
If it is not found, it returns status code *204 Not Found*.

Following parameters are acceptable:

  key=<string>
      Specify the key of the object. This parameters is required.
  vtime=<integer>
      Specify version of the object by UNIX time at UTC. It returns the latest version created before the time. This parameter can't be used with *vname=*.
  vname=<string>
      Specify version of the object by name. This parameter can't be used with *vtime=*.


POST /api/remove
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Removes the object. This is similer to *POST /api/delete?key=<key>*, but don't delete the actual data if the MDS supports versioning.

If it succeeded, it returns status code *200 OK*.
If it is not found, it returns status code *204 Not Found*.

Following parameters are acceptable:

  key=<string>
      Specify the key of the object. This parameters is required.


GET /api/url
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Selects a data server (DS) which stores the data, and returns URL to get the data directly from the DS. This is valid only when *--http* option or *--http-redirect-port* option is specified on the DS.

If the object is found, it returns status code *200 OK* and URL in *text/plain* format.
If the object is not found, it returns status code *404 Not Found*.

Following parameters are acceptable:

  key=<string>
      Specify the key of the object. This parameters is required.
  vtime=<integer>
      Specify version of the object by UNIX time at UTC. It returns the latest version created before the time. This parameter can't be used with *vname=*.
  vname=<string>
      Specify version of the object by name. This parameter can't be used with *vtime=*.

Related: :ref:`howto_ddt`


/redirect/<key>
^^^^^^^^^^^^^^^^^^^^^^

GET /redirect/<key>
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

This is similar to *GET /api/url?key=<key>*, but this returns status code *302 Found* and redirects using *Location:* header.

Related: :ref:`howto_ddt`


MessagePack-RPC API
----------------------

.. TODO

Getting API
^^^^^^^^^^^^^^^

get(key:Raw) -> (data:Raw, attributes:Map<Raw,Raw>)
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Gets data and attributes of the object.


get_data(key:Raw) -> data:Raw
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Gets data of the object.


get_attrs(key:Raw) -> attributes:Map<Raw,Raw>
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Gets attributes of the object.


read(key:Raw, offset:Integer, size:Integer) -> data:Raw
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Gets a part data of the object.


Getting specific version API
^^^^^^^^^^^^^^^

gett(vtime:Integer, key:Raw) -> (data:Raw, attributes:Map<Raw,Raw>)
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

gett_data(vtime:Integer, key:Raw) -> data:Raw
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

gett_attrs(vtime:Integer, key:Raw) -> attributes:Map<Raw,Raw>
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

readt(vtime:Integer, key:Raw, offset:Integer, size:Integer) -> data:Raw
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""


getv(vname:Raw, key:Raw) -> (data:Raw, attributes:Map<Raw,Raw>)
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

getv_data(vname:Raw, key:Raw) -> data:Raw
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

getv_attrs(vname:Raw, key:Raw) -> attributes:Map<Raw,Raw>
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

readv(vname:Raw, key:Raw, offset:Integer, size:Integer) -> data:Raw
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""


Adding API
^^^^^^^^^^^^^^^

add(key:Raw, data:Raw, attributes:Map<Raw,Raw>) -> objectKey:Object
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

add_data(key:Raw, data:Raw) -> objectKey:Object
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

addv(vname:Raw, key:Raw, data:Raw, attributes:Map<Raw,Raw>) -> objectKey:Object
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

addv_data(vname:Raw, key:Raw, data:Raw) -> objectKey:Object
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""


Deleting API
^^^^^^^^^^^^^^^

delete(key:Raw) -> deleted:Boolean
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

deletet(vtime:Integer, key:Raw) -> deleted:Boolean
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

deletev(vname:Raw, key:Raw) -> deleted:Boolean
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

remove(key:Raw) -> removed:Boolean
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""


In-place updating API
^^^^^^^^^^^^^^^

update_attrs(key:Raw, attributes:Map<Raw,Raw>) -> objectKey:Object
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""


Direct getting API
^^^^^^^^^^^^^^^

getd_data(objectKey:Object) -> data:Raw
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

readd(objectKey:Object, offset:Integer, size:Integer) -> data:Raw
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

