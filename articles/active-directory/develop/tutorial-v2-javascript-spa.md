---
title: JavaScript egyoldalas alkalmazás oktatóanyaga – Microsoft Identity platform | Azure
description: Hogyan hívhatják meg a JavaScript SPA-alkalmazások olyan API-t, amelyhez hozzáférési jogkivonatok szükségesek Azure Active Directory v 2.0 végponttal
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6f0253490d39e69d491dd5fd3ab0d0d0a32d47bb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181562"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Bejelentkezés a felhasználókba és a Microsoft Graph API meghívása egy JavaScript egyoldalas alkalmazásból (SPA)

Ez az útmutató bemutatja, hogyan használható a JavaScript egyoldalas alkalmazás (SPA):
- Bejelentkezés személyes fiókokba, valamint munkahelyi és iskolai fiókokba
- Hozzáférési jogkivonat beszerzése
- Hívja meg az Microsoft Graph API-t vagy más API-kat, amelyek hozzáférési jogkivonatokat igényelnek a *Microsoft Identity platform végpontján*

>[!NOTE]
> Ha még nem ismeri a Microsoft Identity platformot, javasoljuk, hogy kezdje a [bejelentkezési felhasználókat, és szerezzen be egy hozzáférési jogkivonatot egy JavaScript Spa-](quickstart-v2-javascript.md)útmutatóban.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Az útmutató által létrehozott minta alkalmazás működése

![Bemutatja, hogyan működik az oktatóanyag által generált minta alkalmazás](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>További információ

Az útmutatóban létrehozott minta alkalmazás lehetővé teszi, hogy a JavaScript SPA lekérdezze a Microsoft Graph API-t vagy egy webes API-t, amely elfogadja a tokeneket a Microsoft Identity platform végpontján. Ebben az esetben a felhasználó bejelentkezése után hozzáférési jogkivonatot kér a rendszer, és hozzáadja a HTTP-kérésekhez az engedélyezési fejlécen keresztül. Ez a token a felhasználó profiljának és e-maileknek az **MS Graph API**használatával való beszerzésére szolgál. A token beszerzését és megújítását a **Microsoft Authentication Library (MSAL) kezeli a javascripthez**.

### <a name="libraries"></a>Kódtárak

Ez az útmutató a következő könyvtárat használja:

|Kódtár|Leírás|
|---|---|
|[msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft hitelesítési függvénytár JavaScripthez|

## <a name="set-up-your-web-server-or-project"></a>Webkiszolgáló vagy projekt beállítása

> Inkább a minta projektjét szeretné letölteni? [Töltse le a projektfájlok fájljait](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> A kód minta konfigurálásához a végrehajtás előtt ugorjon a [konfigurációs lépésre](#register-your-application).

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag futtatásához helyi webkiszolgálóra, például [Node. js](https://nodejs.org/en/download/), [.net Core](https://www.microsoft.com/net/core)vagy IIS Express [Visual Studio 2017](https://www.visualstudio.com/downloads/)-integrációra van szükség.

* Az útmutató utasításai a Node. js-ben létrehozott webkiszolgálón alapulnak. Javasoljuk, hogy a [Visual Studio Code](https://code.visualstudio.com/download) -ot használja integrált fejlesztői környezetként (ide).

## <a name="create-your-project"></a>A projekt létrehozása

Győződjön meg arról, hogy telepítve van a [Node. js](https://nodejs.org/en/download/) , majd hozzon létre egy mappát az alkalmazás üzemeltetéséhez. Itt egy egyszerű [expressz](https://expressjs.com/) webkiszolgálót fogunk megvalósítani a `index.html` fájl kiszolgálásához.

1. Először használja a Visual Studio Code integrált terminált, keresse meg a Project mappát, majd telepítse az Expresst a NPM használatával.

1. Ezután hozzon létre egy nevű `server.js`. js-fájlt, majd adja hozzá a következő kódot:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

Most már rendelkezik egy egyszerű kiszolgálóval a gyógyfürdő kiszolgálásához. Az oktatóanyag végén a kívánt mappa szerkezete a következő:

![a kívánt SPA-mappa szerkezetének szöveges ábrázolása](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>A SPA felhasználói felületének létrehozása

1. Hozzon `index.html` létre egy fájlt a JavaScript Spa számára. Ez a fájl a **bootstrap 4 keretrendszerrel** létrehozott felhasználói felületet implementálja, és parancsfájlokat importál a konfigurációhoz, a hitelesítéshez és az API-híváshoz.

   A `index.html` fájlban adja hozzá a következő kódot:

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
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

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > A MSAL. js fájl verzióját az előző szkriptben lecserélheti a legújabb, a [MSAL. js kiadásban](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)megjelent verzióra.

2. Hozzon létre egy nevű `ui.js`. js-fájlt, amely a DOM-elemeket fogja elérni és frissíteni, majd adja hozzá a következő kódot:

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

A hitelesítés további folytatása előtt regisztrálja alkalmazását **Azure Active Directoryon**.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókot a jobb felső sarokban, majd állítsa be a portál munkamenetét a használni kívánt Azure AD-bérlőre.
1. Nyissa meg a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapját.
1. Ha megjelenik az **Alkalmazás regisztrálása** oldal, adjon nevet az alkalmazásnak.
1. A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
1. Az **átirányítási URI** szakaszban válassza ki a **webplatformot a** legördülő listából, majd állítsa be az értéket a webkiszolgálón alapuló alkalmazás URL-címére.
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.
1. Ez a rövid útmutató az [implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) engedélyezését igényli. A regisztrált alkalmazás bal oldali ablaktábláján válassza a **hitelesítés**lehetőséget.
1. A **Speciális beállítások**területén az **implicit engedélyezés**területen jelölje be az **azonosító tokenek** és a **hozzáférési tokenek** jelölőnégyzetet. Az azonosító jogkivonatok és hozzáférési tokenek megadása kötelező, mert az alkalmazásnak be kell jelentkeznie a felhasználókba, és hívnia kell egy API-t.
1. Kattintson a **Mentés** gombra.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Adja meg a Node. js átirányítási URL-címét
>
> A Node. js-ben beállíthatja a webkiszolgáló portját a *Server. js* fájlban. Ez az oktatóanyag a 3000-es portot használja, de bármilyen más elérhető portot is használhat.
>
> Ha egy átirányítási URL-címet szeretne beállítani az alkalmazás regisztrációs adatai között, váltson vissza az **alkalmazás regisztrációja** ablaktáblára, és tegye a következők egyikét:
>
> - Állítsa *`http://localhost:3000/`* be az **átirányítási URL-címet**.
> - Ha egyéni TCP-portot használ, használja *`http://localhost:<port>/`* a (ahol * \<a port>* az egyéni TCP-port száma).
>   1. Másolja az **URL-címet** .
>   1. Váltson vissza az **alkalmazás regisztrációja** ablaktáblára, és illessze be a másolt értéket az **átirányítási URL-címként**.
>

### <a name="configure-your-javascript-spa"></a>A JavaScript SPA konfigurálása

Hozzon létre egy nevű `authConfig.js`új. js fájlt, amely tartalmazza a hitelesítés konfigurációs paramétereit, és adja hozzá a következő kódot:

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
    scopes: ["Mail.Read"]
  };
```

 Az elemek magyarázata:
 - Enter_the_Application_Id_Here>a regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosítója** . * \<*
 - Enter_the_Cloud_Instance_Id_Here>az Azure-felhő példánya. * \<* A fő vagy a globális Azure-felhőhöz egyszerűen *https://login.microsoftonline.com*írja be a következőt:. Az **országos** felhők (például Kína) esetében lásd: [nemzeti felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
 - Enter_the_Tenant_info_here>az alábbi lehetőségek egyikére van beállítva: * \<*
   - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére** (például *contoso.microsoft.com*).
   - Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket **szervezetekkel**.
   - Ha az alkalmazás *minden szervezeti címtárban és személyes Microsoft-fiókban is támogatja a fiókokat*, cserélje le ezt az értéket **közösre**. Ha *csak a személyes Microsoft-fiókok*támogatását szeretné korlátozni, cserélje le ezt az értéket a **fogyasztókkal**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>A Microsoft Authentication Library (MSAL) használatával jelentkezzen be a felhasználóba

Hozzon létre egy nevű `authPopup.js`új. js fájlt, amely tartalmazza a hitelesítési és a jogkivonat-beszerzési logikát, és adja hozzá a következő kódot:

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }

   function callMSGraph(theUrl, accessToken, callback) {
       var xmlHttp = new XMLHttpRequest();
       xmlHttp.onreadystatechange = function () {
           if (this.readyState == 4 && this.status == 200) {
              callback(JSON.parse(this.responseText));
           }
       }
       xmlHttp.open("GET", theUrl, true); // true for asynchronous
       xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
       xmlHttp.send();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
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
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

### <a name="more-information"></a>További információ

Miután a felhasználó első alkalommal kiválasztja a **Bejelentkezés** gombot, a `signIn` metódus meghívja `loginPopup` a felhasználót a bejelentkezéshez. Ezzel a módszerrel megnyílik egy előugró ablak a *Microsoft Identity platform végpontján* a felhasználó hitelesítő adatainak megadásához és érvényesítéséhez. Sikeres bejelentkezés után a rendszer visszairányítja a felhasználót az eredeti *index. html* oldalra. A rendszer a tokent fogadja `msal.js`, dolgozza fel, és a jogkivonatban található információkat gyorsítótárazza. Ez a jogkivonat *azonosító jogkivonat* , és alapszintű információt tartalmaz a felhasználóról, például a felhasználó megjelenítendő nevét. Ha bármilyen célra szeretné használni a token által biztosított bármilyen adatforrást, győződjön meg róla, hogy a háttér-kiszolgáló érvényesíti ezt a jogkivonatot annak biztosításához, hogy a jogkivonatot a rendszer egy érvényes felhasználónak adja ki az alkalmazás számára.

Az útmutató által létrehozott SPA a Microsoft Graph `acquireTokenSilent` API felhasználói profil `acquireTokenPopup` adatainak lekérdezéséhez használt *hozzáférési token* beszerzésére hívja fel a kapcsolatot. Ha olyan mintát kell használnia, amely érvényesíti az azonosító jogkivonatot, tekintse meg [ezt](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub Active-Directory-JavaScript-singlepageapp-DotNet-webapi-v2 minta") a minta alkalmazást a githubban. A minta egy ASP.NET webes API-t használ a jogkivonat-ellenőrzéshez.

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

A kezdeti bejelentkezés után nem kívánja megkérni a felhasználókat, hogy minden alkalommal újra hitelesíteni tudják az erőforrásokhoz való hozzáféréshez szükséges jogkivonatot. Így a *acquireTokenSilent* a legtöbb időt kell használni a tokenek beszerzéséhez. Vannak azonban olyan helyzetek, amikor kényszeríteni kell a felhasználókat, hogy együttműködjön a Microsoft Identity platform-végponttal. Példák erre vonatkozóan:

- A felhasználóknak újra meg kell adniuk a hitelesítő adataikat, mert a jelszó lejárt.
- Az alkalmazás hozzáférést kér egy erőforráshoz, és szüksége van a felhasználó belefoglalására.
- Kétfaktoros hitelesítés szükséges.

A *acquireTokenPopup* meghívásával megnyílik egy előugró ablak (vagy a *acquireTokenRedirect* átirányítja a felhasználókat a Microsoft Identity platform-végpontra). Ebben az ablakban a felhasználóknak kapcsolatba kell lépniük a hitelesítő adataik megerősítésével, a szükséges erőforrás jóváhagyásával, vagy a kétfaktoros hitelesítés végrehajtásával.

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` metódus felhasználói beavatkozás nélkül kezeli a tokenek beszerzését és megújítását. A `loginPopup` (vagy `loginRedirect`) első `acquireTokenSilent` futtatása után a metódus általában a védett erőforrásokhoz való hozzáféréshez használt jogkivonatok beszerzésére szolgál a további hívásokhoz. (A kérések és a megújítási hívások csendesen történnek.) `acquireTokenSilent` bizonyos esetekben sikertelen lehet. Előfordulhat például, hogy a felhasználó jelszava lejárt. Az alkalmazás két módon tudja kezelni ezt a kivételt:

1. `acquireTokenPopup` Azonnal megteheti a hívást, ami elindítja a felhasználó bejelentkezési kérését. Ezt a mintát gyakran használják online alkalmazásokban, ahol az alkalmazás nem hitelesített tartalmakat használ a felhasználó számára. Az irányított telepítő által generált minta ezt a mintát használja.

1. Az alkalmazások vizuálisan is megadhatják a felhasználónak, hogy egy interaktív bejelentkezésre van szükség, így a felhasználó kiválaszthatja a bejelentkezéshez szükséges megfelelő időt, vagy később is újra `acquireTokenSilent` próbálkozhat az alkalmazással. Ezt általában akkor érdemes használni, ha a felhasználó az alkalmazás egyéb funkcióit nem lehet megszakítani. Előfordulhat például, hogy nem hitelesített tartalom érhető el az alkalmazásban. Ebben az esetben a felhasználó dönthet arról, hogy mikor szeretné bejelentkezni a védett erőforrás eléréséhez, vagy az elavult információk frissítéséhez.

> [!NOTE]
> Ez a rövid útmutató `loginPopup` a `acquireTokenPopup` és a metódusokat használja alapértelmezés szerint. Ha böngészőként használja az Internet Explorert, ajánlott a és `loginRedirect` `acquireTokenRedirect` a metódusok használata, mert az Internet Explorer az előugró ablakokat kezelő módszerével kapcsolatos [ismert probléma](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) . Ha szeretné megtudni, hogyan érheti el ugyanazt az eredményt a használatával `Redirect methods`, tekintse meg a (z) című [témakört](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>A Microsoft Graph API meghívása az imént beszerzett token használatával

1. Először hozzon létre egy nevű `graphConfig.js`. js fájlt, amely a REST-végpontokat fogja tárolni. Adja hozzá a következő kódot:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Az elemek magyarázata:
   - Enter_the_Graph_Endpoint_Here>az MS Graph API példánya. * \<* A globális MS Graph API végpont esetében egyszerűen cserélje le ezt a karakterláncot a következőre: `https://graph.microsoft.com`. A nemzeti Felhőbeli üzembe helyezések esetében tekintse meg [Graph API dokumentációját](https://docs.microsoft.com/graph/deployments).

1. Ezután hozzon létre egy nevű `graph.js`. js-fájlt, amely Rest-hívást hajt végre Microsoft Graph API-nak, és adja hozzá a következő kódot:

   ```javascript
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
       .catch(error => console.log(error))
   }
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>További információ a védett API-k REST-hívásáról

Az útmutatóban létrehozott minta alkalmazásban a `callMSGraph()` METÓDUS használatával http `GET` -kérelem hozható létre egy olyan védett erőforráson, amely jogkivonatot igényel. A kérés ezután visszaadja a tartalmat a hívónak. Ez a metódus hozzáadja a beszerzett jogkivonatot a *http-engedélyezési fejlécben*. Az útmutatóban létrehozott minta alkalmazás esetében az erőforrás a Microsoft Graph API *Me* -végpontja, amely megjeleníti a felhasználó profiljának adatait.

## <a name="test-your-code"></a>A kód tesztelése

1. Konfigurálja úgy a kiszolgálót, hogy az *index. html* fájljának helyétől függően TCP-portot hallgasson. A Node. js esetében indítsa el a webkiszolgálót a port figyeléséhez a következő parancsok parancssori parancssorból történő futtatásával az alkalmazás mappájából:

   ```bash
   npm install
   npm start
   ```
1. A böngészőben adja meg a **http://localhost:3000** vagy **http://localhost:{port}** a értéket, ahol a *port* a webkiszolgáló által figyelt port. Ekkor meg kell jelennie az *index. html* fájl tartalmának és a **Bejelentkezés** gombnak.

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

A Microsoft Graph API-nak a felhasználónak *. Read* hatókörrel kell rendelkeznie a felhasználói profil olvasásához. Alapértelmezés szerint a rendszer automatikusan hozzáadja ezt a hatókört a regisztrációs portálon regisztrált összes alkalmazáshoz. A Microsoft Graph egyéb API-jai, valamint a háttér-kiszolgálóhoz tartozó egyéni API-k további hatóköröket is igényelhetnek. A Microsoft Graph API-nak például a *mail. Read* hatókört kell megadnia a felhasználó levelezésének listázásához.

> [!NOTE]
> A rendszer a hatókörök számának növelésével további hozzájárulásokat is kérhet a felhasználótól.

Ha egy háttérbeli API-nak nincs szüksége hatókörre (nem ajánlott), a *clientId* használhatja a jogkivonatok beszerzésére irányuló hívások hatókörét.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
