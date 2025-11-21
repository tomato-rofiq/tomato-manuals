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

## Create a Realm

A realm in Keycloak is equivalent to a tenant. Each realm allows an administrator to create isolated groups of applications and users. Initially, Keycloak includes a single realm, called master. Use this realm only for managing Keycloak and not for managing any applications.

Practically, a single realm should be used by one application (technically called a "client") because within this realm you may define roles to users of your application and because JWTs issued to your client are realm-specific.

You can also have separate realms for different dev environments. For example, three realms for dev, staging, and production. Or you can have different realms to manage users of different companies. For example myapp-company1, myapp-company2, and so on.

Let's create your first realm.

1. click "Manage realms" from the left-hand menu.
2. click "Create realm"

![manage realms](./assets/images/manage_realms.png)

3. Enter `myrealm` in the Realm name field and click create.

![create realm](./assets/images/create_realm.png)
![create realm result](./assets/images/create_realm2.png)

## Create a User

1. Verify that you are still in the myrealm realm.
2. Click "Users" in the left-hand menu.

![create realm result](./assets/images/create_realm2.png)

3. Click "Create new user".

![users](./assets/images/users.png)

![create user](./assets/images/create_user.png)

4. Fill in the form with the following values:
    - Username: myuser
    - First name: any first name
    - Last name: any last name
5. Click "Create".

Although you've created a user, they don't have any credentials to login with so next let's give the newly created user a password.

1. Click Credentials at the top of the page and click set password.

![credentials](./assets/images/credentials.png)

2. Fill in the Set password form with a password.
3. Toggle Temporary to Off so that the user does not need to update this password at the first login.
4. click save.

![set password](./assets/images/set_password.png)
![set password](./assets/images/password_result.png)


