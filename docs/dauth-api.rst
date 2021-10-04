======================
dAuth API リファレンス
======================

概要
====

dAuth のクライアントアプリケーション（dAuth をアイデンティティプロバイダーとして利用するアプリケーション）のバックエンド構築をサポートする API。

.. caution::

  本 API を利用するには、dAuth で作成したプロバイダーウォレットのクライアント ID とクライアントシークレットを元に取得できる API アクセストークンが必要です（下記 :ref:`認証 <auth>` の項を参照）。これらは各クライアントアプリケーションの秘密情報であるため、フロントエンドで保管しないようにしてください。すなわち、本 API は原則としてバックエンドから利用するようにしてください。

ベース URL
----------

.. code-block::

    https://api.manage-dev.dauth.world

スキーマ
--------

リクエスト・レスポンスともに、すべてのデータの ``Content-Type`` は ``application/json`` とする。また、すべてのタイムスタンプは UNIX time 形式で表現される。

.. _auth:

認証
----

dAuth で作成したプロバイダーウォレットのクライアント ID とクライアントシークレットを利用して以下（Client Credentials Flow）を実行することで取得できるアクセストークンを ``Authorization`` ヘッダに設定する。

.. code-block:: sh

    $ curl -X POST \
      https://auth.manage-dev.dauth.world/oauth/token \
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

データ型
============

各エンドポイントのリクエストパラメータ表に記載された Type は JSON データ型であるが、一部、厳密に特定のフォーマットを要求するデータ型が存在するため、それらについて補足説明を行う。以降、これらを指す場合は、JSON データ型の名称（string など）ではなく、以下で定義する名称（address など）を使用することとする。

address
-------

ブロックチェーンアカウントの識別子に相当する 20 バイトの値の 16 進数文字列表記。

.. note::

    英字に関しては大文字小文字を問いませんが、本 API のレスポンスに含まれる address には、大文字と小文字が混在した（`ERC55`_ に準拠してエンコーディングが行われた）値が使用されます。

.. code-block::
    :caption: example

    "0xC6Fb61820696416639fce82E00f24C5DAe63c89C"

エンドポイント一覧
==================

POST /assets/initialize
-----------------------

新規デジタルアセットを登録する。

.. note::

    POST /assets/initialize を実行した時点におけるデジタルアセットの発行量は 0 です。発行は POST /assets/mint で行います。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

=============== ======= ==== ===========
Name            Type    In   Description
=============== ======= ==== ===========
``id``          integer body デジタルアセットの ID
``name``        string  body デジタルアセットの名称
``description`` string  body デジタルアセットの詳細
``image``       string  body デジタルアセットの画像 URL
=============== ======= ==== ===========

.. caution::

    デジタルアセットはブロックチェーン上に存在するため、ID は（他のユーザーが発行したデジタルアセット含め）既存のデジタルアセットと重複しない値を指定する必要があります。

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

POST /assets/mint
-----------------

デジタルアセットを発行する。

.. caution::

    POST /assets/mint を実行する前に、POST /assets/initialize を実行してデジタルアセットを登録する必要があります。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

========== ======= ==== ===========
Name       Type    In   Description
========== ======= ==== ===========
``id``     integer body デジタルアセットの ID
``to``     string  body デジタルアセットの発行先アドレス
``amount`` string  body デジタルアセットの発行量
========== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

GET /assets
-----------

API の実行主体であるプロバイダーウォレットが登録したデジタルアセットの一覧を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

なし

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    [
      {
        "id": 1,
        "providerWalletID": "epBqMBla",
        "name": "Asset1",
        "description": "Asset 1",
        "image": "https://dummyimage.com/256x256/0092a5/ffffff.png",
        "updatedAt": 1231006505,
        "createdAt": 1231006505
      },
      {
        "id": 2,
        "providerWalletID": "epBqMBla",
        "name": "Asset2",
        "description": "Asset 2",
        "image": "https://dummyimage.com/256x256/0092a5/ffffff.png",
        "updatedAt": 1231006505,
        "createdAt": 1231006505
      }
    ]

==================== ======= ===========
Name                 Type    Description
==================== ======= ===========
``id``               integer デジタルアセットの ID
``providerWalletID`` string  デジタルアセットを発行したプロバイダーウォレットの ID
``name``             string  デジタルアセットの名称
``description``      string  デジタルアセットの詳細
``image``            string  デジタルアセットの画像 URL
``updatedAt``        integer デジタルアセットの（メタデータの）最終更新日時
``createdAt``        integer デジタルアセットの登録日時
==================== ======= ===========

GET /assets/{id}
---------------------

指定したデジタルアセットの情報を取得する

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

====== ======= ==== ===========
Name   Type    In   Description
====== ======= ==== ===========
``id`` integer path デジタルアセットの ID
====== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {
      "id": 1,
      "providerWalletID": "epBqMBla",
      "name": "Asset1",
      "description": "Asset 1",
      "image": "https://dummyimage.com/256x256/0092a5/ffffff.png",
      "updatedAt": 1231006505,
      "createdAt": 1231006505
    }

==================== ======= ===========
Name                 Type    Description
==================== ======= ===========
``id``               integer デジタルアセットの ID
``providerWalletID`` string  デジタルアセットを発行したプロバイダーウォレットの ID
``name``             string  デジタルアセットの名称
``description``      string  デジタルアセットの詳細
``image``            string  デジタルアセットの画像 URL
``updatedAt``        integer デジタルアセットのメタデータの最終更新日時
``createdAt``        integer デジタルアセットの登録日時
==================== ======= ===========

PATCH /assets/{id}
-----------------------

指定したデジタルアセットのメタデータを更新する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

=============== ======= ==== ===========
Name            Type    In   Description
=============== ======= ==== ===========
``id``          integer path デジタルアセットの ID
``name``        string  body デジタルアセットの名称
``description`` string  body デジタルアセットの詳細
``image``       string  body デジタルアセットの画像 URL
=============== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

GET /chain/identities/{address}/tokenBalances
---------------------------------------------

指定したブロックチェーンアカウントが保有するトークンの残高の一覧を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

=========== ======= ==== ===========
Name        Type    In   Description
=========== ======= ==== ===========
``address`` address path ブロックチェーンアカウントのアドレス
=========== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    [
      {
        "id": 1,
        "amount": 1
      },
      {
        "id": 2,
        "amount": 1
      }
    ]

========== ======= ===========
Name       Type    Description
========== ======= ===========
``id``     integer トークンの ID
``amount`` integer トークンの残高
========== ======= ===========

GET /chain/identities/{address}/badgeBalances
---------------------------------------------

指定したブロックチェーンアカウントが保有するバッジの残高の一覧を取得する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

=========== ======= ==== ===========
Name        Type    In   Description
=========== ======= ==== ===========
``address`` address path ブロックチェーンアカウントのアドレス
=========== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    [
      {
        "id": 1,
        "amount": 1
      },
      {
        "id": 2,
        "amount": 1
      }
    ]

========== ======= ===========
Name       Type    Description
========== ======= ===========
``id``     integer バッジ ID
``amount`` integer バッジ残高
========== ======= ===========

.. _ERC55: https://github.com/ethereum/EIPs/blob/master/EIPS/eip-55.md
