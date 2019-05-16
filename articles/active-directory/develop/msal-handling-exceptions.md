---
title: Hibák és kivételek (MSAL) |} Az Azure
description: Megtudhatja, hogyan kezelje a hibákat és kivételeket, feltételes hozzáférés és a jogcímek kihívást jelentenek az MSAL alkalmazásokban.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: f1972a870ac15e1ca8dde963eef6cf7f1caf3039
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544186"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Kivételek és hibák az MSAL használatával
A Microsoft hitelesítési tár (MSAL) kivételek hibaelhárítása alkalmazásfejlesztők számára, és nem a végfelhasználók számára való megjelenítése, készültek. Nem honosított kivétel üzeneteket.

Kivételek és hibák feldolgozásakor a kivétel típusát maga és a hiba kódja segítségével kivételek között.  Hibakódok listáját lásd: [hitelesítési és engedélyezési hibakódok](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>.NET exceptions
Kivételek feldolgozásakor a kivétel típusát maga is használhat, és a `ErrorCode` tag különbséget tenni a kivételek között. Értékeit `ErrorCode` állandók típusú [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet#fields).

Tekintse meg a mezőket az is lehet [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet#fields), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields), [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet#fields).

Ha [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) lépett fel, a hiba kódja tartalmazhat egy kódot, amely találhatja meg [hitelesítési és engedélyezési hibakódok](reference-aadsts-error-codes.md).

### <a name="common-exceptions"></a>Gyakori kivételek
Íme a gyakori kivételek, előfordulhat, hogy hibajelzést és néhány lehetséges megoldások.

| Kivétel | Hibakód | Kockázatcsökkentés|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: A felhasználó vagy a rendszergazda nem egyezett bele az alkalmazás használata a(z) {appName} nevű {appId} Azonosítójú. Küldjön egy interaktív engedélyezési kérést ehhez a felhasználói és erőforráshoz.| Felhasználói beleegyezés első első kell. Ha nem használ a .NET Core (amely nem rendelkezik minden olyan webes felhasználói Felületet), hívja meg (csak egyszer) `AcquireTokeninteractive`. Ha a .NET Core keretrendszert használ, vagy nem szeretné egy `AcquireTokenInteractive`, a felhasználói beleegyezés URL-címre navigálhat: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read . A hívás `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: A felhasználó a multi-factor authentication használata szükséges.| Van nincs megoldás – Ha a többtényezős hitelesítés van konfigurálva a bérlő számára és AAD úgy dönt, kényszeríteni kell, hogy egy interaktív folyamat tartalék például `AcquireTokenInteractive` vagy `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields) |AADSTS90010: Az engedélyezési típus használatával nem támogatott a */közös* vagy */consumers* végpontok. Használja a */organizations* vagy bérlőspecifikus végpont. Használt */közös*.| Amint azt az üzenetet az Azure ad-ből, a szolgáltató rendelkeznie kell egy bérlő vagy egyéb jellegű */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields) | AADSTS70002: A kérelem törzsének tartalmaznia kell a következő paraméter: "titkos ügyfélkódot vagy client_assertion".| Ez akkor fordulhat elő, ha az alkalmazás nem lett regisztrálva az Azure AD-alkalmazás nyilvános ügyfél. Az Azure Portalon szerkesztheti a jegyzékfájl az alkalmazás és a készlet a `allowPublicClient` való `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user üzenet: Nem sikerült azonosítani a bejelentkezett felhasználó| A könyvtár nem tudta lekérdezni a jelenlegi Windows bejelentkezett felhasználó, vagy a felhasználó nem AD vagy AAD-hez csatlakoztatott (munkahelyi csatlakozó felhasználók nem támogatottak). 1. megoldás: az UWP, ellenőrizze, hogy az alkalmazás a következő képességekkel rendelkezik: Vállalati hitelesítési, a magánhálózatok (ügyfél és kiszolgáló), a felhasználói fiók adatait. 2. megoldás: A saját logikát beolvasni a felhasználónevet (például john@contoso.com), és használja a `AcquireTokenByIntegratedWindowsAuth` képernyő, amely a felhasználónév.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Ez a módszer egy protokoll által az Active Directory (AD) elérhetővé tett támaszkodik. Ha a felhasználó nem AD biztonsági ("kezelt" felhasználó) lett létrehozva az Azure Active Directoryban, ez a metódus sikertelen lesz. Felhasználók AD-ben létrehozott, valamint az aad-ben ("összevont" felhasználók) által támogatott hitelesítési módszer, ez nem interaktív is kihasználhatják. Megoldás: Interaktív hitelesítés használata.|

## <a name="javascript-errors"></a>JavaScript-hibák

MSAL.js hiba objektumokat absztrakt és a gyakori hibák a különböző típusú besorolása és megfelelően kezelni a hibaüzenetekhez hasonló hibák részletes eléréséhez kezelőfelületet biztosít.

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
A hiba osztály kiterjesztésével rendelkeznek hozzáféréssel a következő tulajdonságokkal:
* **AuthError.message:** Ez a hibaüzenet megegyezik.
* **AuthError.stack:** A fellépő hibákat trasování zásobníku. Lehetővé teszi, hogy a hiba a kezdőpont követést.

**Alkalmazáshiba-típusok**

A következő alkalmazáshiba-típusok érhetők el:

* *AuthError:* Alap hiba osztály a MSAL.js erőforrástár, váratlan hibák esetében is alkalmazható.

* *ClientAuthError:* Hiba osztály, amely azt jelzi, hogy az ügyfél-hitelesítési probléma. A legtöbb hiba a tár származó ClientAuthErrors lesz. Ezek lehetnek hibákat, például egy bejelentkezési metódus hívása, ha folyamatban van, a felhasználók bejelentkezés vagy egyebek megszakítása.

* *ClientConfigurationError:* Hiba az osztály kiterjesztése ClientAuthError megelőzően a kérések mikor lépett fel a megadott felhasználói konfigurációs paraméterek helytelen formátumú vagy hiányzik.

* *Kiszolgálóhibái:* Hiba az osztály a hiba a karakterláncokat, a hitelesítési kiszolgáló által küldött képviselő. Ezek hibákat, például a kérés érvénytelen formátumú vagy paramétereket, vagy más hibák, amelyek megakadályozzák a kiszolgáló hitelesítése és engedélyezése a felhasználó lehet.

* *InteractionRequiredAuthError:* Hiba osztály kiterjesztése, amelyek egy interaktív hívás igénylő Kiszolgálóhibák Kiszolgálóhibái. Ez a rendszer által kiváltott `acquireTokenSilent` Ha a felhasználó kell létesíteni a kiszolgálóval, adja meg hitelesítő adatait, vagy a hitelesítés/engedélyezés vonatkozó beleegyezés. Hibakódok "interaction_required", "login_required", "consent_required" közé tartozik.

A hibakezelés hitelesítési folyamatok és az átirányítási módszert (`loginRedirect`, `acquireTokenRedirect`), a visszahívás nevezett regisztrálás sikeres vagy sikertelen az átirányítás használata után kell `handleRedirectCallback()` módszert az alábbiak szerint:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

Az előugró élmény módszereit (`loginPopup`, `acquireTokenPopup`) adja vissza az ígéretek, így használhatja a promise minta (helyőrzőkre és .catch) látható módon kezelni őket:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Szükséges kapcsolati hibák

Hibát ad vissza, ha a felhasználói felületre vonatkozó interakciót szükség. Ez azt jelenti, hogy egy token beszerzése a nem interaktív módszerhez próbált (például `acquireTokenSilent`), de MSAL nem tudott azt csendes módban. Lehetséges okai a következők:

* be kell jelentkeznie
* jóváhagyást kell
* haladjon végig a multi-factor authentication felhasználói élményt kell.

A szervizelés például egy interaktív metódus meghívására `acquireTokenPopup` vagy `acquireTokenRedirect`:

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

## <a name="conditional-access-and-claims-challenges"></a>Feltételes hozzáférés és a jogcímek kihívásai
Jogkivonatok lekérésének lépéseiről a háttérben, amikor az alkalmazás hibaüzeneteket amikor egy [feltételes hozzáférési jogcímek challenge](conditional-access-dev-guide.md) például a többtényezős hitelesítési szabályzat API-k által igényelt próbál hozzáférni.

A minta kezelni ezt a hibát az interaktív módon az MSAL használatával jogkivonat beszerzése. Interaktív módon tokenbeolvasás kéri a felhasználót, és megfelelnek a feltételes hozzáférési szabályzat lehetőséget biztosít.

Bizonyos esetekben a feltételes hozzáférést igénylő API hívásakor a hiba a jogcímek kihívást fogadhat az API-ból. Példány, ha a feltételes hozzáférési szabályzat az, hogy a felügyelt eszközök (Intune) a hibaüzenet hasonló lesznek [AADSTS53000: Az eszköz nem kezelhető, az erőforrás eléréséhez szükséges](reference-aadsts-error-codes.md) vagy hasonló karakter. Ebben az esetben kéri a felhasználót, hogy megfelelnek a megfelelő házirendet, hogy a jogcímek jogkivonat beolvasási hívásában adhat át.

### <a name="net"></a>.NET
Az MSAL.NET feltételes hozzáférést igénylő API hívásakor az alkalmazás jogcím challenge kivételek kezelnie kell. Ez fog megjelenni az [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) ahol a [jogcímek](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) tulajdonság nem lehet üres.

A jogcím kihívás kezelése érdekében meg kell használnia a `.WithClaim()` módszere a `PublicClientApplicationBuilder` osztály.

### <a name="javascript"></a>JavaScript
Csendes jogkivonatok beolvasásakor (használatával `acquireTokenSilent`) MSAL.js használatával, az alkalmazás hibák léphetnek fel amikor egy [feltételes hozzáférési jogcímek challenge](conditional-access-dev-guide.md) például a többtényezős hitelesítési szabályzat API-k által igényelt próbál hozzáférni.

A minta kezelni ezt a hibát, hogy az MSAL.js token beszerzéséhez például interaktív hívás `acquireTokenPopup` vagy `acquireTokenRedirect` az alábbi példában látható módon:

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

A token beszerzése a interaktív módon kéri a felhasználót, és megfelelnek a feltételes hozzáférési szabályzat lehetőséget biztosít.

Feltételes hozzáférést igénylő API hívásakor a jogcímek kihívást a hibát az API-ból is kap. Ebben az esetben a hibát, mert visszaküldött jogcímek átadható `extraQueryParameters` a tokenek beszerzésére, hogy a megfelelő házirendet kielégítéséhez kéri a felhasználót, a hívást:

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>Újrapróbálkozás után hibák és kivételek

### <a name="http-error-codes-500-600"></a>A hibakódjai HTTP 500-600
MSAL.NET valósít meg egy egyszerű újra – miután a hibákat HTTP hiba mechanizmus kódjai 500 – 600.

### <a name="http-429"></a>HTTP 429
Ha "túl sok kérelem" miatt a szolgáltatás Token kiszolgáló (STS) túlságosan elfoglalt, hibát ad vissza HTTP 429-es kapcsolatban, amikor is a mutatóval próbálja meg újra (Retry-After válasz mező), a késés másodpercben vagy egy dátumot.

#### <a name="net"></a>.NET
A [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) kivétel felületek `System.Net.Http.Headers.HttpResponseHeaders` tulajdonságként `namedHeaders`. Így kihasználhatja az hibakódot az alkalmazások megbízhatóságának javítása az adatokat. Abban az esetben az imént ismertetett, használhatja a `RetryAfterproperty` (típusú `RetryConditionHeaderValue`) és számítási mikor próbálkozzon újra.

Íme egy példa egy démon alkalmazáshoz (az ügyfélhitelesítő adatok folyamata segítségével), de meg, hogy a módszerekkel, egy token beszerzése sikerült alkalmazkodáshoz.

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
