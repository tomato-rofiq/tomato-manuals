---
layout: default
title: "How to implement Keycloak in a JavaScript-based Single Page Application (SPA)"
permalink: /keycloak/en/quickjs/
---

**最終更新日：2025年12月（作成者：Rofiq）** [JP](https://tomato-rofiq.github.io/tomato-manuals/keycloak/jp/quickjs) [EN](https://tomato-rofiq.github.io/tomato-manuals/keycloak/en/quickjs)

# How to implement Keycloak in a JavaScript-based Single Page Application (SPA)

this guide explains the basics of how you can implement keycloak onto a javascript based single page application. the guide only is only meant to walk you through an example bare bones implementation and run it locally.

we will be using the quickstart project that can be found here: [keycloak-spa-quickstart](https://github.com/keycloak/keycloak-quickstarts/tree/main/js/spa)

## Prerequisites

please use WSL if you are on Windows.

To compile and run this quickstart you will need:
- Node.js 18.16.0+
- Keycloak 21+
- Docker 20+

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

once the keycloak server is running in docker through the command, access the keycloak admin console the same way you did in the previous guide, using the username and password `admin`. 

next, you will import the realm provided in by the quickstart from here: [realm-import.json](https://github.com/keycloak/keycloak-quickstarts/blob/main/js/spa/config/realm-import.json). from the link above, you can either copy paste the json or click the download button to download the json file itself. i've also pasted the raw json below for convenience.

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

the realm-import json configures the following attributes for the `quickstart` realm:

- One client (spa) for a local single-page app.
- Two users (a normal user and an admin).
- A couple of roles (user, admin) and some client-level permissions.

next, through the keycloak admin console, when creating a new realm, either paste the json you've copied from github into the Resource File input field or click the Browse button to select the json file you downloaded. then click Create.

![import realm json](./assets/images/create_realm3.png)

your current realm should automatically become the new imported `Quickstart` realm. this is all we need to configure from the server side.

## Build and Deploy the Quickstart SPA

if you have not cloned the quickstart projects repository, please clone the quickstart github repository from [here](https://github.com/keycloak/keycloak-quickstarts/tree/main).

open a bash terminal and navigate to the root of the project. in this case, you have to navigate to the SPA quickstart project folder like what the image below shows. 

![current directory](./assets/images/curr_dir.png)

run the following commands to run the quickstart.

```bash
npm install
npm start
```

![npm install output](./assets/images/npm_install_start.png)

## Access the Quickstart SPA

You can access the application with the following URL: [http://localhost:8080](http://localhost:8080).

Try to login with any of these users:

| Username | Password | Roles |
|---------|----------|-------|
| alice   | alice    | user  |
| admin   | admin    | admin |

![quickstart user login](./assets/images/quickstart_login.png)
![quickstart user login](./assets/images/quickstart_postlogin.png)

once authenticated, you can perform the actions written on each of the buttons at the top of the screen.

this marks the end of the quickstart.

## Walkthrough of the Quickstart SPA Source Code

now we will take a closer look at the source code of the keycloak implementation on the quickstart SPA. our aim is to try to understand what is actually programmed in the SPA that makes keycloak work in it.

please open the index.html file located at: `keycloak-quickstarts/js/spa/public/index.html`.

from the top of the html code, inside the `<head>` is the keycloak import. it imports the keycloak-js npm package which allows you to use the features of keycloak in your application.

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

there are a few ways to import keycloak-js and this is only one example. if you are curious, i suggest just asking any llm about other ways to import keycloak while referencing this code.

the next section of the html is the div in the `<body>` which contains all the buttons you saw at the top of the screen. there isn't much to else to explain.

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

inside the script that follows the div is the most important part of this html. it shows the simplest examples for how to use some of keycloak's features.

```js
import Keycloak from "keycloak-js";
const outputElement = document.getElementById("output");
const nameElement = document.getElementById("name");
const userElement = document.getElementById("user");
```

the JS code begins by import the keycloak-js library and getting references to the DOM elements from the div.

following this code are 2 helper functions, `output(content)` and `showProfile()` which are used by the button handlers written after them.

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

`output(content)` displays content in the output area, converting objects to formatted JSON. `showProfile()` extracts the user's name from the ID token and displays it, then shows the user interface.

after this code are the button handlers. there are five handlers corresponding to the five buttons.

the logout handler calls `keycloak.logout()` which logs out the user.

the showIdToken handler calls `keycloak.idTokenParsed` which returns the ID token to be parsed by the `output()` helper function.

the showAccessToken handler calls `keycloak.tokenParsed` which returns the acess token to be parsed by the `output()` helper function. 

notice that the refreshToken handler is an async function. the reason for this is because it is calling `keycloak.updateToken(-1)` which uses await. this particular keycloak function takes in a number representing the seconds after a token expires for keycloak to refresh it. -1 means that it should refresh the token immediately.

after refreshing the token, the new token is parsed by `output()` helper function. the `showProfile()` helper function called after that to re-render the user's name in case that information changed between refreshes even though it is unlikely.

the showMyAccount handler is also an async function because it calls `keycloak.accountManagement()` which again uses await. this keycloak function redirects the user to keycloak's account management screen (note that this is different from the keycloak administration console).

```js
const keycloak = new Keycloak({
  url: "http://localhost:8180",
  realm: "quickstart",
  clientId: "spa",
});
```

this code is used to create a keycloak instance in order to connect to a local keycloak server using the parameters in the constructor. so in this case, this instance is looking for a keycloak server on port 8180 containing a realm called "quickstart" containing a clientId of "spa".

without the code above, this SPA would not be able to communicate with our keycloak server running locally in docker.

```js
await keycloak.init({ onLoad: "login-required" });
```

this line of code is what actually triggers the rendering of the login page belonging to our client. note that this is not the login screen of the keycloak admin console. 

![quickstart user login](./assets/images/quickstart_login.png)

`onLoad: "login-required"` basically says that when the page loads, login is required, thus show the login screen if the user is not currently authenticated.

lastly, the `showProfile()` function is called in the next line in order to show the user's name immediately after they've logged in.

## Closing Remarks

after following this guide, you should think about which lines specifically are essential for a successful client to keycloak server connection. then based on those lines of code, make sure that its procedure of implementation makes sense in your head. after that, you can try to apply that procedure on a more practical web application to verify that it is correct. 

that is what we are going to do in the next guide here: [implementing keycloak on a ReactJS application](https://tomato-rofiq.github.io/tomato-manuals/keycloak/jp/react).


