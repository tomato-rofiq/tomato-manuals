---
layout: default
title: "Docker 上で Keycloak を始める方法"
permalink: /keycloak/setup/
---

# Docker 上で Keycloak を始める方法

本ガイドは、Keycloak.org が提供する公式「Getting Started」ドキュメント [getting-started-docker](https://www.keycloak.org/getting-started/getting-started-docker) をもとに、日本語の読者向けに作成されたものです。

## 前提条件

公式ガイドでは使用するオペレーティングシステムに関する要件は特に示されていませんが、本ガイドでは Linux 環境または Mac OS 上での利用を推奨します。  

これは、Windows ユーザーの場合、Web 開発中に Windows 固有の問題を避けるため、便利な方法として Windows Subsystem for Linux (WSL) の使用をおすすめするという意味です。

WSL の使い方がわからない場合は、Microsoft の以下のドキュメントをご参照ください。[learn-wsl-install](https://learn.microsoft.com/en-us/windows/wsl/install)

また、本ガイドでは Docker も必要です。Docker Desktop がインストールされていることを確認してください。WSL ユーザーは、Docker Desktop の設定でデフォルトの Ubuntu ディストリビューションにアクセスできるように構成してください。

このガイドでは、Docker Desktop およびターミナルから Docker を使用する基本的な知識があることを前提としています。

## Keycloak の起動

Docker Desktop が起動していることを確認してください。

ターミナルを開きます。

ターミナルで、Keycloak を起動するために次のコマンドを入力します：

```bash
docker run -p 127.0.0.1:8080:8080 -e KC_BOOTSTRAP_ADMIN_USERNAME=admin -e KC_BOOTSTRAP_ADMIN_PASSWORD=admin quay.io/keycloak/keycloak:26.4.5 start-dev
```

![start keycloak in the terminal](./assets/images/bash_keycloak.png)

このコマンドは、Keycloak をローカルのポート 8080 で公開して起動し、ユーザー名 admin、パスワード admin の初期。

ターミナルは開いたままにして、イメージを実行し続けてください。  
また、Docker Desktop でイメージが実行中かどうかを確認することもできます。

![keycloak in docker desktop](./assets/images/docker_desktop_keycloak.png)

## 管理コンソールへのログイン

これで、[http://localhost:8080/admin](http://localhost:8080/admin) から Keycloak、より正確には Keycloak Admin Console にアクセスできます。

アクセスすると、Keycloak が提供するログイン画面が表示されます。


![admin console login](./assets/images/admin_console_login.png)

前にターミナルで実行した `docker run` コマンドでは、Keycloak Admin Console にログインするための一時的なユーザー名 `admin` とパスワード `admin` が指定されていました。

![admin console home](./assets/images/admin_console_home.png)

## Realm（レルム）の作成

Keycloak における realm（レルム） は、テナントに相当します。各レルムでは、管理者がアプリケーションやユーザーを分離して管理できます。初期状態では master と呼ばれるレルムが 1 つだけ含まれています。この master レルムは Keycloak 自体を管理するためのものであり、アプリケーションの管理には使用しないでください。

一般的に、1 つのレルムは 1 つのアプリケーション（Keycloak の用語では「クライアント」）で使用することが推奨されます。これは、レルム内でアプリケーションのユーザー向けのロールを定義できること、またクライアントに発行される JWT がレルム固有であるためです。

開発環境ごとにレルムを分けることもできます。例えば、開発（dev）、ステージング（staging）、本番（production）で 3 つのレルムを用意する運用があります。また、会社ごとにユーザーを分離したい場合は、myapp-company1、myapp-company2 のように複数のレルムを作成することも可能です。

それでは、最初のレルムを作成してみましょう。

1. 左側メニューから 「Manage realms」 をクリックします。
2. 「Create realm」 をクリックします。

![manage realms](./assets/images/manage_realms.png)

3. 「Realm name」欄に `myrealm` と入力し、**Create** をクリックします。  

![create realm](./assets/images/create_realm.png)
![create realm result](./assets/images/create_realm2.png)

## ユーザーの作成

1. まだ `myrealm` レルムにいることを確認します。  
2. 左側メニューの 「Users」 をクリックします。  

![create realm result](./assets/images/create_realm2.png)

3. 「Create new user」 をクリックします。  

![users](./assets/images/users.png)
![create user](./assets/images/create_user.png)

4. 以下の値でフォームを入力します：  
    - Username: myuser  
    - First name: 任意の名前  
    - Last name: 任意の名字  
5. 「Create」 をクリックします。  

ユーザーを作成しましたが、まだログイン用の認証情報はありません。次に、作成したユーザーにパスワードを設定します。  

1. ページ上部の 「Credentials」 をクリックし、「Set Password」 をクリックします。  

![credentials](./assets/images/credentials.png)

2. 「Set password」フォームにパスワードを入力します。  
3. Temporary を Off に切り替え、ユーザーが初回ログイン時にパスワードを変更する必要がないようにします。  
4. Save をクリックします。  

![set password](./assets/images/set_password.png)
![set password](./assets/images/password_result.png)


