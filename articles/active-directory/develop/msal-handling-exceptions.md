---
title: Hibák és kivételek (MSAL) | Azure
description: Ismerje meg, hogyan kezelheti a hibákat és kivételeket, a feltételes hozzáférést és a jogcímeket a MSAL-alkalmazásokban.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: negoe
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 280746281fd45b3286cc76be5d3483f0cc65f90f
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70872802"
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
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: A kérelem törzsének a következő paramétert kell tartalmaznia: "client_secret vagy client_assertion".| Ez a kivétel akkor fordulhat elő, ha az alkalmazás nem nyilvános ügyfélalkalmazásként van regisztrálva az Azure AD-ben. A Azure Portal szerkessze az alkalmazás jegyzékfájlját, és állítsa be a `allowPublicClient` `true`következőt:. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user-üzenet: Nem azonosítható a bejelentkezett felhasználó| A függvénytár nem tudta lekérdezni az aktuálisan bejelentkezett Windows-felhasználót, vagy a felhasználó nem AD vagy HRE csatlakoztatott (a munkahelyhez csatlakoztatott felhasználók nem támogatottak). 1\. megoldás: a UWP győződjön meg arról, hogy az alkalmazás a következő képességekkel rendelkezik: Vállalati hitelesítés, magánhálózat (ügyfél és kiszolgáló), felhasználói fiók adatai. 2\. mérséklés: Implementálja saját logikáját a Felhasználónév beolvasásához (például john@contoso.com), és használja `AcquireTokenByIntegratedWindowsAuth` a felhasználónevet tartalmazó űrlapot.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Ez a módszer Active Directory (AD) által közzétett protokollra támaszkodik. Ha a felhasználó a Azure Active Directory AD-biztonsági mentés nélkül lett létrehozva ("felügyelt" felhasználó), ez a metódus sikertelen lesz. Az AD-ben létrehozott és a HRE által támogatott felhasználók ("összevont" felhasználók) kihasználhatják ezt a nem interaktív hitelesítési módszert. Kockázatcsökkentő Használjon interaktív hitelesítést.|

### `MsalUiRequiredException`

A MSAL.net által visszaadott általános állapotkódok egyike a `AcquireTokenSilent()` hívásakor `MsalError.InvalidGrantError`. Ez az állapotkód azt jelenti, hogy az alkalmazásnak újra meg kell hívnia a hitelesítési könyvtárat, de interaktív módban (AcquireTokenInteractive vagy AcquireTokenByDeviceCodeFlow a nyilvános ügyfélalkalmazások számára, és el kell végeznie egy kihívást a web Appsben). Ennek az az oka, hogy a hitelesítési jogkivonat kiállítása előtt további felhasználói beavatkozásra van szükség.

Az idő nagy részében `AcquireTokenSilent` , amikor a művelet meghiúsul, az azért van, mert a jogkivonat-gyorsítótár nem rendelkezik a kérelemnek megfelelő jogkivonatokkal. A hozzáférési tokenek 1 órán belül lejárnak, `AcquireTokenSilent` és egy frissítési jogkivonat alapján megpróbálnak beolvasni egy újat (OAuth2 kifejezésekben ez a "refresh token" folyamat). Ez a folyamat több okból is meghiúsulhat, például ha a bérlői rendszergazda szigorúbb bejelentkezési házirendeket konfigurál. 

A beavatkozás célja, hogy a felhasználó műveletet hajtson végre. Bizonyos feltételek egyszerűen feloldhatók a felhasználók számára (például elfogadják a használati feltételeket egyetlen kattintással), és néhány nem oldható fel a jelenlegi konfigurációval (például a szóban forgó gépnek egy adott vállalati hálózathoz kell csatlakoznia). Némi segítség a felhasználónak a többtényezős hitelesítés beállításában, vagy a Microsoft Authenticator telepítését az eszközön.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`besorolások enumerálása

A MSAL egy olyan `Classification` mezőt tesz elérhetővé, amely lehetővé teszi, hogy jobb felhasználói élményt nyújtson, például hogy tájékoztassa a felhasználót arról, hogy a jelszava lejárt, vagy hogy bizonyos erőforrások használatához meg kell adnia az engedélyt. A támogatott értékek az `UiRequiredExceptionClassification` enumerálás részét képezik:

| Besorolás    | Jelentés           | Ajánlott kezelési |
|-------------------|-------------------|----------------------|
| BasicAction | A feltételt a felhasználói interakció feloldható az interaktív hitelesítési folyamat során. | A AcquireTokenInteractively () hívása. |
| AdditionalAction | A feltételt az interaktív hitelesítési folyamaton kívül további javító interakciók is feloldhatók a rendszerrel. | Hívja meg a AcquireTokenInteractively () egy olyan üzenet megjelenítéséhez, amely ismerteti a javító műveletet. A hívó alkalmazás dönthet úgy, hogy elrejti a additional_action igénylő folyamatokat, ha a felhasználó nem valószínű, hogy elvégzi a javító műveletet. |
| MessageOnly      | A feltétel jelenleg nem oldható fel. Az interaktív hitelesítési folyamat elindításakor megjelenik egy üzenet, amely ismerteti a feltételt. | A AcquireTokenInteractively () hívásával megjeleníthető egy üzenet, amely ismerteti a feltételt. A AcquireTokenInteractively () UserCanceled-hibát ad vissza, miután a felhasználó beolvasta az üzenetet, és bezárja az ablakot. A hívó alkalmazás dönthet úgy, hogy elrejti a message_only eredményező folyamatokat, ha a felhasználó nem valószínű, hogy kihasználja az üzenetet.|
| ConsentRequired  | A felhasználói beleegyezés hiányzik vagy vissza lett vonva. | A AcquireTokenInteractively () hívásával adja meg a felhasználót a beleegyezés megadásához. |
| UserPasswordExpired | A felhasználó jelszava lejárt. | Hívja meg a AcquireTokenInteractively (), hogy a felhasználó alaphelyzetbe állíthatja a jelszavát. |
| PromptNeverFailed| Az interaktív hitelesítés a következő paraméterrel lett meghívva: prompt = soha, kényszerítve a MSAL, hogy a böngésző cookie-kra támaszkodjon, és ne jelenjen meg a böngésző. Ez nem sikerült. | A AcquireTokenInteractively () hívása prompt nélkül. None |
| AcquireTokenSilentFailed | A MSAL SDK nem rendelkezik elegendő információval a tokennek a gyorsítótárból való beolvasásához. Ennek oka az lehet, hogy a gyorsítótárban nincsenek tokenek, vagy nem található a fiók. A hibaüzenet további részleteket tartalmaz.  | A AcquireTokenInteractively () hívása. |
| Nincsenek    | További részletek nincsenek megadva. A feltételt a felhasználói interakció feloldható az interaktív hitelesítési folyamat során. | A AcquireTokenInteractively () hívása. |

## <a name="code-example"></a>Mintakód

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
   res = await application.AcquireTokenInteractive(scopes)
                          .ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```


## <a name="javascript-errors"></a>JavaScript-hibák

A MSAL. js olyan hibaüzeneteket biztosít, amelyek absztrakt és osztályozzák a gyakori hibák különböző típusait. Emellett felületet biztosít a hibák konkrét részleteinek eléréséhez, például a hibaüzenetek megfelelő kezeléséhez.

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
* **AuthError. Message:**  Ugyanaz, mint a errorMessage.
* **AuthError. stack:** Az eldobott hibák verem-nyomkövetése. A nyomkövetés kiindulási pontja a hiba.

**Hibák típusai**

A következő típusú hibák érhetők el:

* *AuthError:* A MSAL. js függvénytár alapszintű hiba osztálya váratlan hibákhoz is használatos.

* *ClientAuthError:* Hiba osztály, amely az ügyfél-hitelesítéssel kapcsolatos problémát jelöli. A könyvtárból érkező hibák többsége ClientAuthErrors lesz. Ezek a hibák olyanok, mint a bejelentkezési módszer meghívása, amikor a bejelentkezés folyamatban van, a felhasználók pedig megszakítják a bejelentkezést és így tovább. 

* *ClientConfigurationError:* Hiba osztály: a ClientAuthError kibővítése a kérések előtt, amikor a megadott felhasználói konfigurációs paraméterek hibásak vagy hiányoznak.

* *ServerError* A hitelesítési kiszolgáló által küldött hibaüzeneteket jelölő hiba osztálya. Ezek lehetnek olyan hibák, mint például az érvénytelen kérelmek formátuma vagy paraméterei, vagy bármilyen más hiba, amely megakadályozza, hogy a kiszolgáló hitelesítse vagy engedélyezze a felhasználót.

* *InteractionRequiredAuthError:* A ServerError kiterjesztése a kiszolgálói hibákra, amelyek interaktív hívást igényelnek. Ez a hiba akkor fordul `acquireTokenSilent` elő, ha a felhasználónak a kiszolgálóval való interakcióra van szüksége a hitelesítő adatok vagy a hitelesítés/engedélyezés jóváhagyása érdekében. A hibakódok közé tartoznak a következők: "interaction_required", "login_required", "consent_required".

A hitelesítési folyamatokban a (z) átirányítási`loginRedirect`módszerekkel (, `acquireTokenRedirect`) való hibakezelés érdekében regisztrálnia kell a visszahívást, amely sikeres vagy sikertelen volt, `handleRedirectCallback()` miután az átirányítás a következő módszer használatával történik:

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

A rendszer hibát ad vissza, ha nem interaktív módszert használ a jogkivonat beszerzésére (például `acquireTokenSilent`), és a MSAL nem tudta csendesen megtenni.

A lehetséges okok a következők:

* a felhasználónak be kell jelentkeznie
* a felhasználónak hozzá kell járulnia
* a felhasználónak át kell haladnia a multi-Factor Authentication szolgáltatással.

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
A tokenek csendes lekérése esetén előfordulhat, hogy az alkalmazás hibát jelez, ha egy olyan API-val kapcsolatos [feltételes hozzáférési jogcímet](conditional-access-dev-guide.md) igényel, mint például az MFA-szabályzat.

Ennek a hibának a kezelésére szolgáló minta a jogkivonat interaktív beszerzése a MSAL használatával. A jogkivonat interaktív beszerzése arra kéri a felhasználót, hogy adja meg a szükséges feltételes hozzáférési szabályzatot.

Bizonyos esetekben, amikor feltételes hozzáférést igénylő API-t hív meg, az API-val kapcsolatos hiba esetén a jogcím-kihívás is megjelenhet. Ha például a feltételes hozzáférési házirend felügyelt eszközzel (Intune) rendelkezik, a hiba a következőhöz hasonló [lesz: AADSTS53000: Az eszköz számára szükséges az erőforráshoz](reference-aadsts-error-codes.md) való hozzáférés, vagy valami hasonló. Ebben az esetben átadhatja a jogcímeket a beszerzési jogkivonat hívásában, hogy a rendszer kérje a felhasználótól a megfelelő szabályzat teljesítését.

### <a name="net"></a>.NET
Ha a MSAL.NET feltételes hozzáférést igénylő API-t hív meg, az alkalmazásnak kezelnie kell a jogcímek kivételeit. Ez olyan [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) fog megjelenni, ahol a [jogcím](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) tulajdonság nem üres.

A jogcím-kihívás kezeléséhez az `.WithClaim()` `PublicClientApplicationBuilder` osztály metódusát kell használnia.

### <a name="javascript"></a>JavaScript
Ha a tokeneket a MSAL. js `acquireTokenSilent`használatával csendesen (a használatával) kérdezi le, az alkalmazás hibákat kaphat, ha egy olyan API-nak szüksége van egy [feltételes hozzáférési jogcímek feladatára](conditional-access-dev-guide.md) , mint például az MFA-szabályzat.

A hiba kezelésére szolgáló minta egy interaktív hívást tesz lehetővé a token MSAL. js-ben való beszerzéséhez `acquireTokenPopup` , `acquireTokenRedirect` például a következő példában vagy a-ben:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

A jogkivonat interaktív beszerzése kéri a felhasználót, és lehetőséget biztosít számukra a kötelező feltételes hozzáférési szabályzat kielégítésére.

Ha feltételes hozzáférést igénylő API-t hív meg, az API-val kapcsolatos hiba esetén a jogcímek kérdését is megkaphatja. Ebben az esetben átadhatja a hibában `claimsRequest` visszaadott jogcímeket az `AuthenticationParameters.ts` osztály mezőjébe, hogy az megfeleljen a megfelelő házirendnek. 

További részletekért lásd: [további jogcímek igénylése](active-directory-optional-claims.md) .

## <a name="retrying-after-errors-and-exceptions"></a>Próbálkozás a hibák és kivételek után

A MSAL meghívásakor várhatóan saját újrapróbálkozási szabályzatokat kell megvalósítani. A MSAL HTTP-hívásokat kezdeményez a HRE szolgáltatáshoz, és alkalmanként hibák léphetnek fel, például a hálózat leállhat, vagy a kiszolgáló túlterhelt.  

### <a name="http-error-codes-500-600"></a>HTTP-hibakódok 500-600

A MSAL.NET egy egyszerű újrapróbálkozási mechanizmust valósít meg a 500-600-es HTTP-hibakódok miatti hibák esetén.

### <a name="http-429"></a>HTTP 429

Ha a szolgáltatás-jogkivonat-kiszolgáló (STS) túl sok kéréssel túl van terhelve, a 429 HTTP-hibát ad vissza, amely arra utal, hogy mikor próbálkozzon újra az idő függvényében. A hiba a `Retry-After` válasz mezőből olvasható.

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
