---
title: Hitelesítés és engedélyezés
description: Ismerje meg a Azure App Service beépített hitelesítési és engedélyezési támogatását, valamint azt, hogy miként segítheti az alkalmazás védelmét a jogosulatlan hozzáférés ellen.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: ff0eb102d37f285279c041ff91b7a89e157259eb
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672247"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Hitelesítés és engedélyezés az Azure App Service-ben

> [!NOTE]
> Jelenleg a HRE v2 (beleértve az MSAL-t is) nem támogatott az Azure App Services és Azure Functions esetén. Tekintse át a frissítéseket.
>

A Azure App Service beépített hitelesítési és engedélyezési támogatást biztosít, így a felhasználók bejelentkezhetnek és hozzáférhetnek az adatokhoz a webalkalmazásban, a REST API-ban és a mobil háttérben, valamint [Azure functions](../azure-functions/functions-overview.md)is. Ez a cikk azt ismerteti, hogyan egyszerűsíthető a App Service az alkalmazás hitelesítésének és engedélyezésének egyszerűsítése.

A biztonságos hitelesítés és az engedélyezés a biztonság alapos megismerését igényli, beleértve az összevonás, a titkosítás, a [JSON webes tokenek (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) kezelését, a [támogatás típusát](https://oauth.net/2/grant-types/)és így tovább. App Service biztosítja ezeket a segédprogramokat, így több időt és energiát is igénybe vehet, hogy üzleti értéket biztosítson az ügyfelek számára.

> [!IMPORTANT]
> Nem szükséges App Service használni a AuthN/AuthO. Számos webes keretrendszer biztonsági funkciókkal van ellátva, és igény szerint használhatja őket. Ha több rugalmasságra van szüksége, mint amennyit App Service biztosít, saját segédprogramjait is megírhatja.  
>
> Ha azonban a távoli hitelesítéshez nem App Service lehetőségek bármelyikét használja, vegye figyelembe, hogy a [Chrome 80 a cookie-k SameSite-re való bevezetését](https://www.chromestatus.com/feature/5088147346030592) (kiadás dátuma: március 2020), és az alkalmazás hitelesítési mechanizmusa megszakíthatja az ügyféloldali böngészők frissítésének időpontját. Az ASP.NET Core dokumentációja arról tartalmaz információt, hogyan kezelheti ezt az alkalmazásban, a [http: Browser SameSite módosítja a hatás hitelesítését](/dotnet/core/compatibility/3.0-3.1#http-browser-samesite-changes-impact-authentication). Hasznos útmutatást nyújt arról, hogyan tesztelheti ezt a feltörési változást a főbb böngészőknél, függetlenül attól, hogy ASP.NET Core vagy sem használ.
>

A natív Mobile apps szolgáltatással kapcsolatos információkért lásd: a [felhasználói hitelesítés és a mobileszközök engedélyezése a Azure app Service](../app-service-mobile/app-service-mobile-auth.md)használatával.

## <a name="how-it-works"></a>Működési elv

A hitelesítési és engedélyezési modul ugyanazon a Sandboxon fut, mint az alkalmazás kódja. Ha engedélyezve van, minden bejövő HTTP-kérelem áthalad az alkalmazás kódjának kezelése előtt.

![](media/app-service-authentication-overview/architecture.png)

Ez a modul több dolgot kezel az alkalmazásban:

- A felhasználók hitelesítése a megadott szolgáltatóval
- Jogkivonatok ellenőrzése, tárolása és frissítése
- Felügyeli a hitelesített munkamenetet.
- Azonosító adatokat szúr be a kérelem fejlécbe

A modul külön fut az alkalmazás kódjától, és az Alkalmazásbeállítások használatával van konfigurálva. Nem szükségesek SDK-k, meghatározott nyelvek vagy az alkalmazás kódjának módosítása. 

### <a name="user-claims"></a>Felhasználói jogcímek

Az összes nyelvi keretrendszer esetében App Service a felhasználói jogcímeket a kód számára elérhetővé teszi a kérések fejlécére. A ASP.NET 4,6-alkalmazások esetében App Service a hitelesített felhasználó jogcímeivel tölti fel a [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) értéket, így a standard .net-kód mintát is használhatja, beleértve a `[Authorize]` attribútumot is. Hasonlóképpen, a PHP-alkalmazások esetében a App Service tölti fel a `_SERVER['REMOTE_USER']` változót. Java-alkalmazások esetén a jogcímek a [tomcat servletből érhetők el](containers/configure-language-java.md#authenticate-users-easy-auth).

[Azure functions](../azure-functions/functions-overview.md)esetén a `ClaimsPrincipal.Current` nem hidratálja a .net-kódot, de továbbra is megtalálhatja a felhasználói jogcímeket a kérések fejlécében.

További információ: hozzáférés a [felhasználói jogcímekhez](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Jogkivonat-tároló

A App Service beépített jogkivonat-tárolót biztosít, amely a webalkalmazások, API-k vagy natív mobil alkalmazások felhasználóinak társított jogkivonatok tárháza. Ha bármely szolgáltatóval engedélyezi a hitelesítést, a jogkivonat-tároló azonnal elérhetővé válik az alkalmazás számára. Ha az alkalmazás kódjának a felhasználó nevében kell hozzáférnie a szolgáltatók adataihoz, például: 

- közzététel a hitelesített felhasználó Facebook-idővonalán
- olvassa el a felhasználó vállalati adatait a Azure Active Directory Graph API vagy akár a Microsoft Graph

Általában kódot kell írnia a tokenek összegyűjtéséhez, tárolásához és frissítéséhez az alkalmazásban. A jogkivonat-tárolóval egyszerűen [lekérheti a jogkivonatokat](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) , amikor szüksége van rájuk, és [közli, hogy app Service,](app-service-authentication-how-to.md#refresh-identity-provider-tokens) ha érvénytelenné válnak. 

Az azonosító jogkivonatok, a hozzáférési tokenek és a hitelesített munkamenet számára gyorsítótárazott frissítési tokenek, és csak a társított felhasználó számára érhetők el.  

Ha nem kell jogkivonatokkal dolgoznia az alkalmazásban, akkor letilthatja a jogkivonat-tárolót.

### <a name="logging-and-tracing"></a>Naplózás és nyomkövetés

Ha [engedélyezi az alkalmazások naplózását](troubleshoot-diagnostic-logs.md), a rendszer közvetlenül a naplófájlokban fogja látni a hitelesítési és engedélyezési nyomkövetéseket. Ha olyan hitelesítési hiba jelenik meg, amelyet nem várt, a meglévő alkalmazás-naplók alapján kényelmesen megkeresheti az összes adatot. Ha engedélyezi a [Sikertelen kérelmek nyomkövetését](troubleshoot-diagnostic-logs.md), láthatja, hogy pontosan milyen szerepet játszott a hitelesítési és engedélyezési modul egy sikertelen kérelemben. A nyomkövetési naplók között keressen `EasyAuthModule_32/64`nevű modulra mutató hivatkozásokat. 

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
> Egy megbízható böngészőből érkező hívások hívása a App Service egy másik REST API a App Service vagy a [Azure functions](../azure-functions/functions-overview.md) hitelesíthető a kiszolgáló által irányított folyamat használatával. További információ: [a hitelesítés és az engedélyezés testreszabása app Serviceban](app-service-authentication-how-to.md).
>

Az alábbi táblázat a hitelesítési folyamat lépéseit mutatja be.

| Lépés: | Szolgáltatói SDK nélkül | Szolgáltatói SDK-val |
| - | - | - |
| 1. Jelentkezzen be a felhasználóba | Átirányítja az ügyfelet `/.auth/login/<provider>`ra. | Az ügyfél kódja közvetlenül a szolgáltató SDK-val aláírja a felhasználót, és hitelesítési jogkivonatot kap. További információt a szolgáltató dokumentációjában talál. |
| 2. hitelesítés utáni | A szolgáltató átirányítja az ügyfelet `/.auth/login/<provider>/callback`ba. | Az ügyfél kódja a [szolgáltatótól kapott jogkivonatot](app-service-authentication-how-to.md#validate-tokens-from-providers) az ellenőrzéshez `/.auth/login/<provider>`. |
| 3. hitelesített munkamenet létrehozása | App Service a hitelesített cookie-t adja hozzá válaszként. | App Service visszaadja a saját hitelesítési tokenjét az ügyfél kódjához. |
| 4. hitelesített tartalom kiszolgálása | Az ügyfél hitelesítési cookie-t is tartalmaz a következő kérelmekben (a böngésző automatikusan kezeli). | Az ügyfél kódja `X-ZUMO-AUTH` fejlécben lévő hitelesítési jogkivonatot jeleníti meg (amelyet automatikusan Mobile Apps ügyféloldali SDK-k kezelnek). |

Az ügyféloldali böngészők esetében a App Service automatikusan irányíthatja az összes nem hitelesített felhasználót `/.auth/login/<provider>`. A felhasználók egy vagy több `/.auth/login/<provider>`-hivatkozással is bejelentkezhetnek az alkalmazásba, ha az Ön által választott szolgáltatón keresztül jelentkeznek be.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Engedélyezési viselkedés

A [Azure Portalban](https://portal.azure.com)számos viselkedést konfigurálhat app Service engedélyezéshez, ha a bejövő kérelem nincs hitelesítve.

![](media/app-service-authentication-overview/authorization-flow.png)

A következő címsorok leírják a beállításokat.

### <a name="allow-anonymous-requests-no-action"></a>Névtelen kérelmek engedélyezése (nincs művelet)

Ez a beállítás elhalasztja az alkalmazás kódjához való nem hitelesített forgalom engedélyezését. A hitelesített kérések esetében App Service a HTTP-fejlécekben is továbbítja a hitelesítési adatokat. 

Ez a lehetőség nagyobb rugalmasságot biztosít a névtelen kérelmek kezelésére. Például lehetővé teszi, hogy [több bejelentkezési szolgáltatót nyújtson](app-service-authentication-how-to.md#use-multiple-sign-in-providers) be a felhasználók számára. Azonban kódot kell írnia. 

### <a name="allow-only-authenticated-requests"></a>Csak hitelesített kérelmek engedélyezése

A beállítás **bejelentkezik \<szolgáltató >** . App Service átirányítja az összes névtelen kérelmet a kiválasztott szolgáltató `/.auth/login/<provider>`éhez. Ha a névtelen kérelem egy natív mobil alkalmazásból származik, a visszaadott válasz egy `HTTP 401 Unauthorized`.

Ezzel a beállítással nem kell bármilyen hitelesítési kódot írnia az alkalmazásban. A felhasználó jogcímeinek vizsgálatával a finomabb engedélyezés, például a szerepkör-specifikus hitelesítés kezelhető (lásd: [hozzáférés a felhasználói jogcímekhez](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> A hozzáférés ezen a módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos, ha az alkalmazások nyilvánosan elérhető kezdőlapot szeretnének, például sok egyoldalas alkalmazásban.

## <a name="more-resources"></a>További források

[Oktatóanyag: Azure App Service teljes körű hitelesítése és engedélyezése a felhasználók számára (Windows)](app-service-web-tutorial-auth-aad.md)  
[Oktatóanyag: a felhasználók teljes körű hitelesítése és engedélyezése Azure App Service Linux rendszeren](containers/tutorial-auth-aad.md)  
[A hitelesítés és az engedélyezés testreszabása App Service](app-service-authentication-how-to.md)

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
