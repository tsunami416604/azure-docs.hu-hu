---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 94d57abc95dabf1da579f6d2105ca6c74140a86f
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293755"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>A Microsoft-hitelesítési tár (MSAL) segítségével a felhasználók bejelentkeztetése

1.  Hozzon létre egy `app.js` nevű fájlt. Ha a Visual Studiót használja, válassza ki a projektet (projekt gyökérmappájában), kattintson a jobb gombbal, és válassza: `Add`  >  `New Item`  >  `JavaScript File`:
2.  Adja hozzá a következő kódot a `app.js` fájlt:

```javascript
// Graph API endpoint to show user profile
var graphApiEndpoint = "https://graph.microsoft.com/v1.0/me";

// Graph API scope used to obtain the access token to read user profile
var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

// Initialize application
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});

//Previous version of msal uses redirect url via a property
if (userAgentApplication.redirectUri) {
    userAgentApplication.redirectUri = msalconfig.redirectUri;
}

window.onload = function () {
    // If page is refreshed, continue to display user info
    if (!userAgentApplication.isCallback(window.location.hash) && window.parent === window && !window.opener) {
        var user = userAgentApplication.getUser();
        if (user) {
            callGraphApi();
        }
    }
}

/**
 * Call the Microsoft Graph API and display the results on the page. Sign the user in if necessary
 */
function callGraphApi() {
    var user = userAgentApplication.getUser();
    if (!user) {
        // If user is not signed in, then prompt user to sign in via loginRedirect.
        // This will redirect user to the Azure Active Directory v2 Endpoint
        userAgentApplication.loginRedirect(graphAPIScopes);
        // The call to loginRedirect above frontloads the consent to query Graph API during the sign-in.
        // If you want to use dynamic consent, just remove the graphAPIScopes from loginRedirect call.
        // As such, user will be prompted to give consent when requested access to a resource that 
        // he/she hasn't consented before. In the case of this application - 
        // the first time the Graph API call to obtain user's profile is executed.
    } else {
        // If user is already signed in, display the user info
        var userInfoElement = document.getElementById("userInfo");
        userInfoElement.parentElement.classList.remove("hidden");
        userInfoElement.innerHTML = JSON.stringify(user, null, 4);

        // Show sign-out button
        document.getElementById("signOutButton").classList.remove("hidden");

        // Now Call Graph API to show the user profile information:
        var graphCallResponseElement = document.getElementById("graphResponse");
        graphCallResponseElement.parentElement.classList.remove("hidden");
        graphCallResponseElement.innerText = "Calling Graph ...";

        // In order to call the Graph API, an access token needs to be acquired.
        // Try to acquire the token used to query Graph API silently first:
        userAgentApplication.acquireTokenSilent(graphAPIScopes)
            .then(function (token) {
                //After the access token is acquired, call the Web API, sending the acquired token
                callWebApiWithToken(graphApiEndpoint, token, graphCallResponseElement, document.getElementById("accessToken"));

            }, function (error) {
                // If the acquireTokenSilent() method fails, then acquire the token interactively via acquireTokenRedirect().
                // In this case, the browser will redirect user back to the Azure Active Directory v2 Endpoint so the user 
                // can reenter the current username/ password and/ or give consent to new permissions your application is requesting.
                // After authentication/ authorization completes, this page will be reloaded again and callGraphApi() will be executed on page load.
                // Then, acquireTokenSilent will then get the token silently, the Graph API call results will be made and results will be displayed in the page.
                if (error) {
                    userAgentApplication.acquireTokenRedirect(graphAPIScopes);
                }
            });
    }
}

/**
 * Callback method from sign-in: if no errors, call callGraphApi() to show results.
 * @param {string} errorDesc - If error occur, the error message
 * @param {object} token - The token received from sign-in
 * @param {object} error - The error string
 * @param {string} tokenType - The token type: For loginRedirect, tokenType = "id_token". For acquireTokenRedirect, tokenType:"access_token".
 */
function loginCallback(errorDesc, token, error, tokenType) {
    if (errorDesc) {
        showError(msal.authority, error, errorDesc);
    } else {
        callGraphApi();
    }
}

/**
 * Show an error message in the page
 * @param {string} endpoint - the endpoint used for the error message
 * @param {string} error - Error string
 * @param {string} errorDesc - Error description
 */
function showError(endpoint, error, errorDesc) {
    var formattedError = JSON.stringify(error, null, 4);
    if (formattedError.length < 3) {
        formattedError = error;
    }
    document.getElementById("errorMessage").innerHTML = "An error has occurred:<br/>Endpoint: " + endpoint + "<br/>Error: " + formattedError + "<br/>" + errorDesc;
    console.error(error);
}

```

<!--start-collapse-->
### <a name="more-information"></a>További információ

Miután egy felhasználó rákattint a *"A Microsoft Graph API meghívása"* gomb az első alkalommal `callGraphApi` metódust hívja `loginRedirect` bejelentkezni a felhasználó. Ez a módszer eredményezi átirányítása a felhasználó számára a *Microsoft Azure Active Directory v2 végpontot* kérni, és a felhasználó hitelesítő adatainak ellenőrzésére. Eredményeként a bejelentkezés sikeres, a felhasználó visszatérhet az eredeti átirányítási *index.html* oldalon, és a egy jogkivonat érkezett, által feldolgozott `msal.js` és a jogkivonatban található az adatbázisban tárolja. Ez a token más néven a *azonosító jogkivonat* és alapvető információkat szeretne a felhasználó, például a felhasználó megjelenítendő nevét tartalmazza. Ha azt tervezi, bármilyen célból a token által biztosított adatokat használja, ellenőrizze, hogy ez a token érvényesíti a háttérkiszolgáló garantálja, hogy a jogkivonat érvényes felhasználónak az alkalmazáshoz kiállított szeretne.

Ez az útmutató által létrehozott SPA nem derül közvetlenül az azonosító jogkivonat használata – ehelyett meghívja `acquireTokenSilent` és/vagy `acquireTokenRedirect` beszerezni egy *hozzáférési jogkivonat* a Microsoft Graph API lekérdezéséhez használt. Ha egy mintát, amely ellenőrzi az azonosító jogkivonat, vessen egy pillantást [ez](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "active-directory-javascript-singlepageapp-dotnet-webapi-v2 Github-minta") mintaalkalmazás github – a mintát használja egy ASP .NET webes API a jogkivonat érvényesítésére.

#### <a name="getting-a-user-token-interactively"></a>A felhasználó interaktív jogkivonat beolvasása

Az első bejelentkezés után, nem szeretné, hogy kérje meg a felhasználók számára, hogy hitelesítse magát újra, minden alkalommal, amikor szükségük van egy erőforrás – így eléréséhez egy jogkivonat kérelmezéséhez *acquireTokenSilent* használandó a legtöbbször szerzi be a jogkivonatokat. Vannak helyzetek, de ilyen például, hogy szeretne-e kommunikálni az Azure Active Directory v2 végpontot – a felhasználókat:
- Felhasználók előfordulhat, hogy meg kell adnia a hitelesítő adataikat, mert lejárt a jelszava
- Az alkalmazás, amely a felhasználónak van szüksége, hogy engedélyt adjanak az erőforrásokhoz való hozzáférést
- Kéttényezős hitelesítés megadása kötelező

Hívása a *acquireTokenRedirect(scope)* felhasználók átirányítása az Azure Active Directory v2 végpontot eredményez (vagy *acquireTokenPopup(scope)* egy előugró ablak az eredmény) Ha a felhasználók interaktív kell megerősíti a hitelesítő adataikat, engedélyezi a hogy a szükséges erőforrást, vagy befejezése a két kéttényezős hitelesítéssel.

#### <a name="getting-a-user-token-silently"></a>A felhasználói token első beavatkozás nélkül
A ` acquireTokenSilent` metódus kezeli a token beszerzését és -megújítás, felhasználói beavatkozás nélkül. Miután `loginRedirect` (vagy `loginPopup`) hajtja végre az első alkalommal `acquireTokenSilent` hívások kérelem vagy token megújítása csendes módban végzett hívások – a védett erőforrások eléréséhez használt tokenek beszerzése érdekében a gyakran használt módszer.
`acquireTokenSilent` sikertelen lehet, hogy bizonyos esetekben – például a felhasználó jelszava lejárt. Az alkalmazás ehhez a kivételhez, két módon tudják kezelni:

1.  Hívás `acquireTokenRedirect` azonnal, aminek eredményeképpen kéri a felhasználót, hogy jelentkezzen be. Az online alkalmazások gyakran használják ezt a mintát, ha ott nem nem hitelesített tartalmat, az alkalmazás a felhasználó számára elérhető. Az interaktív telepítés által létrehozott példa ezt a mintát.

2. Alkalmazások tudja végrehajtani a vizuális jelzés a felhasználót, hogy egy interaktív bejelentkezési szükség, hogy a felhasználó kiválaszthatja a megfelelő időben való bejelentkezéshez, vagy az alkalmazás megpróbálhatja `acquireTokenSilent` egy későbbi időpontban. Ez általában akkor használatos, amikor a felhasználó használhatja az alkalmazás egyéb funkciók anélkül, hogy szakadhat meg – például nincs nem hitelesített tartalom érhető el az alkalmazást. Ebben az esetben a felhasználó eldöntheti, ha szeretnének bejelentkezni a védett erőforrás elérésére, vagy a frissítés az elavult adatokat.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>A Microsoft Graph API segítségével közvetlenül megszerzett jogkivonattal hívása

Adja hozzá a következő kódot a `app.js` fájlt:

```javascript
/**
 * Call a Web API using an access token.
 * @param {any} endpoint - Web API endpoint
 * @param {any} token - Access token
 * @param {object} responseElement - HTML element used to display the results
 * @param {object} showTokenElement = HTML element used to display the RAW access token
 */
function callWebApiWithToken(endpoint, token, responseElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        console.log(data);
                        responseElement.innerHTML = JSON.stringify(data, null, 4);
                        if (showTokenElement) {
                            showTokenElement.parentElement.classList.remove("hidden");
                            showTokenElement.innerHTML = token;
                        }
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response as error in the page
                        showError(endpoint, data);
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error);
        });
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>További információ a védett API REST-hívást gondoskodik

A jelen útmutató által létrehozott mintaalkalmazásban a `callWebApiWithToken()` , hogy a HTTP módszert `GET` kérelmek ellen védett erőforrás, amely a jogkivonat szükséges, és majd a tartalom térjen vissza a hívónak. Ez a metódus hozzáadja a beszerzett lexikális elem szerepel a *HTTP engedélyeztetési fejléc*. A mintaalkalmazás, ez az útmutató által létrehozott, az erőforrás a Microsoft Graph API *me* végpont – amely a felhasználói profil adatait jeleníti meg.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adjon meg egy metódust a felhasználó kijelentkeztetése

Adja hozzá a következő kódot a `app.js` fájlt:

```javascript
/**
 * Sign out the user
 */
function signOut() {
    userAgentApplication.logout();
}
```
