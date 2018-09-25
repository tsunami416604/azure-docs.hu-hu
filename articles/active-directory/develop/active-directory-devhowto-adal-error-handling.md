---
title: Ajánlott hibakezelési eljárások az Azure Active Directory Authentication Library (ADAL) ügyfelek
description: Útmutatás és ajánlott eljárások az ADAL ügyfélalkalmazások hibakezelési biztosít.
services: active-directory
documentationcenter: ''
author: danieldobalian
manager: mtillman
ms.author: celested
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.custom: ''
ms.openlocfilehash: db1d2f16c6497ce3c14d162a9c354dda995058f6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974782"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Ajánlott hibakezelési eljárások az Azure Active Directory Authentication Library (ADAL) ügyfelek

Ez a cikk nyújt útmutatást a hibák típusát, hogy a fejlesztők, használatakor előfordulhatnak adal-t felhasználók hitelesítésére. Adal-t használja, ha nincsenek számos olyan esetekben, ahol egy fejlesztői kell lépést, és hibáinak kezelése. Megfelelő hibakezelést biztosítja a nagyszerű végfelhasználói élményt, és korlátozza az, amikor a végfelhasználó jelentkezzen be.

Ez a cikk az adal-t, és hogy az alkalmazás képes kezelni minden esetben megfelelően által támogatott platformonként bizonyos esetekben tárgyaljuk. Hiba című útmutató alapján van felosztva ADAL API-k által biztosított token beszerzéséhez mintái alapján két nagyobb kategóriába sorolhatók:

- **AcquireTokenSilent**: ügyfél csendes jogkivonat beszerzéséhez megkísérli (nincs felhasználói felület), és meghiúsulhat, ha nem sikerül az adal-t. 
- **AcquireToken**: ügyfél csendes beszerzési megkísérelheti, de interaktív bejelentkezést igénylő kérelmek is végrehajthat.

> [!TIP]
> Célszerű a összes hibák és kivételek adal-t és az Azure AD használata esetén. Naplók nem csak segít annak megértésében, az alkalmazás általános állapotát, de akkor is fontos, ha a szélesebb körű problémákat hibakeresés. Előfordulhat, hogy az alkalmazás bizonyos hibák elhárítása, amíg azok szélesebb körű tervezési kapcsolatos problémák elhárítása érdekében a kód módosításait igénylő, előfordulhat, hogy mutatót. 
> 
> Ebben a dokumentumban ismertetett megvalósítása a hibaállapotok, ha a hiba kódja és a leírás kell jelentkezzen a korábban említett okokból. Tekintse meg a [hiba- és naplózási referencia](#error-and-logging-reference) példák a naplózás kódot. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent próbál meg, hogy a felhasználó nem látja a felhasználói felület (UI) garanciájával egy token beszerzéséhez. Nincsenek több olyan esetekben, ahol a beavatkozás nélküli megszerzése sikertelen lehet, és interaktív kérelmeket, vagy egy alapértelmezett kezelő által kezelni kell. Hogy ismerje meg, hogy milyen ügyről van, hogy mikor és hogyan lehet minden esetben az alábbi szakaszok a alkalmazni.

Nincs az operációs rendszer, amihez szükséges, az alkalmazásnak adott hibakezelési által generált hibák készletét. További információkért lásd: "Operációs rendszer" hibák szakasz [hiba- és naplózási referencia](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

- [Natív ügyfél](developer-glossary.md#native-client) alkalmazások (iOS, Android, .NET asztali vagy Xamarin)
- [Webes ügyfél](developer-glossary.md#web-client) hívó alkalmazásokhoz egy [erőforrás](developer-glossary.md#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Hiba eseteket és végrehajtható lépései

Alapvetően két olyan eset létezik AcquireTokenSilent hibák:

| Eset | Leírás |
|------|-------------|
| **1. eset**: hiba feloldható egy interaktív bejelentkezés: | Egy érvényes jogkivonatok hiánya által okozott hibák egy interaktív kérelem szükség. Pontosabban a gyorsítótárban, és a egy érvénytelen vagy lejárt frissítési jogkivonat szükséges egy AcquireToken hívás megoldásához.<br><br>Ezekben az esetekben a végfelhasználó kell felszólítja, hogy jelentkezzen be. Az alkalmazás egy interaktív kérést ehhez azonnal végfelhasználói beavatkozást (például a bejelentkezési gomb lenyomásával), vagy után később választhat. A választás attól függ, hogy az alkalmazás kívánt viselkedése.<br><br>A kód ebben az esetben és akkor diagnosztizálhatja a hibákat a következő szakaszban talál.|
| **2. eset**: hiba nem oldható fel egy interaktív bejelentkezés | A hálózati és az ideiglenes vagy átmeneti hibák, vagy más hibák egy interaktív AcquireToken kérelem végrehajtása nem oldja meg a problémát. A szükségtelen interaktív bejelentkezési kérések is is meghiúsítja a végfelhasználók számára. Adal-t automatikusan megkísérli egy egyetlen újrapróbálkozás AcquireTokenSilent hibák a legtöbb hibát.<br><br>Az ügyfélalkalmazás megpróbálhat egy későbbi időpontban induljanak újra, de mikor és hogyan kell tenni az alkalmazás viselkedését és a kívánt végfelhasználói élmény függ. Ha például az alkalmazás teheti meg egy AcquireTokenSilent próbálkozzon újra néhány perc múlva, vagy valamilyen végfelhasználói művelet adott válaszként. Azonnali újrapróbálkozást eredményez az alkalmazás szabályozás alatt áll, és nem kísérelte meg.<br><br>Ugyanaz a hiba miatt sikertelenül működő későbbi próbálkozáskor nem jelenti azt, az ügyfél egy interaktív kérést AcquireToken, érdemes megtenni, módon nem oldja meg a hiba.<br><br>A kód ebben az esetben és akkor diagnosztizálhatja a hibákat a következő szakaszban talál. |

### <a name="net"></a>.NET

Az alábbi útmutatót példákat ad az hibakezelési ADAL módszerekkel együtt: 

- acquireTokenSilentAsync(…)
- acquireTokenSilentSync(…) 
- acquireTokenSilent(...) [elavult]
- acquireTokenByRefreshToken(...) [elavult] 

A kód a következő lenne kell végrehajtani:

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

Az alábbi útmutatót példákat ad az hibakezelési ADAL módszerekkel együtt: 

- acquireTokenSilentSync(…)
- acquireTokenSilentAsync(...)
- acquireTokenSilent(...) [elavult]

A kód a következő lenne kell végrehajtani:

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

Az alábbi útmutatót példákat ad az hibakezelési ADAL módszerekkel együtt: 

- acquireTokenSilentWithResource(…)

A kód a következő lenne kell végrehajtani:

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

AcquireToken a jogkivonatok beszerzéséhez használt alapértelmezett ADAL módszer. Azokban az esetekben, ahol a felhasználói azonosítót kötelező megadni AcquireToken csendes első jogkivonat beszerzéséhez megkísérli, akkor jeleníti meg felhasználói felület szükség esetén (kivéve, ha PromptBehavior.Never átadott). Olyan esetekben, ahol Alkalmazásidentitás szükség AcquireToken próbál egy token beszerzéséhez, de a felhasználói felület nem jelenik meg, ahogy a felhasználó nem teljes. 

AcquireToken hibák kezelésekor a hibakezelés függ, a platform és, forgatókönyv az alkalmazás próbál elérni. 

Az operációs rendszer is létrehozhat egy hibát, megkövetelő függ az adott alkalmazás hibakezelési készletét. További információkért lásd a "Az operációs rendszer hibái" [hiba- és naplózási referencia](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

- Natív ügyfélalkalmazások számára (iOS, Android, .NET asztali vagy Xamarin)
- Webes alkalmazások, amelyek meg az erőforrás API-t (.NET)
- Egylapos alkalmazások (JavaScript)
- Service-to-Service-alkalmazás (.NET, Java)
  - Minden esetben, beleértve a alapú meghatalmazásos
  - A alapú meghatalmazásos meghatározott forgatókönyvek

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Hiba eseteket és felkínál: natív ügyfélalkalmazások

Natív ügyfélalkalmazás fejleszt, van-e néhány hiba kezelési esetekben érdemes figyelembe venni a hálózati hibákat, átmeneti hibák és egyéb platformspecifikus kapcsolatban. A legtöbb esetben egy alkalmazás nem azonnali újrapróbálkozás, de inkább várja meg, amely felszólítja egy bejelentkezési végfelhasználói beavatkozást. 

Nincsenek néhány speciális eseteket, amelyben egyetlen újrapróbálkozási, azzal megoldhatja a problémát. Például amikor egy felhasználó kell lehetővé teszik az adatok az eszközön vagy az Azure ad-ben broker befejeződött a kezdeti hiba után töltse le. 

Hiba esetben egy alkalmazás felhasználói Felületét, hogy lehetővé teszik a végfelhasználó néhány interakciót, amely felszólítja az ismételt végrehajtásához is jelenthet. Például ha az eszköz offline hiba sikertelen volt, egy AcquireToken kérő üzenet "Próbálja meg újra bejelentkezni" gomb újra ahelyett, hogy a hiba azonnali újrapróbálkozás. 

Hiba történt a natív alkalmazások kezelése két esetben definiálni:

|  |  |
|------|-------------|
| **1. eset**:<br>Nem újrapróbálható hiba (a legtöbb esetben) | 1. Ne kísérelje meg azonnali újrapróbálkozás. Jelenleg a végfelhasználó felhasználói felület az adott hiba, amely meghív egy újrapróbálkozási ("Újra bejelentkezési", "Töltse le az Azure AD-közvetítő alkalmazás" stb.) alapján. |
| **2. eset**:<br>Újrapróbálkozást lehetővé tevő hiba | 1. Előfordulhat, hogy a végfelhasználó számára megadott eredményezi, a siker állapota egy egyetlen újra hajtható végre.<br><br>2. Újrapróbálkozás sikertelen lesz, ha jelenleg a végfelhasználó felhasználói felület az adott hiba, amely meghív egy újrapróbálkozási ("Újra bejelentkezési", "Töltse le az Azure AD-közvetítő alkalmazás", stb.) alapján. |

> [!IMPORTANT]
> Ha egy felhasználói fiókot átadott csendes hívást, és nem az adal-t, a későbbi interaktív kérelem lehetővé teszi, hogy a végfelhasználó jelentkezzen be egy másik fiókot. Egy olyan felhasználói fiókkal a sikeres AcquireToken, miután az alkalmazás ellenőriznie kell, a bejelentkezett felhasználó felel meg az alkalmazások helyi felhasználói objektum. Eltérés nem ad kivételt (kivéve az Objective C), de azokban az esetekben, ahol a felhasználó ismert helyileg előtt a hitelesítési kérések (például egy sikertelen csendes hívás) kell alkalmazni.
>

#### <a name="net"></a>.NET

Az alábbi útmutatót példákat ad az összes nem csendes AcquireToken(...) együtt hibakezelés ADAL módszerek *kivételével*: 

- AcquireTokenAsync (..., IClientAssertionCertification,...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync(…,UserAssertion,…)   

A kód a következő lenne kell végrehajtani:

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
> Támogatja a PromptBehavior.Never, amelynek AcquireTokenSilent viselkedést ADAL .NET rendelkezik egy további figyelembe veszi.
>

Az alábbi útmutatót példákat ad az hibakezelési ADAL módszerekkel együtt: 

- acquireToken(..., PromptBehavior.Never)

A kód a következő lenne kell végrehajtani:

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

Az alábbi útmutatót példákat ad az összes nem csendes AcquireToken(...) együtt hibakezelés ADAL módszerek. 

A kód a következő lenne kell végrehajtani:

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

Az alábbi útmutatót példákat ad az összes nem csendes AcquireToken(...) együtt hibakezelés ADAL módszerek. 

A kód a következő lenne kell végrehajtani:

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

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Hiba eseteket és felkínál: webes alkalmazások, amelyek meg az erőforrás API-t (.NET)

Létrehozásakor egy .NET-webalkalmazás, amely meghívja ezt kér le egy tokent, erőforrás-engedélyezési kód használatával, a csak kód szükséges: egy alapértelmezett kezelő az Általános eset. 

Az alábbi útmutatót példákat ad az hibakezelési ADAL módszerekkel együtt: 

- AcquireTokenByAuthorizationCodeAsync(…)

A kód a következő lenne kell végrehajtani:

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

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Hiba eseteket és felkínál: egyoldalas alkalmazások (adal.js)

Egy egyoldalas alkalmazás adal.js használata AcquireToken létrehozásakor, a hibakezelési kóddal hasonlít, amely egy tipikus csendes hívás. Kifejezetten a adal.js AcquireToken soha nem jelenít meg egy felhasználói Felületet. 

Sikertelen AcquireToken rendelkezik a következő esetekben:

|  |  |
|------|-------------|
| **1. eset**:<br>Az interaktív kéréssel feloldható | 1. Ha login() meghiúsul, ne végezzen azonnali újrapróbálkozást. Egyetlen felhasználói művelet kérni fogja az újrapróbálkozás után ismételje meg.|
| **2. eset**:<br>Nem Resolvable interaktív kéréssel. Hiba – Újrapróbálkozást lehetővé tevő is. | 1. A fő felhasználó megadott eredményezi, a siker állapota egy egyetlen újra hajtható végre.<br><br>2. Újrapróbálkozás sikertelen lesz, ha a végfelhasználónak az adott hiba általános ismételt meghívására képes alapján művelet ("próbálja meg újra bejelentkezni"). |
| **3. eset**:<br>Nem Resolvable interaktív kéréssel. Nem újrapróbálható hiba. | 1. Ne kísérelje meg azonnali újrapróbálkozás. A végfelhasználónak az adott hiba általános ismételt meghívására képes alapján művelet ("próbálja meg újra bejelentkezni"). |

A kód a következő lenne kell végrehajtani:

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

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Hiba eseteket és felkínál: service-to-service-alkalmazások (csak a .NET)

Egy AcquireToken használó service-to-service-alkalmazás létrehozásakor, néhány kulcsfontosságú hibák vannak a kódot kell kezelnie. A hiba csak igénybe, hogy térjen vissza a hiba a hívó alkalmazást (a – meghatalmazásos esetek) vagy a alkalmazni az újrapróbálkozási stratégia. 

#### <a name="all-scenarios"></a>Az összes forgatókönyv

A *összes* service-to-service alkalmazás-forgatókönyvek, beleértve a alapú meghatalmazásos:

- Ne kísérelje meg azonnali újrapróbálkozást. Egyetlen ismételje meg az egyes ADAL kísérlet sikertelen volt a kérelmeket. 
- Csak akkor folytassa újrapróbálkozás után a felhasználó vagy alkalmazás művelet utasításokat egy újra. Például egy démon application set időköz működő kell megvárni a következő esedékes, majd próbálja megismételni.

Az alábbi útmutatót példákat ad az hibakezelési ADAL módszerekkel együtt: 

- AcquireTokenAsync (..., IClientAssertionCertification,...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync (..., UserAssertion,...)

A kód a következő lenne kell végrehajtani:

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

#### <a name="on-behalf-of-scenarios"></a>A alapú meghatalmazásos forgatókönyvek

A *--meghatalmazásos* service-to-service alkalmazási esetekben.

Az alábbi útmutatót példákat ad az hibakezelési ADAL módszerekkel együtt: 

- AcquireTokenAsync (..., UserAssertion,...)

A kód a következő lenne kell végrehajtani:

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

Elkésztettünk egy [teljes minta](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) , amely bemutatja, hogy ebben a forgatókönyvben.

## <a name="error-and-logging-reference"></a>Hiba- és naplózási referencia

### <a name="logging-personal-identifiable-information-pii--organizational-identifiable-information-oii"></a>Naplózás a személyes azonosításra alkalmas adatokat (PII) és a szervezeti azonosításra alkalmas adatok (OII)
ADAL naplózás alapértelmezés szerint nem rögzítése és bármely személyazonosításra alkalmas adatok vagy OII jelentkezzen. A kódtár segítségével az alkalmazásfejlesztők a naplózó osztály egy beállítására szolgáló keresztül a bekapcsolás. Személyazonosításra alkalmas adatok vagy OII bekapcsolásával, az alkalmazás feladata biztonságos nagyon érzékeny adatok kezelésére, és minden olyan szabályozási követelményeknek megfelelő vesz igénybe.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>ADAL-könyvtár hibák

ADAL hibaüzenetek, a forráskódot böngészhet a [azure-activedirectory-erőforrástár-az-dotnet-tárház](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) a legjobb hiba-hivatkozás.

#### <a name="guidance-for-error-logging-code"></a>Útmutató a naplózás hibakód

ADAL .NET naplózási függően változik. a platformot, amelyen dolgozik. Tekintse meg a [naplózási wiki](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) naplózásának engedélyezése a kódot.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>ADAL-könyvtár hibák

ADAL hibaüzenetek, a forráskódot böngészhet a [azure-activedirectory-erőforrástár-for-android-tárház](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) a legjobb hiba-hivatkozás.

#### <a name="operating-system-errors"></a>Az operációs rendszer hibái

Android operációs rendszer hibái az adal-t AuthenticationException keresztül érhetők el, "SERVER_INVALID_REQUEST" azonosíthatóak, és tovább lehet keresztül a hibák leírásának részletes. 

Gyakori hibák és az elvégzendő lépésekkel, ha az alkalmazás vagy a végfelhasználók észlel őket teljes listájáért tekintse meg a [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Útmutató a naplózás hibakód

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

#### <a name="adal-library-errors"></a>ADAL-könyvtár hibák

ADAL hibaüzenetek, a forráskódot böngészhet a [azure-activedirectory-erőforrástár-az-objc tárház](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) a legjobb hiba-hivatkozás.

#### <a name="operating-system-errors"></a>Az operációs rendszer hibái

Ha a felhasználók használnak webes nézetek és hitelesítés jellege iOS-hibák bejelentkezés során fordulhatnak elő. Ennek oka lehet például SSL hibák, vagy időtúllépés, illetve hálózati hibák feltételek:

- Jogosultság a megosztási bejelentkezések nem állandó, és a gyorsítótár üresen jelenik meg. A következő kódsort a kulcslánchoz való felvételével oldható meg: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- A hibák NsUrlDomain van állítva a művelet az alkalmazáslogika függően változik. Tekintse meg a [NSURLErrorDomain dokumentációja](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) lehet kezelni, konkrét példányok esetén.
- Lásd: [ADAL Obj-C gyakori problémák](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) az ADAL Objective-C csapata tartja karban a gyakori hibák listáját.

#### <a name="guidance-for-error-logging-code"></a>Útmutató a naplózás hibakód

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

### <a name="guidance-for-error-logging-code---javascript"></a>Útmutató a hibák naplózásának kóddal – JavaScript 

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

* [Az azure AD fejlesztői útmutató] [AAD-Dev-Guide]
* [Az azure AD hitelesítési Kódtárai] [AAD-hitelesítési-tárak]
* [Az azure AD hitelesítési forgatókönyvei] [AAD-hitelesítés – forgatókönyvek]
* [Alkalmazások integrálása az Azure Active Directory] [AAD-integrálása-alkalmazások]

Használja a megjegyzéseket, visszajelzést és pontosításához, és a tartalom a következő.

[![Jelentkezzen be a gomb][AAD-Sign-In]] [ AAD-Sign-In] 
 <!--Reference style links --> [AAD-hitelesítési-tárak]: [AAD-hitelesítés – forgatókönyvek]./active-directory-authentication-libraries.md: hitelesítés – scenarios.md [AAD-Dev-Guide]: az Azure-ad-fejlesztők számára – guide.md [AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md [AZURE-portál]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

