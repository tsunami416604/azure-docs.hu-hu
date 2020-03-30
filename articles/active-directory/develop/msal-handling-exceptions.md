---
title: Hibák és kivételek (MSAL)
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan kezelhetők a hibák és kivételek, a feltételes hozzáférés és a jogcímekkel kapcsolatos kihívások az MSAL-alkalmazásokban.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/22/2019
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: f78c64fc0ba25dc3310b24e873dbae266ea2f281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050322"
---
# <a name="handle-msal-exceptions-and-errors"></a>MSAL-kivételek és hibák kezelése

Ez a cikk áttekintést nyújt a különböző típusú hibákról és javaslatokat a gyakori bejelentkezési hibák kezelésére.

## <a name="msal-error-handling-basics"></a>Az MSAL hibakezelési alapjai

A Microsoft Authentication Library (MSAL) kivételei az alkalmazásfejlesztők számára a hibaelhárításra szolgálnak – nem a végfelhasználók számára történő megjelenítésre. A kivételüzenetek nincsenek honosítva.

Kivételek és hibák feldolgozásakor használhatja magát a kivételtípust és a hibakódot a kivételek megkülönböztetéséhez.  A hibakódok listáját a [Hitelesítési és engedélyezési hibakódok című témakörben tetszésszerint.](reference-aadsts-error-codes.md)

A bejelentkezési élmény során hibákléphetnek fel a beleegyezésekkel, a feltételes hozzáféréssel (MFA, az eszközkezelés, a helyalapú korlátozásokkal), a jogkivonat-kiállítással és -beváltással, valamint a felhasználói tulajdonságokkal kapcsolatban.

Az alkalmazás hibakezelésével kapcsolatos további részletekért tekintse meg a használt nyelvnek megfelelő alábbi szakaszt.

## <a name="net"></a>[.NET](#tab/dotnet)

A .NET kivételek feldolgozásakor magát a kivételtípust és a tagot használhatja a `ErrorCode` kivételek megkülönböztetésére. `ErrorCode`értékek [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet)típusú állandók.

Az [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), A [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)és az [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet)mezőket is megnézheti.

Ha [a MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) megjelenik, próbálja meg [a Hitelesítési és engedélyezési hibakódokat,](reference-aadsts-error-codes.md) és nézze meg, hogy a kód ott szerepel-e.

### <a name="common-net-exceptions"></a>Gyakori .NET kivételek

Az alábbiakban a gyakori kivételeket és néhány lehetséges megoldást olvashat:  

| Kivétel | Hibakód | Kezelés|
| --- | --- | --- |
| [MsalUiSzükségesKivétel](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: A felhasználó vagy a rendszergazda nem járult hozzá a(z) "{appId}" azonosítójú alkalmazás használatához, amelynek neve "{appName}". Interaktív engedélyezési kérelem küldése ehhez a felhasználóhoz és erőforráshoz.| Először meg kell szereznie a felhasználói beleegyezést. Ha nem a .NET Core programot használja (amely nem rendelkezik webes `AcquireTokeninteractive`felhasználói felülettel), hívja meg (csak egyszer) . Ha .NET core-t használ, vagy nem `AcquireTokenInteractive`szeretne csinálni egy, a felhasználó `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`egy URL-címre navigálhat, hogy beleegyezését adja: . hívni: `AcquireTokenInteractive``app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiSzükségesKivétel](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: A felhasználónak többtényezős hitelesítést (MFA) kell használnia.| Nincs mérséklés. Ha az MFA konfigurálva van a bérlőhöz, és az Azure Active Directory (AAD) `AcquireTokenInteractive` `AcquireTokenByDeviceCode`úgy dönt, hogy kényszeríti azt, akkor egy interaktív folyamathoz kell visszatérnie, például vagy .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: A támogatás típusa nem támogatott a */common* vagy */consumers végpontok.* Használja a */organizations* vagy a bérlő-specifikus végpont. A */common*kapcsolót használta.| Amint azt az Üzenet az Azure AD, a hatóság kell egy bérlő vagy más */organizations.*|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: A kérelemtörzsnek a `client_secret or client_assertion`következő paramétert kell tartalmaznia: .| Ez a kivétel akkor érhető el, ha az alkalmazás nem regisztrált nyilvános ügyfélalkalmazásként az Azure AD-ben. Az Azure Portalon szerkesztheti az alkalmazás `allowPublicClient` jegyzékfájlját, és állítsa a . `true` |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: Nem lehet azonosítani a bejelentkezett felhasználót| A tár nem tudta lekérdezni az aktuális Windows bejelentkezett felhasználót, vagy ez a felhasználó nem csatlakozott AD-hez vagy AAD-hez (a munkahelyi csatlakozott felhasználók nem támogatottak). 1. kockázatcsökkentés: az UWP-n ellenőrizze, hogy az alkalmazás rendelkezik-e a következő képességekkel: Vállalati hitelesítés, Magánhálózatok (Ügyfél és kiszolgáló), Felhasználói fiók adatai. 2. kockázatcsökkentés: Valósítsa meg saját logikáját a felhasználónév lekéréséhez (például john@contoso.com), és használja a `AcquireTokenByIntegratedWindowsAuth` felhasználónévben található űrlapot.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Ez a módszer az Active Directory (AD) által elérhetővé tett protokollra támaszkodik. Ha egy felhasználó az Azure Active Directoryban jött létre AD-támogatás nélkül ("felügyelt" felhasználó), ez a módszer sikertelen lesz. Az AD-ben létrehozott és AzAD ("összevont" felhasználók) által támogatott felhasználók élvezhetik ezt a nem interaktív hitelesítési módszert. Kockázatcsökkentés: Használjon interaktív hitelesítést.|

### `MsalUiRequiredException`

Az egyik gyakori állapotkód, `AcquireTokenSilent()` amelyet MSAL.NET a hívás során a `MsalError.InvalidGrantError`. Ez az állapotkód azt jelenti, hogy az alkalmazásnak újra meg kell hívnia a hitelesítési könyvtárat, de interaktív módban (AcquireTokenInteractive vagy AcquireTokenByDeviceCodeFlow nyilvános ügyfélalkalmazásokhoz, és kihívást kell tennie a webes alkalmazásokban). Ennek az az oka, hogy a hitelesítési jogkivonat kiállítása előtt további felhasználói beavatkozásra van szükség.

Az idő nagy `AcquireTokenSilent` részében, ha nem sikerül, ez azért van, mert a jogkivonat-gyorsítótár nem rendelkezik a kérésnek megfelelő jogkivonatokkal. A hozzáférési jogkivonatok 1 `AcquireTokenSilent` óra múlva lejárnak, és megpróbálnak egy újat lekérni egy frissítési jogkivonat alapján (OAuth2 kifejezésekben ez a "Token frissítése" folyamat). Ez a folyamat is sikertelen lehet különböző okok miatt, például ha egy bérlői rendszergazda szigorúbb bejelentkezési házirendeket konfigurál. 

Az interakció célja, hogy a felhasználó egy műveletet. Néhány ilyen feltételt a felhasználók egyszerűen feloldhatnak (például egyetlen kattintással elfogadhatják a Használati feltételeket), és néhányat nem lehet megoldani az aktuális konfigurációval (például a kérdéses gépnek egy adott vállalati hálózathoz kell csatlakoznia). Néhány súgó segít a felhasználónak a többtényezős hitelesítés beállításában, vagy telepítheti a Microsoft Authenticator-t az eszközére.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`osztályozási számbavétel

Az MSAL `Classification` egy olyan mezőt mutat be, amelyet elolvashat, hogy jobb felhasználói élményt nyújtson, például közölje a felhasználóval, hogy a jelszó lejárt, vagy hogy beleegyezését kell adnia bizonyos erőforrások használatához. A támogatott értékek a `UiRequiredExceptionClassification` felsoranak részét képezik:

| Osztályozás    | Jelentés           | Ajánlott kezelés |
|-------------------|-------------------|----------------------|
| Alapvető művelet | A feltétel az interaktív hitelesítési folyamat során a felhasználói beavatkozással oldható meg. | AcquireTokenInteractively() hívása. |
| További művelet | A feltétel az interaktív hitelesítési folyamaton kívül a rendszerrel való további helyreállító interakcióval oldható meg. | Hívja meg acquiretokeninteractively() egy üzenetet, amely elmagyarázza a javítási műveletet. A hívó alkalmazás elrejtheti a additional_action igénylő folyamatokat, ha a felhasználó valószínűleg nem hajtvégre a javítási műveletet. |
| MessageOnly      | Az állapot ot jelenleg nem lehet megoldani. Az interaktív hitelesítési folyamat indításakor megjelenik egy üzenet, amely elmagyarázza a feltételt. | Hívja meg acquiretokeninteractively() egy üzenetet, amely elmagyarázza a feltételt. Az AcquireTokenInteractively() a UserCanceled hibát adja vissza, miután a felhasználó beolvassa az üzenetet, és bezárja az ablakot. A hívó alkalmazás elrejtheti azokat a folyamatokat, amelyek message_only eredményeznek, ha a felhasználó valószínűleg nem használja ki az üzenet előnyeit.|
| Hozzájárulásszükséges  | A felhasználó hozzájárulása hiányzik, vagy visszavonták. | Hívja meg acquiretokeninteractively() a felhasználó beleegyezését. |
| UserPasswordExpired | A felhasználó jelszava lejárt. | Hívja meg az AcquireTokenInteractively() gombot, hogy a felhasználó alaphelyzetbe állíthassa a jelszavát. |
| PromptNeverFailed (Nem sikerült)| Az interaktív hitelesítés tüstént prompt=never paraméterrel lett meghívva, így az MSAL böngészőcookie-kra támaszkodott, és nem jeleníthette meg a böngészőt. Ez nem sikerült. | AcquireTokenInteractively() hívása prompt nélkül.None |
| AcquireTokenSilentFailed | Az MSAL SDK nem rendelkezik elegendő információval ahhoz, hogy tokeneket hozzon le a gyorsítótárból. Ennek az lehet az oka, hogy nincs token a gyorsítótárban, vagy egy fiók nem található. A hibaüzenet további részleteket tartalmaz.  | AcquireTokenInteractively() hívása. |
| None    | További részleteket nem közölünk. A feltétel az interaktív hitelesítési folyamat során a felhasználói beavatkozással oldható meg. | AcquireTokenInteractively() hívása. |

## <a name="net-code-example"></a>Példa .

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
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
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

## <a name="javascript"></a>[Javascript](#tab/javascript)

Az MSAL.js olyan hibaobjektumokat biztosít, amelyek absztraktmódon absztraktak és osztályozzák a gyakori hibák különböző típusait. Azt is előírja, felület eléréséhez konkrét részleteket a hibák, mint például a hibaüzenetek kezelni őket megfelelően.

### <a name="error-object"></a>Hibaobjektum

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

A hibaosztály kiterjesztésével a következő tulajdonságokhoz férhet hozzá:
- `AuthError.message`: Ugyanaz, `errorMessage`mint a .
- `AuthError.stack`: A kidobott hibák veremnyomkövetése.

### <a name="error-types"></a>Hibatípusok

A következő hibatípusok érhetők el:

- `AuthError`: Az MSAL.js könyvtár alaphibaosztálya, amely szintén nem várt hibákra szolgál.

- `ClientAuthError`: Hibaosztály, amely az ügyfélhitelesítéssel kapcsolatos problémát jelez. A tárból származó legtöbb hiba ügyfélautherrors lesz. Ezek a hibák olyan dolgokból erednek, mint például egy bejelentkezési metódus hívása, amikor a bejelentkezés már folyamatban van, a felhasználó megszakítja a bejelentkezést, és így tovább.

- `ClientConfigurationError`: Hibaosztály, `ClientAuthError` a kérelmek megtervezése előtt kivált, ha az adott felhasználói konfigurációs paraméterek hibásak vagy hiányoznak.

- `ServerError`: Hibaosztály, a hitelesítési kiszolgáló által küldött hibakarakterláncokat jelöli. Ezek lehetnek olyan hibák, mint például az érvénytelen kérelemformátumok vagy paraméterek, vagy bármely más olyan hiba, amely megakadályozza, hogy a kiszolgáló hitelesítse vagy engedélyezze a felhasználót.

- `InteractionRequiredAuthError`: Hibaosztály, `ServerError` kiterjeszti a kiszolgálói hibákat, amelyek interaktív hívást igényelnek. Ezt a hibát `acquireTokenSilent` az adja, ha a felhasználónak kapcsolatba kell lépnie a kiszolgálóval a hitelesítéshez/engedélyezéshez szükséges hitelesítő adatok vagy hozzájárulás megadásához. A hibakódok `"login_required"`közé `"consent_required"`tartozik `"interaction_required"`a , és a .

Az átirányítási metódusokkal (`loginRedirect`, `acquireTokenRedirect`) történő hitelesítési folyamatokban történő hibakezeléshez regisztrálnia kell `handleRedirectCallback()` a visszahívást, amelyet az átirányítás után sikeresen vagy sikertelenül hívmeg az alábbi módszerrel:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Az előugró élmény (`loginPopup` `acquireTokenPopup`, ) visszaígéretei, így az ígéretminta (.then és .catch) segítségével az alábbi módon kezelhetők:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Interakciót igénylő hibák

Hiba jelenik meg, amikor egy token megszerzésének nem interaktív `acquireTokenSilent`módszerét próbálja használni, például a , de az MSAL nem tudta csendben megtenni.

Ennek lehetséges okai a következők:

- be kell jelentkeznie
- hozzájárulásra van szüksége
- többtényezős hitelesítési élményt kell átélnie.

A kármentesítés egy interaktív módszer `acquireTokenPopup` `acquireTokenRedirect`hívására szolgál, mint például: vagy:

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

## <a name="python"></a>[Python](#tab/python)

Az MSAL python-ban a legtöbb hiba az API-hívás ból származó visszatérési értékként kerül átadásra. A hiba a Microsoft identity platform JSON-válaszát tartalmazó szótárként jelenik meg.

* A sikeres válasz `"access_token"` tartalmazza a kulcsot. A válasz formátumát az OAuth2 protokoll határozza meg. További információ: [5.1 Successful Response](https://tools.ietf.org/html/rfc6749#section-5.1)
* A hibaválasz `"error"` tartalmazza, és általában `"error_description"`. A válasz formátumát az OAuth2 protokoll határozza meg. További információ: [5.2 Hibaválasz](https://tools.ietf.org/html/rfc6749#section-5.2)

Hiba esetén a `"error_description"` kulcs ember által olvasható üzenetet tartalmaz; amely viszont általában tartalmaz egy Microsoft identity platform hibakódot. A különböző hibakódokról a [Hitelesítési és engedélyezési hibakódok című](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes)témakörben talál részleteket.

A Python msal-ban a kivételek ritkák, mert a legtöbb hibát hibaérték visszaadásával kezelik. A `ValueError` kivétel csak akkor történik meg, ha probléma merül fel azzal kapcsolatban, hogy hogyan próbálja használni a könyvtárat – például ha az API-paraméter(eke)t helytelenül formázza.

## <a name="java"></a>[Java](#tab/java)

A Java msal-jében háromféle kivétel `MsalClientException` `MsalServiceException`létezik: , , és `MsalInteractionRequiredException`; mind, ami `MsalException`örökli a .

- `MsalClientException`a rendszer akkor jelenik meg, ha a könyvtárvagy az eszköz helyi hibája történik.
- `MsalServiceException`a rendszer akkor jelenik meg, amikor a biztonságos jogkivonat-szolgáltatás (STS) hibaüzenetet ad vissza, vagy más hálózati hiba lép fel.
- `MsalInteractionRequiredException`akkor kerül elő, ha a hitelesítés sikeréhez felhasználói felületi beavatkozásra van szükség.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException`http-fejlécek vissza a kérelmeket az STS. Érje el őket keresztül`MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Az egyik gyakori állapotkódok vissza MSAL `AcquireTokenSilently()` `InvalidGrantError`a Java hívás kor . Ez azt jelenti, hogy a hitelesítési jogkivonat kiadása előtt további felhasználói beavatkozásra van szükség. Az alkalmazásnak újra meg kell hívnia a `AuthorizationCodeParameters` `DeviceCodeParameters` hitelesítési könyvtárat, de interaktív módban küldéssel vagy nyilvános ügyfélalkalmazások esetén.

Az idő nagy `AcquireTokenSilently` részében, ha nem sikerül, ez azért van, mert a jogkivonat-gyorsítótár nem rendelkezik a kérésnek megfelelő jogkivonatot. A hozzáférési jogkivonatok egy `AcquireTokenSilently` órán belül lejárnak, és egy frissítési jogkivonat alapján megpróbálnak beszerezni egy újat. OAuth2 kifejezésekben ez a frissítési jogkivonat-folyamat. Ez a folyamat is sikertelen lehet különböző okok miatt, például amikor egy bérlői rendszergazda szigorúbb bejelentkezési házirendeket konfigurál.

A hibát eredményező egyes feltételeket a felhasználók könnyen feloldják. Előfordulhat például, hogy el kell fogadniuk a Használati feltételeket. Vagy lehet, hogy a kérés nem teljesíthető az aktuális konfigurációval, mert a gépnek csatlakoznia kell egy adott vállalati hálózathoz.

Az MSAL `reason` kiteszi a mezőt, amelynek segítségével jobb felhasználói élményt biztosíthat. A `reason` mező például arra késztetheti, hogy tájékoztassa a felhasználót arról, hogy a jelszava lejárt, vagy hogy beleegyezését kell adnia bizonyos erőforrások használatához. A támogatott értékek a `InteractionRequiredExceptionReason` felsoranak részét képezik:

| Ok | Jelentés | Ajánlott kezelés |
|---------|-----------|-----------------------------|
| `BasicAction` | A feltétel az interaktív hitelesítési folyamat során felhasználói beavatkozással oldható meg | Hívás `acquireToken` interaktív paraméterekkel |
| `AdditionalAction` | A feltétel az interaktív hitelesítési folyamaton kívüli, a rendszerrel való további helyreállító interakcióval oldható meg. | Hívja `acquireToken` meg az interaktív paraméterekkel, hogy megjelenítse a javítási műveletet elmagyarázó üzenetet. A hívó alkalmazás elrejtheti a további műveletet igénylő folyamatokat, ha a felhasználó valószínűleg nem hajtvégre a javítási műveletet. |
| `MessageOnly` | Az állapot ot jelenleg nem lehet megoldani. Interaktív hitelesítési folyamat indítása a feltételt magyarázó üzenet megjelenítéséhez. | Az `acquireToken` interaktív paraméterekkel való hívás a feltételt magyarázó üzenet megjelenítéséhez. `acquireToken`visszaadja `UserCanceled` a hibát, miután a felhasználó beolvassa az üzenetet, és bezárja az ablakot. Az alkalmazás elrejtheti az üzenetet eredményező folyamatokat, ha a felhasználó valószínűleg nem használja ki az üzenet előnyeit. |
| `ConsentRequired`| A felhasználó hozzájárulása hiányzik, vagy visszavonták. |Hívjon `acquireToken` interaktív paraméterekkel, hogy a felhasználó beleegyezését adhassa. |
| `UserPasswordExpired` | A felhasználó jelszava lejárt. | Hívás `acquireToken` interaktív paraméterrel, hogy a felhasználó visszaállíthassa jelszavát |
| `None` |  További részletek a dis. A feltétel az interaktív hitelesítési folyamat során a felhasználói beavatkozással oldható meg. | Hívás `acquireToken` interaktív paraméterekkel |

### <a name="code-example"></a>Példa kódra

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

Az MSAL iOS és macOS rendszerhez készült hibáinak teljes listája az [MSALError enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Az összes MSAL által `MSALErrorDomain` okozott hibát a tartomány adja vissza.

Rendszerhibák esetén az MSAL `NSError` az eredetit adja vissza a rendszer API-jából. Ha például a jogkivonat-beszerzés a hálózati kapcsolat hiánya miatt `NSURLErrorDomain` sikertelen, az MSAL hibát ad vissza a tartománnyal és `NSURLErrorNotConnectedToInternet` a kóddal.

Javasoljuk, hogy legalább a következő két MSAL-hibát kezelje az ügyféloldalon:

- `MSALErrorInteractionRequired`: A felhasználónak interaktív kérést kell tennie. Számos feltétel vezethet ez a hiba, például egy lejárt hitelesítési munkamenet, vagy a további hitelesítési követelmények szükségességét. Hívja meg az MSAL interaktív token beszerzési API-t a helyreállításhoz. 

- `MSALErrorServerDeclinedScopes`: Néhány vagy az összes hatókört elutasították. Döntse el, hogy csak a megadott hatókörökkel folytatja-e, vagy leállítja a bejelentkezési folyamatot.

> [!NOTE]
> A `MSALInternalError` felsorolás csak referencia- és hibakeresésre használható. Ne próbálja meg futásidőben automatikusan kezelni ezeket a hibákat. Ha az alkalmazás a rendszer alatt `MSALInternalError`álló hibák bármelyikével találkozik, akkor érdemes lehet egy általános felhasználónak szóló üzenetet megjelenítenie, amely elmagyarázza, hogy mi történt.

Például `MSALInternalErrorBrokerResponseNotReceived` azt jelenti, hogy a felhasználó nem végezte el a hitelesítést, és manuálisan visszatért az alkalmazásba. Ebben az esetben az alkalmazásnak meg kell jelennie egy általános hibaüzenetben, amely ismerteti, hogy a hitelesítés nem fejeződött be, és azt kell javasolnia, hogy próbálja meg újra hitelesíteni magát.

A következő C célkitűzés mintakód bemutatja az egyes gyakori hibafeltételek kezelésére vonatkozó bevált gyakorlatokat:

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>Feltételes hozzáférés és jogcímek kihívásai

Amikor a jogkivonatok csendesen, az alkalmazás hibaüzeneteket kaphat, ha egy [feltételes hozzáférés jogcímkérés,](../azuread-dev/conditional-access-dev-guide.md) például az MFA-szabályzat van szükség egy API-t próbál hozzáférni.

A hiba kezelésének mintája az MSAL használatával történő token interaktív beszerzése. A jogkivonat interaktív beolvasása kéri a felhasználót, és lehetőséget ad nekik a szükséges feltételes hozzáférési szabályzat teljesítésére.

Bizonyos esetekben, amikor egy API-t igénylő feltételes hozzáférés, kaphat egy jogcímkihívást az API-ból a hiba. Ha például a feltételes hozzáférési házirend felügyelt eszközzel (Intune) rendelkezik, a hiba az [AADSTS53000-hez hasonló lesz: Az eszköznek az erőforrás vagy](reference-aadsts-error-codes.md) valami hasonló eléréséhez kell kezelnie. Ebben az esetben átadhatja a jogcímeket a beszerzési jogkivonat-hívásban, hogy a rendszer kérje a felhasználót a megfelelő szabályzat teljesítésére.

### <a name="net"></a>.NET

Ha egy API-t igénylő feltételes hozzáférést MSAL.NET, az alkalmazás kell kezelni jogcímkihívás kivételek. Ez [msalserviceexceptionként](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) jelenik meg, ahol a [Jogcímek](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) tulajdonság nem lesz üres.

A jogcímkihívás kezeléséhez az `.WithClaim()` `PublicClientApplicationBuilder` osztály metódusát kell használnod.

### <a name="javascript"></a>JavaScript

Ha az MSAL.js használatával csendesen `acquireTokenSilent`(használatával) szerzi be a jogkivonatokat, az alkalmazás hibaüzeneteket kaphat, ha egy feltételes hozzáférési [jogcímkérési kihívásra](../azuread-dev/conditional-access-dev-guide.md) van szükség, például az MFA-házirendre egy olyan API-ra, amelyhez hozzá próbál hozzáférni.

A hiba kezelésére az a minta, hogy interaktív hívást kezdeményez `acquireTokenPopup` az `acquireTokenRedirect` MSAL.js-ben lévő jogkivonat megszerzéséhez, például a következő példában:

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

A jogkivonat interaktív beolvasása kéri a felhasználót, és lehetőséget ad nekik a szükséges feltételes hozzáférési szabályzat teljesítésére.

Feltételes hozzáférést igénylő API-hívása esetén az API-ból származó hiba jogcímkihívást kaphat. Ebben az esetben a hiba miatt visszaadott `claimsRequest` jogcímeket `AuthenticationParameters.ts` átadhatja az osztály mezőjének, hogy megfeleljen a megfelelő házirendnek. 

További igények kérése további részletekért lásd: [További jogcímek kérése.](active-directory-optional-claims.md)

### <a name="msal-for-ios-and-macos"></a>MSAL iOS és macOS rendszerekre

Az MSAL iOS és macOS rendszerhez lehetővé teszi, hogy speciális jogcímeket kérjen interaktív és csendes jogkivonat-beszerzési forgatókönyvekben is.

Egyéni jogcímek igényléséhez `MSALSilentTokenParameters` `MSALInteractiveTokenParameters`adja meg a vagy `claimsRequest` a mezőben.

További [információ: Egyéni jogcímek kérése az MSAL használatával iOS-hez és macOS-hez.](request-custom-claims.md)

## <a name="retrying-after-errors-and-exceptions"></a>Újrapróbálkozás hibák és kivételek után

Az MSAL hívásakor saját újrapróbálkozási házirendeket kell megvalósítania. Az MSAL HTTP-hívásokat kezdeményez az AAD szolgáltatáshoz, és időnként hibák léphetnek fel, például a hálózat leléphet, vagy a kiszolgáló túlterhelt.  

### <a name="http-error-codes-500-600"></a>HTTP-hibakód: 500-600

MSAL.NET az 500-600-as HTTP-hibakóddal rendelkező hibák egyszerű újrapróbálkozási mechanizmusát valósítja meg.

### <a name="http-429"></a>HTTP 429

Ha a szolgáltatástoken-kiszolgáló (STS) túl sok kéréssel van túlterhelve, a 429-es HTTP-hibát adja vissza, és egy tippet ad arra, hogy mennyi idő múlva próbálkozhat újra a `Retry-After` válaszmezőben.

### <a name="net"></a>.NET

[A MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) `System.Net.Http.Headers.HttpResponseHeaders` tulajdonságként `namedHeaders`kerül fel. A hibakódból származó további információk segítségével javíthatja az alkalmazások megbízhatóságát. A leírt esetben használhatja `RetryAfterproperty` a (típus) `RetryConditionHeaderValue`és a számítási, amikor újra kell próbálni.

Íme egy példa egy démonalkalmazáshoz az ügyfél hitelesítő adatainak folyamata használatával. Ezt a token megszerzésének bármely módszeréhez igazíthatja.

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
