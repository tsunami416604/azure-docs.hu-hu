---
title: Hitelesítési és engedélyezési az Azure App Service szolgáltatásban |} Microsoft Docs
description: Fogalmi referenciája és áttekintése a hitelesítési / engedélyezési a beállítást, az Azure App Service
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/29/2016
ms.author: mahender
ms.openlocfilehash: c180dcf5d769245f3fa2485ccee2cbc18ecf5f67
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Hitelesítés és engedélyezés az Azure App Service-ben

Az Azure App Service biztosít, beépített hitelesítési és engedélyezési támogatja, így a felhasználók bejelentkezhetnek, és adatelérés írása minimális, illetve nem kód a webalkalmazás, az API és a mobil háttérből, valamint [Azure Functions](../azure-functions/functions-overview.md). Ez a cikk ismerteti, hogyan App Service egyszerűbbé teszi a hitelesítési és engedélyezési az alkalmazásra vonatkozóan. 

Biztonságos hitelesítési és engedélyezési szükséges ismeri részletesen biztonsági összevonási, beleértve a titkosítást, [JSON webes jogkivonatok (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) felügyeleti, [típusok biztosítani](https://oauth.net/2/grant-types/), és így tovább. App Service ezeket a segédprogramokat biztosít, így több idő és energia képes költeni üzleti értéket szolgáltatásában az ügyfélnek.

> [!NOTE]
> Ön nem szükséges az App Service használata a hitelesítéshez és engedélyezéshez. Sok webes keretrendszerek vannak becsomagolva biztonsági szolgáltatásokat, és ha szeretné használni őket. App Service biztosít, mint nagyobb rugalmasságot van szüksége, is írhatnak a saját segédprogramok.  
>

Natív mobilalkalmazások vonatkozó információkért lásd: [felhasználó hitelesítése és engedélyezése az Azure App Service mobile apps](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Működés

A hitelesítési és engedélyezési modul, az alkalmazás kódjában azonos védőfal futtatja. Ha engedélyezve van, minden bejövő HTTP-kérelem haladnak keresztül, mielőtt az alkalmazás kódjában kezeli.

![](media/app-service-authentication-overview/architecture.png)

Ez a modul kezeli az alkalmazás számos dolgot:

- Hitelesíti a felhasználókat a megadott szolgáltatón.
- Ellenőrzi, tárolja, és frissíti a jogkivonatok
- Kezeli a hitelesített munkamenet
- Azonosító adatok kérelemfejléc be esetében

A modul külön futtatja az alkalmazás kódjáról, és alkalmazás-beállításokkal van konfigurálva. Nincs SDK-k, bizonyos nyelveken vagy az alkalmazás kódjának módosítások szükségesek. 

### <a name="user-claims"></a>Felhasználói jogcímek

Az összes nyelvi keretrendszert az App Service elérhetővé a felhasználói jogcímek a kódot úgy, hogy azok be a kérelem fejlécében. Az ASP.NET 4.6 alkalmazásokat az App Service tölti fel [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) a hitelesített felhasználó JOGCÍMEKKEL rendelkező és a szabványos .NET kódmintát követhesse, beleértve a `[Authorize]` attribútum. Hasonlóképpen, a PHP-alkalmazások esetén az App Service tölti fel a `_SERVER['REMOTE_USER']` változó.

A [Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` van nem hidratált a .NET-kódot, de továbbra is a felhasználói jogcímek a kérelem fejlécében.

További információkért lásd: [eléréséhez a felhasználói jogcímek](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Jogkivonat-tároló

App Service egy beépített jogkivonat-tároló, amely a felhasználók a web apps, az API-k vagy a natív mobilalkalmazások társított tokenek tára biztosít. Ha engedélyezi a hitelesítést a bármely szolgáltató, a jogkivonat-tároló érhető el azonnal az alkalmazáshoz. Ha az alkalmazás kódjában adatok eléréséhez a felhasználó nevében, a szolgáltatóktól, mint: 

- küldje el a hitelesített felhasználó Facebook ütemterv
- a felhasználó vállalati adatokat olvasni az Azure Active Directory Graph API vagy akár a Microsoft Graph

Az azonosító-jogkivonatokat, a hozzáférési jogkivonatok és a frissítési jogkivonatokat a gyorsítótárban a hitelesített munkamenet, és elérhető-e a kapcsolódó felhasználó csak fontosságúak.  

Kód gyűjtésére, tárolására és a frissítés ezeket a jogkivonatokat, az alkalmazás általában kell írnia. A jogkivonat-tároló, a most [lekérni a jogkivonatokat](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) Ha szüksége van rájuk és [kérje meg őket frissíteni az App Service](app-service-authentication-how-to.md#refresh-access-tokens) mikor válik érvénytelen. 

Ha nem szeretne dolgozni a jogkivonatok az alkalmazásban, letilthatja a jogkivonat-tároló.

### <a name="logging-and-tracing"></a>Naplózás és nyomkövetés

Ha Ön [alkalmazásnaplózás engedélyezése](web-sites-enable-diagnostic-log.md), hitelesítési és engedélyezési nyomkövetési adatokat közvetlenül a naplófájlok a látni fogja. Nem várt hitelesítési hibaüzenet jelenik meg, ha a meglévő alkalmazásnaplók keresésével kényelmesen megtalálhatja részleteit. Ha engedélyezi a [sikertelen kérelmek követésének](web-sites-enable-diagnostic-log.md), láthatja pontosan milyen szerepet a hitelesítési és engedélyezési modul lehetséges, hogy a sikertelen kérelmek játszott. A nyomkövetési naplók keresse meg nevű modulra mutató hivatkozást `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Identitásszolgáltatók

App Service az [összevont identitás](https://en.wikipedia.org/wiki/Federated_identity), amelyben egy külső identitásszolgáltatótól kezeli a felhasználói identitások és a hitelesítési folyamat meg. Alapértelmezés szerint öt identitás-szolgáltatóktól érhetők el: 

| Szolgáltató | Bejelentkezési végpont |
| - | - |
| [Azure Active Directory](../active-directory/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-fiók](../active-directory/develop/active-directory-appmodel-v2-overview.md) | `/.auth/login/microsoft` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/docs/basics/authentication) | `/.auth/login/twitter` |

Ha engedélyezi a hitelesítési és engedélyezési e szolgáltatók egyikét, a bejelentkezési végpont érhető el a felhasználók hitelesítéséhez és a hitelesítési tokenek az szolgáltató-ellenőrzéshez. Megadhatja a felhasználók tetszőleges számú, bejelentkezési beállítás alkalmazásaiba. Integrálhatja más identitásszolgáltató vagy [saját egyéni identitáskezelési megoldás][custom-auth].

## <a name="authentication-flow"></a>Hitelesítési folyamat

A hitelesítési folyamat megegyezik a minden szolgáltató, de eltér attól függően, hogy a szolgáltató SDK bejelentkezni:

- Szolgáltató SDK nélkül: az alkalmazás összevont bejelentkezés az App Service delegálja. Ez helyzet általában a böngésző alkalmazásokkal, amelyek jelenthet, a szolgáltató bejelentkezési oldal a felhasználó számára. A kiszolgálóoldali kódban kezeli a bejelentkezési folyamat, így is nevezik _kiszolgáló által vezérelt folyamat_ vagy _server folyamat_. Ebben az esetben a webalkalmazások vonatkozik. A Mobile Apps-ügyfél SDK használatával, mert az SDK az App Service hitelesítés bejelentkeztetik a felhasználókat a webes nézet megnyitása a felhasználók bejelentkeznek natív alkalmazások is vonatkozik. 
- SDK-szolgáltatóval: az alkalmazás felhasználó jelentkezik be manuálisan, és ezután elküldi a hitelesítési lexikális eleme az App Service érvényesítése. Ez helyzet általában a böngésző nélküli alkalmazásokkal, amely nem jelent-e a szolgáltató bejelentkezési oldal a felhasználó számára. Az alkalmazás kódjának kezeli a bejelentkezési folyamat, így is nevezik _ügyfél által vezérelt folyamat_ vagy _ügyfél folyamatában_. Ebben az esetben a REST API-k, vonatkozik [Azure Functions](../azure-functions/functions-overview.md), és webböngésző JavaScript-ügyfelek számára, valamint nagyobb rugalmasságot biztosítanak a bejelentkezési folyamat a szükséges webes alkalmazások. A szolgáltató SDK használatával a felhasználók bejelentkeznek natív mobilalkalmazások is vonatkozik.

> [!NOTE]
> Az App Service szolgáltatásban egy megbízható böngészőalkalmazás hívásait meghívja a REST API-t egy másik App Service-ben vagy [Azure Functions](../azure-functions/functions-overview.md) használ a kiszolgáló által vezérelt folyamat hitelesítheti. További információkért lásd: [hitelesíti a felhasználókat az Azure App Service]().
>

Az alábbi táblázat bemutatja a hitelesítési folyamat lépéseit.

| Lépés | Szolgáltató SDK nélkül | SDK-szolgáltatóval |
| - | - | - |
| 1. A bejelentkezési felhasználói | Átirányítja az ügyfelet `/.auth/login/<provider>`. | Ügyfélkód felhasználó jelentkezik be közvetlenül az SDK-szolgáltató, és egy hitelesítési jogkivonatot kap. Tanulmányozza a szolgáltató dokumentációját. |
| 2. Utóhitelesítés | Szolgáltató átirányítja az ügyfelet `/.auth/login/<provider>/callback`. | Ügyfélkód visszaküldés jogkivonat-szolgáltatót, hogy a `/.auth/login/<provider>` érvényesítéshez. |
| 3. Hitelesített munkamenetet | App Service hitelesített cookie-t ad választ. | App Service a saját hitelesítési jogkivonat ügyfél kódot ad vissza. |
| 4. Hitelesített tartalmat | Ügyfél hitelesítési cookie-t (a böngésző automatikusan kezeli) kérelmeknél tartalmazza. | Ügyfélkód megadja a hitelesítési jogkivonat `X-ZUMO-AUTH` fejléc (a Mobile Apps-ügyfél SDK-k automatikusan kezeli). |

Az ügyfelek böngészőin, az App Service automatikusan is közvetlen minden nem hitelesített felhasználókat `/.auth/login/<provider>`. Egy vagy több is jelenthet felhasználók `/.auth/login/<provider>` jelentkezzen be az alkalmazás a kiválasztott szolgáltatóval mutató hivatkozásokat tartalmaz.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Engedélyezési viselkedése

Az a [Azure-portálon](https://portal.azure.com), az App Service engedélyezési számos viselkedés konfigurálható.

![](media/app-service-authentication-overview/authorization-flow.png)

A következő fejlécek beállításokat ismertetik.

### <a name="allow-all-requests-default"></a>Engedélyezi az összes kérelem (alapértelmezett)

Hitelesítési és engedélyezési nem kezeli az App Service (kikapcsolt). 

Válassza ezt a beállítást, ha nincs szüksége a hitelesítési és engedélyezési, vagy ha azt szeretné, hogy a saját hitelesítési és engedélyezési kód írása.

### <a name="allow-only-authenticated-requests"></a>Csak a hitelesített kérések engedélyezése

A beállítás **jelentkezzen be az \<szolgáltató >**. App Service átirányítja a felhasználókat az összes névtelen kérésekkel `/.auth/login/<provider>` úgy dönt, a szolgáltató. Ha a névtelen kérelem natív mobilalkalmazás származik, a rendszer a visszaadott választ egy `HTTP 401 Unauthorized`.

Ezzel a beállítással nem kell az alkalmazás hitelesítési kód írása. Pontosabban engedélyezési, mint például a szerepkör-specifikus, kezelhetők a felhasználói jogcímek vizsgálatával (lásd: [eléréséhez a felhasználói jogcímek](app-service-authentication-how-to.md#access-user-claims)).

### <a name="allow-all-requests-but-validate-authenticated-requests"></a>Engedélyezi az összes kérelmet, de hitelesített kérelmek ellenőrzése

A beállítás **engedélyezése névtelen kérelmek**. Ez a beállítás bekapcsolja a hitelesítési és engedélyezési az App Service-ben, de eltér az alkalmazás kódjának felhasználását engedélyezési döntésekhez. Hitelesített kéréseknél az App Service is továbbítja a HTTP-fejlécek hitelesítési adatok mentén. 

Ez a beállítás nagyobb rugalmasságot biztosítanak a kezelési névtelen kérelem biztosít. Például lehetővé teszi, hogy [jelenleg több bejelentkezési lehetőségek](app-service-authentication-how-to.md#configure-multiple-sign-in-options) a felhasználók számára. Azonban hogy írhat kódot. 

## <a name="more-resources"></a>További erőforrások

[Oktatóanyag: Helyszerepkörre, és felhasználók-végpont az Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Oktatóanyag: Helyszerepkörre, és felhasználók-végpont az Azure App Service Linux](containers/tutorial-auth-aad.md)  
[Testre szabhatja a hitelesítési és engedélyezési az App Service-ben](app-service-authentication-how-to.md)

Szolgáltatói útmutatókat:

* [Az alkalmazás Azure Active Directory bejelentkezési használandó konfigurálása][AAD]
* [Az alkalmazás használatához Facebook bejelentkezési konfigurálása][Facebook]
* [Az alkalmazás használatához Google bejelentkezési konfigurálása][Google]
* [Az alkalmazás használatához Microsoft Account bejelentkezés konfigurálása][MSA]
* [Az alkalmazás használatához Twitter bejelentkezési konfigurálása][Twitter]
* [Útmutató: az alkalmazás egyéni hitelesítés használata][custom-auth]

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
