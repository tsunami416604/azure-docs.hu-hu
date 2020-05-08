---
title: JavaScript egyoldalas alkalmazás oktatóanyaga 2,0 – Microsoft Identity platform | Azure
description: Hogyan használhatják a JavaScript SPA-alkalmazások az Auth Code flow-t olyan API meghívásához, amelyhez hozzáférési tokenek szükségesek Azure Active Directory v 2.0 végponttal
services: active-directory
documentationcenter: dev-center-name
author: hahamil
manager: CelesteDG
ROBOTS: NOINDEX
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f664f5fa4219a8bcc32230b352e90cc2516faceb
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890384"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa---msaljs-20"></a>Jelentkezzen be a felhasználókba, és hívja meg a Microsoft Graph API-t egy JavaScript egyoldalas alkalmazásból (SPA) – MSAL. js 2,0

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány aspektusa változhat az általános elérhetősége előtt.

Ez az oktatóanyag a MSAL. js egy olyan verzióját használja, amely az OAuth 2,0 engedélyezési kód folyamatát használja a PKCE. Ha többet szeretne megtudni erről a protokollról, valamint az implicit folyamat és az engedélyezési kód áramlása közötti különbségekről, tekintse meg a [dokumentációt](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow). Ha az implicit folyamatot használó oktatóanyagot keres, tekintse meg a [MSAL. js v1 oktatóanyagot](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-spa).

A MSAL. js ezen verziója az aktuális MSAL-Core függvénytáron fejleszthető, és az engedélyezési kód folyamatát használja a böngészőben. A régi könyvtárban elérhető legtöbb funkció ebben a verzióban érhető el, de a hitelesítési folyamathoz is vannak árnyalatok. Ez a verzió **nem** támogatja az implicit folyamatot.

Ez az útmutató bemutatja, hogyan használható a JavaScript egyoldalas alkalmazás (SPA):
- Bejelentkezés személyes fiókokba, valamint munkahelyi és iskolai fiókokba
- Hozzáférési jogkivonat beszerzése
- Hívja meg az Microsoft Graph API-t vagy más API-kat, amelyek hozzáférési jogkivonatokat igényelnek a *Microsoft Identity platform végpontján*

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Az útmutató által létrehozott minta alkalmazás működése

![Bemutatja, hogyan működik az oktatóanyag által generált minta alkalmazás](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>További információ

Az útmutatóban létrehozott minta alkalmazás lehetővé teszi, hogy a JavaScript SPA lekérdezze a Microsoft Graph API-t vagy egy webes API-t, amely elfogadja a tokeneket a Microsoft Identity platform végpontján. Ebben az esetben a felhasználó bejelentkezése után hozzáférési jogkivonatot kér a rendszer, és hozzáadja a HTTP-kérésekhez az engedélyezési fejlécen keresztül. A token beszerzését és megújítását a Microsoft Authentication Library (MSAL) kezeli.

### <a name="libraries"></a>Kódtárak

Ez az útmutató a következő könyvtárat használja:

|Kódtár|Leírás|
|---|---|
|[msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Microsoft hitelesítési függvénytár JavaScript-tallózóhoz|

## <a name="set-up-your-web-server-or-project"></a>Webkiszolgáló vagy projekt beállítása

Inkább a minta projektjét szeretné letölteni? A projekt helyi webkiszolgálón, például a Node. js használatával történő futtatásához a projektfájlok klónozása:

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

A kód minta konfigurálásához a végrehajtás előtt ugorjon a [konfigurációs lépésre](#register-your-application).

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag futtatásához helyi webkiszolgálóra, például [Node. js](https://nodejs.org/en/download/) -re vagy [.net Core](https://www.microsoft.com/net/core)-ra van szükség.

* Ha a Node. js-t használja a projekt futtatásához, telepítsen egy integrált fejlesztői környezetet (IDE), például a [Visual Studio Code](https://code.visualstudio.com/download) -ot a projektfájlok szerkesztéséhez.

* Az oktatóanyag utasításai a Node. js-re épülnek.

## <a name="create-your-project"></a>A projekt létrehozása

Győződjön meg arról, hogy telepítve van a [Node. js](https://nodejs.org/en/download/) , majd hozzon létre egy mappát az alkalmazás üzemeltetéséhez. Ezután hozzon létre egy kis [expressz](https://expressjs.com/) webkiszolgálót a `index.html` fájl kiszolgálásához.

1. Először keresse meg a projekt mappáját a terminálon, majd futtassa a következő NPM-parancsokat.
    ```console
    npm init -y
    npm install @azure/msal-Browser
    npm install express
    ```
2. Ezután hozzon létre egy *Server. js*nevű. js fájlt, majd adja hozzá a következő kódot:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

Most már rendelkezik egy egyszerű kiszolgálóval a gyógyfürdő kiszolgálásához. Az oktatóanyag végén a kívánt mappa szerkezete a következő:

![a kívánt SPA-mappa szerkezetének szöveges ábrázolása](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>A SPA felhasználói felületének létrehozása

1. Hozzon létre egy *index. html* fájlt a JavaScript Spa számára az *alkalmazás* mappájában. Ez a fájl a **bootstrap 4 keretrendszerrel** létrehozott felhasználói felületet implementálja, és parancsfájlokat importál a konfigurációs, hitelesítési és API-hívásokhoz.

   Az *index. html* fájlban adja hozzá a következő kódot:

   ```html
   <!DOCTYPE html>
   <html lang="en">
      <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
         <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

         <!-- IE support: add promises polyfill before msal.js  -->
         <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js"></script>

         <!-- adding Bootstrap 4 for UI components  -->
         <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
         <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
         <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand" href="/">MS Identity Platform</a>
            <div class="btn-group ml-auto dropleft">
               <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                  Sign In
               </button>
            </div>
         </nav>
         <br>
         <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
         <br>
         <div class="row" style="margin:auto" >
         <div id="card-div" class="col-md-3" style="display:none">
         <div class="card text-center">
            <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mails</button>
            </div>
         </div>
         </div>
         <br>
         <br>
            <div class="col-md-4">
            <div class="list-group" id="list-tab" role="tablist">
            </div>
            </div>
            <div class="col-md-5">
             <div class="tab-content" id="nav-tabContent">
            </div>
            </div>
         </div>
         <br>
         <br>

         <!-- importing bootstrap.js and supporting js libraries -->
         <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
         <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
         <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

         <!-- importing app scripts (load order is important) -->
         <script type="text/javascript" src="./authConfig.js"></script>
         <script type="text/javascript" src="./graphConfig.js"></script>
         <script type="text/javascript" src="./ui.js"></script>

         <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
         <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
         <script type="text/javascript" src="./authPopup.js"></script>
         <script type="text/javascript" src="./graph.js"></script>
      </body>
     </html>
     ```

   > [!TIP]
   > A MSAL. js fájl verzióját az előző szkriptben lecserélheti a legújabb, a [MSAL. js kiadásban](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)megjelent verzióra.


2. Most hozzon létre egy *UI. js* nevű. js fájlt, amely a DOM-elemeket fogja elérni és frissíteni, majd adja hozzá a következő kódot:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
   }

   function updateUI(data, endpoint) {
     console.log('Graph API responded at: ' + new Date().toString());

     if (endpoint === graphConfig.graphMeEndpoint) {
       const title = document.createElement('p');
       title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
       const email = document.createElement('p');
       email.innerHTML = "<strong>Mail: </strong>" + data.mail;
       const phone = document.createElement('p');
       phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
       const address = document.createElement('p');
       address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
       profileDiv.appendChild(title);
       profileDiv.appendChild(email);
       profileDiv.appendChild(phone);
       profileDiv.appendChild(address);

     } else if (endpoint === graphConfig.graphMailEndpoint) {
         if (data.value.length < 1) {
           alert("Your mailbox is empty!")
         } else {
           const tabList = document.getElementById("list-tab");
           tabList.innerHTML = ''; // clear tabList at each readMail call
           const tabContent = document.getElementById("nav-tabContent");

           data.value.map((d, i) => {
             // Keeping it simple
             if (i < 10) {
               const listItem = document.createElement("a");
               listItem.setAttribute("class", "list-group-item list-group-item-action")
               listItem.setAttribute("id", "list" + i + "list")
               listItem.setAttribute("data-toggle", "list")
               listItem.setAttribute("href", "#list" + i)
               listItem.setAttribute("role", "tab")
               listItem.setAttribute("aria-controls", i)
               listItem.innerHTML = d.subject;
               tabList.appendChild(listItem)

               const contentItem = document.createElement("div");
               contentItem.setAttribute("class", "tab-pane fade")
               contentItem.setAttribute("id", "list" + i)
               contentItem.setAttribute("role", "tabpanel")
               contentItem.setAttribute("aria-labelledby", "list" + i + "list")
               contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
               tabContent.appendChild(contentItem);
             }
           });
         }
     }
   }
   ```

## <a name="register-your-application"></a>Alkalmazás regisztrálása

Kövesse az utasításokat [egy új egyoldalas alkalmazás regisztrálásához](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration).

#### <a name="set-a-redirect-url-for-nodejs"></a>Adja meg a Node. js átirányítási URL-címét

A Node. js-ben beállíthatja a webkiszolgáló portját a *Server. js* fájlban. Ez az oktatóanyag a 3000-es portot használja, de bármilyen más elérhető portot is használhat.

Ha egy átirányítási URL-címet szeretne beállítani az alkalmazás regisztrációs adatai között, váltson vissza az **alkalmazás regisztrációja** ablaktáblára, és regisztráljon egy új **Spa** -t a következők egyikével:

- Állítsa *`http://localhost:3000/`* be az **átirányítási URL-címet**.
- Ha egyéni TCP-portot használ, használja *`http://localhost:<port>/`* a (ahol * \<a port>* az egyéni TCP-port száma).

### <a name="configure-your-javascript-spa"></a>A JavaScript SPA konfigurálása

Hozzon létre egy *authConfig. js* nevű új. js fájlt, amely tartalmazza a hitelesítés konfigurációs paramétereit, és adja hozzá a következő kódot:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
   scopes: ["openid", "profile", "User.Read"]
  };

   // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
   scopes: ["User.Read", "Mail.Read"]
  };

```

 Az elemek magyarázata:
 - Enter_the_Application_Id_Here>a regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosítója** . * \<*
 - Enter_the_Cloud_Instance_Id_Here>az Azure-felhő példánya. * \<* A fő vagy a globális Azure-felhőhöz egyszerűen *https://login.microsoftonline.com*írja be a következőt:. Az **országos** felhők (például Kína) esetében lásd: [nemzeti felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
 - Enter_the_Tenant_info_here>az alábbi lehetőségek egyikére van beállítva: * \<*
   - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére** (például *contoso.microsoft.com*).
   - Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket **szervezetekkel**.
   - Ha az alkalmazás *minden szervezeti címtárban és személyes Microsoft-fiókban is támogatja a fiókokat*, cserélje le ezt az értéket **közösre**. Ha *csak a személyes Microsoft-fiókok*támogatását szeretné korlátozni, cserélje le ezt az értéket a **fogyasztókkal**.
- *\ Enter_the_Redirect_Uri_Here>* a portálon regisztrált port (*`http://localhost:3000/`*)


Hozzon létre egy nevű `graphConfig.js`új. js fájlt, amely tartalmazza a Microsoft Graph API-t hívó konfigurációs paramétereket, és adja hozzá a következő kódot:
```javascript
// Add here the endpoints for MS Graph API services you would like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
};
```
- Enter_the_Graph_Endpoint_Here>az MS Graph API példánya. * \<* A globális MS Graph API végpont esetében egyszerűen cserélje le ezt a karakterláncot a következőre: `https://graph.microsoft.com`. A nemzeti Felhőbeli üzembe helyezések esetében tekintse meg [Graph API dokumentációját](https://docs.microsoft.com/graph/deployments).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>A Microsoft Authentication Library (MSAL) használatával jelentkezzen be a felhasználóba

### <a name="popup"></a>Lakosság
Hozzon létre egy nevű `authPopup.js`új. js fájlt, amely tartalmazni fogja a bejelentkezési felugró ablak hitelesítési és jogkivonat-beszerzési logikáját, és adja hozzá a következő kódot:

   ```JavaScript
  // Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

function signIn() {
    myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log('id_token acquired at: ' + new Date().toString());

            if (myMSALObj.getAccount()) {
                showWelcomeMessage(myMSALObj.getAccount());
            }
        }).catch(error => {
            console.error(error);
        });
}

function signOut() {
    myMSALObj.logout();
}

function getTokenPopup(request) {
    return myMSALObj.acquireTokenSilent(request)
        .catch(error => {
            console.warn(error);
            console.warn("silent token acquisition fails. acquiring token using popup");

            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request)
                .then(tokenResponse => {
                    return tokenResponse;
                }).catch(error => {
                    console.error(error);
                });
        });
}

function seeProfile() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(loginRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.classList.add('d-none');
                mailButton.classList.remove('d-none');
            }).catch(error => {
                console.error(error);
            });
    }
}

function readMail() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(tokenRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
            }).catch(error => {
                console.error(error);
            });
    }
}
   ```
### <a name="redirect"></a>Átirányítás
Hozzon létre egy nevű `authRedirect.js`új. js-fájlt, amely a bejelentkezési átirányítás hitelesítési és jogkivonat-beszerzési logikáját fogja tartalmazni, és adja hozzá a következő kódot:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;

// Register Callbacks for Redirect flow
myMSALObj.handleRedirectCallback(authRedirectCallBack);

function authRedirectCallBack(error, response) {
    if (error) {
        console.error(error);
    } else {
        if (myMSALObj.getAccount()) {
            console.log('id_token acquired at: ' + new Date().toString());
            showWelcomeMessage(myMSALObj.getAccount());
            getTokenRedirect(loginRequest);
        } else if (response.tokenType === "Bearer") {
            console.log('access_token acquired at: ' + new Date().toString());
        } else {
            console.log("token type is:" + response.tokenType);
        }
    }
}

// Redirect: once login is successful and redirects with tokens, call Graph API
if (myMSALObj.getAccount()) {
    showWelcomeMessage(myMSALObj.getAccount());
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    myMSALObj.logout();
}

// This function can be removed if you do not need to support IE
function getTokenRedirect(request) {
    return myMSALObj.acquireTokenSilent(request)
        .then((response) => {
            console.log(response);
            if (response.accessToken) {
                console.log('access_token acquired at: ' + new Date().toString());
                accessToken = response.accessToken;

                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.style.display = 'none';
                mailButton.style.display = 'initial';
            }
        })
        .catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenRedirect(request);
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest);
}

function readMail() {
    getTokenRedirect(tokenRequest);
}
```

### <a name="more-information"></a>További információ

Miután a felhasználó első alkalommal kiválasztja a **Bejelentkezés** gombot, a `signIn` metódus meghívja `loginPopup` a felhasználót a bejelentkezéshez. Ezzel a módszerrel megnyílik egy előugró ablak a *Microsoft Identity platform végpontján* a felhasználó hitelesítő adatainak megadásához és érvényesítéséhez. Sikeres bejelentkezés után a *msal. js* kezdeményezi az [engedélyezési kód folyamatát](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).

Ekkor a rendszer elküld egy PKCE-védelemmel ellátott hitelesítési kódot a CORS által védett jogkivonat-végpontba, és kicseréli a jogkivonatokat. A rendszer egy azonosító jogkivonatot, hozzáférési tokent és frissítési jogkivonatot fogad, amelyet a *msal. js*feldolgoz, és a jogkivonatban található információk gyorsítótárazva vannak.

Az azonosító jogkivonat a felhasználóval kapcsolatos alapvető adatokat tartalmaz, például a megjelenítendő nevüket. Ha azt tervezi, hogy a token által biztosított összes adatforrást használni fogja, azt a háttér-kiszolgálónak kell érvényesíteni, hogy a tokent egy érvényes felhasználónak adja ki az alkalmazás számára. A frissítési jogkivonat korlátozott élettartammal rendelkezik, és 24 óra elteltével lejár. A frissítési token használatával csendesen szerezhetők be új hozzáférési tokenek.

Az útmutató által létrehozott SPA a Microsoft Graph `acquireTokenSilent` API felhasználói profil `acquireTokenPopup` adatainak lekérdezéséhez használt *hozzáférési token* beszerzésére hívja fel a kapcsolatot. Ha szüksége van egy olyan mintára, amely érvényesíti az azonosító jogkivonatot, tekintse meg az [Active Directory-JavaScript-singlepageapp-DotNet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) minta alkalmazást a githubon. A minta egy ASP.NET webes API-t használ a jogkivonat-ellenőrzéshez.

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

A kezdeti bejelentkezés után a felhasználókat nem kell megkérnie, hogy minden alkalommal újra hitelesíteni tudják az erőforrásokhoz való hozzáféréshez szükséges jogkivonatot. Az ilyen újrahitelesítési kérelmek megelőzése érdekében `acquireTokenSilent`használja a következőt:. Bizonyos esetekben azonban előfordulhat, hogy a felhasználóknak a Microsoft Identity platform végponttal való interakcióra kell kényszeríteni a felhasználókat. Például:

- A felhasználóknak újra meg kell adniuk a hitelesítő adataikat, mert a jelszó lejárt.
- Az alkalmazás hozzáférést kér egy erőforráshoz, és szüksége van a felhasználó belefoglalására.
- Kétfaktoros hitelesítés szükséges.

A `acquireTokenPopup` hívás megnyit egy előugró ablakot ( `acquireTokenRedirect` vagy átirányítja a felhasználókat a Microsoft Identity platform-végpontra). Ebben az ablakban a felhasználóknak kapcsolatba kell lépniük a hitelesítő adataik megerősítésével, a szükséges erőforrás jóváhagyásával, vagy a kétfaktoros hitelesítés végrehajtásával.

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` metódus felhasználói beavatkozás nélkül kezeli a tokenek beszerzését és megújítását. A `loginPopup` (vagy `loginRedirect`) első `acquireTokenSilent` futtatása után a metódus általában a védett erőforrásokhoz való hozzáféréshez használt jogkivonatok beszerzésére szolgál a további hívásokhoz. (A kérések és a megújítási hívások csendesen történnek.) `acquireTokenSilent` bizonyos esetekben sikertelen lehet. Előfordulhat például, hogy a felhasználó jelszava lejárt. Az alkalmazás két módon tudja kezelni ezt a kivételt:

1. A felhasználó bejelentkezési kérésének elindításához hívjon fel `acquireTokenPopup` azonnal egy hívást. Ezt a mintát gyakran használják online alkalmazásokban, ahol az alkalmazás nem hitelesített tartalmakat használ a felhasználó számára. Az irányított telepítő által generált minta ezt a mintát használja.

1. Vizuálisan jelezze a felhasználónak, hogy egy interaktív bejelentkezésre van szükség, hogy a felhasználó kiválassza a megfelelő időt a bejelentkezéshez, vagy az alkalmazás később `acquireTokenSilent` is újra próbálkozik. Ez a módszer általában akkor használható, ha a felhasználó az alkalmazás egyéb funkcióit nem zavarja. Előfordulhat például, hogy nem hitelesített tartalom érhető el az alkalmazásban. Ebben az esetben a felhasználó dönthet arról, hogy mikor szeretné bejelentkezni a védett erőforrás eléréséhez, vagy az elavult információk frissítéséhez.

> [!NOTE]
> Ez a rövid útmutató `loginPopup` a `acquireTokenPopup` és a metódusokat használja alapértelmezés szerint. Ha böngészőként használja az Internet Explorert, ajánlott a és `loginRedirect` `acquireTokenRedirect` a metódusok használata, mert az Internet Explorer az előugró ablakokat kezelő módszerével kapcsolatos [ismert probléma](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) . Ha szeretné megtudni, hogyan valósítható meg Ugyanez az eredmény az átirányítási módszerek használatával, tekintse meg a [*authRedirect. js fájlt*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>A Microsoft Graph API meghívása az imént beszerzett token használatával


 Hozzon létre egy *Graph. js* nevű. js fájlt, amely Rest-hívást hajt végre Microsoft Graph API-nak, és adja hozzá a következő kódot:

   ```javascript

// Helper function to call MS Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>További információ a védett API-k REST-hívásáról

Az útmutatóban létrehozott minta alkalmazásban a `callMSGraph()` METÓDUS használatával http `GET` -kérelem hozható létre egy olyan védett erőforráson, amely jogkivonatot igényel. A kérés ezután visszaadja a tartalmat a hívónak. Ez a metódus hozzáadja a beszerzett jogkivonatot a *http-engedélyezési fejlécben*. Az útmutatóban létrehozott minta alkalmazás esetében az erőforrás a Microsoft Graph API *Me* -végpontja, amely megjeleníti a felhasználó profiljának adatait.

## <a name="test-your-code"></a>A kód tesztelése

1. A Node. js esetében indítsa el a webkiszolgálót az alkalmazás mappájából a következő parancsok futtatásával:

   ```bash
   npm install
   npm start
   ```
1. A böngészőben adja meg a **http://localhost:3000** vagy **http://localhost:{port}** a (z) értéket, ahol a *port* a webkiszolgáló által figyelt port. Ekkor meg kell jelennie az *index. html* fájl tartalmának és a **Bejelentkezés** gombnak.

## <a name="test-your-application"></a>Az alkalmazás tesztelése

Miután a böngésző betölti az *index. html* fájlt, válassza a **Bejelentkezés**lehetőséget. A rendszer felszólítja, hogy jelentkezzen be a Microsoft Identity platform-végponttal:

![A JavaScript SPA-fiók bejelentkezési ablaka](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Adja meg az alkalmazás-hozzáférés beleegyezikét

Amikor először jelentkezik be az alkalmazásba, a rendszer felszólítja, hogy adjon hozzáférést a profilhoz, és jelentkezzen be:

![A "kért engedélyek" ablak](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Alkalmazás eredményeinek megtekintése

A bejelentkezést követően a felhasználói profil adatait a rendszer a Microsoft Graph API-válaszban jeleníti meg:

![A Microsoft Graph API-hívás eredményei](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörökről és a delegált engedélyekről

A Microsoft Graph API-nak a felhasználónak *. Read* hatókörrel kell rendelkeznie a felhasználói profil olvasásához. Alapértelmezés szerint a rendszer automatikusan hozzáadja ezt a hatókört a Azure Portalban regisztrált összes alkalmazáshoz. A Microsoft Graph egyéb API-jai, valamint a háttér-kiszolgálóhoz tartozó egyéni API-k további hatóköröket is igényelhetnek. A Microsoft Graph API-nak például a *mail. Read* hatókört kell megadnia a felhasználó levelezésének listázásához.

> [!NOTE]
> Előfordulhat, hogy a felhasználó további beleegyezik a hatókörök hozzáadásakor.

Ha egy háttérbeli API-nak nincs szüksége hatókörre (nem ajánlott), a *clientId* használhatja a jogkivonatok beszerzésére irányuló hívások hatókörét.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>További lépések

A [MSAL. js GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) -tárház további könyvtári dokumentációt, GYIK-t tartalmaz, és támogatást biztosít.
