---
title: "Hiba történt az ajánlott eljárások az Azure Active Directory Authentication Library (ADAL) ügyfelek kezelése"
description: "Útmutatás és ajánlott eljárások az ADAL ügyfélalkalmazások hibakezelési biztosít."
services: active-directory
documentationcenter: 
author: danieldobalian
manager: mtillman
ms.author: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.custom: 
ms.openlocfilehash: 082953eb860197d2188851f6c8be260797d6ce6d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Hiba történt az ajánlott eljárások az Azure Active Directory Authentication Library (ADAL) ügyfelek kezelése

Ez a cikk nyújt útmutatást a hibák típusát, hogy a fejlesztők, használata során felmerülő adal-t a felhasználók hitelesítéséhez. Adal-t használ, amikor nincsenek több olyan esetben, amikor előfordulhat, hogy a fejlesztők lépést, és a hibák kezelésének kell. Megfelelő hibakezelés egy nagyszerű végfelhasználói élményt biztosítja, és korlátozza a szám, ahányszor a felhasználó be kell jelentkeznie.

Ebben a cikkben megismerkedhet azt az adott esetben adal-t, és hogy az alkalmazás kezelni tud a minden esetben megfelelően által támogatott platformokon. A hiba útmutatás két nagyobb kategóriák, az ADAL API-k által biztosított token beszerzési minták alapján oszlik:

- **AcquireTokenSilent**: ügyfél megpróbálja csendes szolgáltatáshitelesítést egy token (nincs felhasználói felület), és meghiúsulhat, ha nem sikerül az adal-t. 
- **AcquireToken**: ügyfél csendes beszerzési megpróbálhat, de interaktív bejelentkezési igénylő kérelmek is végrehajthat.

> [!TIP]
> Célszerű naplózza az összes hibákat és kivételeket, amikor az adal-t és az Azure AD használatával. Naplók nem csak segít annak megértésében, az alkalmazás általános állapotát, de számára is fontos, amikor szélesebb körű problémák. Előfordulhat, hogy az alkalmazás bizonyos hibák elhárítása, amíg azok feloldásához kódmódosításokat igénylő átfogóbb tervezési problémák, előfordulhat, hogy mutatót. 
> 
> A jelen dokumentum ismerteti megvalósító a hibaállapotokat, amikor a korábban tárgyalt okok miatt a hibakódot és a leírást kell jelentkezik. Tekintse meg a [hiba és naplózási hivatkozás](#error-and-logging-reference) példák a naplózás kódot. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent megpróbálja beolvasni a garantált, hogy a végfelhasználó nem látja a felhasználói felület (UI) jogkivonatot. Nincsenek, több olyan esetekben, ahol a beavatkozás nélküli megszerzése sikertelen lehet, és interaktív kéréseken keresztüli vagy egy alapértelmezett kezelő által kezelt igényeinek. Azt a mintaadatokról idejének és módjának alkalmaz minden esetben a következő szakaszok a alaposabban.

Nincs szükség lehet az alkalmazásra vonatkoznak, hibakezelési operációs rendszer által generált hibák készlete. További információkért lásd: "Operációs rendszer" hibák szakasz [hiba és naplózási hivatkozás](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

- [Natív ügyfél](active-directory-dev-glossary.md#native-client) alkalmazások (iOS, Android, .NET asztali vagy Xamarin)
- [Webes ügyfél](active-directory-dev-glossary.md#web-client) hívó alkalmazásokhoz egy [erőforrás](active-directory-dev-glossary.md#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Hibák és végrehajtandó lépések

Alapvetően két olyan eset létezik AcquireTokenSilent hibák:

| Eset | Leírás |
|------|-------------|
| **1. eset**: hiba feloldható a egy interaktív bejelentkezés: | Érvényes jogkivonatokat hiánya által okozott hibákat interaktív kérelmet szükség. Pontosabban gyorsítótárban való kereséshez és egy érvénytelen vagy lejárt frissítési jogkivonat szükséges egy AcquireToken hívás megoldásához.<br><br>Ebben az esetben a végfelhasználónak kell jelentkezzen be a rendszer kérni. Az alkalmazás beállíthatja hajtson végre egy interaktív kérelem azonnal, végfelhasználói beavatkozást (például szerezze meg a bejelentkezési gomb) után, vagy később. A választott attól függ, hogy az alkalmazás kívánt viselkedése.<br><br>Ebben az esetben és akkor diagnosztizálhatja hibákat a következő szakaszban kiadott kód megtekintésére.|
| **2. eset**: Hiba: nem rendelkező interaktív bejelentkezési feloldható | A hálózati és átmeneti/ideiglenesen hibák, vagy más hibák az interaktív AcquireToken kérelem végrehajtása nem oldja meg a problémát. Szükségtelen interaktív bejelentkezési kérések is képes azoknak a végfelhasználók számára. Adal-t automatikusan megkísérli egy egyetlen újrapróbálkozás AcquireTokenSilent hibáiról leggyakoribb hibák.<br><br>Az ügyfélalkalmazás is megpróbálhatja később bármikor újrapróbálkozást, de mikor és hogyan kell tenni az alkalmazás viselkedését, és a kívánt végfelhasználói élmény függ. Például az alkalmazás teheti meg egy AcquireTokenSilent próbálkozzon újra néhány perc múlva, vagy az egyes végfelhasználói műveletre adott válasz. Egy azonnali újrapróbálkozási az alkalmazás szabályozva eredményez, és nem kell próbálni.<br><br>Ugyanaz a hiba miatt sikertelenül működő későbbi újrapróbálkozást nem jelenti azt, az ügyfél egy interaktív AcquireToken, kérelmet kell tennie, módon nem oldja meg a hiba.<br><br>Ebben az esetben és akkor diagnosztizálhatja hibákat a következő szakaszban kiadott kód megtekintésére. |

### <a name="net"></a>.NET

A következő útmutatást példákat hibakezelési ADAL módszerekkel együtt: 

- acquireTokenSilentAsync(…)
- acquireTokenSilentSync(…) 
- [elavult] acquireTokenSilent(...)
- [elavult] acquireTokenByRefreshToken(...) 

A kód volna kell végrehajtani az alábbiak szerint:

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

A következő útmutatást példákat hibakezelési ADAL módszerekkel együtt: 

- acquireTokenSilentSync(…)
- acquireTokenSilentAsync(...)
- [elavult] acquireTokenSilent(...)

A kód volna kell végrehajtani az alábbiak szerint:

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

A következő útmutatást példákat hibakezelési ADAL módszerekkel együtt: 

- acquireTokenSilentWithResource(…)

A kód volna kell végrehajtani az alábbiak szerint:

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

AcquireToken jogkivonatok lekérésére használt alapértelmezett ADAL módszer. Azokban az esetekben, ahol szükség-e felhasználói identitást AcquireToken szolgáltatáshitelesítést egy token csendes első megkísérli, akkor jeleníti meg felhasználói felület szükség esetén (kivéve, ha a PromptBehavior.Never átadása). Azokban az esetekben, ahol szükség-e az Alkalmazásidentitás AcquireToken megkísérli a szolgáltatáshitelesítést egy token, de felület nem jeleníthető meg, amíg nem felhasználó.  

AcquireToken hibák kezelésekor hibakezelés függ, a platform és az alkalmazás forgatókönyv próbál elérése.  

Az operációs rendszer is hozhat létre a hibákat, függ az adott alkalmazás hibakezelési igénylő készlete. További információkért lásd: "Rendszerhibák" a [hiba és naplózási hivatkozás](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

- Natív ügyfél-alkalmazások (iOS, Android, .NET asztali vagy Xamarin)
- Webes alkalmazásokhoz, amelyek egy erőforrás API (.NET)
- Egylapos alkalmazások (JavaScript)
- Szolgáltatások közötti applications (.NET, Java)
  - Minden forgatókönyvek, köztük a-nevében-:
  - A-nevében-a meghatározott forgatókönyvek

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Hibák és végrehajtandó lépések: natív ügyfél-alkalmazások

Egy natív ügyfélalkalmazás most létrehozása, ha van néhány kezelési hibaeseteknél figyelembe kell venni a hálózati problémák, átmeneti hibák és más platform-specifikus hibák kapcsolatban. A legtöbb esetben az alkalmazás ne hajtsa végre a közvetlen újrapróbálkozások, de inkább várja meg, amely felszólítja a bejelentkezés végfelhasználói beavatkozást.  

Nincsenek néhány bizonyos esetekben egy egyetlen újra azzal megoldhatja a problémát. Például amikor egy felhasználó egy eszközhöz engedélyeznie kell a, vagy az Azure AD broker befejeződött letöltésére, miután a kezdeti hiba. 

Hiba esetén egy alkalmazás felhasználói Felületről lehetővé teszik a végfelhasználó néhány újrapróbálkozást kér beavatkozás végrehajtásához is jelenthet. Például az eszköz nem sikerült a kapcsolat nélküli hiba, ha egy AcquireToken értesítése "Próbálja meg újból bejelentkezni" gomb újra ahelyett, hogy azonnal ismételt próbálkozás a hiba. 

Hiba történt a natív alkalmazások kezelése két esetben definiálni:

|  |  |
|------|-------------|
| **1. eset**:<br>Újrapróbálkozást lehetővé nem hiba (a legtöbb esetben) | 1. Ne kísérelje meg azonnali újrapróbálkozási. A felhasználói felület az adott hiba, amely hívja meg ("Ismételje meg a bejelentkezési", "Az Azure AD letöltési broker alkalmazás" stb) újrapróbálkozást alapján végfelhasználói jelenthet. |
| **2. eset**:<br>Újrapróbálkozást lehetővé tevő hiba | 1. Előfordulhat, hogy a végfelhasználó megadott eredményez a siker állapota egyetlen újrapróbálkozást hajtható végre.<br><br>2. Nem sikerül az újra gombra, ha jelentenek a végfelhasználó felhasználói felület az adott hiba, amely hívja meg ("Ismételje meg a bejelentkezési", "Az Azure AD letöltési közvetítőalkalmazást" stb.) újbóli kísérlet alapján. |

> [!IMPORTANT]
> Ha egy felhasználói fiókot csendes hívás és meghiúsul az adal-ra van átadva, interaktív kérést lehetővé teszi, hogy a felhasználó a bejelentkezés más fiók használatával. A sikeres AcquireToken felhasználói fiókkal az alkalmazás ellenőrizze a bejelentkezett felhasználó felel az alkalmazások helyi felhasználói objektum. Eltérés nem hoz létre egy kivételt (kivéve az Objective C), de azokban az esetekben, ahol a felhasználó ismert helyileg a hitelesítési kérések (például egy sikertelen csendes meghívása) előtt kell tekinteni.
>

#### <a name="net"></a>.NET

A következő útmutatást példákat hiba történt az összes nem néma AcquireToken(...) együtt kezelése ADAL módszerek *kivéve*: 

- AcquireTokenAsync (..., IClientAssertionCertification,...)
- AcquireTokenAsync (..., SecurityMode,...)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync(…,UserAssertion,…)   

A kód volna kell végrehajtani az alábbiak szerint:

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
> ADAL .NET egy további figyelembe megegyezik az támogatja-e PromptBehavior.Never, amely AcquireTokenSilent hasonlóan viselkedik.
>

A következő útmutatást példákat hibakezelési ADAL módszerekkel együtt: 

- acquireToken(..., PromptBehavior.Never)

A kód volna kell végrehajtani az alábbiak szerint:

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

A következő útmutatást példákat hiba történt az összes nem néma AcquireToken(...) együtt kezelése ADAL módszerek. 

A kód volna kell végrehajtani az alábbiak szerint:

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

A következő útmutatást példákat hiba történt az összes nem néma AcquireToken(...) együtt kezelése ADAL módszerek. 

A kód volna kell végrehajtani az alábbiak szerint:

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

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Hibák és végrehajtandó lépések: webes alkalmazások, amelyek egy erőforrás API (.NET)

Ha most létrehozása, amely meghívja a .NET-webalkalmazás lekérdezi az engedélyezési kód használatával erőforrás jogkivonatot, a csak a kódot az általános esetben alapértelmezett leíró. 

A következő útmutatást példákat hibakezelési ADAL módszerekkel együtt: 

- AcquireTokenByAuthorizationCodeAsync(…)

A kód volna kell végrehajtani az alábbiak szerint:

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

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Hibák és végrehajtandó lépések: egyetlen oldal alkalmazások (adal.js)

Ha egy egyoldalas alkalmazás adal.js használata AcquireToken most felépítése, hibakezelési kód hasonlít egy tipikus csendes hívás.  Kifejezetten a adal.js AcquireToken soha nem jelenít meg felhasználói Felületet. 

Nem sikerült AcquireToken rendelkezik a következő esetekben:

|  |  |
|------|-------------|
| **1. eset**:<br>Az interaktív kéréssel feloldható | 1. Ha nem sikerül login(), ne hajtsa végre azonnali újrapróbálkozási. Csak felhasználói művelet újrapróbálkozást kér után próbálkozzon újra.|
| **2. eset**:<br>Nem Resolvable interaktív kéréssel. Hiba: Újrapróbálkozást lehetővé tevő. | 1. A fő végfelhasználó megadott eredményez a siker állapota egyetlen újrapróbálkozást hajtható végre.<br><br>2. Ha ismét sikertelen, a végfelhasználónak a konkrét hiba meghívására képes egy újabb műveletet ("próbálja meg újból bejelentkezni"). |
| **3. eset**:<br>Nem Resolvable interaktív kéréssel. Hiba: nem Újrapróbálkozást lehetővé tevő. | 1. Ne kísérelje meg azonnali újrapróbálkozási. A végfelhasználónak a konkrét hiba meghívására képes egy újabb műveletet ("próbálja meg újból bejelentkezni"). |

A kód volna kell végrehajtani az alábbiak szerint:

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

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Hibák és végrehajtandó lépések: szolgáltatások alkalmazások (csak a .NET)

Egy szolgáltatások közötti alkalmazás AcquireToken most létrehozása, ha néhány főbb hibák vannak a kódot kell kezelni. A hiba csak igénybe, hogy térjen vissza a hiba a hívó alkalmazás (a nevében-az esetek) vagy egy újrapróbálkozási stratégiát alkalmazni. 

#### <a name="all-scenarios"></a>Az összes forgatókönyv

A *összes* service-to-service alkalmazás-forgatókönyveket, a-nevében-, beleértve:

- Ne kísérelje meg egy azonnali újrapróbálkozási. A sikertelen kérelmek ADAL kísérletek egyetlen egyes próbálja meg újra. 
- Csak továbbra is újrapróbálkozás után egy felhasználó vagy alkalmazás műveletet kér egy újra. Például egy démon alkalmazás, amely a készlet időköz működik, majd ismételje meg a következő intervallum várnia kell.

A következő útmutatást példákat hibakezelési ADAL módszerekkel együtt: 

- AcquireTokenAsync (..., IClientAssertionCertification,...)
- AcquireTokenAsync (..., SecurityMode,...)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync (..., UserAssertion,...)

A kód volna kell végrehajtani az alábbiak szerint:

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

#### <a name="on-behalf-of-scenarios"></a>A-nevében-: forgatókönyvek

A *a nevében-az* service-to-service alkalmazás-forgatókönyveket.

A következő útmutatást példákat hibakezelési ADAL módszerekkel együtt: 

- AcquireTokenAsync(…, UserAssertion, …)

A kód volna kell végrehajtani az alábbiak szerint:

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

A Microsoft létrehozott egy [teljes mintát](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) ebben a forgatókönyvben azt mutatja be.

## <a name="error-and-logging-reference"></a>Hiba és a naplózás

### <a name="logging-personal-identifiable-information-pii--organizational-identifiable-information-oii"></a>Naplózás a személyes azonosításra alkalmas adatokat (PII) & szervezeti azonosításra alkalmas adatok (OII)
Alapértelmezés szerint a ADAL naplózási rögzítése vagy nem bármely személyhez köthető adatokat és OII naplózása. A könyvtár lehetővé teszi, hogy az alkalmazásfejlesztők keresztül a tranzakciónaplókat tartalmazó osztály setter a bekapcsolás. Ha bekapcsolja a személyhez köthető adatokat és OII, az alkalmazás felelősséget biztonságosan nagyon érzékeny adatok kezelésére, és a szabályozási követelményeknek megfelelő.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>ADAL-könyvtár hibák

Felfedezése, mely bizonyos ADAL hibákat, a kódot a [azure-Active Directoryban – könyvtár-az-dotnet tárház](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) a legjobb hiba hivatkozás.

#### <a name="guidance-for-error-logging-code"></a>Útmutató a naplózás hibakód

ADAL .NET naplózási módosításokat végzett platformtól függően. Tekintse meg a [naplózási wiki](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) naplózásának engedélyezéséről a kódot.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>ADAL-könyvtár hibák

Felfedezése, mely bizonyos ADAL hibákat, a kódot a [azure activedirectory-könyvtár-az-android-tárház](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) a legjobb hiba hivatkozás.

#### <a name="operating-system-errors"></a>Operációs rendszer hibái

Android operációs rendszer hibák az adal-t AuthenticationException keresztül közzétett, "SERVER_INVALID_REQUEST" azonosíthatóak, és további lehet részletes a hibák leírásának keresztül. 

Gyakori hibák, és Mit hajtson végre az alkalmazás vagy a végfelhasználók előforduló azokat teljes listájáért tekintse meg a [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

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

// PII or OII will NOT be logged
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>ADAL-könyvtár hibák

Felfedezése, mely bizonyos ADAL hibákat, a kódot a [azure-Active Directoryban – könyvtár-az-objc tárház](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) a legjobb hiba hivatkozás.

#### <a name="operating-system-errors"></a>Operációs rendszer hibái

iOS hibák esetleg felmerülő bejelentkezés során, amikor a felhasználók a webes nézetek és hitelesítés jellege használják. Ezt okozhatja például az SSL hibák, időtúllépések és hálózati hibák feltételek:

- A jogosultság megosztásához bejelentkezések nincsenek állandó, és a gyorsítótár üres megjelenik. A következő kódsort felvételével a kulcslánc oldhatja meg: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- A hibák, a művelet attól függően változik, az alkalmazás logikai NsUrlDomain halmaza. Tekintse meg a [NSURLErrorDomain referenciadokumentációt](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) konkrét példányok tudnak kezelni.
- Lásd: [ADAL Obj-C kapcsolatos gyakori hibák](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) az ADAL Objective-C csoport által karbantartott gyakori hibák listája.

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

### <a name="guidance-for-error-logging-code---javascript"></a>Útmutató következő hibanaplózás: - JavaScript 

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

* [Az Azure AD fejlesztői útmutató][AAD-Dev-Guide]
* [Az Azure AD-hitelesítési kódtárakkal][AAD-Auth-Libraries]
* [Az Azure AD hitelesítési forgatókönyvei][AAD-Auth-Scenarios]
* [Alkalmazások integrálása az Azure Active Directoryval][AAD-Integrating-Apps]

Használja a következő, visszajelzést, és segítsen pontosítsa és a tartalom Megjegyzések szakaszban.

[![Jelentkezzen be a gomb][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->
[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

