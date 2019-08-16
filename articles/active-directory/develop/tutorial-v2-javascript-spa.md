---
title: Azure AD v 2.0 JavaScript egyoldalas alkalmazás (SPA) – irányított beállítás | Microsoft Docs
description: Hogyan hívhatják meg a JavaScript SPA-alkalmazások olyan API-t, amely hozzáférési jogkivonatokat igényel Azure Active Directory 2.0-s végponttal
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: cee0884ef20ef9cfd63d81d6f223705d34c65ccb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511793"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Bejelentkezés a felhasználókba és a Microsoft Graph API meghívása egy JavaScript egyoldalas alkalmazásból (SPA)

Ez az útmutató bemutatja, hogyan jelentkezhet be a JavaScript egyoldalas alkalmazás (SPA) a személyes, munkahelyi és iskolai fiókokba, hozzáférési token beszerzése és a Microsoft Graph API vagy más olyan API-k meghívása, amelyek hozzáférési jogkivonatokat igényelnek a Microsoft Identity platform végpontján.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Az útmutató által létrehozott minta alkalmazás működése

![Bemutatja, hogyan működik az oktatóanyagok által generált minta alkalmazás](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>További információ

Az útmutatóban létrehozott minta alkalmazás lehetővé teszi, hogy a JavaScript SPA lekérdezze a Microsoft Graph API-t vagy egy webes API-t, amely elfogadja a Microsoft Identity platform végpontjának jogkivonatait. Ebben az esetben a felhasználó bejelentkezése után hozzáférési jogkivonatot kér a rendszer, és hozzáadja a HTTP-kérésekhez az engedélyezési fejlécen keresztül. A token beszerzését és megújítását a Microsoft Authentication Library (MSAL) kezeli.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Kódtárak

Ez az útmutató a következő könyvtárat használja:

|Erőforrástár|Leírás|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library a JavaScripthez – előzetes verzió|

> [!NOTE]
> a *msal. js* a *Microsoft Identity platform végpontját* célozza meg, amely lehetővé teszi a személyes, iskolai és munkahelyi fiókok bejelentkezését és a jogkivonatok beszerzését. A *Microsoft Identity platform* végpontjának [bizonyos korlátai vannak](azure-ad-endpoint-comparison.md#limitations).
> A v 1.0 és a v 2.0 végpontok közötti különbségek megismeréséhez olvassa el a [végpont összehasonlító útmutatóját](azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Webkiszolgáló vagy projekt beállítása

> Inkább a minta projektjét szeretné letölteni? A következő lehetőségek közül választhat:
> 
> - A Project helyi webkiszolgálóval (például Node. js) való futtatásához [töltse le a Project fájlokat](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - Választható A projekt IIS-kiszolgálóval való futtatásához [töltse le a Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Ezután a kód minta konfigurálásához a végrehajtás előtt ugorjon a [konfigurációs lépésre](#register-your-application).

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag futtatásához helyi webkiszolgálóra, például [Node. js](https://nodejs.org/en/download/), [.net Core](https://www.microsoft.com/net/core)vagy IIS Express [Visual Studio 2017](https://www.visualstudio.com/downloads/)-integrációra van szükség.

* Ha a Node. js-t használja a projekt futtatásához, telepítsen egy integrált fejlesztői környezetet (IDE), például a [Visual Studio Code](https://code.visualstudio.com/download)-ot a projektfájlok szerkesztéséhez.

* Az útmutatóban szereplő utasítások a Node. js és a Visual Studio 2017-es verzión alapulnak, de bármilyen más fejlesztési környezetet vagy webkiszolgálót használhat.

## <a name="create-your-project"></a>A projekt létrehozása

> ### <a name="option-1-nodejs-or-other-web-servers"></a>1\. lehetőség: Node. js vagy más webkiszolgálók
> Győződjön meg arról, hogy telepítette a [Node. js fájlt](https://nodejs.org/en/download/), majd tegye a következőket:
> - Hozzon létre egy mappát az alkalmazás üzemeltetéséhez.
>
> ### <a name="option-2-visual-studio"></a>2\. lehetőség: Visual Studio
> Ha a Visual studiót használja, és új projektet hoz létre, tegye a következőket:
> 1. A Visual Studióban válassza a **fájl** > **új** > **projekt**lehetőséget.
> 1. A **Visual C#\Web** területen válassza az **ASP.NET Web Application (.NET Framework)** (ASP.NET-webalkalmazás (.NET-keretrendszer)) lehetőséget.
> 1. Adja meg az alkalmazás nevét, majd kattintson **az OK gombra**.
> 1. Az **új ASP.net**-webalkalmazás területen válassza az **üres**lehetőséget.

## <a name="create-the-spa-ui"></a>A SPA felhasználói felületének létrehozása
1. Hozzon létre egy *index. html* fájlt a JavaScript Spa számára. Ha a Visual studiót használja, válassza ki a projektet (Project Root mappát), kattintson a jobb gombbal, és válassza az**új elem** >  **hozzáadása** > **HTML-oldal**lehetőséget, és nevezze el a fájlt *index. html*néven.

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

1. Adja hozzá a következő kódot `index.html` a fájlhoz a `<script></script>` címkéken belül:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here"
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

Miután a felhasználó első alkalommal rákattint a **Bejelentkezés** gombra, a `signIn` metódus meghívja `loginPopup` a felhasználót a bejelentkezéshez. Ezzel a módszerrel a *Microsoft Identity platform végpontján* megnyitható egy előugró ablak, amely a felhasználó hitelesítő adatainak megadását és érvényesítését eredményezi. A sikeres bejelentkezés eredményeképpen a rendszer visszairányítja a felhasználót az eredeti *index. html* oldalra, és a rendszer a tokent fogadja, `msal.js` és a tokenben található információkat gyorsítótárazza. Ez a jogkivonat *azonosító jogkivonat* , és alapszintű információt tartalmaz a felhasználóról, például a felhasználó megjelenítendő nevét. Ha bármilyen célra szeretné használni a token által biztosított bármilyen adatforrást, győződjön meg róla, hogy a háttér-kiszolgáló érvényesíti ezt a jogkivonatot annak biztosításához, hogy a jogkivonatot a rendszer egy érvényes felhasználónak adja ki az alkalmazás számára.

Az útmutató által létrehozott Spa a Microsoft Graph `acquireTokenSilent` API felhasználói profil `acquireTokenPopup` adatainak lekérdezéséhez használt *hozzáférési token* beszerzésére hívja fel a kapcsolatot. Ha olyan mintát kell használnia, amely érvényesíti az azonosító jogkivonatot, tekintse meg [ezt]a(https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub Active-Directory-JavaScript-singlepageapp-DotNet-webapi-v2 minta") minta alkalmazást a githubban – a minta egy ASP.net webes API-t használ a jogkivonat-ellenőrzéshez.

#### <a name="getting-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

A kezdeti bejelentkezés után nem kívánja megkérni a felhasználókat, hogy minden alkalommal újra hitelesíteni tudják az erőforrásokhoz való hozzáféréshez szükséges jogkivonatot, így a legtöbb esetben a *acquireTokenSilent* kell használni a tokenek beszerzéséhez. Vannak azonban olyan helyzetek, amikor kényszeríteni kell a felhasználókat, hogy a Microsoft Identity platform-végpontot használják – néhány példa:

- Lehet, hogy a felhasználóknak újból meg kell adniuk a hitelesítési adataikat, mert a jelszó lejárt
- Az alkalmazás olyan erőforráshoz kér hozzáférést, amelyhez szükséges a felhasználó hozzájárulása
- Kétfaktoros hitelesítésre van szükség

A *acquireTokenPopup* meghívása egy előugró ablakban (vagy a *acquireTokenRedirect* a felhasználókat a Microsoft Identity platform végpontra irányítja át), ahol a felhasználóknak a hitelesítő adataik megerősítve kell lenniük, így a adja meg a szükséges erőforrást, vagy végezze el a kétfaktoros hitelesítés végrehajtását.

#### <a name="getting-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` metódus felhasználói beavatkozás nélkül kezeli a tokenek beszerzését és megújítását. A `loginPopup` (vagy `loginRedirect`) első `acquireTokenSilent` futtatása után a metódus általában a védett erőforrásokhoz való hozzáféréshez használt jogkivonatok lekérésére használatos a további hívásokhoz – ahogy a kérelem vagy a megújítási tokenek csendesen történnek.
`acquireTokenSilent`bizonyos esetekben sikertelen lehet – például a felhasználó jelszava lejárt. Az alkalmazás két módon tudja kezelni ezt a kivételt:

1. `acquireTokenPopup` Azonnal megteheti a hívást, amely arra kéri a felhasználót, hogy jelentkezzen be. Ezt a mintát gyakran használják online alkalmazásokban, ahol az alkalmazás nem hitelesített tartalmakat használ a felhasználó számára. Az irányított telepítő által generált minta ezt a mintát használja.

2. Az alkalmazások vizuálisan is megadhatják a felhasználónak, hogy egy interaktív bejelentkezésre van szükség, így a felhasználó kiválaszthatja a bejelentkezéshez szükséges megfelelő időt, vagy később is újra `acquireTokenSilent` próbálkozhat az alkalmazással. Ez általában akkor használatos, ha a felhasználó az alkalmazás egyéb funkcióit nem lehet megszakítani – például nem hitelesített tartalom érhető el az alkalmazásban. Ebben az esetben a felhasználó dönthet arról, hogy mikor szeretné bejelentkezni a védett erőforrás eléréséhez, vagy az elavult információk frissítéséhez.

> [!NOTE]
> Ez a rövid útmutató `loginRedirect` a `acquireTokenRedirect` és a metódust használja, ha a böngészőt az Internet Explorer használja az Internet Explorer böngészőben a felugró ablakok kezelésére vonatkozó [ismert probléma](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) miatt.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>A Microsoft Graph API meghívása az imént beszerzett token használatával

Adja hozzá a következő kódot `index.html` a fájlhoz a `<script></script>` címkéken belül:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>További információ a védett API-k REST-hívásáról

Az útmutatóban létrehozott minta alkalmazásban a `callMSGraph()` metódus használatával http `GET` -kérelem hozható létre egy olyan védett erőforráson, amely egy jogkivonatot igényel, majd visszaküldi a tartalmat a hívónak. Ez a metódus hozzáadja a beszerzett jogkivonatot a *http-engedélyezési fejlécben*. Az útmutatóban létrehozott minta alkalmazás esetében az erőforrás a Microsoft Graph API *Me* -végpont – amely megjeleníti a felhasználó profiljának adatait.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Metódus hozzáadása a felhasználó kijelentkezéséhez

Adja hozzá a következő kódot `index.html` a fájlhoz a `<script></script>` címkéken belül:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>Alkalmazás regisztrálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókot a jobb felső sarokban, majd állítsa be a portál munkamenetét a használni kívánt Azure AD-bérlőre.
1. Nyissa meg a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapját.
1. Amikor megjelenik az **alkalmazás regisztrálása** lap, adja meg az alkalmazás nevét.
1. A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
1. Az **átirányítási URI** szakasz legördülő listájában válassza ki a webplatformot , majd állítsa be az értéket a webkiszolgálón alapuló alkalmazás URL-címére. 

   Az átirányítási URL-cím a Visual Studióban és a Node. js-ben való beállításával és beszerzésével kapcsolatos információkért tekintse meg a következő két szakaszt.

1. Kattintson a **Register** (Regisztrálás) elemre.
1. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.
1. Ez a rövid útmutató az [implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) engedélyezését igényli. A regisztrált alkalmazás bal oldali ablaktábláján válassza a **hitelesítés**lehetőséget.
1. A **Speciális beállítások**területén az **implicit engedélyezés**területen jelölje be az **azonosító tokenek** és a **hozzáférési tokenek** jelölőnégyzetet. AZONOSÍTÓ jogkivonatok és hozzáférési tokenek szükségesek, mert az alkalmazásnak be kell jelentkeznie a felhasználókba, és hívnia kell egy API-t.
1. Kattintson a **Mentés** gombra.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Adja meg a Node. js átirányítási URL-címét
> A Node. js-ben beállíthatja a webkiszolgáló portját a *Server. js* fájlban. Ez az oktatóanyag a 30662-es portot használja hivatkozásként, de bármilyen más elérhető portot is használhat. 
>
> Ha egy átirányítási URL-címet szeretne beállítani az alkalmazás regisztrációs adatai között, váltson vissza az **alkalmazás regisztrációja** ablaktáblára, és tegye a következők egyikét:
>
> - Állítsa *`http://localhost:30662/`* be az **átirányítási URL-címet**.
> - Ha egyéni TCP-portot használ, használja *`http://localhost:<port>/`* a (ahol  *\<a port >* az egyéni TCP-port száma).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Átirányítási URL-cím beállítása a Visual studióhoz
> A Visual Studio átirányítási URL-címének beszerzéséhez tegye a következőket:
> 1. A **megoldáskezelő**területen válassza ki a projektet.
>
>    Megnyílik a **Tulajdonságok** ablak. Ha nincs megnyitva, nyomja le az **F4**billentyűt.
>
>    ![A JavaScriptSPA projekt Tulajdonságok ablak](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Másolja az **URL-címet** .
 
> 1. Váltson vissza az **alkalmazás regisztrációja** ablaktáblára, és illessze be a másolt értéket az **átirányítási URL-címként**.

#### <a name="configure-your-javascript-spa"></a>A JavaScript SPA konfigurálása

1. A Project beállítása során létrehozott *index. html* fájlban adja meg az alkalmazás regisztrációs adatait. A fájl tetején, a `<script></script>` címkék között adja hozzá a következő kódot:

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

    Az elemek magyarázata:
    - A Enter_the_Application_Id_here > a regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosítója** .  *\<*
    - A Enter_the_Tenant_info_here > az alábbi lehetőségek egyikére van beállítva:  *\<*
       - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére** (például *contoso.microsoft.com*).
       - Ha az alkalmazás *minden szervezeti címtárban*támogatja a fiókokat, cserélje leezt az értéket szervezetekkel.
       - Ha az alkalmazás *minden szervezeti címtárban és személyes Microsoft-fiókban is*támogatja a fiókokat, cserélje le ezt az értéket közösre. Ha *csak a személyes Microsoft-fiókok*támogatását szeretné korlátozni, cserélje le ezt az értéket a **fogyasztókkal**.

## <a name="test-your-code"></a>A kód tesztelése

Tesztelje a kódot az alábbi környezetek valamelyikének használatával.

### <a name="test-with-nodejs"></a>Tesztelés Node. js-sel

Ha nem használja a Visual studiót, győződjön meg arról, hogy a webkiszolgáló elindult.

1. Konfigurálja úgy a kiszolgálót, hogy az *index. html* fájljának helyétől függően TCP-portot hallgasson. A Node. js-hez indítsa el a webkiszolgálót, hogy figyelje a portot a következő parancsok parancssori parancssorból történő futtatásával az alkalmazás mappájából:

    ```bash
    npm install
    node server.js
    ```
1. A böngészőben írja be a **http://\<span >\</span > localhost: 30662** vagy **http://\<span >\</span > localhost: {Port}** , ahol a *port* a webkiszolgáló által használt port. figyelés:. Ekkor meg kell jelennie az *index. html* fájl tartalmának és a **Bejelentkezés** gombnak.

### <a name="test-with-visual-studio"></a>Tesztelés a Visual Studióval

Ha a Visual studiót használja, válassza ki a Project megoldást, majd válassza az F5 billentyűt a projekt futtatásához. A böngésző megnyitja a http://<span></span>localhost: {Port} helyet, és a **Bejelentkezés** gombnak láthatónak kell lennie.

## <a name="test-your-application"></a>Az alkalmazás tesztelése

Miután a böngésző betölti az *index. html* fájlt, válassza a **Bejelentkezés**lehetőséget. A rendszer felszólítja, hogy jelentkezzen be a Microsoft Identity platform-végponttal:

![A JavaScript SPA-fiók bejelentkezési ablaka](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Adja meg az alkalmazás-hozzáférés beleegyezikét

Amikor először jelentkezik be az alkalmazásba, a rendszer felszólítja, hogy adjon hozzáférést a profiljához, és jelentkezzen be:

![A "kért engedélyek" ablak](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Alkalmazás eredményeinek megtekintése

A bejelentkezés után a rendszer a felhasználói profil adatait a lapon megjelenő Microsoft Graph API-válaszban adja vissza.

![A Microsoft Graph API-hívás eredményei](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörökről és a delegált engedélyekről

A Microsoft Graph API-nak a felhasználónak *. Read* hatókörrel kell rendelkeznie a felhasználói profil olvasásához. A rendszer alapértelmezés szerint automatikusan hozzáadja ezt a hatókört a regisztrációs portálon regisztrált összes alkalmazáshoz. A Microsoft Graph egyéb API-jai, valamint a háttér-kiszolgálóhoz tartozó egyéni API-k további hatóköröket is igényelhetnek. A Microsoft Graph API például a *naptárak. Read* hatókört igényli a felhasználó naptárának listázásához.

Ha egy alkalmazás kontextusában szeretné elérni a felhasználó naptárait, adja hozzá a *naptárakat. olvassa el* a delegált jogosultságokat az alkalmazás regisztrációs adataihoz. Ezután adja hozzá a *naptárak. Read* hatókört a `acquireTokenSilent` híváshoz.

>[!NOTE]
>A rendszer a hatókörök számának növelésével további hozzájárulásokat is kérhet a felhasználótól.

Ha egy háttérbeli API-nak nincs szüksége hatókörre (nem ajánlott), a *clientId* használhatja a jogkivonatok beszerzésére irányuló hívások hatókörét.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)