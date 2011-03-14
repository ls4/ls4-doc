.. _ja_command:

コマンドラインリファレンス
====================================

.. TODO descrption

.. contents::
   :backlinks: none
   :local:

.. _ja_command_cs:

ls4-cs: Configuration Server
----------------------

::

    Usage: ls4-cs [options]
        -p, --port PORT                  待ち受けポート
        -l, --listen HOST                待ち受けアドレス
        -m, --mds EXPR                   メタデータサーバのアドレス（必須）
        -M, --mds-cache EXPR             メタデータキャッシュサーバのアドレス
        -s, --store PATH                 各種データを保存するデフォルトのディレクトリ（実運用時は必須）
            --fault_store PATH           障害状況ファイルを保存するパス
            --membership_store PATH      メンバシップファイルを保存するパス
            --weight_store PATH          重み情報ファイルを保存するパス
        -o, --log PATH
        -v, --verbose                    デバッグメッセージを表示する
            --trace                      デバッグメッセージとトレースメッセージを表示する
            --color-log                  強制的にログに色を付ける

-p, --port PORT
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

待ち受けるポート番号を指定します。デフォルトは18700です。

-l, --listen HOST
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

待ち受けるアドレスを指定します。デフォルトは0.0.0.0（すべてのアドレス）です。

-m, --mds EXPR
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

MDS (Metadata Server) のアドレスを指定します。指定方法は *スキーマ:式* の形式になります。
デフォルトのスキーマは Tokyo Tyrant (tt:) です。

関連： :ref:`ja_plugin_mds`


-M, --mds-cache EXPR
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

MDSのキャッシュサーバのアドレスを指定します。指定方法は *スキーマ:式* の形式になります。

デフォルトはキャッシュなしです。

関連： :ref:`ja_plugin_mds_cache`


-s, --store PATH
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

障害状況ファイル（fault store）、メンバシップファイル（membership store）、重み情報ファイル（weight store）を保存するパスを指定します。

省略すると、これらのデータは保存されません。必須ではありませんが、実運用時には指定してください。

このオプションは、 *--fault_store* 、 *--membership* 、 *--weight_store* オプションで上書きすることができます。


.. _ja_command_gw:

ls4-gw: Gateway
----------------------

::

    Usage: ls4-gw [options]
        -c, --cs ADDRESS                 Config Serverのアドレス（必須）
        -p, --port PORT                  待ち受けポート
        -l, --listen HOST                待ち受けアドレス
        -t, --http PORT                  HTTPの待ち受けポート
            --http-error-page PATH       HTTPのエラーページ用のeRubyテンプレートファイル
        -R, --read-only                  読み込み専用モード
        -T, --read-only-time TIME        スナップショット時刻を指定した読み込み専用モード
        -N, --read-only-name NAME        バージョン名を指定した読み込み専用モード
        -L, --location STRING            位置を考慮したマスタ選出を有効にする
        -s, --store PATH                 各種データを保存するデフォルトのディレクトリ
            --fault_store PATH           障害状況を永続的にキャッシュするパス
            --membership_store PATH      メンバシップを永続的にキャッシュするパス
            --weight_store PATH          重み情報を永続的にキャッシュするパス
        -o, --log PATH
        -v, --verbose                    デバッグメッセージを表示する
            --trace                      デバッグメッセージとトレースメッセージを表示する
            --color-log                  強制的にログに色を付ける

-c, --cs ADDRESS
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

CS (Configuration Server) のアドレスを指定します。

-p, --port PORT
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

待ち受けるポート番号を指定します。デフォルトは18800です。

-l, --listen HOST
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

待ち受けるアドレスを指定します。デフォルトは0.0.0.0（すべてのアドレス）です。

-t, --http PORT
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

HTTPクライアントを待ち受けるポート番号を指定します。デフォルトでは待ち受けません。

--http-error-page PATH
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

HTTPでサーバエラー時などに表示されるエラーページをカスタマイズするには、この引数にeRubyテンプレートファイルを指定します。

-R, --read-only
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

このGWを経由したクライアントからのデータの変更操作をエラーします。

-T, --read-only-time TIME
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

このGWを経由したクライアントからのデータの変更操作をエラーします。
また、指定された時刻以前に作成されたデータが読み込まれるようにします。

TIMEには、UNIX時刻（世界協定時刻）を整数で指定します。この値は次のコマンドで計算できます：

::

    $ ruby -r time -e 'p Time.at("2011-07-29 11:00:00").utc.to_i'
    1311904800


-N, --read-only-name NAME
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

このGWを経由したクライアントからのデータの変更操作をエラーします。
また、指定されたバージョン名が設定されたデータが読み込まれるようにします


-L, --location STRING
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

→ :ref:`ja_howto_location`


--http-redirect-port PORT
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

→ :ref:`ja_howto_offload`


--http-redirect-path FORMAT
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

→ :ref:`ja_howto_offload`


-s, --store PATH
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

障害状況ファイル（fault store）、メンバシップファイル（membership store）、重み情報ファイル（weight store）を永続的にキャッシュするパスを指定します。

省略すると、これらのデータはメモリ上だけでキャッシュされ、プロセスを終了すると削除されます。

このオプションは、 *--fault_store* 、 *--membership* 、 *--weight_store* オプションで上書きすることができます。


.. _ja_command_ds:

ls4-ds: Data Server
----------------------

DSはGWの機能と同じ機能を持っているため、同じ引数もサポートしています。

::

    Usage: ls4-ds [options]
        -c, --cs ADDRESS                 Config Serverのアドレス（必須）
        -i, --nid ID                     一意なノード名（必須）
        -n, --name NAME                  読みやすい名前（必須）
        -a, --address ADDRESS[:PORT]     このノードのアドレス（必須）
        -l, --listen HOST[:PORT]         待ち受けアドレス
        -g, --rsid IDs                   参加するレプリカセット番号（必須）
        -L, --location STRING            このノードの位置
        -s, --store PATH                 データを保存するディレクトリ（必須）
        -u, --ulog PATH                  更新ログを保存するディレクトリ
        -r, --rts PATH                   リレータイムスタンプを保存するディレクトリ
        -t, --http PORT                  HTTPの待ち受けポート
            --http-error-page PATH       HTTPのエラーページ用のeRubyテンプレートファイル
            --http-redirect-port PORT
            --http-redirect-path FORMAT
        -R, --read-only                  読み込み専用モード
        -N, --read-only-name NAME        スナップショット時刻を指定した読み込み専用モード
        -T, --read-only-time TIME        バージョン名を指定した読み込み専用モード
            --fault_store PATH           障害状況を永続的にキャッシュするパス
            --membership_store PATH      メンバシップを永続的にキャッシュするパス
            --weight_store PATH          重み情報を永続的にキャッシュするパス
        -o, --log PATH
        -v, --verbose                    デバッグメッセージを表示する
            --trace                      デバッグメッセージとトレースメッセージを表示する
            --color-log                  強制的にログに色を付ける

-c, --cs ADDRESS
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

CS (Configuration Server) のアドレスを指定します。

-i, --nid ID
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

このサーバの一意な識別子を整数で指定します。

-n, --name NAME
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

このサーバの名前を指定します。この名前は管理ツールで使われます。

-a, --address ADDRESS[:PORT]
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

このサーバのアドレスを指定します。このサーバは、ここで指定したアドレスでアクセスされます。

デフォルトのポート番号は18900です。

-g, --rsid RSIDs
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

このサーバが参加するレプリカセットの番号を指定します。

--http-error-page PATH
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

HTTPでファイルが見つからなかった場合などに表示されるエラーページをカスタマイズするには、この引数にeRubyテンプレートファイルを指定します。


-L, --location STRING
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

→ :ref:`ja_howto_location`


-s, --store PATH
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

データを保存するディレクトリを指定します。

このディレクトリには障害状況ファイル（fault store）、メンバシップファイル（membership store）、重み情報ファイル（weight store）も永続的にキャッシュされます。

このオプションは、 *--fault_store* 、 *--membership* 、 *--weight_store* オプションで一部を上書きすることができます。


.. _ja_command_standalone:

ls4-standalone: Stand-alone Server
^^^^^^^^^^^^^^^^^^^^^^

ls4-standaloneは、単一のプロセスでサーバ機能を提供するプログラムです。LS4の検証に使用できます。

::

    Usage: ls4-standalone [options]
        -p, --port PORT                  待ち受けポート
        -l, --listen HOST                待ち受けアドレス
        -m, --mds EXPR                   メタデータサーバのアドレス
        -M, --mds-cache EXPR             メタデータキャッシュサーバのアドレス
        -s, --store PATH                 データを保存するディレクトリ
        -u, --ulog PATH                  更新ログを保存するディレクトリ
        -r, --rts PATH                   更新ログを保存するディレクトリ
        -t, --http PORT                  HTTPの待ち受けポート
            --http-error-page PATH       HTTPのエラーページ用のeRubyテンプレートファイル
            --http-redirect-port PORT
            --http-redirect-path FORMAT
        -R, --read-only                  読み込み専用モード
        -N, --read-only-name NAME        スナップショット時刻を指定した読み込み専用モード
        -T, --read-only-time TIME        バージョン名を指定した読み込み専用モード
            --fault_store PATH           障害状況を永続的にキャッシュするパス
            --membership_store PATH      メンバシップを永続的にキャッシュするパス
            --weight_store PATH          重み情報を永続的にキャッシュするパス
        -o, --log PATH
        -v, --verbose                    デバッグメッセージを表示する
            --trace                      デバッグメッセージとトレースメッセージを表示する
            --color-log                  強制的にログに色を付ける

.. TODO


.. _ja_command_ctl:

ls4ctl: 管理ツール
----------------------

::

    Usage: ls4ctl <cs address[:port]> <command> [options]
    command:
       nodes                        ノード一覧を表示する
       stat                         統計情報を表示する
       remove_node <nid>            レプリカセットからノードを取り除く
       locate <key>                 キーがどのサーバに保存されているかを表示する
       weight                       レプリカセットの重みを表示する
       set_weight <rsid> <weight>   レプリカセットの重みを変更する
       mds                          MDSのアドレスを表示する
       set_mds <expr>               MDSのアドレスを変更する
       mds_cache                    MDSキャッシュのアドレスを表示する
       set_mds_cache <expr>         MDSキャッシュのアドレスを変更する
       items                        保存されているデータの数を表示する
       version                      各ノードのバージョンを表示する

関連： :ref:`ja_plugin_mds`

関連： :ref:`ja_plugin_mds_cache`


.. _ja_command_cmd:

ls4cmd: コマンドラインクライアント
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

.. TODO


.. _ja_command_rpc:

ls4rpc: RPCコマンドラインクライアント
----------------------

::

    Usage: ls4rpc <host>:<port> [method [args ...]]

ホスト名とポート番号を指定して起動すると、対話型シェル（IRB; interactive Ruby）が起動します。対話型シェルでは、 *show* とタイプするとサポートされているメソッドの一覧が表示されます。

メソッド名と引数を指定して起動すると、RPCを1回発行して終了します。各引数はYAML形式で指定します。返り値はYAML形式で標準出力に表示されます。

ポート番号は明示する必要があります。サーバのデフォルトのポート番号は以下の通りです：

  CS
    18700
  DS
    18900
  GW
    18800


.. _ja_command_top:

ls4top: 'top'風の負荷監視ツール
----------------------

::

    Usage: ls4top <cs address[:port]>

CSのアドレスとポート番号を指定して起動すると、UNIXのtopコマンドのように負荷の監視ができます。

sキーを押すと、標準モード/短縮モードの2つの表示方法を切り替えることができます。また、ウィンドウのサイズに応じて表示方法が切り替わります。


.. _ja_command_stat:

ls3stat: 監視/可視化システム向けの状態収集ツール
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
        -a, --array                      連想配列形式の代わりに配列形式で結果を表示する
        -o, --only NID_OR_NAMES          このサーバの情報だけを表示する
        -t, --tsv                        結果の表示にTab-Separated-Values形式を使う (デフォルト)
        -j, --json                       結果の表示にJSON形式を使う
        -m, --msgpack                    結果の表示にMessagePack形式を使う
        -y, --yaml                       結果の表示にYAML形式を使う

ls3statは、NagiosやMUNINなどの監視/可視化システム向けの情報収集ツールです。様々な書式で統計情報を表示することができます。

第一引数には、CSのアドレスとポート番号を指定します。

オプション以降の引数には、取得したい情報の名前を指定します。次の情報を取得することができます：

  nid
    サーバの識別子
  address
    IPアドレスとポート番号
  name
    --name引数で指定したサーバの名前
  rsid
    --rsid引数で指定したレプリカセットの番号
  location
    --location引数で指定したサーバの位置
  state
    サーバの状態（activeまたはFAULT）
  time
    動作しているホストのシステム時刻
  uptime
    プロセスの起動時間
  pid
    プロセスID
  version
    バージョン
  read
    参照操作の処理回数（起動時点からの累積回数）
  write
    更新操作の処理回数（起動時点からの累積回数）
  delete
    削除操作の処理回数（起動時点からの累積回数）
  items
    保存されているデータの数

*-a* オプションを指定すると、結果を配列形式で表示します。省略すると連想配列形式で表示します。

取得したい情報を1つも指定しなかった場合は、以下のように起動した場合と同じ動作になります：

::

    $ ls4stat <cs address[:port]> --tsv nid address name read write delete time


