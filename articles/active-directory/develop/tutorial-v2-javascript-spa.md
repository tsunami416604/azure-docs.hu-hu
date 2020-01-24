---
title: JavaScript egyoldalas alkalmazás oktatóanyaga – Microsoft Identity platform | Azure
description: Hogyan hívhatják meg a JavaScript SPA-alkalmazások olyan API-t, amelyhez hozzáférési jogkivonatok szükségesek Azure Active Directory v 2.0 végponttal
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 33e1037a0fe261f9fb0d06a9ebb0b3b323fe8d5f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701263"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Bejelentkezés a felhasználókba és a Microsoft Graph API meghívása egy JavaScript egyoldalas alkalmazásból (SPA)

Ez az útmutató bemutatja, hogyan használható a JavaScript egyoldalas alkalmazás (SPA):
- Bejelentkezés személyes fiókokba, valamint munkahelyi és iskolai fiókokba 
- Hozzáférési jogkivonat beszerzése
- Hívja meg az Microsoft Graph API-t vagy más API-kat, amelyek hozzáférési jogkivonatokat igényelnek a *Microsoft Identity platform végpontján*

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Az útmutató által létrehozott minta alkalmazás működése

![Bemutatja, hogyan működik az oktatóanyag által generált minta alkalmazás](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>További információ

Az útmutatóban létrehozott minta alkalmazás lehetővé teszi, hogy a JavaScript SPA lekérdezze a Microsoft Graph API-t vagy egy webes API-t, amely elfogadja a tokeneket a Microsoft Identity platform végpontján. Ebben az esetben a felhasználó bejelentkezése után hozzáférési jogkivonatot kér a rendszer, és hozzáadja a HTTP-kérésekhez az engedélyezési fejlécen keresztül. A token beszerzését és megújítását a Microsoft Authentication Library (MSAL) kezeli.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Kódtárak

Ez az útmutató a következő könyvtárat használja:

|Részletes ismertetés|Leírás|
|---|---|
|[msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library a JavaScripthez – előzetes verzió|

> [!NOTE]
> A *Msal. js* a Microsoft Identity platform végpontját célozza meg, amely lehetővé teszi a személyes fiókok, az iskolai és munkahelyi fiókok számára a bejelentkezést és a jogkivonatok beszerzését. A Microsoft Identity platform végpontjának [bizonyos korlátai vannak](azure-ad-endpoint-comparison.md#limitations).
> A v 1.0 és a v 2.0 végpontok közötti különbségek megismeréséhez tekintse meg a [végpontok összehasonlító útmutatóját](azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Webkiszolgáló vagy projekt beállítása

> Inkább a minta projektjét szeretné letölteni? A következő lehetőségek közül választhat:
> 
> - Ha a projektet helyi webkiszolgálóval, például a Node. js használatával szeretné futtatni, [töltse le a projektfájlt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - Választható Ha a projektet a Microsoft Internet Information Services (IIS) kiszolgáló használatával szeretné futtatni, [töltse le a Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> A kód minta konfigurálásához a végrehajtás előtt ugorjon a [konfigurációs lépésre](#register-your-application).

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag futtatásához helyi webkiszolgálóra, például [Node. js](https://nodejs.org/en/download/), [.net Core](https://www.microsoft.com/net/core)vagy IIS Express [Visual Studio 2017](https://www.visualstudio.com/downloads/)-integrációra van szükség.

* Ha a Node. js-t használja a projekt futtatásához, telepítsen egy integrált fejlesztői környezetet (IDE), például a [Visual Studio Code](https://code.visualstudio.com/download)-ot a projektfájlok szerkesztéséhez.

* Az útmutatóban szereplő utasítások a Node. js és a Visual Studio 2017-es verzión alapulnak, de bármilyen más fejlesztési környezetet vagy webkiszolgálót használhat.

## <a name="create-your-project"></a>A projekt létrehozása

> ### <a name="option-1-nodejs-or-other-web-servers"></a>1\. lehetőség: Node. js vagy más webkiszolgáló
> Győződjön meg arról, hogy telepítve van a [Node. js](https://nodejs.org/en/download/) , majd hozzon létre egy mappát az alkalmazás üzemeltetéséhez.
>
> ### <a name="option-2-visual-studio"></a>2\. lehetőség: Visual Studio
> Ha a Visual studiót használja, és új projektet hoz létre, kövesse az alábbi lépéseket:
> 1. A Visual Studióban válassza a **fájl** > **új** > **projekt**lehetőséget.
> 1. A **Visual C#\Web** területen válassza az **ASP.NET Web Application (.NET Framework)** (ASP.NET-webalkalmazás (.NET-keretrendszer)) lehetőséget.
> 1. Adja meg az alkalmazás nevét, majd kattintson **az OK gombra**.
> 1. Az **új ASP.net-webalkalmazás**területen válassza az **üres**lehetőséget.

## <a name="create-the-spa-ui"></a>A SPA felhasználói felületének létrehozása
1. Hozzon létre egy *index. html* fájlt a JavaScript Spa számára. Ha a Visual studiót használja, válassza ki a projektet (projekt gyökérkönyvtára). Kattintson a jobb gombbal, és válassza az **új elem** > **hozzáadása** > **HTML-oldal**lehetőséget, és nevezze el a fájl *index. html*nevet.

1. Az *index. html* fájlban adja hozzá a következő kódot:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > A MSAL. js fájl verzióját az előző szkriptben lecserélheti a legújabb, a [MSAL. js kiadásban](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)megjelent verzióra.

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>A Microsoft Authentication Library (MSAL) használatával jelentkezzen be a felhasználóba

Adja hozzá a következő kódot a `index.html` fájlhoz a `<script></script>` címkén belül:

   ```JavaScript
   var msalConfig = {
       auth: {
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
       },
       cache: {
           cacheLocation: "localStorage",
           storeAuthStateInCookie: true
       }
   };

   var graphConfig = {
       graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
   };

   // this can be used for login or token request, however in more complex situations
   // this can have diverging options
   var requestObj = {
        scopes: ["user.read"]
   };

   var myMSALObj = new Msal.UserAgentApplication(msalConfig);
   // Register Callbacks for redirect flow
   myMSALObj.handleRedirectCallback(authRedirectCallBack);


   function signIn() {

       myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
           //Login Success
           showWelcomeMessage();
           acquireTokenPopupAndCallMSGraph();
       }).catch(function (error) {
           console.log(error);
       });
   }

   function acquireTokenPopupAndCallMSGraph() {
       //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
       myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
       }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
       });
   }


   function graphAPICallback(data) {
       document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
   }


   function showWelcomeMessage() {
       var divWelcome = document.getElementById('WelcomeMessage');
       divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
       var loginbutton = document.getElementById('SignIn');
       loginbutton.innerHTML = 'Sign Out';
       loginbutton.setAttribute('onclick', 'signOut();');
   }


   //This function can be removed if you do not need to support IE
   function acquireTokenRedirectAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenRedirect
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenRedirect(requestObj);
            }
        });
   }


   function authRedirectCallBack(error, response) {
       if (error) {
           console.log(error);
       }
       else {
           if (response.tokenType === "access_token") {
               callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
           } else {
               console.log("token type is:" + response.tokenType);
           }
       }
   }

   function requiresInteraction(errorCode) {
       if (!errorCode || !errorCode.length) {
           return false;
       }
       return errorCode === "consent_required" ||
           errorCode === "interaction_required" ||
           errorCode === "login_required";
   }

   // Browser check variables
   var ua = window.navigator.userAgent;
   var msie = ua.indexOf('MSIE ');
   var msie11 = ua.indexOf('Trident/');
   var msedge = ua.indexOf('Edge/');
   var isIE = msie > 0 || msie11 > 0;
   var isEdge = msedge > 0;
   //If you support IE, our recommendation is that you sign-in using Redirect APIs
   //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
   // can change this to default an experience outside browser use
   var loginType = isIE ? "REDIRECT" : "POPUP";

   if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
   }
   else if (loginType === 'REDIRECT') {
       document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
       };
       if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
   } else {
       console.error('Please set a valid login type');
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>További információ

Miután a felhasználó első alkalommal kiválasztja a **Bejelentkezés** gombot, a `signIn` metódus meghívja `loginPopup` a felhasználót a bejelentkezéshez. Ezzel a módszerrel megnyílik egy előugró ablak a *Microsoft Identity platform végpontján* a felhasználó hitelesítő adatainak megadásához és érvényesítéséhez. Sikeres bejelentkezés után a rendszer visszairányítja a felhasználót az eredeti *index. html* oldalra. A rendszer tokent fogad, `msal.js`dolgozza fel, és a jogkivonatban található információkat gyorsítótárazza. Ez a jogkivonat *azonosító jogkivonat* , és alapszintű információt tartalmaz a felhasználóról, például a felhasználó megjelenítendő nevét. Ha bármilyen célra szeretné használni a token által biztosított bármilyen adatforrást, győződjön meg róla, hogy a háttér-kiszolgáló érvényesíti ezt a jogkivonatot annak biztosításához, hogy a jogkivonatot a rendszer egy érvényes felhasználónak adja ki az alkalmazás számára.

Az útmutató által létrehozott fürdő `acquireTokenSilent` és/vagy `acquireTokenPopup`t hív meg a felhasználói profil adatainak Microsoft Graph API-nak a lekérdezéséhez használt *hozzáférési jogkivonat* beszerzéséhez. Ha olyan mintát kell használnia, amely érvényesíti az azonosító jogkivonatot, tekintse meg [ezt](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub Active-Directory-JavaScript-singlepageapp-DotNet-webapi-v2 minta") a minta alkalmazást a githubban. A minta egy ASP.NET webes API-t használ a jogkivonat-ellenőrzéshez.

#### <a name="getting-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

A kezdeti bejelentkezés után nem kívánja megkérni a felhasználókat, hogy minden alkalommal újra hitelesíteni tudják az erőforrásokhoz való hozzáféréshez szükséges jogkivonatot. Így a *acquireTokenSilent* a legtöbb időt kell használni a tokenek beszerzéséhez. Vannak azonban olyan helyzetek, amikor kényszeríteni kell a felhasználókat, hogy együttműködjön a Microsoft Identity platform-végponttal. Példák:

- A felhasználóknak újra meg kell adniuk a hitelesítő adataikat, mert a jelszó lejárt.
- Az alkalmazás hozzáférést kér egy erőforráshoz, és szüksége van a felhasználó belefoglalására.
- Kétfaktoros hitelesítés szükséges.

A *acquireTokenPopup* meghívásával megnyílik egy előugró ablak (vagy a *acquireTokenRedirect* átirányítja a felhasználókat a Microsoft Identity platform-végpontra). Ebben az ablakban a felhasználóknak kapcsolatba kell lépniük a hitelesítő adataik megerősítésével, a szükséges erőforrás jóváhagyásával, vagy a kétfaktoros hitelesítés végrehajtásával.

#### <a name="getting-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` metódus felhasználói beavatkozás nélkül kezeli a tokenek beszerzését és megújítását. `loginPopup` (vagy `loginRedirect`) első futtatása után a rendszer a következő metódust használja a védett erőforrások eléréséhez használt jogkivonatok beszerzéséhez: `acquireTokenSilent`. (A kérések és a megújítási hívások csendesen történnek.) a `acquireTokenSilent` bizonyos esetekben sikertelen lehet. Előfordulhat például, hogy a felhasználó jelszava lejárt. Az alkalmazás két módon tudja kezelni ezt a kivételt:

1. Hívjon `acquireTokenPopup` azonnal, amely elindítja a felhasználó bejelentkezési kérését. Ezt a mintát gyakran használják online alkalmazásokban, ahol az alkalmazás nem hitelesített tartalmakat használ a felhasználó számára. Az irányított telepítő által generált minta ezt a mintát használja.

2. Az alkalmazások vizuálisan is megadhatják a felhasználónak, hogy egy interaktív bejelentkezésre van szükség, így a felhasználó kiválaszthatja a bejelentkezéshez szükséges megfelelő időt, vagy később is újrapróbálkozhat `acquireTokenSilent`. Ezt általában akkor érdemes használni, ha a felhasználó az alkalmazás egyéb funkcióit nem lehet megszakítani. Előfordulhat például, hogy nem hitelesített tartalom érhető el az alkalmazásban. Ebben az esetben a felhasználó dönthet arról, hogy mikor szeretné bejelentkezni a védett erőforrás eléréséhez, vagy az elavult információk frissítéséhez.

> [!NOTE]
> Ez a rövid útmutató a `loginRedirect` és a `acquireTokenRedirect` metódust használja, amikor az Internet Explorer a böngészőt használja. Ezt a gyakorlatot követjük, mert az Internet Explorer az előugró ablakokat kezelő módszerével kapcsolatos [ismert probléma](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) .
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>A Microsoft Graph API meghívása az imént beszerzett token használatával

Adja hozzá a következő kódot a `index.html` fájlhoz a `<script></script>` címkén belül:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>További információ a védett API-k REST-hívásáról

Az útmutatóban létrehozott minta alkalmazásban a `callMSGraph()` módszer a HTTP-`GET` kérések elvégzésére szolgál egy jogkivonatot igénylő védett erőforráson. A kérés ezután visszaadja a tartalmat a hívónak. Ez a metódus hozzáadja a beszerzett jogkivonatot a *http-engedélyezési fejlécben*. Az útmutatóban létrehozott minta alkalmazás esetében az erőforrás a Microsoft Graph API *Me* -végpontja, amely megjeleníti a felhasználó profiljának adatait.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Metódus hozzáadása a felhasználó kijelentkezéséhez

Adja hozzá a következő kódot a `index.html` fájlhoz a `<script></script>` címkén belül:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>Alkalmazás regisztrálása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókot a jobb felső sarokban, majd állítsa be a portál munkamenetét a használni kívánt Azure AD-bérlőre.
1. Nyissa meg a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapját.
1. Ha megjelenik az **Alkalmazás regisztrálása** oldal, adjon nevet az alkalmazásnak.
1. A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
1. Az **átirányítási URI** szakaszban válassza ki a **webplatformot a** legördülő listából, majd állítsa be az értéket a webkiszolgálón alapuló alkalmazás URL-címére.

   További információ a Node. js és a Visual Studio átirányítási URL-címének beállításáról és beszerzéséről: az átirányítási URL-cím beállítása a Node. js-hez és [a Visual Studio átirányítási URL-címének beállítása](#set-a-redirect-url-for-visual-studio).

1. Kattintson a **Register** (Regisztrálás) elemre.
1. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.
1. Ez a rövid útmutató az [implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) engedélyezését igényli. A regisztrált alkalmazás bal oldali ablaktábláján válassza a **hitelesítés**lehetőséget.
1. A **Speciális beállítások**területén az **implicit engedélyezés**területen jelölje be az **azonosító tokenek** és a **hozzáférési tokenek** jelölőnégyzetet. Az azonosító jogkivonatok és hozzáférési tokenek megadása kötelező, mert az alkalmazásnak be kell jelentkeznie a felhasználókba, és hívnia kell egy API-t.
1. Kattintson a **Mentés** gombra.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Adja meg a Node. js átirányítási URL-címét
> A Node. js-ben beállíthatja a webkiszolgáló portját a *Server. js* fájlban. Ez az oktatóanyag a 30662-es portot használja, de bármilyen más elérhető portot is használhat.
>
> Ha egy átirányítási URL-címet szeretne beállítani az alkalmazás regisztrációs adatai között, váltson vissza az **alkalmazás regisztrációja** ablaktáblára, és tegye a következők egyikét:
>
> - *`http://localhost:30662/`* beállítása **átirányítási URL-címként**.
> - Ha egyéni TCP-portot használ, használja a *`http://localhost:<port>/`* (ahol a *\<port >* az egyéni TCP-port száma).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Átirányítási URL-cím beállítása a Visual studióhoz
> A Visual Studio átirányítási URL-címének beszerzéséhez kövesse az alábbi lépéseket:
> 1. A Megoldáskezelő területen válassza ki a projektet.
>
>    Megnyílik a **Tulajdonságok** ablak. Ha nem, nyomja le az F4 billentyűt.
>
>    ![A JavaScriptSPA projekt Tulajdonságok ablak](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Másolja az **URL-címet** .
> 1. Váltson vissza az **alkalmazás regisztrációja** ablaktáblára, és illessze be a másolt értéket az **átirányítási URL-címként**.

#### <a name="configure-your-javascript-spa"></a>A JavaScript SPA konfigurálása

1. A Project beállítása során létrehozott *index. html* fájlban adja meg az alkalmazás regisztrációs adatait. A fájl tetején a `<script></script>` címkén belül adja hozzá a következő kódot:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Helyszín:
    - *\<Enter_the_Application_Id_here >* a regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosítója** .
    - *\<Enter_the_Tenant_info_here >* a következő lehetőségek egyikére van beállítva:
       - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére** (például *contoso.microsoft.com*).
       - Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket **szervezetekkel**.
       - Ha az alkalmazás *minden szervezeti címtárban és személyes Microsoft-fiókban is támogatja a fiókokat*, cserélje le ezt az értéket **közösre**. Ha *csak a személyes Microsoft-fiókok*támogatását szeretné korlátozni, cserélje le ezt az értéket a **fogyasztókkal**.

## <a name="test-your-code"></a>A kód tesztelése

Tesztelje a kódot az alábbi környezetek valamelyikének használatával.

### <a name="test-with-nodejs"></a>Tesztelés Node. js-sel

Ha nem használja a Visual studiót, győződjön meg arról, hogy a webkiszolgáló elindult.

1. Konfigurálja úgy a kiszolgálót, hogy az *index. html* fájljának helyétől függően TCP-portot hallgasson. A Node. js esetében indítsa el a webkiszolgálót a port figyeléséhez a következő parancsok parancssori parancssorból történő futtatásával az alkalmazás mappájából:

    ```bash
    npm install
    node server.js
    ```
1. A böngészőben írja be a **http://\<span >\</span > localhost: 30662** vagy **http://\<span >\</span > localhost: {Port}** , ahol a *port* a webkiszolgáló által figyelt port. Ekkor meg kell jelennie az *index. html* fájl tartalmának és a **Bejelentkezés** gombnak.

### <a name="test-with-visual-studio"></a>Tesztelés a Visual Studióval

Ha a Visual studiót használja, válassza ki a Project megoldást, majd nyomja le az F5 billentyűt a projekt futtatásához. A böngésző megnyitja a http://<span></span>localhost: {Port} helyet, és a **Bejelentkezés** gombnak láthatónak kell lennie.

## <a name="test-your-application"></a>Az alkalmazás tesztelése

Miután a böngésző betölti az *index. html* fájlt, válassza a **Bejelentkezés**lehetőséget. A rendszer felszólítja, hogy jelentkezzen be a Microsoft Identity platform-végponttal:

![A JavaScript SPA-fiók bejelentkezési ablaka](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Adja meg az alkalmazás-hozzáférés beleegyezikét

Amikor először jelentkezik be az alkalmazásba, a rendszer felszólítja, hogy adjon hozzáférést a profilhoz, és jelentkezzen be:

![A "kért engedélyek" ablak](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Alkalmazás eredményeinek megtekintése

A bejelentkezést követően a felhasználói profil adatait a rendszer a Microsoft Graph API-válaszban jeleníti meg:

![A Microsoft Graph API-hívás eredményei](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörökről és a delegált engedélyekről

A Microsoft Graph API-nak a felhasználónak *. Read* hatókörrel kell rendelkeznie a felhasználói profil olvasásához. Alapértelmezés szerint a rendszer automatikusan hozzáadja ezt a hatókört a regisztrációs portálon regisztrált összes alkalmazáshoz. A Microsoft Graph egyéb API-jai, valamint a háttér-kiszolgálóhoz tartozó egyéni API-k további hatóköröket is igényelhetnek. A Microsoft Graph API-nak például a *naptárak. Read* hatókört kell megadnia a felhasználók naptárának listázásához.

Ha egy alkalmazás kontextusában szeretné elérni a felhasználó naptárait, adja hozzá a *naptárakat. olvassa el* a delegált jogosultságokat az alkalmazás regisztrációs adataihoz. Ezután adja hozzá a *naptárak. Read* hatókört a `acquireTokenSilent` híváshoz.

>[!NOTE]
>A rendszer a hatókörök számának növelésével további hozzájárulásokat is kérhet a felhasználótól.

Ha egy háttérbeli API-nak nincs szüksége hatókörre (nem ajánlott), a *clientId* használhatja a jogkivonatok beszerzésére irányuló hívások hatókörét.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
