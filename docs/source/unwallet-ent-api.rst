====================================
unWallet Enterprise API リファレンス
====================================

概要
====

unWallet のクライアントアプリケーション（unWallet をアイデンティティプロバイダーとして利用するアプリケーション）のバックエンド構築をサポートする API。

.. caution::

  本 API を利用するには、unWallet Enterprise で作成したプロバイダーウォレットの API トークンが必要です。これらは各クライアントアプリケーションの秘密情報であるため、フロントエンドで保管しないようにしてください。すなわち、本 API は原則としてバックエンドから利用するようにしてください。

ベース URL
----------

======= ========
Env     Base URL
======= ========
mainnet https://api.ent.unwallet.world
testnet https://api.ent.unwallet.dev
======= ========

スキーマ
--------

リクエスト・レスポンスともに、すべてのデータの ``Content-Type`` は ``application/json`` とする。また、すべてのタイムスタンプは UNIX time 形式で表現される。

.. _auth:

認証
----

unWallet Enterprise で作成したプロバイダーウォレットの API トークンを ``Authorization`` ヘッダに設定する。

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

POST /tokens/initialize
-----------------------

新規トークンを登録する。

.. note::

    POST /tokens/initialize を実行した時点におけるトークンの発行量は 0 です。発行は POST /tokens/mint で行います。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

=============== ======= ==== ===========
Name            Type    In   Description
=============== ======= ==== ===========
``id``          integer body トークンの ID
``name``        string  body トークンの名称
``description`` string  body トークンの詳細
``image``       string  body トークンの画像 URL
=============== ======= ==== ===========

.. caution::

    トークンはブロックチェーン上に存在するため、ID は（他のユーザーが発行したトークン含め）既存のトークンと重複しない値を指定する必要があります。

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

POST /tokens/mint
-----------------

トークンを発行する。

.. caution::

    POST /tokens/mint を実行する前に、POST /tokens/initialize を実行してトークンを登録する必要があります。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

========== ======= ==== ===========
Name       Type    In   Description
========== ======= ==== ===========
``id``     integer body トークンの ID
``to``     string  body トークンの発行先アドレス
``amount`` integer body トークンの発行量
========== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

GET /tokens
-----------

API の実行主体であるプロバイダーウォレットが登録したトークンの一覧を取得する。

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
        "name": "Token1",
        "description": "Token 1",
        "image": "https://dummyimage.com/256x256/0092a5/ffffff.png",
        "updatedAt": 1231006505,
        "createdAt": 1231006505
      },
      {
        "id": 2,
        "providerWalletID": "epBqMBla",
        "name": "Token2",
        "description": "Token 2",
        "image": "https://dummyimage.com/256x256/0092a5/ffffff.png",
        "updatedAt": 1231006505,
        "createdAt": 1231006505
      }
    ]

==================== ======= ===========
Name                 Type    Description
==================== ======= ===========
``id``               integer トークンの ID
``providerWalletID`` string  トークンを発行したプロバイダーウォレットの ID
``name``             string  トークンの名称
``description``      string  トークンの詳細
``image``            string  トークンの画像 URL
``updatedAt``        integer トークンの（メタデータの）最終更新日時
``createdAt``        integer トークンの登録日時
==================== ======= ===========

GET /tokens/{id}
---------------------

指定したトークンの情報を取得する

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

====== ======= ==== ===========
Name   Type    In   Description
====== ======= ==== ===========
``id`` integer path トークンの ID
====== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {
      "id": 1,
      "providerWalletID": "epBqMBla",
      "name": "Token1",
      "description": "Token 1",
      "image": "https://dummyimage.com/256x256/0092a5/ffffff.png",
      "updatedAt": 1231006505,
      "createdAt": 1231006505
    }

==================== ======= ===========
Name                 Type    Description
==================== ======= ===========
``id``               integer トークンの ID
``providerWalletID`` string  トークンを発行したプロバイダーウォレットの ID
``name``             string  トークンの名称
``description``      string  トークンの詳細
``image``            string  トークンの画像 URL
``updatedAt``        integer トークンのメタデータの最終更新日時
``createdAt``        integer トークンの登録日時
==================== ======= ===========

PATCH /tokens/{id}
-----------------------

指定したトークンのメタデータを更新する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

=============== ======= ==== ===========
Name            Type    In   Description
=============== ======= ==== ===========
``id``          integer path トークンの ID
``name``        string  body トークンの名称
``description`` string  body トークンの詳細
``image``       string  body トークンの画像 URL
=============== ======= ==== ===========

レスポンスボディ
^^^^^^^^^^^^^^^^

.. code-block:: json

    {}

POST /metaTransactions
----------------------

指定したコントラクトウォレットアカウントから指定した処理を実行する。

リクエストパラメータ
^^^^^^^^^^^^^^^^^^^^

=============== ======= ==== ===========
Name            Type    In   Description
=============== ======= ==== ===========
``executor``    address body 処理を実行するコントラクトウォレットアカウントのアドレス
``data``        string  body 処理の内容
``signature``   string  body 処理の内容に対する電子署名
=============== ======= ==== ===========

.. note::

    ``signature`` は、``executor`` のオーナーであるブロックチェーンアカウントによって作成されたものである必要があります。

    リクエストパラメータを用意する方法については `unWallet client-side SDK`_ のドキュメントを参照してください。

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

.. _unWallet client-side SDK: https://github.com/SIVIRA/unwallet-client-sdk-js
.. _ERC55: https://github.com/ethereum/EIPs/blob/master/EIPS/eip-55.md
