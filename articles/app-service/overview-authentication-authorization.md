---
title: Hitelesítés és engedélyezés – Azure App Service | Microsoft Docs
description: A Azure App Service hitelesítési/engedélyezési funkciójának fogalmi referenciája és áttekintése
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/24/2018
ms.author: cephalin
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: 53733774968f94ac95d9b3fea6d8fcb422b4e02c
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515171"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Hitelesítés és engedélyezés az Azure App Service-ben

> [!NOTE]
> Jelenleg a HRE v2 (beleértve az MSAL-t is) nem támogatott az Azure App Services és Azure Functions esetén. Tekintse át a frissítéseket.
>

A Azure App Service beépített hitelesítési és engedélyezési támogatást biztosít, így a felhasználók bejelentkezhetnek és hozzáférhetnek az adatokhoz a webalkalmazásban, a REST API-ban és a mobil háttérben, valamint [Azure functions](../azure-functions/functions-overview.md)is. Ez a cikk azt ismerteti, hogyan egyszerűsíthető a App Service az alkalmazás hitelesítésének és engedélyezésének egyszerűsítése. 

A biztonságos hitelesítés és az engedélyezés a biztonság alapos megismerését igényli, beleértve az összevonás, a titkosítás, a [JSON webes tokenek (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) kezelését, a [támogatás típusát](https://oauth.net/2/grant-types/)és így tovább. App Service biztosítja ezeket a segédprogramokat, így több időt és energiát is igénybe vehet, hogy üzleti értéket biztosítson az ügyfelek számára.

> [!NOTE]
> A hitelesítéshez és engedélyezéshez nem szükséges App Service használata. Számos webes keretrendszer biztonsági funkciókkal van ellátva, és igény szerint használhatja őket. Ha több rugalmasságra van szüksége, mint amennyit App Service biztosít, saját segédprogramjait is megírhatja.  
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

### <a name="user-claims"></a>Felhasználói jogcímek

Az összes nyelvi keretrendszer esetében App Service a felhasználói jogcímeket a kód számára elérhetővé teszi a kérések fejlécére. A ASP.net 4,6 alkalmazások esetében app Service a hitelesített felhasználó jogcímeivel tölti fel a [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) értéket, így a szabványos .net-kód mintát is használhatja, `[Authorize]` beleértve az attribútumot is. Hasonlóképpen, a PHP-alkalmazások esetében app Service feltölti `_SERVER['REMOTE_USER']` a változót. Java-alkalmazások esetén a jogcímek a [tomcat servletből érhetők el](containers/configure-language-java.md#authenticate-users-easy-auth).

[](../azure-functions/functions-overview.md) Azurefunctions`ClaimsPrincipal.Current` esetén a nem hidratálja a .net-kódot, de a kérések fejlécében továbbra is megtalálhatja a felhasználói jogcímeket.

További információ: hozzáférés a [felhasználói](app-service-authentication-how-to.md#access-user-claims)jogcímekhez.

### <a name="token-store"></a>Jogkivonat-tároló

A App Service beépített jogkivonat-tárolót biztosít, amely a webalkalmazások, API-k vagy natív mobil alkalmazások felhasználóinak társított jogkivonatok tárháza. Ha bármely szolgáltatóval engedélyezi a hitelesítést, a jogkivonat-tároló azonnal elérhetővé válik az alkalmazás számára. Ha az alkalmazás kódjának a felhasználó nevében kell hozzáférnie a szolgáltatók adataihoz, például: 

- közzététel a hitelesített felhasználó Facebook-idővonalán
- olvassa el a felhasználó vállalati adatait a Azure Active Directory Graph API vagy akár a Microsoft Graph

Általában kódot kell írnia a tokenek összegyűjtéséhez, tárolásához és frissítéséhez az alkalmazásban. A jogkivonat-tárolóval egyszerűen lekérheti [a](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) jogkivonatokat, amikor szüksége van rájuk, és [közli, hogy app Service,](app-service-authentication-how-to.md#refresh-identity-provider-tokens) ha érvénytelenné válnak. 

Az azonosító jogkivonatok, a hozzáférési tokenek és a hitelesített munkamenet számára gyorsítótárazott frissítési tokenek, és csak a társított felhasználó számára érhetők el.  

Ha nem kell jogkivonatokkal dolgoznia az alkalmazásban, akkor letilthatja a jogkivonat-tárolót.

### <a name="logging-and-tracing"></a>Naplózás és nyomkövetés

Ha [engedélyezi az alkalmazások naplózását](troubleshoot-diagnostic-logs.md), a rendszer közvetlenül a naplófájlokban fogja látni a hitelesítési és engedélyezési nyomkövetéseket. Ha olyan hitelesítési hiba jelenik meg, amelyet nem várt, a meglévő alkalmazás-naplók alapján kényelmesen megkeresheti az összes adatot. Ha engedélyezi a [Sikertelen kérelmek nyomkövetését](troubleshoot-diagnostic-logs.md), láthatja, hogy pontosan milyen szerepet játszott a hitelesítési és engedélyezési modul egy sikertelen kérelemben. A nyomkövetési naplók között keresse meg a nevű `EasyAuthModule_32/64`modulra mutató hivatkozásokat. 

## <a name="identity-providers"></a>Identitásszolgáltatók

App Service [összevont identitást](https://en.wikipedia.org/wiki/Federated_identity)használ, amelyben egy harmadik féltől származó identitás-szolgáltató kezeli a felhasználói identitásokat és a hitelesítési folyamatot. Alapértelmezés szerint öt identitás-szolgáltató érhető el: 

| Szolgáltató | Bejelentkezési végpont |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-fiók](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Ha engedélyezi a hitelesítést és az engedélyezést ezen szolgáltatók egyikével, a bejelentkezési végpontja elérhetővé válik a felhasználói hitelesítéshez és a szolgáltatótól származó hitelesítési jogkivonatok érvényesítéséhez. Megadhatja, hogy a felhasználók tetszőleges számú bejelentkezési lehetőséget biztosítson könnyedén. Egy másik identitás-szolgáltatót vagy [saját egyéni identitási megoldást][custom-auth]is integrálhat.

## <a name="authentication-flow"></a>Hitelesítési folyamat

A hitelesítési folyamat az összes szolgáltató esetében azonos, de attól függően különbözik, hogy be kíván-e jelentkezni a szolgáltató SDK-val:

- Szolgáltatói SDK nélkül: Az alkalmazás delegálja az összevont bejelentkezést App Serviceba. Ez általában a böngésző alkalmazásai esetében fordul elő, amely bemutathatja a szolgáltató bejelentkezési lapját a felhasználó számára. A kiszolgálói kód kezeli a bejelentkezési folyamatot, ezért a _kiszolgáló által irányított_ folyamatnak vagy a _kiszolgálói_folyamatnak is nevezik. Ez az eset a böngésző alkalmazásaira vonatkozik. Olyan natív alkalmazásokra is vonatkozik, amelyek a Mobile Apps Client SDK használatával írják alá a felhasználókat, mert az SDK webes nézetet nyit meg a felhasználók App Service hitelesítéssel való aláírásához. 
- Szolgáltatói SDK-val: Az alkalmazás manuálisan aláírja a felhasználókat a szolgáltatóhoz, majd elküldi a hitelesítési jogkivonatot az ellenőrzéshez App Service. Ez általában a böngésző nélküli alkalmazások esetében fordul elő, amely nem tudja bemutatni a szolgáltató bejelentkezési lapját a felhasználónak. Az alkalmazás kódja kezeli a bejelentkezési folyamatot, ezért az _ügyfél által irányított_ folyamatnak vagy az _ügyfél_folyamatának is nevezik. Ez az eset a REST API- [](../azure-functions/functions-overview.md)kra, a Azure Functionsra és a JavaScript böngésző ügyfeleire, valamint a bejelentkezési folyamat nagyobb rugalmasságot igénylő böngésző-alkalmazásokra vonatkozik. Olyan natív Mobile apps-alkalmazásokra is vonatkozik, amelyek a szolgáltató SDK használatával írják alá a felhasználókat.

> [!NOTE]
> Egy megbízható böngészőből érkező hívások hívása a App Service egy másik REST API a App Service vagy a [Azure functions](../azure-functions/functions-overview.md) hitelesíthető a kiszolgáló által irányított folyamat használatával. További információ: [a hitelesítés és az engedélyezés testreszabása app Serviceban](app-service-authentication-how-to.md).
>

Az alábbi táblázat a hitelesítési folyamat lépéseit mutatja be.

| Lépés | Szolgáltatói SDK nélkül | Szolgáltatói SDK-val |
| - | - | - |
| 1. Felhasználó aláírása itt: | Átirányítja az ügyfelet a `/.auth/login/<provider>`következőre:. | Az ügyfél kódja közvetlenül a szolgáltató SDK-val aláírja a felhasználót, és hitelesítési jogkivonatot kap. További információt a szolgáltató dokumentációjában talál. |
| 2. Utóhitelesítés | A szolgáltató átirányítja az `/.auth/login/<provider>/callback`ügyfelet a következőre:. | Az ügyfél [](app-service-authentication-how-to.md#validate-tokens-from-providers) kódja a szolgáltatótól `/.auth/login/<provider>` kapott jogkivonatot érvényesíti. |
| 3. Hitelesített munkamenet létrehozása | App Service a hitelesített cookie-t adja hozzá válaszként. | App Service visszaadja a saját hitelesítési tokenjét az ügyfél kódjához. |
| 4. Hitelesített tartalom kiszolgálása | Az ügyfél hitelesítési cookie-t is tartalmaz a következő kérelmekben (a böngésző automatikusan kezeli). | Az ügyfél kódja a (Mobile apps `X-ZUMO-AUTH` ügyféloldali SDK-k által automatikusan kezelt) fejlécben található hitelesítési tokent jeleníti meg. |

Az ügyféloldali böngészők esetében a App Service automatikusan irányíthatja az összes nem hitelesített `/.auth/login/<provider>`felhasználót. A felhasználók egy vagy több `/.auth/login/<provider>` hivatkozással is bejelentkezhetnek az alkalmazásba, ha az Ön által választott szolgáltatón keresztül jelentkeznek be.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Engedélyezési viselkedés

A [Azure Portal](https://portal.azure.com)számos viselkedéssel konfigurálhatja app Service engedélyezést.

![](media/app-service-authentication-overview/authorization-flow.png)

A következő címsorok leírják a beállításokat.

### <a name="allow-all-requests-default"></a>Összes kérelem engedélyezése (alapértelmezett)

A hitelesítést és az engedélyezést nem a App Service felügyeli (kikapcsolva). 

Akkor válassza ezt a lehetőséget, ha nincs szükség hitelesítésre és engedélyezésre, vagy ha a saját hitelesítési és engedélyezési kódját szeretné írni.

### <a name="allow-only-authenticated-requests"></a>Csak hitelesített kérelmek engedélyezése

A beállítás a  **\<szolgáltató > bejelentkezni**. App Service átirányítja az összes névtelen kérelmet `/.auth/login/<provider>` a kiválasztott szolgáltatóhoz. Ha a névtelen kérelem egy natív mobil alkalmazásból származik, a visszaadott válasz egy `HTTP 401 Unauthorized`.

Ezzel a beállítással nem kell bármilyen hitelesítési kódot írnia az alkalmazásban. A felhasználó jogcímeinek vizsgálatával a finomabb engedélyezés, például a szerepkör-specifikus hitelesítés kezelhető (lásd: [hozzáférés a felhasználói](app-service-authentication-how-to.md#access-user-claims)jogcímekhez).

### <a name="allow-all-requests-but-validate-authenticated-requests"></a>Minden kérelem engedélyezése, de hitelesített kérelmek ellenőrzése

A beállítás **Névtelen kérelmeket tesz lehetővé**. Ez a beállítás bekapcsolja a hitelesítést és az engedélyezést App Serviceban, de az engedélyezési döntéseket az alkalmazás kódjára elhalasztja. A hitelesített kérések esetében App Service a HTTP-fejlécekben is továbbítja a hitelesítési adatokat. 

Ez a lehetőség nagyobb rugalmasságot biztosít a névtelen kérelmek kezelésére. Például lehetővé teszi, hogy [több bejelentkezési szolgáltatót nyújtson](app-service-authentication-how-to.md#use-multiple-sign-in-providers) be a felhasználók számára. Azonban kódot kell írnia. 

## <a name="more-resources"></a>További források

[Oktatóanyag: Felhasználók hitelesítése és engedélyezése Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Oktatóanyag: Felhasználók hitelesítése és engedélyezése a Azure App Service Linux rendszeren](containers/tutorial-auth-aad.md)  
[A hitelesítés és az engedélyezés testreszabása App Service](app-service-authentication-how-to.md)

Szolgáltatóra vonatkozó útmutatók:

* [Az alkalmazás konfigurálása az Azure Active Directory-bejelentkezés használatára][AAD]
* [Az alkalmazás konfigurálása a Facebook-bejelentkezés használatára][Facebook]
* [Az alkalmazás konfigurálása a Google-bejelentkezés használatára][Google]
* [Az alkalmazás konfigurálása a Microsoft-fiókbejelentkezés használatára][MSA]
* [Az alkalmazás konfigurálása a Twitter-bejelentkezés használatára][Twitter]
* [Útmutató: Egyéni hitelesítés használata az alkalmazáshoz][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
