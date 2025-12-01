---
layout: default
title: "JavaScript ベースのシングルページアプリケーション（SPA）に Keycloak を実装する方法"
permalink: /keycloak/en/quickjs/
---

**最終更新日：2025年12月（作成者：Rofiq）**

# How to implement Keycloak in a JavaScript-based Single Page Application (SPA)

this guide explains the basics of how you can implement keycloak onto a javascript based single page application. the guide only is only meant to walk you through an example bare bones implementation and run it locally. 

we will be using the quickstart project that can be found here: [keycloak-spa-quickstart](https://github.com/keycloak/keycloak-quickstarts/tree/main/js/spa).

## Prerequisites

To compile and run this quickstart you will need:
- Node.js 18.16.0+
- Keycloak 21+
- Docker 20+

you will also need to clone the quickstart github repository from [here](https://github.com/keycloak/keycloak-quickstarts/tree/main).

## Starting and Configuring the Keycloak Server

if you followed the previous keycloak setup guide, you will perform the same step in order to start the keycloak server and access the keycloak admin console.p