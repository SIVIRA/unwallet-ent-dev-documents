======================
dAuth API リファレンス
======================

概要
====

dAuth のクライアントアプリケーション（dAuth をアイデンティティプロバイダーとして利用するアプリケーション）のバックエンド構築をサポートする API。

.. caution::

  本 API を利用するには、dAuth で作成したアプリケーションのクライアント ID とクライアントシークレットを元に取得できる API アクセストークンが必要です。これらは各アプリケーションの秘密情報であるため、フロントエンドで保管しないようにしてください。すなわち、本 API は原則としてバックエンドから利用するようにしてください。

ベース URL
----------

.. code-block::

    https://api.manage-dev.dauth.world

スキーマ
--------

リクエスト・レスポンスともに、すべてのデータの ``Content-Type`` は ``application/json`` とする。また、すべてのタイムスタンプは UNIX time 形式で表現される。

認証
----

dAuth で作成したアプリケーションのクライアント ID とクライアントシークレットを利用して以下（Client Credentials Flow）を実行することで取得できるアクセストークンを ``Authorization`` ヘッダに設定する。

.. code-block:: sh

    $ curl -X POST \
      https://auth.id-dev.dauth.world/oauth/token \
      -H 'Content-Type: application/json' \
      -d '{ "client_id": "<YOUR_CLIENT_ID>", "client_secret": "<YOUR_CLIENT_SECRET>", "audience": "https://api.manage-dev.dauth.world", "grant_type": "client_credentials" }'

クライアントエラー
------------------

クライアントエラーは以下の 4 タイプに分類され、それぞれに対応する HTTP ステータスコードが設定されたレスポンスが返却される。

* ``400 Bad Request``：不正な形式の JSON が送信されたり、リクエストの内容が不正だったりした場合
* ``401 Unauthorized``：無効な認証情報で認証しようとした場合
* ``403 Forbidden``：利用可能でない（例えば、停止状態にある）アプリケーションの認証情報を用いて API を実行しようとした場合、もしくはアプリケーションに紐づくサブスクリプションが利用可能でない（例えば、停止状態にある）場合
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

bytes
^^^^^

バイト列の 16 進数表記文字列。

.. code-block::
    :caption: example

    "00000000000000000100000000000000004241444745000000"

checksum256
^^^^^^^^^^^

バイト列の 16 進数表記文字列。32 byte 固定。

.. code-block::
    :caption: example

    "fe329d8bc2a847096c381b2e2ac24878998195b92129269287049d57649e66ca"

name
^^^^

ref. `EOS naming conventions`_

.. code-block::
    :caption: example

    "pidregistry1"

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

GET /identities/{identityID}
----------------------------

指定したアイデンティティの情報を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

============== ==== ==== ===========
Name           Type In   Description
============== ==== ==== ===========
``identityID`` pid  path アイデンティティの ID
============== ==== ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {
      "id": "xxxxxxxxx.pid",
      "nonce": 1,
      "canHoldAssets": true
    }

================= ======= ===========
Name              Type    Description
================= ======= ===========
``id``            integer アイデンティティの ID
``nonce``         integer アイデンティティのナンス（リプレイアタックを防ぐための数字であり、該当アイデンティティによってトランザクションが実行される度にインクリメントされる）
``canHoldAssets`` boolean アイデンティティのアセット保有可否
================= ======= ===========

GET /identities/{identityID}/keys
---------------------------------

指定したアイデンティティに対して権限を有するキーの一覧を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

============== ==== ==== ===========
Name           Type In   Description
============== ==== ==== ===========
``identityID`` pid  path アイデンティティの ID
============== ==== ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    [
      {
        "id": 0,
        "type": "admin",
        "publicKey": "EOSxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "expiresAt": 0
      },
      {
        "id": 1,
        "type": "app",
        "publicKey": "EOSyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy",
        "expiresAt": 1231006505
      }
    ]

============= ========== ===========
Name          Type       Description
============= ========== ===========
``id``        pid        キーの ID
``type``      string     キーの種別（``"admin"`` もしくは ``"app"``）
``publicKey`` public_key キーに対応する公開鍵
``expiresAt`` integer    キーの有効期限（``0`` は無期限）
============= ========== ===========

GET /identities/{identityID}/assets
-----------------------------------

指定したアイデンティティが保有するアセットの一覧を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

============== ==== ==== ===========
Name           Type In   Description
============== ==== ==== ===========
``identityID`` pid  path アイデンティティの ID
============== ==== ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    [
      {
        "assetSourceID": 0,
        "quantity": "1 TOKEN"
      }
    ]

================= ======= ===========
Name              Type    Description
================= ======= ===========
``assetSourceID`` integer アセットソースの ID
``quantity``      asset   アセットの量
================= ======= ===========

POST /identities/{identityID}/transactions
------------------------------------------

指定したアイデンティティからトランザクションを実行する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

============== ========= ==== ===========
Name           Type      In   Description
============== ========= ==== ===========
``identityID`` pid       path アイデンティティの ID
``contract``   name      body 実行したいアクションを提供するコントラクトがデプロイされたアカウントの名前
``action``     name      body 実行したいアクションの名前
``data``       bytes     body 実行したいアクションの引数を EOS のエンコーディングルールに従ってエンコードしたデータ
``signature``  signature body トランザクションの内容に対応した Signed Hash に対する電子署名
============== ========= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {
      "id": "0000000000000000000000000000000000000000000000000000000000000000"
    }

====== =========== ===========
Name   Type        Description
====== =========== ===========
``id`` checksum256 トランザクションの ID
====== =========== ===========

GET /transactions/{transactionID}
---------------------------------

指定したトランザクションの情報を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

================= =========== ==== ===========
Name              Type        In   Description
================= =========== ==== ===========
``transactionID`` checksum256 path トランザクションの ID
================= =========== ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {
      "id": "0000000000000000000000000000000000000000000000000000000000000000",
      "status": "executed"
    }

========== =========== ===========
Name       Type        Description
========== =========== ===========
``id``     checksum256 トランザクションの ID
``status`` string      トランザクションのステータス（``"executing"`` もしくは ``"executed"``）
========== =========== ===========

.. _EOS naming conventions: https://developers.eos.io/manuals/eosio.cdt/latest/best-practices/naming-conventions
.. _EOS built-in types: https://github.com/EOSIO/eos/blob/de78b49b5765c88f4e005046d1489c3905985b94/libraries/chain/abi_serializer.cpp#L89-L127
