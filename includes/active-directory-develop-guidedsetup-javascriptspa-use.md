---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 6f6318f2b4386118d83a72d667e63f88327e240b
ms.sourcegitcommit: abeefca6cd5ca01c3e0b281832212aceff08bf3e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "64993304"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>A Microsoft-hitelesítési tár (MSAL) segítségével a felhasználók bejelentkeztetése

1. Adja hozzá a következő kódot a `index.html` fájlt a `<script></script>` címkék:

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
    var request = {
        scopes: ["user.read"]
    };

    var myMSALObj = new Msal.UserAgentApplication(msalConfig);
    // Register Callbacks for redirect flow
    myMSALObj.handleRedirectCallbacks(acquireTokenRedirectCallBack, acquireTokenErrorRedirectCallBack);

    function signIn() {

        myMSALObj.loginPopup(request).then(function (loginResponse) {
            //Login Success
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }).catch(function (error) {
            console.log(error);
        });
    }


    function acquireTokenPopupAndCallMSGraph() {
        //Call acquireTokenSilent (iframe) to obtain a token for Microsoft Graph
        myMSALObj.acquireTokenSilent(request).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error.errorCode);
            // Call acquireTokenPopup (popup window) in case of acquireTokenSilent failure due to consent or interaction required ONLY
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(request).then(function (tokenResponse) {
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


   function acquireTokenRedirectAndCallMSGraph() {
        //Call acquireTokenSilent (iframe) to obtain a token for Microsoft Graph
        myMSALObj.acquireTokenSilent(request).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            //Call acquireTokenRedirect in case of acquireToken Failure
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenRedirect(request);
            }
        });
    }


    function acquireTokenRedirectCallBack(response) {
        if (response.tokenType === "access_token") {
            callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
        } else {
            console.log("token type is:" + response.tokenType);
        }
    }


    function  acquireTokenErrorRedirectCallBack(error) {
        console.log(error);
    }

    function requiresInteraction(errorCode) {
        if (!errorCode || !errorCode.length) {
            return false;
        }
        return errorCode === "consent_required" ||
            errorCode === "interaction_required" ||
            errorCode === "login_required";
    }


    if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
    }
    else if (loginType === 'REDIRECT') {
        document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(request);
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

Miután egy felhasználó rákattint a **bejelentkezés** gomb, először a `signIn` metódust hívja `loginPopup` bejelentkezni a felhasználó. Ez a módszer eredményezi az előugró ablak megnyitása a *a Microsoft identity platform endpoint* kérni, és a felhasználó hitelesítő adatainak ellenőrzésére. Eredményeként a bejelentkezés sikeres, a felhasználó visszatérhet az eredeti átirányítási *index.html* oldalon, és a egy jogkivonat érkezett, által feldolgozott `msal.js` és a jogkivonatban található az adatbázisban tárolja. Ez a token más néven a *azonosító jogkivonat* és alapvető információkat szeretne a felhasználó, például a felhasználó megjelenítendő nevét tartalmazza. Ha azt tervezi, bármilyen célból a token által biztosított adatokat használja, ellenőrizze, hogy ez a token érvényesíti a háttérkiszolgáló garantálja, hogy a jogkivonat érvényes felhasználónak az alkalmazáshoz kiállított szeretne.

Az ez által generált SPA hívások útmutató `acquireTokenSilent` és/vagy `acquireTokenPopup` beszerezni egy *hozzáférési jogkivonat* kérdezhetők le a Microsoft Graph API a felhasználói profil adatait. Ha egy mintát, amely ellenőrzi az azonosító jogkivonat, vessen egy pillantást [ez](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "active-directory-javascript-singlepageapp-dotnet-webapi-v2 GitHub-minta") mintaalkalmazás github – a mintát használja egy ASP .NET webes API a jogkivonat érvényesítésére.

#### <a name="getting-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Az első bejelentkezés után, nem szeretné, hogy kérje meg a felhasználók számára, hogy hitelesítse magát újra, minden alkalommal, amikor szükségük van egy erőforrás – így eléréséhez egy jogkivonat kérelmezéséhez *acquireTokenSilent* használandó a legtöbbször szerzi be a jogkivonatokat. Vannak helyzetek, de ilyen például, hogy szeretne-e kommunikálni a Microsoft identity platform végpont – a felhasználókat:

- Lehet, hogy a felhasználóknak újból meg kell adniuk a hitelesítési adataikat, mert a jelszó lejárt
- Az alkalmazás olyan erőforráshoz kér hozzáférést, amelyhez szükséges a felhasználó hozzájárulása
- Kétfaktoros hitelesítésre van szükség

Hívása a *acquireTokenPopup* egy előugró ablak eredményez (vagy *acquireTokenRedirect* eredményez a felhasználók átirányítása a Microsoft identity platform végpont) Ha a felhasználók által interaktív kell a hitelesítő adataikat, engedélyezi a hogy a szükséges erőforrást, vagy a kéttényezős hitelesítés befejezése megerősítésével.

#### <a name="getting-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` metódus kezeli a token beszerzését és -megújítás, felhasználói beavatkozás nélkül. Miután `loginPopup` (vagy `loginRedirect`) hajtja végre az első alkalommal `acquireTokenSilent` hívások kérelem vagy token megújítása csendes módban végzett hívások – a védett erőforrások eléréséhez használt tokenek beszerzése érdekében a gyakran használt módszer.
`acquireTokenSilent` sikertelen lehet, hogy bizonyos esetekben – például a felhasználó jelszava lejárt. Az alkalmazás ehhez a kivételhez, két módon tudják kezelni:

1. Hívás `acquireTokenPopup` azonnal, aminek eredményeképpen kéri a felhasználót, hogy jelentkezzen be. Az online alkalmazások gyakran használják ezt a mintát, ha ott nem nem hitelesített tartalmat, az alkalmazás a felhasználó számára elérhető. Az interaktív telepítés által létrehozott példa ezt a mintát.

2. Alkalmazások tudja végrehajtani a vizuális jelzés a felhasználót, hogy egy interaktív bejelentkezési szükség, hogy a felhasználó kiválaszthatja a megfelelő időben való bejelentkezéshez, vagy az alkalmazás megpróbálhatja `acquireTokenSilent` egy későbbi időpontban. Ez általában akkor használatos, amikor a felhasználó használhatja az alkalmazás egyéb funkciók anélkül, hogy szakadhat meg – például nincs nem hitelesített tartalom érhető el az alkalmazást. Ebben az esetben a felhasználó eldöntheti, ha szeretnének bejelentkezni a védett erőforrás elérésére, vagy a frissítés az elavult adatokat.

> [!NOTE]
> Ebben a rövid útmutatóban használt a `loginRedirect` és `acquireTokenRedirect` módszerek, ha a használt böngésző oka az, hogy az Internet Explorer egy [ismert hiba](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) kapcsolatos kezelési előugró Windows Internet Explorer böngészőben.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>A Microsoft Graph API segítségével közvetlenül megszerzett jogkivonattal hívása

Adja hozzá a következő kódot a `index.html` fájlt a `<script></script>` címkék:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>További információ a védett API REST-hívást gondoskodik

A jelen útmutató által létrehozott mintaalkalmazásban a `callMSGraph()` , hogy a HTTP módszert `GET` kérelmek ellen védett erőforrás, amely a jogkivonat szükséges, és majd a tartalom térjen vissza a hívónak. Ez a metódus hozzáadja a beszerzett lexikális elem szerepel a *HTTP engedélyeztetési fejléc*. A mintaalkalmazás, ez az útmutató által létrehozott, az erőforrás a Microsoft Graph API *me* végpont – amely a felhasználói profil adatait jeleníti meg.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adjon meg egy metódust a felhasználó kijelentkeztetése

Adja hozzá a következő kódot a `index.html` fájlt a `<script></script>` címkék:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```
