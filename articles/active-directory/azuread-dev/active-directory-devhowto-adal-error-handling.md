---
title: ADAL ügyfélalkalmazás hibakezelési gyakorlati tanácsok | Azure
description: Hibakezelési útmutatást és gyakorlati tanácsokat tartalmaz az ADAL-ügyfélalkalmazásokhoz.
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 8973412b2d6575d524874ba05b34af7661655e19
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981069"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Az Azure Active Directory hitelesítési kódtárbeli (ADAL) ügyfelekkel kapcsolatos gyakorlati tanácsok kezelése

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Ez a cikk útmutatást nyújt a fejlesztők által a felhasználók hitelesítéséhez az ADAL használatasorán előforduló hibák típusa tekintetében. Az ADAL használatakor számos olyan eset lehet, amikor a fejlesztőnek be kell lépnie és kezelnie kell a hibákat. A megfelelő hibakezelés nagyszerű végfelhasználói élményt biztosít, és korlátozza, hogy a végfelhasználónak hányszor kell bejelentkeznie.

Ebben a cikkben az ADAL által támogatott egyes platformok konkrét eseteit vizsgáljuk, és azt, hogy az alkalmazás hogyan tudja megfelelően kezelni az egyes eseteket. A hibaútmutató két szélesebb kategóriába van osztva az ADAL API-k által biztosított tokenbeszerzési minták alapján:

- **AcquireTokenSilent**: Az ügyfél megkísérli a jogkivonat csendes beszerzését (nincs felhasználói felület), és sikertelen lehet, ha az ADAL sikertelen. 
- **AcquireToken**: Az ügyfél megkísérelheti a csendes akvizíciót, de olyan interaktív kéréseket is végrehajthat, amelyek bejelentkezést igényelnek.

> [!TIP]
> Érdemes naplózni az összes hibát és kivételt az ADAL és az Azure AD használatakor. A naplók nem csak az alkalmazás általános állapotának megértéséhez hasznosak, hanem szélesebb körű problémák hibakeresésekor is fontosak. Bár az alkalmazás helyreállhat bizonyos hibákból, nagyobb tervezési problémákra utalhatnak, amelyek megoldásához kódmódosításokat igényelnek. 
> 
> A jelen dokumentumban szereplő hibafeltételek megvalósításakor naplózza a hibakódot és a leírást a korábban tárgyalt okok miatt. A naplózási kódra vonatkozó példákat a [Hiba és naplózási hivatkozás című témakörben](#error-and-logging-reference) talál. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent megkísérli a jogkivonat beszerzése a garancia, hogy a végfelhasználó nem látja a felhasználói felület (UI). Számos olyan eset van, amikor a csendes akvizíció sikertelen lehet, és interaktív kérelmekvagy alapértelmezett kezelő által kell kezelni. Az alábbi szakaszokban belemerülünk annak sajátosságaiba, hogy mikor és hogyan alkalmazzuk az egyes eseteket.

Az operációs rendszer által generált hibák készlete miatt előfordulhat, hogy az alkalmazásra jellemző hibakezelést igényel. További információt az "Operációs rendszer" hibák című szakaszában, a [Hiba- és naplózási hivatkozás](#error-and-logging-reference)című témakörben talál. 

### <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

- [Natív ügyfélalkalmazások](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#native-client) (iOS, Android, .NET Desktop vagy Xamarin)
- [Erőforrást](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#resource-server) hívó [webügyfél-alkalmazások](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#web-client) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Hibaesetek és végrehajtható lépések

Alapvetően két esetben a AcquireTokenSilent hibák:

| Case (Eset) | Leírás |
|------|-------------|
| **1. eset**: A hiba feloldható egy interaktív bejelentkezéssel | Az érvényes jogkivonatok hiánya által okozott hibák esetén interaktív kérelemre van szükség. Pontosabban a gyorsítótár-keresés és egy érvénytelen/lejárt frissítési jogkivonat egy AcquireToken hívást igényel a feloldáshoz.<br><br>Ezekben az esetekben a végfelhasználót kell kérni, hogy jelentkezzen be. Az alkalmazás választhat, hogy nem egy interaktív kérelmet azonnal, miután a végfelhasználói beavatkozás (például megnyomja a bejelentkezési gombot), vagy később. A választás az alkalmazás kívánt viselkedésétől függ.<br><br>Tekintse meg a kódot a következő szakaszban ebben a konkrét esetben, és a hibákat, amelyek diagnosztizálják azt.|
| **2. eset:** A hiba nem oldható fel interaktív bejelentkezéssel | Hálózati és átmeneti/ideiglenes hibák vagy egyéb hibák esetén az interaktív AcquireToken kérés végrehajtása nem oldja meg a problémát. A szükségtelen interaktív bejelentkezési utasítások a végfelhasználókat is meghiúsíthatják. Az ADAL automatikusan megkísérli a legtöbb hiba egyetlen újrapróbálkozását acquiretokensilent hibák.<br><br>Az ügyfélalkalmazás egy későbbi időpontban is megkísérelheti az újrapróbálkozást, de hogy mikor és hogyan függ az alkalmazás viselkedésétől és a kívánt végfelhasználói élménytől. Például az alkalmazás néhány perc múlva vagy néhány végfelhasználói műveletre válaszul elvégezheti a AcquireTokenSilent újrapróbálkozást. Az azonnali újrapróbálkozás az alkalmazás szabályozását eredményezi, és nem kísérelhető meg.<br><br>Egy későbbi, ugyanazzal a hibával rendelkező újrapróbálkozás nem jelenti azt, hogy az ügyfélnek interaktív kérést kell tennie a AcquireToken használatával, mivel nem oldja meg a hibát.<br><br>Tekintse meg a kódot a következő szakaszban ebben a konkrét esetben, és a hibákat, amelyek diagnosztizálják azt. |

### <a name="net"></a>.NET

Az alábbi útmutató példákat mutat be az ADAL-módszerekkel kapcsolatos hibakezelésre: 

- acquireTokenSilentAsync(...)
- acquireTokenSilentSync(...) 
- [elavult] acquireTokenSilent(...)
- [elavult] acquireTokenByRefreshToken(...) 

A kód a következőképpen valósulna meg:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request. 
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

Az alábbi útmutató példákat mutat be az ADAL-módszerekkel kapcsolatos hibakezelésre: 

- acquireTokenSilentSync(...)
- acquireTokenSilentAsync(...)
- [elavult] acquireTokenSilent(...)

A kód a következőképpen valósulna meg:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

Az alábbi útmutató példákat mutat be az ADAL-módszerekkel kapcsolatos hibakezelésre: 

- acquireTokenSilentWithResource(...)

A kód a következőképpen valósulna meg:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application. 

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken

AcquireToken az alapértelmezett ADAL módszer, amely a tokenek lekéréséhez használt. Azokban az esetekben, ahol a felhasználói identitás szükséges, AcquireToken megpróbálja beszerezni a jogkivonatot csendben először, majd megjeleníti a felhasználói felület, ha szükséges (kivéve, ha PromptBehavior.Never át). Azokban az esetekben, ahol az alkalmazás identitása szükséges, AcquireToken megkísérli a jogkivonat ot, de nem jelenik meg a felhasználói felület, mivel nincs végfelhasználó. 

AcquireToken hibák kezelése során a hibakezelés függ a platform és a forgatókönyv az alkalmazás próbál elérni. 

Az operációs rendszer hibákat is generálhat, amelyek az adott alkalmazástól függő hibakezelést igényelnek. További információt az "Operációs rendszer hibái" című témakörben talál a [Hiba és naplózási hivatkozás című témakörben.](#error-and-logging-reference) 

### <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

- Natív ügyfélalkalmazások (iOS, Android, .NET Desktop vagy Xamarin)
- Erőforrás API-t meghívó webalkalmazások (.NET)
- Egyoldalas alkalmazások (JavaScript)
- Szolgáltatás-szolgáltatás alkalmazások (.NET, Java)
  - Minden forgatókönyv, beleértve a
  - Konkrét forgatókönyvek nevében

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Hibaesetek és végrehajtható lépések: Natív ügyfélalkalmazások

Ha natív ügyfélalkalmazást hoz létre, néhány hibakezelési esetet figyelembe kell vennie, amelyek a hálózati problémákhoz, az átmeneti hibákhoz és más platformspecifikus hibákhoz kapcsolódnak. A legtöbb esetben az alkalmazás nem hajtsa végre az azonnali újrapróbálkozásokat, hanem inkább várjon a végfelhasználói beavatkozásra, amely bejelentkezést kér. 

Van néhány speciális eset, amikor egyetlen újrapróbálkozás megoldhatja a problémát. Például ha egy felhasználónak engedélyeznie kell az adatokat egy eszközön, vagy befejezte az Azure AD-közvetítő letöltését a kezdeti hiba után. 

Hiba esetén az alkalmazás felhasználói felületet jeleníthet meg, hogy a végfelhasználó végrehajthassa az újrapróbálkozást lehetővé tenni. Ha például az eszköz nem sikerült kapcsolat nélküli módban, a "Próbáljon meg újra bejelentkezni" gomb, amely az AcquireToken újrapróbálkozását kéri, és nem azonnal próbálja meg újra a hibát. 

A natív alkalmazásokban a hibakezelést két eset határozhatja meg:

|  |  |
|------|-------------|
| **1. eset:**<br>Nem újrapróbálható hiba (a legtöbb esetben) | 1. Ne próbálkozzon azonnal. Mutassa be a végfelhasználói felhasználói felületet az újrapróbálkozást megmeghívó konkrét hiba alapján (például "Próbálja meg újra bejelentkezni" vagy "Azure AD broker alkalmazás letöltése"). |
| **2. eset**:<br>Újrapróbálható hiba | 1. Hajtson végre egy újrapróbálkozást, mivel előfordulhat, hogy a végfelhasználó olyan állapotba lépett, amely sikeres.<br><br>2. Ha az újrapróbálkozás sikertelen, mutassa be a végfelhasználói felhasználói felületet az újrapróbálkozást megmeghívó konkrét hiba alapján ("Próbálja meg újra bejelentkezni", "Töltse le az Azure AD broker alkalmazást", stb.). |

> [!IMPORTANT]
> Ha egy felhasználói fiók csendes hívás esetén kerül átadásra az ADAL-nak, és sikertelen, a későbbi interaktív kérés lehetővé teszi a végfelhasználó számára, hogy egy másik fiókkal jelentkezzen be. Miután egy sikeres AcquireToken egy felhasználói fiókot használ, az alkalmazásnak ellenőriznie kell, hogy a bejelentkezett felhasználó megfelel az alkalmazások helyi felhasználói objektum. Az eltérés nem hoz létre kivételt (kivéve a C célkitűzést), de figyelembe kell venni azokban az esetekben, amikor a felhasználó a hitelesítési kérelmek előtt helyileg ismert (például sikertelen néma hívás).
>

#### <a name="net"></a>.NET

Az alábbi útmutató példákat tartalmaz a hibakezelésért az összes nem csendes AcquireToken(...) ADAL módszerek, *kivéve:* 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(..., ClientCredential, ...)
- AcquireTokenAsync(..., Ügyfélállítás, ...)
- AcquireTokenAsync(...; UserAssertion,...)   

A kód a következőképpen valósulna meg:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> Az ADAL .NET egy további szempontot is figyelembe vett, mivel támogatja a PromptBehavior.Never-t, amelynek viselkedése például AcquireTokenSilent.
>

Az alábbi útmutató példákat mutat be az ADAL-módszerekkel kapcsolatos hibakezelésre: 

- acquireToken(..., PromptBehavior.Never)

A kód a következőképpen valósulna meg:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

Az alábbi útmutató példákat tartalmaz a hibakezelésért az összes nem csendes AcquireToken(...) ADAL módszerek. 

A kód a következőképpen valósulna meg:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

Az alábbi útmutató példákat tartalmaz a hibakezelésért az összes nem csendes AcquireToken(...) ADAL módszerek. 

A kód a következőképpen valósulna meg:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Hibaesetek és végrehajtható lépések: Erőforrás-API-t (.NET) hívó webalkalmazások

Ha egy .NET webalkalmazást hoz fel, amely egy erőforrás engedélyezési kódját használva jogkivonatot kap, az egyetlen szükséges kód az általános eset alapértelmezett kezelője. 

Az alábbi útmutató példákat mutat be az ADAL-módszerekkel kapcsolatos hibakezelésre: 

- AcquireTokenByAuthorizationCodeAsync(...)

A kód a következőképpen valósulna meg:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Hibaesetek és végrehajtható lépések: Egyoldalas alkalmazások (adal.js)

Ha egy egyoldalas alkalmazást hoz fel az adal.js és acquiretoken használatával, a hibakezelési kód hasonló egy tipikus csendes híváshoz. Az adal.js-ben az AcquireToken soha nem jelenít meg felhasználói felületet. 

A sikertelen AcquireToken a következő esetekkel rendelkezik:

|  |  |
|------|-------------|
| **1. eset:**<br>Feloldható interaktív kéréssel | 1. Ha a login() sikertelen, ne hajtson végre azonnali újrapróbálkozást. Csak akkor próbálkozzon újra, amikor a felhasználói művelet újrapróbálkozik.|
| **2. eset**:<br>Interaktív kéréssel nem oldható fel. A hiba újrapróbálkozásra képes. | 1. Hajtson végre egy újrapróbálkozást, mivel a végfelhasználói fő olyan állapotba lépett, amely sikeres.<br><br>2. Ha az újrapróbálkozás sikertelen, mutasson be a végfelhasználónak egy műveletet az adott hibán alapuló művelettel, amely meghívhat egy újrapróbálkozást ("Próbáljon meg újra bejelentkezni"). |
| **3. eset:**<br>Interaktív kéréssel nem oldható fel. A hiba nem próbálható meg újra. | 1. Ne próbálkozzon azonnal. Mutasson be a végfelhasználónak egy műveletet az adott hiba alapján, amely meghívhat egy újrapróbálkozást ("Próbáljon meg újra bejelentkezni"). |

A kód a következőképpen valósulna meg:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Hibaesetek és végrehajtható lépések: szolgáltatás-szolgáltatás alkalmazások (csak.NET)

Ha egy AcquireToken szolgáltatást használó szolgáltatást nyújtó alkalmazást hoz fel, a kódnak néhány kulcsfontosságú hibát kell kezelnie. A hiba egyetlen célja, hogy visszaadja a hibát a hívó alkalmazásnak (az esetek nevében), vagy alkalmazza az újrapróbálkozási stratégiát. 

#### <a name="all-scenarios"></a>Az összes forgatókönyv

*Minden* szolgáltatás-szolgáltatás alkalmazás forgatókönyvesetén, beleértve a nevükben történő:

- Ne próbálkozzon azonnali újrapróbálkozásra. Az ADAL bizonyos sikertelen kérelmek esetén egyetlen újrapróbálkozást kísérel meg. 
- Csak akkor folytassa az újrapróbálkozást, ha egy felhasználó vagy alkalmazás művelet eretni történik. Például egy démon alkalmazás, amely működik néhány beállított időköz meg kell várni, amíg a következő időközt, hogy újra.

Az alábbi útmutató példákat mutat be az ADAL-módszerekkel kapcsolatos hibakezelésre: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(...,ClientCredential, ...)
- AcquireTokenAsync(...,Ügyfélállítás, ...)
- AcquireTokenAsync(...,UserAssertion, ...)

A kód a következőképpen valósulna meg:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>A forgatókönyvek nevében

A szolgáltatás-szolgáltatás alkalmazás forgatókönyvek *a szolgáltatás-szolgáltatás* alkalmazás forgatókönyvek.

Az alábbi útmutató példákat mutat be az ADAL-módszerekkel kapcsolatos hibakezelésre: 

- AcquireTokenAsync(..., UserAssertion, ...)

A kód a következőképpen valósulna meg:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

Összeállítottunk egy [teljes mintát,](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) amely bemutatja ezt a forgatókönyvet.

## <a name="error-and-logging-reference"></a>Hiba- és naplózási hivatkozás

### <a name="logging-personal-identifiable-information--organizational-identifiable-information"></a>Személyes azonosításra alkalmas adatok naplózása & szervezeti azonosítható adatok 
Alapértelmezés szerint az ADAL naplózása nem rögzíti és nem naplózza a személyes azonosításra alkalmas adatokat vagy a szervezeti azonosítható adatokat. A könyvtár lehetővé teszi az alkalmazásfejlesztők számára, hogy ezt a Logger osztály ban lévő szettelőn keresztül kapcsolják be. A személyes azonosításra alkalmas adatok vagy a szervezeti azonosítható adatok naplózásával az alkalmazás felelősséget vállal a rendkívül érzékeny adatok biztonságos kezeléséért és a szabályozási követelmények nek való megfelelésért.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>ADAL-függvénytár hibái

Adott ADAL-hibák feltárása érdekében az [azure-activedirectory-library-for-dotnet tárban](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) található forráskód a legjobb hibahivatkozás.

#### <a name="guidance-for-error-logging-code"></a>Útmutató a hibanaplózási kódhoz

Az ADAL .NET naplózás a vizsgált platformtól függően változik. A [naplózási wikiben](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) tájékodhat a naplózás engedélyezésének engedélyezéséről.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>ADAL-függvénytár hibái

Adott ADAL-hibák feltárása érdekében az [azure-activedirectory-library-for-android tárban](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) található forráskód a legjobb hibahivatkozás.

#### <a name="operating-system-errors"></a>Az operációs rendszer hibái

Az Android operációs rendszer hibái az ADAL AuthenticationException-en keresztül jelennek meg, "SERVER_INVALID_REQUEST" néven azonosíthatók, és a hibaleírásokon keresztül részletesebbek lehetnek. 

A gyakori hibák teljes listáját és azt, hogy milyen lépéseket kell tenni, amikor az alkalmazás vagy a végfelhasználók találkoznak velük, olvassa el az ADAL Android Wiki című [webhelyet.](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki) 

#### <a name="guidance-for-error-logging-code"></a>Útmutató a hibanaplózási kódhoz

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode. 
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// By default, the `Logger` does not capture any PII or OII

// PII or OII will be logged
Logger.getInstance().setEnablePII(true);

// To STOP logging PII or OII, use the following setter
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>ADAL-függvénytár hibái

Adott ADAL-hibák feltárása érdekében az [azure-activedirectory-library-for-objc tárban](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) található forráskód a legjobb hibahivatkozás.

#### <a name="operating-system-errors"></a>Az operációs rendszer hibái

iOS-hibák merülhetnek fel a bejelentkezés során, amikor a felhasználók webes nézeteket használnak, és a hitelesítés jellege. Ezt olyan körülmények okozhatják, mint a TLS-hibák, időkiírások vagy hálózati hibák:

- A jogosultságmegosztás esetében a bejelentkezések nem állandóak, és a gyorsítótár üresen jelenik meg. A problémamegoldásához adja hozzá a következő kódsort a kulcskarikához:`[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Az NsUrlDomain hibakészlete esetén a művelet az alkalmazás logikájának függően változik. Az [NSURLErrorDomain referenciadokumentációjában](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) található a kezelhető példányok.
- Az ADAL Objective-C csapat által fenntartott gyakori hibák listáját lásd: [ADAL Obj-C Common Issues.](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror)

#### <a name="guidance-for-error-logging-code"></a>Útmutató a hibanaplózási kódhoz

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Útmutató a hibanaplózási kódhoz - JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```

## <a name="related-content"></a>Kapcsolódó tartalom

* [Azure AD hitelesítési kódtárak][AAD-Auth-Libraries]
* [Azure AD-hitelesítési forgatókönyvek][AAD-Auth-Scenarios]
* [Alkalmazások integrálása az Azure Active Directoryval][AAD-Integrating-Apps]

Használja az alábbi megjegyzések részt, hogy visszajelzést adjon, és segítsen nekünk a tartalom finomításában és alakításában.

[![A "Bejelentkezés a Microsofttal" gomb][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Integrating-Apps]:../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

