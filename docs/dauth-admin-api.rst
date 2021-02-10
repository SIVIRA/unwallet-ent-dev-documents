============================
dAuth Admin API リファレンス
============================

概要
====

dAuth の管理操作（OIDC クライアントアプリケーションの登録やプロバイダー PID の作成、デジタルアセットの発行など）を行う API。

ベース URL
----------

.. code-block::

    https://api.admin.dev.dauth.world

スキーマ
--------

リクエスト・レスポンスともに、すべてのデータの ``Content-Type`` は ``application/json`` とする。また、すべてのタイムスタンプは UNIX time 形式で表現される。

認証
----

dAuth アカウントに対して発行される開発者コードを ``Authorization`` ヘッダに設定する。

クライアントエラー
------------------

クライアントエラーは以下の 3 タイプに分類され、それぞれに対応する HTTP ステータスコードが設定されたレスポンスが返却される。

* ``400 Bad Request``：不正な形式の JSON が送信されたり、リクエストの内容が不正だったりした場合
* ``401 Unauthorized``：無効な認証情報で認証しようとした場合
* ``404 Not Found``：指定したリソースが見つからなかった場合

なお、すべてのエラーレスポンスのボディは以下の形式で表現される。

.. code-block:: json

    { "message": "..." }

追加データ型
============

pid
---

`EOS naming conventions`_ に従った 13 文字の文字列。文字列の末尾は ``.pid``。

.. code-block::
    :caption: example

    "xxxxxxxxx.pid"

その他
------

以下のデータ型は、`EOS built-in types`_ の同名のデータ型の文字列表記を表すこととする。

asset
^^^^^

数量とシンボルを組み合わせた文字列。

.. code-block::
    :caption: example

    "1 TOKEN"

public_key
^^^^^^^^^^

公開鍵を表す文字列。

.. code-block::
    :caption: example

    "EOS7VRGNds4uyZVUxYW9G7iAeXmLnTDBjQN9XnCMkaDFfN1ppATjY"

signature
^^^^^^^^^

電子署名を表す文字列。

.. code-block::
    :caption: example

    "SIG_K1_KhcfweTEr66h6K8dNMz77RZvLJqu7C5SvhLE9KP1EdgELTLB8qf99HgTzjrtHdSuehfoVjujNiC5qbEc7dVh6PN8zZhycU"

エンドポイント一覧
==================

GET /user
---------

認証されたユーザーの情報を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

なし

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {
      "id": 1,
      "name": "dev",
      "email": "dev@sivira.co",
      "emailVerified": true,
      "developerCode": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "updatedAt": 1231006505,
      "createdAt": 1231006505
    }

================= ======= ===========
Name              Type    Description
================= ======= ===========
``id``            integer ユーザーの ID
``name``          string  ユーザーの名称
``email``         string  ユーザーのメールアドレス
``emailVerified`` boolean ユーザーのメールアドレスが検証済みかどうか
``developerCode`` string  ユーザーの開発者コード
``updatedAt``     integer ユーザー情報の最終更新時刻
``createdAt``     integer ユーザー登録時刻
================= ======= ===========

PATCH /user
-----------

認証されたユーザーの情報を更新する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

======== ====== ==== ===========
Name     Type   In   Description
======== ====== ==== ===========
``name`` string body ユーザーの名称
======== ====== ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

POST /user/applications
-----------------------

認証されたユーザーが操作権限を有する新しいアプリケーションを作成する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

============= ============= ==== ===========
Name          Type          In   Description
============= ============= ==== ===========
``name``      string        body アプリケーションの名称
``type``      string        body アプリケーションの種別（``native`` ``non_interactive`` ``regular_web`` ``spa`` のいずれか）
``callbacks`` array[string] body アプリケーションのコールバック URL 一覧
============= ============= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {
      "id": 1,
      "name": "Sample SPA App",
      "type": "spa",
      "clientID": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "clientSecret": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "callbacks": [
        "https://sample-spa.app/callback"
      ],
      "updatedAt": 1231006505,
      "createdAt": 1231006505
    }

================ ============= ===========
Name             Type          Description
================ ============= ===========
``id``           integer       アプリケーションの ID
``name``         string        アプリケーションの名称
``type``         string        アプリケーションの種別（``native`` ``non_interactive`` ``regular_web`` ``spa`` のいずれか）
``clientID``     string        アプリケーションのクライアント ID
``clientSecret`` string        アプリケーションのクライアントシークレット
``callbacks``    array[string] アプリケーションのコールバック URL　一覧
``updatedAt``    integer       アプリケーション情報の最終更新時刻
``createdAt``    integer       アプリケーションの作成時刻
================ ============= ===========

GET /user/applications
----------------------

認証されたユーザーが操作権限を有するアプリケーションの一覧を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

なし

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    [
      {
        "id": 1,
        "name": "Sample SPA App",
        "type": "spa",
        "clientID": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "clientSecret": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "callbacks": [
          "https://sample-spa.app/callback"
        ],
        "updatedAt": 1231006505,
        "createdAt": 1231006505
      }
    ]

================ ============= ===========
Name             Type          Description
================ ============= ===========
``id``           integer       アプリケーションの ID
``name``         string        アプリケーションの名称
``type``         string        アプリケーションの種別（``native`` ``non_interactive`` ``regular_web`` ``spa`` のいずれか）
``clientID``     string        アプリケーションのクライアント ID
``clientSecret`` string        アプリケーションのクライアントシークレット
``callbacks``    array[string] アプリケーションのコールバック URL　一覧
``updatedAt``    integer       アプリケーション情報の最終更新時刻
``createdAt``    integer       アプリケーションの作成時刻
================ ============= ===========

POST /user/identities
---------------------

認証されたユーザーが操作権限を有する新しいアイデンティティを作成する。

.. note:: 本エンドポイントは、dAuth wallet 経由で実行してください。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

============= ========== ==== ===========
Name          Type       In   Description
============= ========== ==== ===========
``name``      string     body アイデンティティの名称
``pid``       pid        body アイデンティティの PID 識別子
``signature`` signature  body Initial Signed Hash に対する電子署名
``publicKey`` public_key body アイデンティティの管理者権限公開鍵
============= ========== ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {
      "id": 1,
      "name": "Sample Identity",
      "pid": "xxxxxxxxx.pid",
      "publicKey": "EOSxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "nonce": 1,
      "updatedAt": 1231006505,
      "createdAt": 1231006505
    }

============= ========== ===========
Name          Type       Description
============= ========== ===========
``id``        integer    アイデンティティの ID
``name``      string     アイデンティティの名称
``pid``       pid        アイデンティティの PID 識別子
``publicKey`` public_key アイデンティティに対して権限を有するキーに対応する公開鍵
``nonce``     integer    アイデンティティのナンス（リプレイアタックを防ぐための数字であり、該当アイデンティティによってトランザクションが実行される度にインクリメントされる）
``updatedAt`` integer    アイデンティティ情報の最終更新時刻
``createdAt`` integer    アイデンティティ作成時刻
============= ========== ===========

GET /user/identities
--------------------

認証されたユーザーが操作権限を有するアイデンティティの一覧を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

なし

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    [
      {
        "id": 1,
        "name": "Sample Identity",
        "pid": "xxxxxxxxx.pid",
        "publicKey": "EOSxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "nonce": 1,
        "updatedAt": 1231006505,
        "createdAt": 1231006505
      }
    ]

============= ========== ===========
Name          Type       Description
============= ========== ===========
``id``        integer    アイデンティティの ID
``name``      string     アイデンティティの名称
``pid``       pid        アイデンティティの PID 識別子
``publicKey`` public_key アイデンティティに対して権限を有するキーに対応する公開鍵
``nonce``     integer    アイデンティティのナンス（リプレイアタックを防ぐための数字であり、該当アイデンティティによってトランザクションが実行される度にインクリメントされる）
``updatedAt`` integer    アイデンティティ情報の最終更新時刻
``createdAt`` integer    アイデンティティ作成時刻
============= ========== ===========

GET /applications/{applicationID}
---------------------------------

指定したアプリケーションの情報を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

================= ======= ==== ===========
Name              Type    In   Description
================= ======= ==== ===========
``applicationID`` integer path アプリケーションの ID
================= ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    [
      {
        "id": 1,
        "name": "Sample SPA App",
        "type": "spa",
        "clientID": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "clientSecret": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "callbacks": [
          "https://sample-spa.app/callback"
        ],
        "updatedAt": 1231006505,
        "createdAt": 1231006505
      }
    ]

================ ============= ===========
Name             Type          Description
================ ============= ===========
``id``           integer       アプリケーションの ID
``name``         string        アプリケーションの名称
``type``         string        アプリケーションの種別（``native`` ``non_interactive`` ``regular_web`` ``spa`` のいずれか）
``clientID``     string        アプリケーションのクライアント ID
``clientSecret`` string        アプリケーションのクライアントシークレット
``callbacks``    array[string] アプリケーションのコールバック URL　一覧
``updatedAt``    integer       アプリケーション情報の最終更新時刻
``createdAt``    integer       アプリケーションの作成時刻
================ ============= ===========

PATCH /applications/{applicationID}
-----------------------------------

指定したアプリケーションの情報を更新する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

================= ============= ==== ===========
Name              Type          In   Description
================= ============= ==== ===========
``applicationID`` integer       path アプリケーションの ID
``name``          string        body アプリケーションの名称
``type``          string        body アプリケーションの種別（``native`` ``non_interactive`` ``regular_web`` ``spa`` のいずれか）
``callbacks``     array[string] body アプリケーションのコールバック URL 一覧
================= ============= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

DELETE /applications/{applicationID}
------------------------------------

指定したアプリケーションを削除する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

================= ======= ==== ===========
Name              Type    In   Description
================= ======= ==== ===========
``applicationID`` integer path アプリケーションの ID
================= ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

GET /applications/{applicationID}/admins
----------------------------------------

指定したアプリケーションに対して操作権限を有するユーザーの一覧を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

================= ======= ==== ===========
Name              Type    In   Description
================= ======= ==== ===========
``applicationID`` integer path アプリケーションの ID
================= ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    [
      {
        "id": 1,
        "name": "dev",
        "email": "dev@sivira.co"
      }
    ]

========= ======= ===========
Name      Type    Description
========= ======= ===========
``id``    integer ユーザーの ID
``name``  string  ユーザーの名称
``email`` string  ユーザーのメールアドレス
========= ======= ===========

PUT /applications/{applicationID}/admins/{userID}
-------------------------------------------------

指定したアプリケーションに対して操作権限を有するユーザーを追加する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

================= ======= ==== ===========
Name              Type    In   Description
================= ======= ==== ===========
``applicationID`` integer path アプリケーションの ID
``userID``        integer path ユーザーの ID
================= ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

DELETE /applications/{applicationID}/admins/{userID}
----------------------------------------------------

指定したアプリケーションに対して指定したユーザーが有する操作権限を削除する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

================= ======= ==== ===========
Name              Type    In   Description
================= ======= ==== ===========
``applicationID`` integer path アプリケーションの ID
``userID``        integer path ユーザーの ID
================= ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

GET /identities/{identityID}
----------------------------

指定したアイデンティティの情報を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

============== ======= ==== ===========
Name           Type    In   Description
============== ======= ==== ===========
``identityID`` integer path アイデンティティの ID
============== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {
      "id": 1,
      "name": "Sample Identity",
      "pid": "xxxxxxxxx.pid",
      "publicKey": "EOSxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "nonce": 1,
      "updatedAt": 1231006505,
      "createdAt": 1231006505
    }

============= ========== ===========
Name          Type       Description
============= ========== ===========
``id``        integer    アイデンティティの ID
``name``      string     アイデンティティの名称
``pid``       pid        アイデンティティの PID 識別子
``publicKey`` public_key アイデンティティに対して権限を有するキーに対応する公開鍵
``nonce``     integer    アイデンティティのナンス（リプレイアタックを防ぐための数字であり、該当アイデンティティによってトランザクションが実行される度にインクリメントされる）
``updatedAt`` integer    アイデンティティ情報の最終更新時刻
``createdAt`` integer    アイデンティティ作成時刻
============= ========== ===========

PATCH /identities/{identityID}
------------------------------

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

指定したアイデンティティの情報を更新する。

============== ======= ==== ===========
Name           Type    In   Description
============== ======= ==== ===========
``identityID`` integer path アイデンティティの ID
``name``       string  body アイデンティティの名称
============== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

GET /identities/{identityID}/admins
-----------------------------------

指定したアイデンティティに対して操作権限を有するユーザーの一覧を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

============== ======= ==== ===========
Name           Type    In   Description
============== ======= ==== ===========
``identityID`` integer path アイデンティティの ID
============== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    [
      {
        "id": 1,
        "name": "dev",
        "email": "dev@sivira.co"
      }
    ]

========= ======= ===========
Name      Type    Description
========= ======= ===========
``id``    integer ユーザーの ID
``name``  string  ユーザーの名称
``email`` string  ユーザーのメールアドレス
========= ======= ===========

PUT /identities/{identityID}/admins/{userID}
--------------------------------------------

指定したアイデンティティに対して操作権限を有するユーザーを追加する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

============== ======= ==== ===========
Name           Type    In   Description
============== ======= ==== ===========
``identityID`` integer path アイデンティティの ID
``userID``     integer path ユーザーの ID
============== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

DELETE /identities/{identityID}/admins/{userID}
-----------------------------------------------

指定したアイデンティティに対して指定したユーザーが有する操作権限を削除する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

============== ======= ==== ===========
Name           Type    In   Description
============== ======= ==== ===========
``identityID`` integer path アイデンティティの ID
``userID``     integer path ユーザーの ID
============== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

POST /identities/{identityID}/assetSources
------------------------------------------

指定したアイデンティティによって新しいアセットソースを作成する。

.. note:: 本エンドポイントは、dAuth wallet 経由で実行してください。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

================ ========= ==== ===========
Name             Type      In   Description
================ ========= ==== ===========
``identityID``   integer   path アセットソースを作成するアイデンティティの ID
``id``           integer   body アセットソースの ID
``maxSupply``    asset     body アセットの発行量上限
``transferable`` boolean   body アセットの譲渡可否（無指定は ``false``）
``metadata``     string    body アセットのメタデータ（JSON 文字列）
``signature``    signature body アセットソース作成アクションに対応した Signed Hash に対する電子署名
================ ========= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {
      "id": 1,
      "identityID": 1,
      "supply": "0 TOKEN",
      "maxSupply": "100000000 TOKEN",
      "transferable": true,
      "metadata": "{\"name\": \"Sample Transferable Fungible Token\"}",
      "createdAt": 1231006505
    }

================ ======= ===========
Name             Type    Description
================ ======= ===========
``id``           integer アセットソースの ID
``identityID``   integer アセットソースを作成するアイデンティティの ID
``supply``       asset   アセットの総発行量
``maxSupply``    asset   アセットの発行量上限
``transferable`` boolean アセットの譲渡可否（無指定は ``false``）
``metadata``     string  アセットのメタデータ（JSON 文字列）
``createdAt``    integer アセットソース作成時刻
================ ======= ===========

GET /identities/{identityID}/assetSources
-----------------------------------------

指定したアイデンティティによって作成されたアセットソースの一覧を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

============== ======= ==== ===========
Name           Type    In   Description
============== ======= ==== ===========
``identityID`` integer path アイデンティティの ID
============== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    [
      {
        "id": 1,
        "identityID": 1,
        "supply": "0 TOKEN",
        "maxSupply": "100000000 TOKEN",
        "transferable": true,
        "metadata": "{\"name\": \"Sample Transferable Fungible Token\"}",
        "createdAt": 1231006505
      }
    ]

================ ======= ===========
Name             Type    Description
================ ======= ===========
``id``           integer アセットソースの ID
``identityID``   integer アセットソースを作成するアイデンティティの ID
``supply``       asset   アセットの総発行量
``maxSupply``    asset   アセットの発行量上限
``transferable`` boolean アセットの譲渡可否（無指定は ``false``）
``metadata``     string  アセットのメタデータ（JSON 文字列）
``createdAt``    integer アセットソース作成時刻
================ ======= ===========

GET /assetSources/{assetSourceID}
---------------------------------

指定したアセットソースの情報を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

================= ======= ==== ===========
Name              Type    In   Description
================= ======= ==== ===========
``assetSourceID`` integer path アセットソースの ID
================= ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {
      "id": 1,
      "identityID": 1,
      "supply": "0 TOKEN",
      "maxSupply": "100000000 TOKEN",
      "transferable": true,
      "metadata": "{\"name\": \"Sample Transferable Fungible Token\"}",
      "createdAt": 1231006505
    }

================ ======= ===========
Name             Type    Description
================ ======= ===========
``id``           integer アセットソースの ID
``identityID``   integer アセットソースを作成するアイデンティティの ID
``supply``       asset   アセットの総発行量
``maxSupply``    asset   アセットの発行量上限
``transferable`` boolean アセットの譲渡可否（無指定は ``false``）
``metadata``     string  アセットのメタデータ（JSON 文字列）
``createdAt``    integer アセットソース作成時刻
================ ======= ===========

POST /assetSources/{assetSourceID}/assets
-----------------------------------------

指定したアセットソースから新しいアセットを発行する。

.. note:: 本エンドポイントは、dAuth wallet 経由で実行してください。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

================= ========= ==== ===========
Name              Type      In   Description
================= ========= ==== ===========
``assetSourceID`` integer   path アセットソースの ID
``pid``           pid       body アセット発行先 PID の識別子
``quantity``      asset     body アセットの発行量
``signature``     signature body アセット発行アクションに対応した Signed Hash に対する電子署名
================= ========= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {
      "assetSourceID": 1,
      "pid": "xxxxxxxxx.pid",
      "quantity": "1 TOKEN"
    }

================= ======= ===========
Name              Type    Description
================= ======= ===========
``assetSourceID`` integer アセットソースの ID
``pid``           pid     アセット発行先 PID の識別子
``quantity``      asset   アセットの発行量
================= ======= ===========

GET /search/users
-----------------

ユーザーを検索する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

===== ====== ===== ===========
Name  Type   In    Description
===== ====== ===== ===========
``q`` string query 検索キーワード
===== ====== ===== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    [
      {
        "id": 1,
        "name": "dev",
        "email": "dev@sivira.co"
      }
    ]

========= ======= ===========
Name      Type    Description
========= ======= ===========
``id``    integer ユーザーの ID
``name``  string  ユーザーの名称
``email`` string  ユーザーのメールアドレス
========= ======= ===========

.. _EOS naming conventions: https://developers.eos.io/manuals/eosio.cdt/latest/best-practices/naming-conventions
.. _EOS built-in types: https://github.com/EOSIO/eos/blob/de78b49b5765c88f4e005046d1489c3905985b94/libraries/chain/abi_serializer.cpp#L89-L127
