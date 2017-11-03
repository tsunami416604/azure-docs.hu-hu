---
title: "Hitelesítési és engedélyezési az Azure App Service szolgáltatásban |} Microsoft Docs"
description: "Fogalmi referenciája és áttekintése a hitelesítési / engedélyezési a beállítást, az Azure App Service"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/29/2016
ms.author: mahender
ms.openlocfilehash: f0d2644903181cd2e20166feae4f90ddd4037fa8
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Hitelesítés és engedélyezés az Azure App Service-ben
## <a name="what-is-app-service-authentication--authorization"></a>Mi az App Service hitelesítés / engedélyezés?
App Service hitelesítés / engedélyezés olyan szolgáltatás, amely lehetővé teszi a felhasználók bejelentkezni, így nem kell a a háttéralkalmazás kódjának módosítása az alkalmazás. Az alkalmazás védelme és a felhasználói adatok segítségével egyszerűen biztosít.

App Service az összevont identitás, amelyben egy külső identitásszolgáltatótól tárolja a fiókokat, és hitelesíti a felhasználókat. Az alkalmazás támaszkodik a szolgáltató azonosító adatok, hogy az alkalmazás nem rendelkezik, ez az információ tárolásához. App Service támogatja a beépített öt identitás-szolgáltatóktól: Azure Active Directory, a Facebook, a Google, a Microsoft Account és a Twitter. Az alkalmazás ezen identitás-szolgáltatóktól tetszőleges számú segítségével a felhasználók hogyan bejelentkeznek az lehetőségeket. Bontsa ki a beépített támogatása, hogy egy másik identitásszolgáltató integrálhatja vagy [saját egyéni identitáskezelési megoldás][custom-auth].

Ha szeretné máris kipróbálni, tekintse meg az alábbi oktatóanyagok egyikét [hitelesítés hozzáadása az iOS-alkalmazás] [ iOS] (vagy [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms], vagy [Cordova]).

## <a name="how-authentication-works-in-app-service"></a>Az App Service authentication működése
Hogy a hitelesítés az identitás-szolgáltatóktól egyikének használatával, először az alkalmazás ismernie az identitásszolgáltató konfigurálása. Az identitásszolgáltató azonosítót és titkos kulcsok, az App Service biztosító fog adja meg. Ezzel befejezte a megbízhatósági kapcsolatban, hogy az App Service ellenőrizhesse a felhasználó helyességi feltételek, például a hitelesítési tokenek az identitásszolgáltató által.

A felhasználó bejelentkezhet e szolgáltatók egyikének használatával, olyan végponttal, amely képes bejelentkeztetni a felhasználókat, hogy az adott szolgáltató a felhasználó kell átirányítani. Ha használják az ügyfelek a webböngésző, akkor is, amely képes bejelentkeztetni a felhasználókat a végpont összes nem hitelesített felhasználókat automatikusan közvetlen App Service. Ellenkező esetben szüksége lesz az ügyfeleknek a közvetlen `{your App Service base URL}/.auth/login/<provider>`, ahol `<provider>` a következő értékek valamelyike: aad-ben, a facebook, google, a microsoft vagy a twitteren. Mobil és API-forgatókönyvek magyarázatát a cikk későbbi szakaszokban.

Az alkalmazás egy webböngészőn keresztül kapcsolatba kerülő felhasználók számára, hogy azok maradjanak hitelesített, akkor keresse meg az alkalmazás cookie fog rendelkezni. A más ügyféltípusok, például Mobiltelefonról, egy JSON webes jogkivonatot (JWT), amely kell megadni a `X-ZUMO-AUTH` fejléc, kap, az ügyfélnek. A Mobile Apps-ügyfél SDK-k fogja kezelni ezt meg. Másik lehetőségként az Azure Active Directory-identitás token vagy a hozzáférési jogkivonat közvetlenül szerepelni fog a `Authorization` fejléc, mint egy [tulajdonosi jogkivonattal](https://tools.ietf.org/html/rfc6750).

App Service a kapacitásprofillal szemben érvényesíti a cookie-k vagy a jogkivonatot, amely az alkalmazás bocsát ki a felhasználók hitelesítésére. Ha szeretné korlátozni, ki férhet hozzá az alkalmazás, lásd: a [engedélyezési](#authorization) szakasz a cikk későbbi részében.

### <a name="mobile-authentication-with-a-provider-sdk"></a>Mobil hitelesítési szolgáltatóval SDK
Követően a háttérkiszolgálón minden be van állítva, módosíthatja a mobil ügyfelek App Service bejelentkezni. Kétféleképpen itt:

* A megadott identitásszolgáltató közzéteszi az azonosságának és dokumentumaikhoz az App Service SDK használja.
* Egyetlen sor kódot használja, úgy, hogy a felhasználók bejelentkezhetnek a Mobile Apps-ügyfél SDK.

> [!TIP]
> A legtöbb alkalmazás által használandó SDK szolgáltató get egységesebb, amikor a felhasználók bejelentkeznek, frissítési támogatása, és egyéb előnyök, amely meghatározza a szolgáltató.
> 
> 

A szolgáltató SDK használatakor felhasználók bejelentkezve is nagyobb mértékben integrálható az alkalmazás futó operációs rendszer számára. Ez is lehetővé teszi a szolgáltató jogkivonat és bizonyos felhasználói adatok az ügyfélen, így sokkal könnyebben graph API-kat használnak, és a felhasználói élmény testreszabásáról. Alkalmanként a blogok és fórumok, megjelenik ez néven az "ügyfél folyamatában" vagy "ügyfél által vezérelt folyamat", mert az ügyfélen kód jelentkezik be a felhasználók és az Ügyfélkód ugyanaz a szolgáltató jogkivonat a hozzáférést.

A szolgáltató token beszerzését követően kell érvényesítéshez App Service küldendő. App Service érvényesíti a jogkivonatot, miután az App Service egy új App Service-jogkivonatot az ügyfél számára visszaadott hoz létre. A Mobile Apps-ügyfél SDK segédmódszereket kezelése az exchange, és automatikusan csatolja a token összes kérelmet, az alkalmazás háttéralkalmazása rendelkezik. A fejlesztők is, hogy a szolgáltató token mutató hivatkozás Ha így dönt.

### <a name="mobile-authentication-without-a-provider-sdk"></a>Mobil hitelesítési szolgáltató SDK nélkül
Ha nem szeretné, hogy a szolgáltató SDK beállításához, jelentkezzen be az Ön Azure App Service Mobile Apps szolgáltatásának engedélyezheti. A Mobile Apps-ügyfél SDK fog a szolgáltató a webes nézet megnyitásához, és jelentkezzen be a felhasználó. Alkalmanként blogok és fórumok, jelennek meg ezt nevezik a "server folyamat" vagy "kiszolgáló felé irányuló adatfolyam", mert a kiszolgáló a folyamat, amely képes bejelentkeztetni a felhasználókat kezeli, és az ügyfél SDK soha nem megkapja a szolgáltató jogkivonatot.

Ez a folyamat elindításához kód a hitelesítés az oktatóanyag az egyes platformokon tartalmazza. A folyamat végén az ügyfél SDK az App Service-tokent, és rendelkezik a token automatikusan csatolja az összes kérelmet, az alkalmazás háttéralkalmazása.

### <a name="service-to-service-authentication"></a>Szolgáltatások közötti hitelesítés
Bár a felhasználók hozzáférést biztosíthat az alkalmazás, megbízom a saját API hívása egy másik alkalmazás. Lehet például egy webes alkalmazás egy másik webes alkalmazást egy API-hívás. Ebben a forgatókönyvben segítségével hitelesítő adatokat a szolgáltatás-fiók felhasználói hitelesítő adatok helyett a szolgáltatáshitelesítést egy token. A szolgáltatásfiók is van egy *egyszerű* az Azure Active Directory projector és használó hitelesítés ilyen fiókja is nevezik egy szolgáltatások közötti forgatókönyv.

> [!IMPORTANT]
> Mobile apps szolgáltatásban a felhasználói eszközökön futnak, mert mobilalkalmazás tegye *nem* megbízható alkalmazások számít, és ne használja a szolgáltatás egyszerű áramlását. Ehelyett egy felhasználói folyamat, amely korábban ismertetett kell használják.
> 
> 

Szolgáltatások közötti forgatókönyvek esetén az App Service megvédheti az alkalmazás Azure Active Directory használatával. A hívó alkalmazás ugyanúgy kell megadni. egy Azure Active Directory szolgáltatás egyszerű engedélyezési jogkivonatot, biztosít az ügyfél-azonosító és ügyfél titkos az Azure Active Directory előállított. Ez a forgatókönyv ASP.NET API-alkalmazások használó például magyarázza az oktatóanyagban [Service API Apps egyszerű hitelesítés] [apia-szolgáltatás].

Ha azt szeretné, az App Service hitelesítés használatára egy szolgáltatások közötti forgatókönyv kezelésére, ügyféltanúsítványok vagy egyszerű hitelesítést is használhatja. Az Azure-ban ügyfél tanúsítványokkal kapcsolatos további információkért lásd: [hogyan a TLS kölcsönös hitelesítés beállítása a Web Apps](app-service-web-configure-tls-mutual-auth.md). Egyszerű hitelesítés az ASP.NET kapcsolatos információkért lásd: [hitelesítési szűrők ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

API-alkalmazásba az App Service logic app Service fiókot hitelesítést egy különleges esetben szabványban leírt [az App Service a Logic apps üzemeltetett egyéni API használata](../logic-apps/logic-apps-custom-hosted-api.md).

## <a name="authorization"></a>Az engedélyezés az App Service működése
A kérelmek férhetnek hozzá az alkalmazás teljes hozzáféréssel rendelkeznek. App Service hitelesítés / engedélyezés beállítható, hogy az összes, az alábbi viselkedésmódok:

* Az alkalmazás eléréséhez csak hitelesített kérések engedélyezése.
  
    A böngészőben egy névtelen kérést küld, ha az App Service átirányítja a oldal, amely úgy dönt, hogy a felhasználók bejelentkezhetnek az identitásszolgáltató számára. Ha a mobileszközt, a rendszer a visszaadott választ egy HTTP *401 nem engedélyezett* választ.
  
    Ezzel a beállítással nem kell minden hitelesítési kód írása az alkalmazásban. Ha egyeztetését engedélyezési van szüksége, a kód a felhasználó adatai érhető el.
* Engedélyezi az összes kérelem eléri az alkalmazást, de hitelesített kérelmek érvényesíti, és adják át a hitelesítési adatok a HTTP-fejlécek.
  
    Ez a beállítás eltér az alkalmazás kódjának felhasználását engedélyezési döntésekhez. Kezelési névtelen kérelem nagyobb rugalmasságot biztosít, de írhat kódot kell.
* Engedélyezi az összes kérelmet az alkalmazás eléréséhez, és nincs a művelet végrehajtása a hitelesítési adatok a kérelmeket.
  
    Ez az eset, a hitelesítési / engedélyezési funkció ki van kapcsolva. A hitelesítési és engedélyezési feladatai vannak az alkalmazás kódjában kódon múlik.

Az előző egyike által vezérelt a **hitelesítetlen kérés esetén elvégzendő művelet** lehetőséget az Azure portálon. Ha úgy dönt, ** jelentkezzen be az *szolgáltatónevet* **, összes kérelmet hitelesíteni kell. **Kérés (intézkedés) engedélyezése** eltér az engedélyezéssel kapcsolatos döntést kell a kódot, de a hitelesítési adatokat továbbra is tartalmazza. Ha azt szeretné, a kód mindent kezelni, letilthatja a hitelesítési / engedélyezési szolgáltatás.

## <a name="working-with-user-identities-in-your-application"></a>Az alkalmazás felhasználói azonosítók használata
App Service alkalmazás bizonyos felhasználói információk speciális fejlécek használatával továbbítja. Külső kérelmek letiltása ezek a fejlécek értékét, és csak jelen if App Service hitelesítés / engedélyezés. Néhány példa fejlécek a következők:

* X-MS-CLIENT-TAG-NEVE
* AZ X-MS-CLIENT-EGYSZERŰ-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Bármilyen nyelven vagy keretrendszerben írt kódot az ezek a fejlécek kérheti le a szükséges információkat. Az ASP.NET 4.6-alkalmazások esetén a **ClaimsPrincipal** automatikusan állítsa be a megfelelő értékekkel.

Az alkalmazás további felhasználó adatait egy HTTP GET keresztül is beszerezheti a a `/.auth/me` végpont az alkalmazás. Egy érvényes tokent, a kért része egy JSON-adattartalom a használt szolgáltató adatait, az alapul szolgáló szolgáltató token és néhány más információkat ad vissza. A Mobile Apps server SDK-k az adatok segédmódszereket biztosítanak. További információkért lásd: [használata az Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), és [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="documentation-and-additional-resources"></a>Dokumentáció és további források
### <a name="identity-providers"></a>Identitás-szolgáltatóktól
Az alábbi oktatóanyagok bemutatják, hogyan konfigurálhatja a különböző hitelesítésszolgáltatók használandó App Service:

* [Az alkalmazás Azure Active Directory bejelentkezési használandó konfigurálása][AAD]
* [Az alkalmazás használatához Facebook bejelentkezési konfigurálása][Facebook]
* [Az alkalmazás használatához Google bejelentkezési konfigurálása][Google]
* [Az alkalmazás használatához Microsoft Account bejelentkezés konfigurálása][MSA]
* [Az alkalmazás használatához Twitter bejelentkezési konfigurálása][Twitter]

Ha szeretné használni az azonosítási rendszer eltérő, feltéve, itt is használhatja a [tekintse meg az egyéni hitelesítési támogatás a Mobile Apps .NET Server SDK][custom-auth], a webalkalmazások, mobilalkalmazások vagy API-alkalmazások használható.

### <a name="mobile-applications"></a>Mobilalkalmazás
Az alábbi oktatóanyagok bemutatják, hogyan hitelesítés hozzáadása a mobil ügyfelek a kiszolgáló által vezérelt folyamata segítségével:

* [Hitelesítés hozzáadása az iOS-alkalmazás][iOS]
* [Hitelesítés hozzáadása az Android-alkalmazás][Android]
* [Hitelesítés hozzáadása a Windows-alkalmazás][Windows]
* [Hitelesítés hozzáadása a Xamarin.iOS-alkalmazás][Xamarin.iOS]
* [Hitelesítés hozzáadása Xamarin.Android-alkalmazáshoz][Xamarin.Android]
* [Hitelesítés hozzáadása a Xamarin.Forms-alkalmazás][Xamarin.Forms]
* [Hitelesítés hozzáadása a Cordova-alkalmazáshoz][Cordova]

Ha azt szeretné, hogy az ügyfél által vezérelt folyamat használata az Azure Active Directory, használja a következőket:

* [Az Active Directory Authentication Library használata iOS-hez][ADAL-iOS]
* [Használja az Active Directory hitelesítési könyvtár androidhoz][ADAL-Android]
* [A Windows és a Xamarin használható az Active Directory hitelesítési könyvtár][ADAL-dotnet]

Ha azt szeretné, hogy használják az ügyfél által vezérelt Facebook, használja a következőket:

* [A Facebook SDK használata iOS-hez](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Ha azt szeretné, hogy használják az ügyfél által vezérelt Twitter, használja a következőket:

* [Twitter háló használata iOS-hez](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Ha azt szeretné, hogy használják az ügyfél által vezérelt Google, használja a következőket:

* [A Google-bejelentkezés SDK használata iOS-hez](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

<!-- ### API applications
The following tutorials show how to protect your API apps:

* [User authentication for API Apps in Azure App Service][apia-user]
* [Service principal authentication for API Apps in Azure App Service][apia-service] -->

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
