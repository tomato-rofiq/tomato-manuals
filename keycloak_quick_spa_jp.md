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
