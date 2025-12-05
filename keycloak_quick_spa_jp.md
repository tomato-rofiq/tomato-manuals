---
layout: default
title: "JavaScript ベースのシングルページアプリケーション（SPA）に Keycloak を実装する方法"
permalink: /keycloak/jp/quickjs/
---

**最終更新日：2025年12月（作成者：Rofiq）** [JP](https://tomato-rofiq.github.io/tomato-manuals/keycloak/jp/quickjs) [EN](https://tomato-rofiq.github.io/tomato-manuals/keycloak/en/quickjs)

# JavaScript ベースのシングルページアプリケーション（SPA）に Keycloak を実装する方法

このガイドでは、JavaScript ベースのシングルページアプリケーション（SPA）に Keycloak を実装するための基本的な手順を説明しています。ここでの内容は、あくまで最小構成の実装例をローカル環境で動作させることを目的としたものです。

こちらで確認できるクイックスタートプロジェクトを使用します： [keycloak-spa-quickstart](https://github.com/keycloak/keycloak-quickstarts/tree/main/js/spa)

## 前提条件

Windows をご利用の場合は、WSL を使用してください。

このクイックスタートをコンパイルして実行するには、以下が必要です：
- Node.js 18.16.0 以上
- Keycloak 21 以上
- Docker 20 以上

## Keycloak サーバーの起動と設定

前回の Keycloak セットアップガイドに従っている場合、Keycloak サーバーを起動し、Keycloak 管理コンソールへアクセスする手順は同じです。ただし、今回使用する `docker run` コマンドは、セットアップガイドで使用したコマンドと以下の点が異なります：

- コンテナ名を「keycloak」として指定している
- ホストの 8180 番ポートをコンテナの 8180 番ポートにマッピングしている
- Keycloak にデフォルトの 8080 ではなく 8180 番ポートで待ち受けるよう指示している

以下のコマンドを Bash ターミナルで実行し、Docker 上で Keycloak を起動してください。  
その際、Docker Desktop を開いて、コンテナが実際に起動していることを確認するのを忘れないようにしてください。

```bash
docker run --name keycloak -p 8180:8180 -e KC_BOOTSTRAP_ADMIN_USERNAME=admin -e KC_BOOTSTRAP_ADMIN_PASSWORD=admin  quay.io/keycloak/keycloak:26.4.5 start-dev --http-port=8180
```

必要であれば、コマンド内で指定している Keycloak のバージョンを最新のものに変更して構いません。

![start keycloak in docker](./assets/images/quickstart_docker.png)

コマンドで Keycloak サーバーを Docker 上で起動したら、前回のガイドと同じ方法で管理コンソールにアクセスしてください。ユーザー名とパスワードは `admin` です。

次に、クイックスタートで提供されているレルムをインポートします。こちらから取得できます：  
[realm-import.json](https://github.com/keycloak/keycloak-quickstarts/blob/main/js/spa/config/realm-import.json)

上記リンクから JSON をコピー＆ペーストしても構いませんし、ダウンロードボタンを押してファイルとして取得しても問題ありません。便宜上、以下に生の JSON も掲載しています。

![import realm json](./assets/images/realm_import.png)

```json
{
  "realm": "quickstart",
  "enabled": true,
  "clients": [
    {
      "clientId": "spa",
      "enabled": true,
      "publicClient": true,
      "directAccessGrantsEnabled": true,
      "redirectUris": [ "http://localhost:8080/*" ]
    }
  ],
  "users" : [
    {
      "username" : "alice",
      "enabled": true,
      "email" : "alice@keycloak.org",
      "firstName": "Alice",
      "lastName": "Liddel",
      "credentials" : [
        { "type" : "password",
          "value" : "alice" }
      ],
      "realmRoles": [ "user", "offline_access"  ],
      "clientRoles": {
        "account": [ "manage-account" ]
      }
    },
    {
      "username" : "admin",
      "enabled": true,
      "email" : "test@admin.org",
      "firstName": "Admin",
      "lastName": "Test",
      "credentials" : [
        { "type" : "password",
          "value" : "admin" }
      ],
      "realmRoles": [ "user","admin" ],
      "clientRoles": {
        "realm-management": [ "realm-admin" ],
        "account": [ "manage-account" ]
      }
    }
  ],
  "roles" : {
    "realm" : [
      {
        "name": "user",
        "description": "User privileges"
      },
      {
        "name": "admin",
        "description": "Administrator privileges"
      }
    ]
  }
}
```

`realm-import.json` は、`quickstart` レルムに対して以下の属性を設定します：

- ローカルで動作するシングルページアプリ用のクライアント（spa）
- 2 名のユーザー（一般ユーザーと管理者）
- いくつかのロール（user、admin）およびクライアントレベルの権限

次に、Keycloak 管理コンソールから新しいレルムを作成する際、  
GitHub からコピーした JSON を Resource File の入力欄に貼り付けるか、  
Browse ボタンをクリックしてダウンロードした JSON ファイルを選択してください。  
その後、Create をクリックします。

![import realm json](./assets/images/create_realm3.png)

現在のレルムは、自動的にインポートされた `Quickstart` レルムに切り替わります。  
サーバー側での設定はこれで完了です。

## クイックスタート SPA のビルドとデプロイ

まだクイックスタートプロジェクトのリポジトリをクローンしていない場合は、こちらから GitHub のクイックスタートリポジトリをクローンしてください：  
[https://github.com/keycloak/keycloak-quickstarts/tree/main](https://github.com/keycloak/keycloak-quickstarts/tree/main)

Bash ターミナルを開き、プロジェクトのルートディレクトリへ移動します。  
今回の場合、下の画像のように SPA クイックスタートのプロジェクトフォルダーへ移動する必要があります。

![current directory](./assets/images/curr_dir.png)

以下のコマンドを実行して、クイックスタートを起動してください。

```bash
npm install
npm start
```

![npm install output](./assets/images/npm_install_start.png)

## クイックスタート SPA へのアクセス

以下の URL からアプリケーションにアクセスできます：  
[http://localhost:8080](http://localhost:8080)

次のいずれかのユーザーでログインを試してください：

| ユーザー名 | パスワード | ロール |
|-----------|------------|--------|
| alice     | alice      | user   |
| admin     | admin      | admin  |

![quickstart user login](./assets/images/quickstart_login.png)
![quickstart user login](./assets/images/quickstart_postlogin.png)

認証が完了すると、画面上部にある各ボタンに記載された動作を実行できるようになります。

これでクイックスタートは完了です。

## クイックスタート SPA ソースコードの解説

ここからは、クイックスタート SPA に実装されている Keycloak のソースコードを詳しく見ていきます。  
目的は、SPA 内で Keycloak がどのように動作しているのか、実際にどのようなコードが書かれているのかを理解することです。

まず、以下の場所にある `index.html` ファイルを開いてください：  
`keycloak-quickstarts/js/spa/public/index.html`

HTML コードの先頭、`<head>` 内に Keycloak のインポートがあります。  
ここでは keycloak-js の npm パッケージを読み込み、アプリケーション内で Keycloak の機能を使用できるようにしています。

```html
<script type="importmap">
  {
    "imports": {
      "keycloak-js": "/vendor/keycloak.js"
    }
  }
</script>
<link rel="modulepreload" href="/vendor/keycloak.js">
```

Keycloak-js のインポート方法はいくつかあり、ここで紹介しているのはその一例に過ぎません。  
もし他のインポート方法に興味があれば、このコードを参考にしつつ、任意の LLM に質問してみるとよいでしょう。

次に、`<body>` 内の div ですが、ここには画面上部に表示されていたすべてのボタンが定義されています。  
特に難しい部分はなく、説明すべき内容も多くありません。

```html
<div id="user" style="display: none;">
  <button id="logout" type="button">Logout</button>
  <button id="showMyAccount" type="button">My Account</button>
  <button id="showIdToken" type="button">Show ID Token</button>
  <button id="showAccessToken" type="button">Show Access Token</button>
  <button id="refreshToken" type="button">Refresh</button>
  <hr>
  <h2 id="name"></h2>
  <pre id="output"></pre>
</div>
```

div に続く `<script>` 内のコードが、この HTML で最も重要な部分です。  
ここでは、Keycloak の機能をどのように利用するのか、その最もシンプルな使用例が示されています。

```js
import Keycloak from "keycloak-js";
const outputElement = document.getElementById("output");
const nameElement = document.getElementById("name");
const userElement = document.getElementById("user");
```

JS コードは、まず keycloak-js ライブラリをインポートし、先ほどの div 内にある各 DOM 要素を取得するところから始まります。

その後に続くのが、2 つのヘルパー関数 `output(content)` と `showProfile()` です。  
これらは、後に記述されているボタン用のハンドラーから利用される補助関数として実装されています。

```js
function output(content) {
  if (typeof content === "object") {
    content = JSON.stringify(content, null, 2);
  }

  outputElement.textContent = content;
}

function showProfile() {
  const name =
    keycloak.idTokenParsed.name ||
    keycloak.idTokenParsed.preferred_username;

  nameElement.textContent = `Hello ${name}`;
  userElement.style.display = "block";
}
```

`output(content)` は、オブジェクトを整形した JSON に変換しつつ、出力エリアに表示するための関数です。  
`showProfile()` は ID トークンからユーザー名を取得して表示し、その後 UI を表示します。

このコードの後に、5 つのボタンに対応する 5 つのボタンハンドラーが続きます。

**logout ハンドラー**では `keycloak.logout()` を呼び出し、ユーザーをログアウトします。

**showIdToken ハンドラー**では `keycloak.idTokenParsed` を呼び出し、ID トークンを取得して `output()` ヘルパー関数で表示します。

**showAccessToken ハンドラー**では `keycloak.tokenParsed` を呼び出し、アクセストークンを取得して `output()` ヘルパー関数で表示します。

**refreshToken ハンドラー**が async 関数になっている理由は、内部で `await keycloak.updateToken(-1)` を呼んでいるためです。  
`updateToken()` は、トークンの有効期限が切れてから何秒後に更新するかを指定する関数で、`-1` を指定すると「すぐに更新する」という意味になります。

トークン更新後、新しいトークンは `output()` ヘルパー関数で表示されます。  
さらに念のため `showProfile()` が呼び出され、更新によってユーザー情報が変わっていた場合に備えて再表示されます（実際にはあまり起こりませんが）。

**showMyAccount ハンドラー**も async 関数です。  
これは `await keycloak.accountManagement()` を呼び出し、Keycloak のアカウント管理画面にユーザーをリダイレクトします。  
※ 管理コンソール（Keycloak Admin Console）とは別物なので注意してください。

```js
const keycloak = new Keycloak({
  url: "http://localhost:8180",
  realm: "quickstart",
  clientId: "spa",
});
```

このコードは、Keycloak インスタンスを作成し、コンストラクタに与えられたパラメータを使用してローカルの Keycloak サーバーへ接続するためのものです。  
つまりこの場合、このインスタンスは **ポート 8180** で動作している Keycloak サーバーにアクセスし、  
その中の **"quickstart" という Realm** と、  
その Realm に含まれる **clientId: "spa"** のクライアントを利用するよう設定されています。

このコードがなければ、この SPA は Docker 上でローカル実行されている Keycloak サーバーと通信することができません。

```js
await keycloak.init({ onLoad: "login-required" });
```

このコードの一行が、実際にクライアント専用のログインページを表示させる処理をトリガーしています。  
ここで表示されるのは **Keycloak 管理コンソールのログイン画面ではなく、クライアント用に設定されたログイン画面** である点に注意してください。

![quickstart user login](./assets/images/quickstart_login.png)

`onLoad: "login-required"` は、「ページが読み込まれたらログインが必須であり、未認証の場合はログイン画面を表示する」という意味になります。

最後に、次の行で `showProfile()` が呼び出され、ログイン直後にユーザー名を画面に表示します。

## 締めの説明

このガイドを通して、**Keycloak サーバーとクライアント（SPA）が正常に接続するために、どのコードが本質的に重要なのか** を意識しながら読み進めてください。  
重要な行を理解したら、その実装手順の流れが頭の中でしっかりつながっているか確認しましょう。

そのうえで、今回のクイックスタートで学んだ流れを、より実践的な Web アプリケーションで再現できるか試してみると、理解がより深まります。

次のガイドでは、それを踏まえて **ReactJS アプリケーションへの Keycloak 実装** を行います：  
[implementing keycloak on a ReactJS application](https://tomato-rofiq.github.io/tomato-manuals/keycloak/jp/react).
