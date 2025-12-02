---
layout: default
title: "JavaScript ベースのシングルページアプリケーション（SPA）に Keycloak を実装する方法"
permalink: /keycloak/en/quickjs/
---

**最終更新日：2025年12月（作成者：Rofiq）**

# How to implement Keycloak in a JavaScript-based Single Page Application (SPA)

this guide explains the basics of how you can implement keycloak onto a javascript based single page application. the guide only is only meant to walk you through an example bare bones implementation and run it locally.

we will be using the quickstart project that can be found here: [keycloak-spa-quickstart](https://github.com/keycloak/keycloak-quickstarts/tree/main/js/spa)

## Prerequisites

please use WSL if you are on Windows.

To compile and run this quickstart you will need:
- Node.js 18.16.0+
- Keycloak 21+
- Docker 20+

you will also need to clone the quickstart github repository from [here](https://github.com/keycloak/keycloak-quickstarts/tree/main).

## Starting and Configuring the Keycloak Server

if you followed the previous keycloak setup guide, you will perform the same step in order to start the keycloak server and access the keycloak admin console. compared to the docker run command from the setup guide, this command differs from these points:

- given the container a name "keycloak"
- map the host port 8180 to container port 8180
- tell keycloak to listen to port 8180 instead of deafault 8080

run the following command in your bash terminal to start keycloak in docker. dont forget to have docker desktop open while doing this to see make sure that the container is actually running.

```bash
docker run --name keycloak -p 8180:8180 -e KC_BOOTSTRAP_ADMIN_USERNAME=admin -e KC_BOOTSTRAP_ADMIN_PASSWORD=admin  quay.io/keycloak/keycloak:26.4.5 start-dev --http-port=8180
```

feel free to change the keycloak version specified in the command to the latest version available if you want to. 

![start keycloak in docker](./assets/images/quickstart_docker.png)

once the keycloak server is running in docker through the command, access the keycloak admin console the same way you did in the previous guide.

 

