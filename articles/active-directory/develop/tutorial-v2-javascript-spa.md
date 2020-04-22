---
title: JavaScript egyoldalas alkalmazásbemutató - Microsoft identity platform | Azure
description: Hogyan hívhatnak meg javascript SPA-alkalmazások olyan API-t, amelyhez az Azure Active Directory 2.0-s verziójú végpontja hozzáférési jogkivonatokat igényel?
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
ms.openlocfilehash: 52b7c582848dd24f6d9963a9d37c8f12c5db6149
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678029"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Jelentkezzen be a felhasználókhoz, és hívja fel a Microsoft Graph API-t egy JavaScript egyoldalas alkalmazásból (SPA)

Ez az útmutató bemutatja, hogy a JavaScript egyoldalas alkalmazás (SPA) hogyan:
- Bejelentkezés személyes fiókok, valamint munkahelyi és iskolai fiókok
- Hozzáférési jogkivonat beszerzése
- Hívja meg a Microsoft Graph API-t vagy más API-kat, amelyek hozzáférési jogkivonatokat igényelnek a *Microsoft identity platform végpontjáról*

>[!NOTE]
> Ha most ismeri a Microsoft identitásplatformot, javasoljuk, hogy kezdje a [Bejelentkezési felhasználókkal, és szerezzen be egy hozzáférési jogkivonatot a JavaScript SPA rövid útmutatójában.](quickstart-v2-javascript.md)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Az útmutató által létrehozott mintaalkalmazás működése

![Bemutatja, hogyan működik az oktatóanyag által létrehozott mintaalkalmazás](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>További információ

Az útmutató által létrehozott mintaalkalmazás lehetővé teszi, hogy a JavaScript SPA lekérdezze a Microsoft Graph API-t vagy egy webes API-t, amely elfogadja a Microsoft identity platform végponttokenjeit. Ebben a forgatókönyvben, miután egy felhasználó bejelentkezik, egy hozzáférési jogkivonatot kér, és hozzáadja a HTTP-kérelmek az engedélyezési fejlécen keresztül. Ez a jogkivonat a felhasználó profiljának és e-mailjeinek az **MS Graph API-n**keresztül történő megszerzésére szolgál. A tokenek beszerzését és megújítását a **Microsoft JavaScript-hitelesítési könyvtára (MSAL)** kezeli.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Kódtárak

Ez az útmutató a következő könyvtárat használja:

|Erőforrástár|Leírás|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft JavaScript-hitelesítési tár|

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Webkiszolgáló vagy projekt beállítása

> Inkább letölti a minta projektjét? [Töltse le a projektfájlokat](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> Ha a kódmintát a végrehajtás előtt szeretné konfigurálni, ugorjon a [konfigurációs lépésre.](#register-your-application)

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag futtatásához helyi webkiszolgálóra van szükség, például [a Node.js](https://nodejs.org/en/download/), [a .NET Core](https://www.microsoft.com/net/core)vagy az IIS Express integrációra a Visual Studio [2017-tel.](https://www.visualstudio.com/downloads/)

* Az útmutatóban található utasítások a Node.js-ben beépített webkiszolgálón alapulnak. Javasoljuk, hogy a [Visual Studio Code-ot](https://code.visualstudio.com/download) használja integrált fejlesztői környezetként (IDE).

## <a name="create-your-project"></a>A projekt létrehozása

Győződjön meg arról, hogy telepítette [a Node.js fájlt,](https://nodejs.org/en/download/) majd hozzon létre egy mappát az alkalmazás üzemeltetéséhez. Ott egy egyszerű [Express](https://expressjs.com/) webszervert valósítunk meg a fájl kiszolgálására. `index.html`

1. Először a Visual Studio Code integrált termináljának használatával keresse meg a projektmappát, majd telepítse az Express programot az NPM használatával.

1. Ezután hozzon létre egy `server.js`.js nevű fájlt, majd adja hozzá a következő kódot:

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

Most már van egy egyszerű szerver szolgálja a SPA. Az oktatóanyag végén található kívánt mappastruktúra a következő:

![a tervezett SPA mappaszerkezet szöveges ábrázolása](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Az SPA felhasználói felületének létrehozása

1. Hozzon `index.html` létre egy fájlt a JavaScript SPA. Ez a fájl a **Bootstrap 4 Framework** rendszerrel készített felhasználói felületet valósítja meg, és parancsfájlfájlokat importál a konfigurációhoz, a hitelesítéshez és az API-híváshoz.

   A `index.html` fájlba adja hozzá a következő kódot:

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
   > Az MSAL.js verziót az előző parancsfájlban lecserélheti az [MSAL.js kiadások](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)alatt kiadott legújabb verzióra.

2. Most hozzon létre egy `ui.js`.js nevű fájlt, amely megnyitja és frissíti a DOM elemeket, és adja hozzá a következő kódot:

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

Mielőtt továbblépne a hitelesítéssel, regisztrálja az alkalmazást az **Azure Active Directoryban.**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Ha a fiók hozzáférést biztosít egynél több bérlőhöz, válassza ki a jobb felső sarokban lévő fiókot, majd állítsa be a portálmunkamenetet a használni kívánt Azure AD-bérlőre.
1. Nyissa meg a Microsoft identity platform ot a [fejlesztőknek Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon.
1. Ha megjelenik az **Alkalmazás regisztrálása** oldal, adjon nevet az alkalmazásnak.
1. A **Támogatott fióktípusok csoportban**válassza a Fiókok lehetőséget **bármely szervezeti címtárban és személyes Microsoft-fiókban.**
1. Az **ÁTirányítás URI-csoportban** válassza ki a **webes** platformot a legördülő listából, majd állítsa be az értéket a webkiszolgálón alapuló alkalmazás URL-címére.
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosító** értékét későbbi használatra.
1. Ehhez a rövid útmutatóhoz engedélyezni kell az [Implicit támogatási folyamatot.](v2-oauth2-implicit-grant-flow.md) A regisztrált alkalmazás bal oldali ablaktáblájában válassza a **Hitelesítés**lehetőséget.
1. A **Speciális**beállítások **csoportban**jelölje be az **Azonosító-jogkivonatok és az Access-jogkivonatok** jelölőnégyzetet. **Access tokens** Azonosító jogkivonatok és hozzáférési jogkivonatok szükségesek, mert ez az alkalmazás be kell jelentkeznie a felhasználók és egy API-t kell hívnia.
1. Kattintson a **Mentés** gombra.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Átirányítási URL beállítása a Node.js-hez
>
> A Node.js fájlban beállíthatja a webkiszolgáló *portját.* Ez az oktatóanyag a 3000-es portot használja, de bármely más elérhető portot használhat.
>
> Ha átirányítási URL-címet szeretne beállítani az alkalmazás regisztrációs adatai között, váltson vissza az **Alkalmazásregisztrációs** ablaktáblára, és tegye az alábbiak egyikét:
>
> - Állítsa *`http://localhost:3000/`* be **átirányítási URL-címként.**
> - Ha egyéni TCP-portot használ, *`http://localhost:<port>/`* használja (ahol * \<a port>* az egyéni TCP-portszám).
>   1. Másolja az **URL-értéket.**
>   1. Váltson vissza az **Alkalmazásregisztrációs** ablaktáblára, és illessze be a másolt értéket **átirányítási URL-címként.**
>

### <a name="configure-your-javascript-spa"></a>A JavaScript SPA konfigurálása

Hozzon létre egy új `authConfig.js`.js nevű fájlt, amely tartalmazza a hitelesítés konfigurációs paramétereit, és adja hozzá a következő kódot:

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
 - Enter_the_Application_Id_Here>a regisztrált alkalmazás **alkalmazásazonosítója.** * \<*
 - Enter_the_Cloud_Instance_Id_Here>az Azure-felhő példánya. * \<* A fő vagy globális Azure-felhőhöz egyszerűen írja be a beírást. *https://login.microsoftonline.com* **A nemzeti** felhők (például Kína), lásd: [Nemzeti felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
 - Enter_the_Tenant_info_here>az alábbi beállítások egyikére van beállítva: * \<*
   - Ha az alkalmazás támogatja a *fiókok ebben a szervezeti címtárban,* cserélje le ezt az értéket a **bérlői azonosító** vagy bérlő **neve** (például *contoso.microsoft.com).*
   - Ha az alkalmazás *bármely szervezeti címtárban támogatja a fiókokat,* cserélje le ezt az értéket **szervezetekre.**
   - Ha az alkalmazás támogatja *a szervezeti címtárban és a személyes Microsoft-fiókokban lévő fiókokat,* cserélje le ezt az értéket a **közös értékre.** Ha csak *a személyes Microsoft-fiókokra*szeretné korlátozni a támogatást, cserélje le ezt az értéket **a fogyasztókra**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>A felhasználó bejelentkezéséhez a Microsoft hitelesítési tár (MSAL) segítségével jelentkezzen be

Hozzon létre egy új `authPopup.js`.js nevű fájlt, amely tartalmazza a hitelesítési és token-beszerzési logikát, és adja hozzá a következő kódot:

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

<!--start-collapse-->
### <a name="more-information"></a>További információ

Miután a felhasználó **Sign In** első alkalommal kiválasztja a `signIn` Bejelentkezés `loginPopup` gombot, a metódus meghívja a felhasználót. Ez a módszer megnyit egy előugró ablakot a *Microsoft identity platform végponttal,* hogy kérje és érvényesítse a felhasználó hitelesítő adatait. A sikeres bejelentkezés után a felhasználó visszakerül az eredeti *index.html* lapra. A jogkivonat fogadása, `msal.js`a , és a tokenben található információk gyorsítótárba. Ez a jogkivonat az *id token,* és alapvető információkat tartalmaz a felhasználó, például a felhasználó megjelenítendő nevét. Ha azt tervezi, hogy a jogkivonat által biztosított adatok bármilyen célra, győződjön meg arról, hogy ezt a jogkivonatot a háttérkiszolgáló érvényesíti annak biztosítása érdekében, hogy a jogkivonatot egy érvényes felhasználó az alkalmazáshoz.

Az útmutató által létrehozott `acquireTokenSilent` spa hívásokat hív, és/vagy `acquireTokenPopup` a Microsoft Graph API felhasználói profiladatainak lekérdezéséhez használt *hozzáférési jogkivonat* megszerzésére szolgál. Ha szüksége van egy mintára, amely érvényesíti az azonosító jogkivonatot, tekintse meg [ezt a](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 minta") mintaalkalmazást a GitHubon. A minta egy ASP.NET webes API-t használ a token érvényesítése.

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

A kezdeti bejelentkezés után nem szeretné megkérni a felhasználókat, hogy minden alkalommal, amikor egy erőforrás eléréséhez jogkivonatot kell kérniük, minden alkalommal, amikor tokenre van szükségük. Így *acquireTokenSilent* kell használni az idő nagy részében a tokenek megszerzéséhez. Vannak azonban olyan helyzetek, amikor a felhasználókat a Microsoft identity platform végpontjának kommunikálására kell kényszerítenie. Példák erre vonatkozóan:

- A felhasználóknak újra meg kell adniuk a hitelesítő adataikat, mert a jelszó lejárt.
- Az alkalmazás hozzáférést kér egy erőforráshoz, és a felhasználó beleegyezésére van szüksége.
- Kétfaktoros hitelesítés szükséges.

Az *acquireTokenPopup hívása* megnyit egy előugró ablakot (vagy *acquireTokenRedirect* átirányítja a felhasználókat a Microsoft identity platform végpontjára). Ebben az ablakban a felhasználóknak a hitelesítő adataik megerősítésével, a szükséges erőforrás beleegyezésével vagy a kétfaktoros hitelesítés befejezésével kell együttműködniük.

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` módszer kezeli a tokenek beszerzése és megújítása felhasználói beavatkozás nélkül. Miután `loginPopup` `loginRedirect`(vagy ) végre az `acquireTokenSilent` első alkalommal, a módszer általánosan használt jogkivonatok eléréséhez használt védett erőforrások későbbi hívásokhoz. (A tokenek kérelmezésére vagy megújítására irányuló hívások csendesen történik.) `acquireTokenSilent` bizonyos esetekben sikertelen lehet. Előfordulhat például, hogy a felhasználó jelszava lejárt. Az alkalmazás kétféleképpen tudja kezelni ezt a kivételt:

1. Azonnal kezdeményezze `acquireTokenPopup` a hívást, amely elindítja a felhasználói bejelentkezési kérdést. Ezt a mintát gyakran használják olyan online alkalmazásokban, ahol az alkalmazásban nincs nem hitelesített tartalom. Az irányított beállítás által létrehozott minta ezt a mintát használja.

1. Az alkalmazások vizuálisan is jelezhetik a felhasználónak, hogy interaktív bejelentkezésre van szükség, így a felhasználó `acquireTokenSilent` kiválaszthatja a megfelelő időpontot a bejelentkezéshez, vagy az alkalmazás később újrapróbálkozhat. Ez gyakran használatos, ha a felhasználó az alkalmazás más funkcióit is használhatja anélkül, hogy megszakadna. Előfordulhat például, hogy nem hitelesített tartalom érhető el az alkalmazásban. Ebben az esetben a felhasználó eldöntheti, hogy mikor szeretne bejelentkezni a védett erőforrás eléréséhez, vagy frissítse az elavult információkat.

> [!NOTE]
> Ez a rövid `loginPopup` `acquireTokenPopup` útmutató alapértelmezés szerint a és a metódusokat használja. Ha az Internet Explorer böngészőt használja, ajánlott `loginRedirect` `acquireTokenRedirect` használni és módszereket használni, mivel ismert [probléma](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) van azzal kapcsolatban, hogy az Internet Explorer hogyan kezeli az előugró ablakokat. Ha szeretné látni, hogyan érheti el `Redirect methods`ugyanazt az eredményt a használatával, kérjük, [olvassa el](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js)a .
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Hívja meg a Microsoft Graph API-t az imént beszerzett token használatával

1. Először hozzon létre egy `graphConfig.js`.js nevű fájlt, amely tárolja a REST-végpontokat. Adja hozzá a következő kódot:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Az elemek magyarázata:
   - Enter_the_Graph_Endpoint_Here>az MS Graph API példánya. * \<* A globális MS Graph API-végpont esetében `https://graph.microsoft.com`egyszerűen cserélje le ezt a karakterláncot a programra. A nemzeti felhőalapú telepítések ről a [Graph API dokumentációja](https://docs.microsoft.com/graph/deployments)című dokumentumban olvashat.

1. Ezután hozzon létre egy `graph.js`.js nevű fájlt, amely REST-hívást kezdeményez a Microsoft Graph API-ba, és adja hozzá a következő kódot:

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

<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>További információ a REST-hívásról egy védett API-val szemben

Az útmutató által létrehozott mintaalkalmazásban a `callMSGraph()` módszer http-kérelmet `GET` nyújt be egy jogkivonatot igénylő védett erőforrással szemben. A kérés ezután visszaadja a tartalmat a hívónak. Ez a módszer hozzáadja a beszerzett jogkivonatot a *HTTP-engedélyezési fejléchez.* Az útmutató által létrehozott mintaalkalmazás esetében az erőforrás a Microsoft Graph API *me* végpont, amely a felhasználó profiladatait jeleníti meg.

<!--end-collapse-->

## <a name="test-your-code"></a>A kód tesztelése

1. Állítsa be úgy a kiszolgálót, hogy az *index.html* fájl helyétől függő TCP-portot hallgasson. Node.js esetén indítsa el a webkiszolgálót a port meghallgatásához úgy, hogy az alkalmazásmappából parancssori parancssori parancssorban futtatja a parancsokat:

   ```bash
   npm install
   npm start
   ```
1. A böngészőben **http://localhost:3000** adja **http://localhost:{port}** meg vagy a , ahol a *port* az a port, amelyet a webkiszolgáló figyel. Meg kell jelennie az *index.html* fájl tartalmának és a **Bejelentkezés** gombnak.

## <a name="test-your-application"></a>Az alkalmazás tesztelése

Miután a böngésző betölti az *index.html* fájlt, válassza **a Bejelentkezés**lehetőséget. A rendszer kéri, hogy jelentkezzen be a Microsoft identity platform végpontjával:

![A JavaScript SPA-fiók bejelentkezési ablaka](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Hozzájárulás hozzájárulása az alkalmazáshoz való hozzáféréshez

Amikor először jelentkezik be az alkalmazásba, a rendszer kéri, hogy adjon hozzáférést a profiljához, és jelentkezzen be:

![A "Kért engedélyek" ablak](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Alkalmazáseredmények megtekintése

A bejelentkezés után a rendszer a rendszer a következő ként jelenik meg a Microsoft Graph API-válaszában a felhasználói profil adatait adja vissza:

![A Microsoft Graph API-hívásának eredményei](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörökről és a delegált engedélyekről

A Microsoft Graph API-nak a *user.read* hatókörre van szüksége a felhasználói profil olvasásához. Alapértelmezés szerint ez a hatókör automatikusan hozzáadódik a regisztrációs portálon regisztrált minden alkalmazáshoz. A Microsoft Graph egyéb API-k, valamint a háttérkiszolgáló egyéni API-jai további hatóköröket igényelhetnek. A Microsoft Graph API-nak például a *Mail.Read* hatókörre van szüksége a felhasználó e-mailjeinek listázásához.

> [!NOTE]
> A rendszer további jóváhagyásokat kérhet a felhasználótól, ahogy növeli a hatókörök számát.

Ha egy háttér-API nem igényel hatókört (nem ajánlott), *használhatja az ügyfélazonosító* t, mint a jogkivonatok beszerzéséhez a hívások hatóköre.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
