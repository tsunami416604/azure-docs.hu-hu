---
title: Hibák és kivételek (MSAL) | Azure
description: Ismerje meg, hogyan kezelheti a hibákat és kivételeket, a feltételes hozzáférést és a jogcímeket a MSAL-alkalmazásokban.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c294e3bd8ac04454c2d715c665e0da4f9a4f4535
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835021"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Kivételek és hibák kezelése a MSAL használatával
A Microsoft Authentication Library (MSAL) kivételei az alkalmazások fejlesztői számára készültek a hibák megoldásához, és nem a végfelhasználók számára történő megjelenítéshez. A kivételek üzenetei nincsenek honosítva.

A kivételek és hibák feldolgozásakor használhatja a kivétel típusát és a hibakódot a kivételek megkülönböztetése érdekében.  A hibakódok listáját lásd: [hitelesítési és engedélyezési hibakódok](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>.NET-kivételek
Kivételek feldolgozásakor használhatja a kivétel típusát és a `ErrorCode` tagot a kivételek közötti különbségtételhez. Az értékek `ErrorCode` a [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet)típusú állandók.

Megtekintheti a [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), a [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)és a [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet)mezőket is.

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) esetén a hibakód tartalmazhat egy kódot, amely a [hitelesítési és engedélyezési hibakódokban](reference-aadsts-error-codes.md)található.

### <a name="common-exceptions"></a>Gyakori kivételek
Itt láthatók azok a gyakori kivételek, amelyek felmerülhetnek, és néhány lehetséges megoldást is felhasználhat.

| Kivétel | Hibakód | Kezelés|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: A felhasználó vagy a rendszergazda nem egyezett bele a (z) {appName} nevű, {appId} AZONOSÍTÓJÚ alkalmazás használatára. Interaktív engedélyezési kérelem küldése ehhez a felhasználóhoz és erőforráshoz.| Először be kell szereznie a felhasználói beleegyező engedélyt. Ha nem a .NET Core-ot használja (amely nem rendelkezik webes felhasználói felülettel), akkor a (csak `AcquireTokeninteractive`egyszer) hívást hívja meg. Ha a .net Core-ot használja `AcquireTokenInteractive`, vagy nem szeretné elvégezni a használatát, a felhasználó megkeresheti az URL-címet a beleegyezés megadásához:. https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read Hívás `AcquireTokenInteractive`:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: A többtényezős hitelesítés használatához a felhasználónak kell lennie.| Nincs megoldás, ha az MFA konfigurálva van a bérlőhöz, és a HRE úgy dönt, hogy kikényszeríti azt, egy interaktív folyamatra, például `AcquireTokenInteractive` vagy `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: A támogatás típusa nem támogatott a */gyakori hibák* -vagy */consumers* -végpontokon. Használja a */Organizations* vagy a bérlő-specifikus végpontot. A */gyakori hibák*használta.| Ahogy az az Azure AD-ban is szerepel, a szolgáltatónak Bérlővel vagy egyéb */Organizations*kell rendelkeznie.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: A kérelem törzsének a következő paramétert kell tartalmaznia: "client_secret vagy client_assertion".| Ez akkor fordulhat elő, ha az alkalmazás nincs nyilvános ügyfélalkalmazásként regisztrálva az Azure AD-ben. A Azure Portal szerkessze az alkalmazás jegyzékfájlját, és állítsa be a `allowPublicClient` `true`következőt:. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user-üzenet: Nem azonosítható a bejelentkezett felhasználó| A függvénytár nem tudta lekérdezni az aktuálisan bejelentkezett Windows-felhasználót, vagy a felhasználó nem AD vagy HRE csatlakoztatott (a munkahelyhez csatlakoztatott felhasználók nem támogatottak). 1\. megoldás: a UWP győződjön meg arról, hogy az alkalmazás a következő képességekkel rendelkezik: Vállalati hitelesítés, magánhálózat (ügyfél és kiszolgáló), felhasználói fiók adatai. 2\. mérséklés: Implementálja saját logikáját a Felhasználónév beolvasásához (például john@contoso.com), és használja `AcquireTokenByIntegratedWindowsAuth` a felhasználónevet tartalmazó űrlapot.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Ez a módszer Active Directory (AD) által közzétett protokollra támaszkodik. Ha a felhasználó a Azure Active Directory AD-biztonsági mentés nélkül lett létrehozva ("felügyelt" felhasználó), ez a metódus sikertelen lesz. Az AD-ben létrehozott és a HRE által támogatott felhasználók ("összevont" felhasználók) kihasználhatják ezt a nem interaktív hitelesítési módszert. Kockázatcsökkentő Használjon interaktív hitelesítést.|

## <a name="javascript-errors"></a>JavaScript-hibák

A MSAL. js olyan hibaüzeneteket biztosít, amelyek absztrakt és osztályozzák a gyakori hibák különböző típusait, és rendelkeznek egy felülettel a hibák adott részleteinek eléréséhez, például a hibaüzenetek megfelelő kezeléséhez.

**Hiba objektum**

```javascript                                
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```                
A hiba osztály kibővítésével a következő tulajdonságokat érheti el:
* **AuthError. Message:** Ez ugyanaz, mint a errorMessage.
* **AuthError. stack:** Az eldobott hibák verem-nyomkövetése. A nyomkövetés kiindulási pontja a hiba.

**Hibák típusai**

A következő típusú hibák érhetők el:

* *AuthError:* A MSAL. js függvénytár alapszintű hiba osztálya váratlan hibákhoz is használatos.

* *ClientAuthError:* A hiba osztály, amely az ügyfél-hitelesítéssel kapcsolatos problémát jelöli. A könyvtárból érkező hibák többsége ClientAuthErrors lesz. Ezek olyan hibák lehetnek, mint például a bejelentkezési módszer meghívása, amikor a bejelentkezés folyamatban van, a felhasználók pedig megszakítják a bejelentkezést stb.

* *ClientConfigurationError:* Hiba osztály: a ClientAuthError kibővítése a kérések előtt, amikor a megadott felhasználói konfigurációs paraméterek hibásak vagy hiányoznak.

* *ServerError* A hitelesítési kiszolgáló által küldött hibaüzeneteket jelölő hiba osztálya. Ezek lehetnek olyan hibák, mint például az érvénytelen kérelmek formátuma vagy paraméterei, vagy bármilyen más hiba, amely megakadályozza, hogy a kiszolgáló hitelesítse vagy engedélyezze a felhasználót.

* *InteractionRequiredAuthError:* A hiba osztálya a ServerError kiterjesztésével olyan kiszolgálói hibákat jelöl, amelyek interaktív hívást igényelnek. Ez akkor fordul elő `acquireTokenSilent` , ha a felhasználónak a kiszolgálóval való interakcióra van szüksége a hitelesítő adatok vagy a hitelesítés/engedélyezés jóváhagyása érdekében. A hibakódok közé tartoznak a következők: "interaction_required", "login_required", "consent_required".

A hitelesítési folyamatokban az átirányítási módszerekkel (`loginRedirect`, `acquireTokenRedirect`) való hibakezelés érdekében regisztrálnia kell a visszahívást, amely sikeres vagy `handleRedirectCallback()` sikertelen volt az átirányítás után, a következő módon:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

Az előugró élmény (`loginPopup`, `acquireTokenPopup`) metódusok visszaküldési lehetőséget biztosítanak, így a megígért minta (... és. Catch) segítségével a következőképpen kezelheti őket:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Interakció szükséges hibák

A rendszer hibát ad vissza, ha felhasználói felületi interakcióra van szükség. Ez azt jelenti, hogy nem interaktív módszert próbált használni a token beszerzéséhez (például `acquireTokenSilent`), de a MSAL nem lehetett csendesen megtenni. A lehetséges okok a következők:

* be kell jelentkeznie
* meg kell egyeznie
* egy többtényezős hitelesítési felülettel kell eljárnia.

A szervizelés olyan interaktív módszer `acquireTokenPopup` meghívása, mint a vagy: `acquireTokenRedirect`

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="conditional-access-and-claims-challenges"></a>A feltételes hozzáférés és a jogcímek kihívásai
Ha a tokeneket csendes módban kéri le, előfordulhat, hogy az alkalmazás hibát jelez, ha egy olyan API-ra van szükség, amely egy olyan [feltételes hozzáférési](conditional-access-dev-guide.md) jogcímet igényel, mint például az MFA-szabályzat.

Ennek a hibának a kezelésére szolgáló minta a jogkivonat interaktív beszerzése a MSAL használatával. A jogkivonat interaktív beszerzése arra kéri a felhasználót, hogy adja meg a szükséges feltételes hozzáférési szabályzatot.

Bizonyos esetekben, amikor feltételes hozzáférést igénylő API-t hív meg, az API-val kapcsolatos hiba esetén a jogcím-kihívás is megjelenhet. Ha például a feltételes hozzáférési házirend felügyelt eszközzel (Intune) rendelkezik, a hiba a következőhöz hasonló [lesz: AADSTS53000: Az eszköz számára szükséges az erőforráshoz](reference-aadsts-error-codes.md) való hozzáférés, vagy valami hasonló. Ebben az esetben átadhatja a jogcímeket a beszerzési jogkivonat hívásában, hogy a rendszer kérje a felhasználótól a megfelelő szabályzat teljesítését.

### <a name="net"></a>.NET
Ha a MSAL.NET feltételes hozzáférést igénylő API-t hív meg, az alkalmazásnak kezelnie kell a jogcímek kivételeit. Ez olyan [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) fog megjelenni, ahol a [jogcím](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) tulajdonság nem üres.

A jogcím-kihívás kezeléséhez az `.WithClaim()` `PublicClientApplicationBuilder` osztály metódusát kell használnia.

### <a name="javascript"></a>JavaScript
Ha a tokeneket a MSAL. js `acquireTokenSilent`használatával csendesen (a használatával) kérdezi le, az alkalmazás hibákat kaphat, ha egy olyan API-nak szüksége van egy [feltételes hozzáférési](conditional-access-dev-guide.md) jogcímek feladatára, mint például az MFA-szabályzat.

A hiba kezelésére szolgáló minta egy interaktív hívást tesz lehetővé a token MSAL. js-ben való beszerzéséhez `acquireTokenPopup` , `acquireTokenRedirect` például a következő példában vagy a-ben:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    myMSALObj.acquireTokenPopup(accessTokenRequest).then(
        function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
});
```

A jogkivonat interaktív beszerzése kéri a felhasználót, és lehetőséget biztosít számukra a kötelező feltételes hozzáférési szabályzat kielégítésére.

Ha feltételes hozzáférést igénylő API-t hív meg, az API-val kapcsolatos hiba esetén a jogcímek kérdését is megkaphatja. Ebben az esetben átadhatja a hibában visszaadott jogcímeket `extraQueryParameters` a jogkivonatok beszerzésére irányuló hívásban, hogy a felhasználó a megfelelő szabályzat teljesítését kérje:

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>Próbálkozás a hibák és kivételek után

### <a name="http-error-codes-500-600"></a>HTTP-hibakódok 500-600
A MSAL.NET egy egyszerű újrapróbálkozási mechanizmust valósít meg a 500-600-es HTTP-hibakódok miatti hibák esetén.

### <a name="http-429"></a>HTTP 429
Ha a szolgáltatás-jogkivonat-kiszolgáló (STS) túl elfoglalt a "túl sok kérelem" miatt, a 429 HTTP-hibát ad vissza, amely arra utal, hogy mikor próbálkozzon újra (az újrapróbálkozási válasz mező) a másodpercben vagy egy dátumban.

#### <a name="net"></a>.NET
A [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) -kivételi felületek `System.Net.Http.Headers.HttpResponseHeaders` tulajdonságként `namedHeaders`jelennek meg. Ezért további információkat is kihasználhat a hibakódból az alkalmazások megbízhatóságának növelése érdekében. Abban az esetben, ha az imént leírtuk, a `RetryAfterproperty` (típus `RetryConditionHeaderValue`) és a számítási kapacitás használatával próbálkozzon újra.

Íme egy példa egy Daemon-alkalmazásra (az ügyfél hitelesítő adatait tartalmazó folyamat használatával), de a tokent beszerző módszerek bármelyikéhez alkalmazkodhat.

```csharp
do
{
 retry = false;
 TimeSpan? delay;
 try
 {
  result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                        .ExecuteAsync();
 }
 catch (MsalServiceException serviceException)
 {
  if (ex.ErrorCode == "temporarily_unavailable")
  {
   RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
   if (retryAfter.Delta.HasValue)
   {
    delay = retryAfter.Delta;
   }
   else if (retryAfter.Date.HasValue)
   {
    delay = retryAfter.Date.Value.Offset;
   }
  }
 }

    …
 if (delay.HasValue)
 {
  Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
  retry = true;
 }
} while (retry);
```
