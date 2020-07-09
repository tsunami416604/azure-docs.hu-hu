---
title: Hitelesítés és engedélyezés
description: Ismerje meg a Azure App Service és Azure Functions beépített hitelesítési és engedélyezési támogatását, valamint azt, hogy miként védheti meg alkalmazásait a jogosulatlan hozzáférés ellen.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 04/15/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: f51a396e997a9e6392f3e86a6f77e581753d6ada
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83196437"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Hitelesítés és engedélyezés Azure App Service és Azure Functions

> [!NOTE]
> Ekkor a ASP.NET Core jelenleg nem támogatja az aktuális felhasználó feltöltését a hitelesítési/engedélyezési szolgáltatással.
>

A Azure App Service beépített hitelesítési és engedélyezési támogatást biztosít, így a felhasználók bejelentkezhetnek és hozzáférhetnek az adatokhoz a webalkalmazásban, a REST API-ban és a mobil háttérben, valamint [Azure functions](../azure-functions/functions-overview.md)is. Ez a cikk azt ismerteti, hogyan egyszerűsíthető a App Service az alkalmazás hitelesítésének és engedélyezésének egyszerűsítése.

A biztonságos hitelesítés és az engedélyezés a biztonság alapos megismerését igényli, beleértve az összevonás, a titkosítás, a [JSON webes tokenek (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) kezelését, a [támogatás típusát](https://oauth.net/2/grant-types/)és így tovább. App Service biztosítja ezeket a segédprogramokat, így több időt és energiát is igénybe vehet, hogy üzleti értéket biztosítson az ügyfelek számára.

> [!IMPORTANT]
> A szolgáltatás használata nem kötelező a hitelesítéshez és az engedélyezéshez. Használhatja a választott webes keretrendszer csomagban található biztonsági funkcióit, vagy megírhatja saját segédprogramjait is. Ne feledje azonban, hogy a [Chrome 80 a cookie-k SameSite-re való bevezetését](https://www.chromestatus.com/feature/5088147346030592) (2020. március), valamint az egyéni távoli hitelesítést vagy más, a helyek közötti cookie-kat használó egyéb forgatókönyveket is megszakíthatja az ügyfél Chrome-böngészők frissítésekor. A megkerülő megoldás összetett, mert a különböző böngészőkhöz különböző SameSite-viselkedéseket kell támogatni. 
>
> A App Service által üzemeltetett ASP.NET Core 2,1-es és újabb verziók már nem javítottak ehhez a feltörési változáshoz, és a Chrome 80 és a régebbi böngészők megfelelő kezelését végzik. Továbbá ugyanez a javítás a ASP.NET-keretrendszer 4.7.2 is üzembe kerül a App Service példányokon a januári 2020-es verzióban. További információért, például arról, hogy miként fogadta el az alkalmazás a javítást, tekintse meg a [Azure app Service SameSite-cookie frissítése](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/)című témakört.
>

A natív Mobile apps szolgáltatással kapcsolatos információkért lásd: a [felhasználói hitelesítés és a mobileszközök engedélyezése a Azure app Service](../app-service-mobile/app-service-mobile-auth.md)használatával.

## <a name="how-it-works"></a>Működés

A hitelesítési és engedélyezési modul ugyanazon a Sandboxon fut, mint az alkalmazás kódja. Ha engedélyezve van, minden bejövő HTTP-kérelem áthalad az alkalmazás kódjának kezelése előtt.

![](media/app-service-authentication-overview/architecture.png)

Ez a modul több dolgot kezel az alkalmazásban:

- A felhasználók hitelesítése a megadott szolgáltatóval
- Jogkivonatok ellenőrzése, tárolása és frissítése
- Felügyeli a hitelesített munkamenetet.
- Azonosító adatokat szúr be a kérelem fejlécbe

A modul külön fut az alkalmazás kódjától, és az Alkalmazásbeállítások használatával van konfigurálva. Nem szükségesek SDK-k, meghatározott nyelvek vagy az alkalmazás kódjának módosítása. 

### <a name="userapplication-claims"></a>Felhasználói/alkalmazási jogcímek

Az összes nyelvi keretrendszer esetében a App Service a bejövő jogcímek (azaz egy hitelesített végfelhasználó vagy ügyfélalkalmazás) által a kód számára elérhető jogcímeket a kérések fejlécére szúrja be. A ASP.NET 4,6 alkalmazások esetében App Service a hitelesített felhasználó jogcímeivel tölti fel a [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) értéket, így a szabványos .net-kód mintát is használhatja, beleértve az `[Authorize]` attribútumot is. Hasonlóképpen, a PHP-alkalmazások esetében App Service feltölti a `_SERVER['REMOTE_USER']` változót. Java-alkalmazások esetén a jogcímek a [tomcat servletből érhetők el](containers/configure-language-java.md#authenticate-users-easy-auth).

[Azure functions](../azure-functions/functions-overview.md)esetén a `ClaimsPrincipal.Current` .net-kód nem töltődik fel, de továbbra is megkeresheti a felhasználói jogcímeket a kérések fejlécében, vagy lekérheti az `ClaimsPrincipal` objektumot a kérelem környezetében, vagy akár egy kötési paraméteren keresztül is. További információért lásd: [az ügyfél-identitások használata](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) .

További információ: hozzáférés a [felhasználói jogcímekhez](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Jogkivonat-tároló

A App Service beépített jogkivonat-tárolót biztosít, amely a webalkalmazások, API-k vagy natív mobil alkalmazások felhasználóinak társított jogkivonatok tárháza. Ha bármely szolgáltatóval engedélyezi a hitelesítést, a jogkivonat-tároló azonnal elérhetővé válik az alkalmazás számára. Ha az alkalmazás kódjának a felhasználó nevében kell hozzáférnie a szolgáltatók adataihoz, például: 

- közzététel a hitelesített felhasználó Facebook-idővonalán
- a felhasználó vállalati adatai beolvasása a Microsoft Graph API használatával

Általában kódot kell írnia a tokenek összegyűjtéséhez, tárolásához és frissítéséhez az alkalmazásban. A jogkivonat-tárolóval egyszerűen [lekérheti a jogkivonatokat](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) , amikor szüksége van rájuk, és [közli, hogy app Service,](app-service-authentication-how-to.md#refresh-identity-provider-tokens) ha érvénytelenné válnak. 

Az azonosító tokenek, a hozzáférési tokenek és a frissítési tokenek gyorsítótárazva vannak a hitelesített munkamenet számára, és csak a hozzárendelt felhasználó férhetnek hozzájuk.  

Ha nem kell jogkivonatokkal dolgoznia az alkalmazásban, akkor letilthatja a jogkivonat-tárolót.

### <a name="logging-and-tracing"></a>Naplózás és nyomkövetés

Ha [engedélyezi az alkalmazások naplózását](troubleshoot-diagnostic-logs.md), a rendszer közvetlenül a naplófájlokban fogja látni a hitelesítési és engedélyezési nyomkövetéseket. Ha olyan hitelesítési hiba jelenik meg, amelyet nem várt, a meglévő alkalmazás-naplók alapján kényelmesen megkeresheti az összes adatot. Ha engedélyezi a [Sikertelen kérelmek nyomkövetését](troubleshoot-diagnostic-logs.md), láthatja, hogy pontosan milyen szerepet játszott a hitelesítési és engedélyezési modul egy sikertelen kérelemben. A nyomkövetési naplók között keresse meg a nevű modulra mutató hivatkozásokat `EasyAuthModule_32/64` . 

## <a name="identity-providers"></a>Identitásszolgáltatók

App Service [összevont identitást](https://en.wikipedia.org/wiki/Federated_identity)használ, amelyben egy harmadik féltől származó identitás-szolgáltató kezeli a felhasználói identitásokat és a hitelesítési folyamatot. Alapértelmezés szerint öt identitás-szolgáltató érhető el: 

| Szolgáltató | Bejelentkezési végpont |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-fiók](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Ha engedélyezi a hitelesítést és az engedélyezést ezen szolgáltatók egyikével, a bejelentkezési végpontja elérhetővé válik a felhasználói hitelesítéshez és a szolgáltatótól származó hitelesítési jogkivonatok érvényesítéséhez. Megadhatja, hogy a felhasználók tetszőleges számú bejelentkezési lehetőséget biztosítson könnyedén. Egy másik identitás-szolgáltatót vagy [saját egyéni identitási megoldást][custom-auth]is integrálhat.

## <a name="authentication-flow"></a>Hitelesítési folyamat

A hitelesítési folyamat az összes szolgáltató esetében azonos, de attól függően különbözik, hogy be kíván-e jelentkezni a szolgáltató SDK-val:

- Szolgáltatói SDK nélkül: az alkalmazás delegálja az összevont bejelentkezést App Serviceba. Ez általában a böngésző alkalmazásai esetében fordul elő, amely bemutathatja a szolgáltató bejelentkezési lapját a felhasználó számára. A kiszolgálói kód kezeli a bejelentkezési folyamatot, ezért a _kiszolgáló által irányított_ folyamatnak vagy a _kiszolgálói_folyamatnak is nevezik. Ez az eset a böngésző alkalmazásaira vonatkozik. Olyan natív alkalmazásokra is vonatkozik, amelyek a Mobile Apps Client SDK használatával írják alá a felhasználókat, mert az SDK webes nézetet nyit meg a felhasználók App Service hitelesítéssel való aláírásához. 
- A Provider SDK használatával: az alkalmazás manuálisan aláírja a felhasználókat a szolgáltatóhoz, majd elküldi a hitelesítési jogkivonatot az ellenőrzéshez App Service. Ez általában a böngésző nélküli alkalmazások esetében fordul elő, amely nem tudja bemutatni a szolgáltató bejelentkezési lapját a felhasználónak. Az alkalmazás kódja kezeli a bejelentkezési folyamatot, ezért az _ügyfél által irányított_ folyamatnak vagy az _ügyfél_folyamatának is nevezik. Ez az eset a REST API-kra, a [Azure Functionsra](../azure-functions/functions-overview.md)és a JavaScript böngésző ügyfeleire, valamint a bejelentkezési folyamat nagyobb rugalmasságot igénylő böngésző-alkalmazásokra vonatkozik. Olyan natív Mobile apps-alkalmazásokra is vonatkozik, amelyek a szolgáltató SDK használatával írják alá a felhasználókat.

> [!NOTE]
> A App Service egy megbízható böngészőből kezdeményezett hívásokat a App Service vagy [Azure functions](../azure-functions/functions-overview.md) egy másik REST API a kiszolgáló által irányított folyamattal lehet hitelesíteni. További információ: [a hitelesítés és az engedélyezés testreszabása app Serviceban](app-service-authentication-how-to.md).
>

Az alábbi táblázat a hitelesítési folyamat lépéseit mutatja be.

| Lépés | Szolgáltatói SDK nélkül | Szolgáltatói SDK-val |
| - | - | - |
| 1. Jelentkezzen be a felhasználóba | Átirányítja az ügyfelet a következőre: `/.auth/login/<provider>` . | Az ügyfél kódja közvetlenül a szolgáltató SDK-val aláírja a felhasználót, és hitelesítési jogkivonatot kap. További információt a szolgáltató dokumentációjában talál. |
| 2. hitelesítés utáni | A szolgáltató átirányítja az ügyfelet a következőre: `/.auth/login/<provider>/callback` . | Az ügyfél kódja a [szolgáltatótól kapott jogkivonatot](app-service-authentication-how-to.md#validate-tokens-from-providers) `/.auth/login/<provider>` érvényesíti. |
| 3. hitelesített munkamenet létrehozása | App Service a hitelesített cookie-t adja hozzá válaszként. | App Service visszaadja a saját hitelesítési tokenjét az ügyfél kódjához. |
| 4. hitelesített tartalom kiszolgálása | Az ügyfél hitelesítési cookie-t is tartalmaz a következő kérelmekben (a böngésző automatikusan kezeli). | Az ügyfél kódja a `X-ZUMO-AUTH` (Mobile apps ügyféloldali SDK-k által automatikusan kezelt) fejlécben található hitelesítési tokent jeleníti meg. |

Az ügyféloldali böngészők esetében a App Service automatikusan irányíthatja az összes nem hitelesített felhasználót `/.auth/login/<provider>` . A felhasználók egy vagy több `/.auth/login/<provider>` hivatkozással is bejelentkezhetnek az alkalmazásba, ha az Ön által választott szolgáltatón keresztül jelentkeznek be.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Engedélyezési viselkedés

A [Azure Portalban](https://portal.azure.com)számos viselkedést konfigurálhat app Service engedélyezéshez, ha a bejövő kérelem nincs hitelesítve.

![](media/app-service-authentication-overview/authorization-flow.png)

A következő címsorok leírják a beállításokat.

### <a name="allow-anonymous-requests-no-action"></a>Névtelen kérelmek engedélyezése (nincs művelet)

Ez a beállítás elhalasztja az alkalmazás kódjához való nem hitelesített forgalom engedélyezését. A hitelesített kérések esetében App Service a HTTP-fejlécekben is továbbítja a hitelesítési adatokat. 

Ez a lehetőség nagyobb rugalmasságot biztosít a névtelen kérelmek kezelésére. Például lehetővé teszi, hogy [több bejelentkezési szolgáltatót nyújtson](app-service-authentication-how-to.md#use-multiple-sign-in-providers) be a felhasználók számára. Azonban kódot kell írnia. 

### <a name="allow-only-authenticated-requests"></a>Csak hitelesített kérelmek engedélyezése

A beállítás **bejelentkezik a szolgáltatással \<provider> **. App Service átirányítja az összes névtelen kérelmet `/.auth/login/<provider>` a kiválasztott szolgáltatóhoz. Ha a névtelen kérelem egy natív mobil alkalmazásból származik, a visszaadott válasz egy `HTTP 401 Unauthorized` .

Ezzel a beállítással nem kell bármilyen hitelesítési kódot írnia az alkalmazásban. A felhasználó jogcímeinek vizsgálatával a finomabb engedélyezés, például a szerepkör-specifikus hitelesítés kezelhető (lásd: [hozzáférés a felhasználói jogcímekhez](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> A hozzáférés ezen a módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos, ha az alkalmazások nyilvánosan elérhető kezdőlapot szeretnének, például sok egyoldalas alkalmazásban.

> [!NOTE]
> A hitelesítés/engedélyezés korábban egyszerű hitelesítésnek volt ismert.
>

## <a name="more-resources"></a>További erőforrások

[Oktatóanyag: Azure App Service teljes körű hitelesítése és engedélyezése a felhasználók számára (Windows)](app-service-web-tutorial-auth-aad.md)  
[Oktatóanyag: a felhasználók teljes körű hitelesítése és engedélyezése Azure App Service Linux rendszeren](containers/tutorial-auth-aad.md)  
[A hitelesítés és az engedélyezés testreszabása app Service](app-service-authentication-how-to.md) 
 [Az Azure AppService EasyAuth .net Core-integrációja (harmadik fél)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) 
 [Azure app Service hitelesítés használata a .net Core-ban (harmadik fél)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)

Szolgáltatóra vonatkozó útmutatók:

* [Az alkalmazás konfigurálása az Azure Active Directory-bejelentkezés használatára][AAD]
* [Az alkalmazás konfigurálása a Facebook-bejelentkezés használatára][Facebook]
* [Az alkalmazás konfigurálása a Google-bejelentkezés használatára][Google]
* [Az alkalmazás konfigurálása a Microsoft-fiókbejelentkezés használatára][MSA]
* [Az alkalmazás konfigurálása a Twitter-bejelentkezés használatára][Twitter]
* [Útmutató: egyéni hitelesítés használata az alkalmazáshoz][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
