---
title: Hitelesítés és engedélyezés az Azure App Service szolgáltatásban |} A Microsoft Docs
description: Fogalmi referenciája és áttekintése a hitelesítési / engedélyezési funkció az Azure App Service-ben
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
ms.author: mahender,cephalin
ms.openlocfilehash: 6aa7f8c3b9d21d9c55aee3ce49f2bc140769a855
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408064"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Hitelesítés és engedélyezés az Azure App Service-ben

Az Azure App Service biztosít beépített hitelesítés és engedélyezés támogatja, így a felhasználók, és minimális írása vagy a web App alkalmazásban, az API és a mobil háttérrendszer kódot nem adatok eléréséhez, valamint [Azure Functions](../azure-functions/functions-overview.md). Ez a cikk bemutatja, hogyan App Service egyszerűbbé teszi a hitelesítés és engedélyezés az alkalmazáshoz. 

Biztonságos hitelesítés és engedélyezés szükséges ismeri részletesen biztonságát, összevonást, beleértve a titkosítást, [JSON web tokenek (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) felügyeleti, [engedélyezési típusok](https://oauth.net/2/grant-types/), és így tovább. App Service ezeket a segédprogramokat biztosít, így több időt és energiát Bajos dolgaival, hogy az ügyfelek üzleti értéket.

> [!NOTE]
> Ön nem szükséges az App Service használata a hitelesítéshez és engedélyezéshez. Számos webes keretrendszer biztonsági funkciókkal vannak csoportosítva, és igény szerint használhatja őket. Ha nagyobb rugalmasságot, mint az App Service biztosítja, a saját segédprogramok is kiírhatja.  
>

Natív mobilalkalmazások vonatkozó információkért lásd: [felhasználó hitelesítése és engedélyezése az Azure App Service mobile apps](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Működés

A hitelesítési és engedélyezési modul fut, az alkalmazáskódban az azonos tesztkörnyezetben. Ha engedélyezve van, minden bejövő HTTP-kérés haladnak keresztül, az alkalmazáskód által kezelt meglepően előtt.

![](media/app-service-authentication-overview/architecture.png)

Ez a modul kezeli az alkalmazás több szempontot:

- A megadott szolgáltató használatával hitelesítheti a felhasználókat
- Ellenőrzi, tárolja és frissíti a jogkivonatok
- Kezeli a hitelesített munkamenet
- Azonosító adatok kérelem fejlécként kódtárba

A modul külön-külön futtatja az alkalmazás kódjában, és Alkalmazásbeállítások használatával van konfigurálva. Egy SDK-k, a bizonyos nyelveken, vagy a módosítások az alkalmazás kódjának sem szükséges. 

### <a name="user-claims"></a>Felhasználói jogcímek

Az összes nyelvi keretrendszert App Service-ben elérhetővé a felhasználói jogcímek a kód által a kérelem fejlécében való injektálása őket. ASP.NET 4.6-alkalmazások esetén az App Service tölti fel [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) a hitelesített felhasználói jogcímeket, az így követheti a standard szintű .NET-kódmintát, beleértve a `[Authorize]` attribútum. Hasonlóképpen, a PHP-alkalmazások esetén az App Service tölti fel a `_SERVER['REMOTE_USER']` változó.

A [Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` van a .NET-kód, nem hidratált, de továbbra is találhatja meg a felhasználói jogcímek a kérelem fejlécében.

További információkért lásd: [eléréséhez a felhasználói jogcímek](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Jogkivonat-tároló

Az App Service biztosítja egy beépített jogkivonat-tároló, amely a jogkivonatokat, amelyeket a webalkalmazások, API-k vagy natív mobilalkalmazásokat a felhasználókhoz vannak társítva. Ha engedélyezi a hitelesítést a bármely szolgáltatónál, az alkalmazás azonnal érhető el a jogkivonat-tároló. Ha az alkalmazás kódjában kell elérni az adatokat az ilyen szolgáltatók, a felhasználó nevében, például: 

- Közzététel a Facebook-idővonalon a hitelesített felhasználó
- a felhasználó vállalati adatokat olvasni az Azure Active Directory Graph API vagy akár a Microsoft Graph

Általában kell írnia a kódot gyűjtése, tárolása és frissítése az alkalmazásban ezek a jogkivonatok. A jogkivonat-tároló az imént [a jogkivonatok](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) amikor szüksége van rájuk, és [állapítható meg, hogy frissíteni őket az App Service](app-service-authentication-how-to.md#refresh-access-tokens) mikor válik érvénytelen. 

Az azonosító-jogkivonatokat, hozzáférési jogkivonatok és frissítési biztonsági jogkivonat a gyorsítótárban a hitelesített munkamenet, és csak az a felhasználó által elérhető számukra.  

Ha nem kell az alkalmazás a jogkivonatok használata, letilthatja a jogkivonat-tároló.

### <a name="logging-and-tracing"></a>Naplózás és nyomkövetés

Ha Ön [alkalmazásnaplózás engedélyezése](web-sites-enable-diagnostic-log.md), hitelesítési és engedélyezési nyomkövetések látni fogja a naplófájlok közvetlenül a. Nem várt dátumértékek hitelesítési hibaüzenet jelenik meg, ha a meglévő alkalmazás naplók alapján kényelmesen megtalálhatja a részletekről. Ha engedélyezi a [sikertelen kérelmek követésének](web-sites-enable-diagnostic-log.md), láthatja pontosan milyen szerepet a hitelesítési és engedélyezési modul játszott előfordulhat, hogy a sikertelen kérelem. Az a nyomkövetési naplókat, keresse meg a hivatkozások nevű modulra `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Identitásszolgáltatók

App Service-ben használt [összevont identitás](https://en.wikipedia.org/wiki/Federated_identity), amelyben egy harmadik féltől származó identitásszolgáltatót kezeli a felhasználói identitások és a hitelesítési folyamat az Ön számára. Alapértelmezés szerint elérhetők a öt identitásszolgáltató használatához: 

| Szolgáltató | Bejelentkezési végpont |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-fiók](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Ha engedélyezi a hitelesítés és engedélyezés az egyik ilyen szolgáltatók, a bejelentkezési végpont használata felhasználói hitelesítéshez, és a szolgáltató által a hitelesítési tokenek ellenőrzésére szolgáló érhető el. Tetszőleges számú ezek bejelentkezési lehetőségek közül a felhasználók könnyedén biztosíthat. Egy másik identitásszolgáltatót is integrálhatja vagy [a saját egyéni identitáskezelési megoldás][custom-auth].

## <a name="authentication-flow"></a>A hitelesítési folyamatból

A hitelesítési folyamat megegyezik az összes, de az eltér attól függően, hogy a szolgáltató SDK felhasználóval való bejelentkezéshez:

- Szolgáltatói SDK nélkül: az alkalmazás összevont bejelentkezés App Service-ben irányelvmodulnak delegálja. Ez általában a helyzet a böngészőben megjelenő alkalmazásokba, amelyhez a felhasználónak a szolgáltató bejelentkezési oldal is jelenthet. A kiszolgálói kód a bejelentkezési folyamat kezeli, ezért a más néven _kiszolgáló által vezérelt folyamat_ vagy _server flow_. Ebben az esetben a webes alkalmazásokra vonatkozik. Natív alkalmazások, a Mobile Apps ügyfél-SDK használatával, mert az SDK-t, amelyekben az App Service-hitelesítés bejelentkezhetnek a felhasználók a webes nézet nyílik meg a felhasználók bejelentkezhetnek is vonatkozik. 
- SDK-szolgáltatóval: az alkalmazás felhasználói manuálisan jelentkezik be, és ezután elküldi a hitelesítési jogkivonat az App Service-ellenőrzés céljából. Ez általában a helyzet böngésző nélküli alkalmazásokkal, amelyek a szolgáltató bejelentkezési oldal nem jelenik meg a felhasználónak. Az alkalmazás kódjának a bejelentkezési folyamat kezeli, ezért a más néven _ügyfél által vezérelt folyamat_ vagy _client flow_. Ebben az esetben a REST API-k, érvényes [Azure Functions](../azure-functions/functions-overview.md), és a JavaScript webböngésző-ügyfelek számára, valamint a bejelentkezési folyamat nagyobb rugalmasságot igénylő webes alkalmazások. Natív mobilalkalmazásokban, a szolgáltató SDK-val a felhasználók bejelentkezhetnek is vonatkozik.

> [!NOTE]
> Az App Service szolgáltatásban egy megbízható böngészőalkalmazás hívásait meghívja a REST API-t egy másik App Service-ben vagy [Azure Functions](../azure-functions/functions-overview.md) lehet hitelesíteni a kiszolgáló által vezérelt folyamatot. További információkért lásd: [testre szabhatja a hitelesítés és engedélyezés az App Service-ben](app-service-authentication-how-to.md).
>

Az alábbi táblázat a hitelesítési folyamat lépéseit.

| Lépés | Szolgáltatói SDK nélkül | SDK-szolgáltatóval |
| - | - | - |
| 1. A bejelentkezési felhasználói | Átirányítja az ügyfelet, hogy `/.auth/login/<provider>`. | Ügyfélkód felhasználó jelentkezik be közvetlenül a szolgáltató SDK-val, és a egy hitelesítési tokent kap. Információ a szolgáltató dokumentációjában talál. |
| 2. Utóhitelesítés | Szolgáltató átirányítja az ügyfelet, hogy `/.auth/login/<provider>/callback`. | Ügyfélkód bejegyzések jogkivonat-szolgáltatót a `/.auth/login/<provider>` ellenőrzés céljából. |
| 3. Hitelesített munkamenet létrehozása | App Service-ben hitelesített cookie ad választ. | App Service-ben a saját hitelesítési jogkivonat ügyféloldali kódot ad vissza. |
| 4. Hitelesített tartalmat szolgálnak ki | Ügyfél hitelesítési cookie-k tartalmazza (a böngésző automatikusan kezeli) későbbi kérelmeket. | Ügyfélkód megadja a hitelesítési jogkivonat `X-ZUMO-AUTH` (automatikusan kezeli a Mobile Apps-ügyfél SDK-k) fejléc. |

Az ügyfelek böngészőin, az App Service automatikusan is közvetlen minden nem hitelesített felhasználókat `/.auth/login/<provider>`. Felhasználók is is jelenthet, egy vagy több `/.auth/login/<provider>` jelentkezzen be az alkalmazás a választott szolgáltató mutató hivatkozásokat.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Engedélyezési viselkedés

Az a [az Azure portal](https://portal.azure.com), App Service-ben engedélyezési viselkedések számos konfigurálható.

![](media/app-service-authentication-overview/authorization-flow.png)

A következő fejléceket beállításokat ismertetik.

### <a name="allow-all-requests-default"></a>Lehetővé teszi az összes kérelem (alapértelmezett)

Hitelesítés és engedélyezés (kikapcsolva) az App Service által nem kezelt. 

Válassza ezt a lehetőséget, ha már nincs szüksége, hitelesítési és engedélyezési, vagy ha azt szeretné, a saját hitelesítési és engedélyezési kód írására.

### <a name="allow-only-authenticated-requests"></a>Csak hitelesített kérések engedélyezése

A beállítás **bejelentkezés a következővel \<szolgáltató >**. Az App Service összes névtelen kérelem átirányítja `/.auth/login/<provider>` , válassza ki a szolgáltató. Ha a névtelen kérelem érkezik egy natív mobilalkalmazásból, van-e a visszaadott válasz egy `HTTP 401 Unauthorized`.

Ezzel a beállítással nem kell hitelesítési kód írása az alkalmazásban. Kifinomultabb engedélyezési, például a szerepkör-specifikus engedélyezési, tudja kezelni vizsgálatával szerezheti be a felhasználói jogcímek (lásd: [eléréséhez a felhasználói jogcímek](app-service-authentication-how-to.md#access-user-claims)).

### <a name="allow-all-requests-but-validate-authenticated-requests"></a>Lehetővé teszi az összes kérelmet, de a hitelesített kérelmek ellenőrzése

A beállítás **engedélyezése névtelen kérelmek**. Ezt a beállítást kapcsolja be a hitelesítés és engedélyezés az App Service-ben, de az alkalmazás kódjának felhasználását engedélyezési döntésekhez késleltet. Hitelesített kéréseknél App Service-ben is továbbítja a hitelesítési adatokat a HTTP-fejléceket. 

Ez a beállítás a névtelen kérelmek kezelése nagyobb rugalmasságot nyújt. Ha például lehetővé teszi, hogy [található több bejelentkezési szolgáltatók](app-service-authentication-how-to.md#use-multiple-sign-in-providers) a felhasználók számára. Azonban a kódot kell írnia. 

## <a name="more-resources"></a>További erőforrások

[Oktatóanyag: Hitelesítése és engedélyezése a felhasználók teljes körű az Azure App Service-ben (Windows)](app-service-web-tutorial-auth-aad.md)  
[Oktatóanyag: Hitelesítése és engedélyezése a felhasználók teljes körű az Azure App Service Linux rendszeren](containers/tutorial-auth-aad.md)  
[Testre szabhatja a hitelesítés és engedélyezés az App Service-ben](app-service-authentication-how-to.md)

Szolgáltatóspecifikus útmutatókat:

* [Azure Active Directory-bejelentkezés használatához az alkalmazás konfigurálása][AAD]
* [Az alkalmazások a Facebook-bejelentkezés konfigurálása][Facebook]
* [Az alkalmazások a Google-bejelentkezés konfigurálása][Google]
* [Az alkalmazások a Microsoft Account login konfigurálása][MSA]
* [Az alkalmazások a Twitter-bejelentkezés konfigurálása][Twitter]
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
