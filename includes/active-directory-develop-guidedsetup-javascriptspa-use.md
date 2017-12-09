
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>A Microsoft hitelesítési könyvtár (MSAL) segítségével bejelentkezés a felhasználói

1.  Hozzon létre egy fájlt `app.js`. Ha a Visual Studio használ, válassza ki a project (projekt gyökérmappájában), kattintson a jobb gombbal, és válassza: `Add`  >  `New Item`  >  `JavaScript File`:
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

        // Show Sign-Out button
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
 * @param {object} token - The token received from login
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

Miután a felhasználó kattint a *"A Microsoft Graph API hívása"* gomb, először `callGraphApi` metódushívások `loginRedirect` jelentkezhet be a felhasználó. Ez a módszer eredményezi irányít át a felhasználót, hogy a *Microsoft Azure Active Directory v2 végpont* kérni, és a felhasználó hitelesítő adatainak ellenőrzése. Hatására a sikeres bejelentkezés, a felhasználót a rendszer átirányítja az eredetire *index.html* lap, és egy jogkivonat érkezett, által feldolgozott `msal.js` és a rendszer gyorsítótárazza a jogkivonat található információkat. Ez a token is ismert, a *azonosító token* és a felhasználó, például a felhasználó megjelenített neve alapvető információkat tartalmazza. Ha használja a célokat a token által megadott adatokat, győződjön meg arról, hogy ez a token érvényesíti a háttérkiszolgálón garantálja, hogy a jogkivonat részére adták ki egy érvényes felhasználói alkalmazás szeretné.

Ez az útmutató által generált SPA hajtsa végre közvetlenül a Azonosítót jogkivonatban használatát – ehelyett meghívja `acquireTokenSilent` és/vagy `acquireTokenRedirect` megszerzése egy *hozzáférési jogkivonat* kérdezhetők le a Microsoft Graph API-val. Ha egy mintát, amely érvényesíti a azonosító jogkivonatot kell, vessen egy pillantást [ez](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Github active-directory-javascript-singlepageapp-dotnet-webapi-v2 minta") mintaalkalmazást a Githubon – a minta használja az ASP .NET webes API-t a jogkivonatok érvényesség-ellenőrzése.

#### <a name="getting-a-user-token-interactively"></a>A felhasználói token első interaktív módon

Miután a kezdeti bejelentkezés, nem szeretné a kérje meg felhasználók minden alkalommal, amikor kérjen így elért egy erőforrást – tokent kell újból hitelesítésre *acquireTokenSilent* kell használni a legtöbbször ennek tokenek beszerzése. Vannak azonban olyan helyzetekben, amelyekre szüksége van azzal kényszerítheti a felhasználók kommunikálni az Azure Active Directory v2 végpont – például:
-   Felhasználók esetleg adja meg újra a hitelesítő adataikat, mert lejárt a jelszava
-   Az alkalmazás, amelyet a felhasználó járul hozzá az erőforráshoz való hozzáférés
-   Kéttényezős hitelesítés szükség

Hívja a *acquireTokenRedirect(scope)* irányít át felhasználók az Azure Active Directory v2 végpont eredményez (vagy *acquireTokenPopup(scope)* eredménye egy előugró ablak) Ha a felhasználók számára kell a erősítse meg a hitelesítő adataikat, adjon engedélye szükséges erőforrás, vagy befejezése a két kéttényezős hitelesítéssel.

#### <a name="getting-a-user-token-silently"></a>A felhasználói token első beavatkozás nélkül
A ` acquireTokenSilent` metódus kezeli a token kérése és -megújítás felhasználói beavatkozás nélkül. Miután `loginRedirect` (vagy `loginPopup`) végrehajtása az első alkalommal `acquireTokenSilent` az beszerzése a jogkivonatokat, mivel kérelmezése vagy megújítása jogkivonatok hívásainak beavatkozás nélkül történik a további hívások - védett erőforrások eléréséhez használt általánosan használt módszer.
`acquireTokenSilent`sikertelen lehet, hogy bizonyos esetekben – például a jelszó lejárt. Az alkalmazás kezeli ezt a kivételt, két módon:

1.  Hívás `acquireTokenRedirect` azonnal, ennek eredményeképpen a felhasználótól a bejelentkezéshez. Ezt a mintát gyakran használják az online alkalmazások esetén nincs hitelesítve tartalma az alkalmazásban a felhasználó számára elérhető. Ez az interaktív telepítő által létrehozott mintánkban Ez a minta.

2. Alkalmazások is végezhet egy visual arra utal, hogy a felhasználót, hogy egy interaktív bejelentkezés szükség, hogy a felhasználó kiválaszthatja a megfelelő időben való bejelentkezéshez, vagy az alkalmazás újra `acquireTokenSilent` egy későbbi időpontban. Ez általában akkor használatos, amikor a felhasználó éppen megszakad nélkül tudja használni az alkalmazás egyéb funkciók – például nincs hitelesítve tartalom elérhető az alkalmazásban. Ebben az esetben a felhasználó megadhatja, hogy jelentkezzen be a védett erőforrások eléréséhez, vagy frissítse az elavult adatokat szeretne.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>A Microsoft Graph API használatával csak megszerzett jogkivonattal hívható

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>További információ a ellen védett API REST hívás

Ez az útmutató által létrehozott minta alkalmazásban a `callWebApiWithToken()` módszer használható HTTP `GET` kérelem ellen védett erőforrás jogkivonat szükséges, és térjen vissza a tartalom a hívó. Ez a módszer hozzáadja a megszerzett lexikális elem szerepel a *HTTP Authorization fejlécet*. A mintaalkalmazás, ez az útmutató által létrehozott, az erőforrás a Microsoft Graph API *me* végpont – amely a felhasználói profil adatait jeleníti meg.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>A felhasználó kijelentkezik egy olyan metódus hozzáadása

Adja hozzá a következő kódot a `app.js` fájlt:

```javascript
/**
 * Sign-out the user
 */
function signOut() {
    userAgentApplication.logout();
}
```
