---
title: Hitelesítési és engedélyezési az Azure App Service mobile apps szolgáltatásban |} Microsoft Docs
description: Fogalmi referenciája és áttekintése a hitelesítési / engedélyezési funkció az Azure App Service-, kifejezetten a mobile apps szolgáltatásban
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 237310c607eb8488e53631b6e69d01703d1ebf99
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Hitelesítési és engedélyezési az Azure App Service mobile apps szolgáltatásban

Ez a cikk ismerteti, hogyan működik a hitelesítéshez és engedélyezéshez, hogy natív alkalmazást, ha az App Service háttér fejlesztése során. App Service integrált hitelesítési és engedélyezési, tartalmaz, így a mobilalkalmazások bejelentkezhet a felhasználók az App Service-ben minden programkód módosítása nélkül. Az alkalmazás védelme és a felhasználói adatok segítségével egyszerűen biztosít. 

Ez a cikk mobilalkalmazás fejlesztési összpontosít. Gyorsan használatba az App Service hitelesítés és a mobilalkalmazás-engedélyezés, tekintse meg az alábbi oktatóanyagok [hitelesítés hozzáadása az iOS-alkalmazás] [ iOS] (vagy [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms], vagy [Cordova ]). 

A hitelesítési és engedélyezési működése az App Service információkért lásd: [hitelesítési és engedélyezési az Azure App Service](../app-service/app-service-authentication-overview.md).

## <a name="authentication-with-provider-sdk"></a>Hitelesítési szolgáltatóval SDK

Követően az App Service-ben minden be van állítva, módosíthatja a mobil ügyfelek App Service bejelentkezni. Kétféleképpen itt:

* A megadott identitásszolgáltató közzéteszi az azonosságának és dokumentumaikhoz az App Service SDK használja.
* Egyetlen sor kódot használja, úgy, hogy a felhasználók bejelentkezhetnek a Mobile Apps-ügyfél SDK.

> [!TIP]
> A legtöbb alkalmazás által használandó SDK szolgáltató get egységesebb, amikor a felhasználók bejelentkeznek, támogatják a frissítési token használatát, és egyéb előnyök, amely meghatározza a szolgáltató.
> 
> 

A szolgáltató SDK használatakor felhasználók bejelentkezve is nagyobb mértékben integrálható az alkalmazás futó operációs rendszer számára. Ez a módszer egy szolgáltató token és bizonyos felhasználói adatok az ügyfélen, így sokkal könnyebben graph API-kat használnak, és a felhasználói élmény testre is biztosít. Alkalmanként blogok és fórumok, azt nevezzük az "ügyfél folyamatában" vagy "ügyfél által vezérelt folyamat" mert felhasználók ügyfél bejelentkezik a kódot, és az Ügyfélkód szolgáltató jogkivonat hozzáféréssel rendelkezik.

A szolgáltató token beszerzését követően kell érvényesítéshez App Service küldendő. App Service érvényesíti a jogkivonatot, miután az App Service egy új App Service-jogkivonatot az ügyfél számára visszaadott hoz létre. A Mobile Apps-ügyfél SDK segédmódszereket kezelése az exchange, és automatikusan csatolja a token összes kérelmet, az alkalmazás háttér rendelkezik. A fejlesztők is, hogy a szolgáltató token mutató hivatkozás.

A hitelesítési folyamat további információkért lásd: [App Service-hitelesítési folyamat](../app-service/app-service-authentication-overview.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Hitelesítési szolgáltató SDK nélkül

Ha nem szeretné, hogy a szolgáltató SDK beállításához, jelentkezzen be az Ön Azure App Service Mobile Apps szolgáltatásának engedélyezheti. A Mobile Apps-ügyfél SDK fog a szolgáltató a webes nézet megnyitásához, és jelentkezzen be a felhasználó. Alkalmanként blogok és fórumok, nevezik a "server folyamat" vagy "kiszolgáló felé irányuló adatfolyam", mert a kiszolgáló a folyamat, amely képes bejelentkeztetni a felhasználókat kezeli, és az ügyfél SDK soha nem megkapja a szolgáltató jogkivonatot.

Ez a folyamat elindításához kód a hitelesítés az oktatóanyag az egyes platformokon tartalmazza. A folyamat végén az ügyfél SDK az App Service-tokent, és rendelkezik a token automatikusan csatolja az összes kérelmet, az alkalmazás háttéralkalmazása.

A hitelesítési folyamat további információkért lásd: [App Service-hitelesítési folyamat](../app-service/app-service-authentication-overview.md#authentication-flow). 
## <a name="more-resources"></a>További erőforrások

Az alábbi oktatóanyagok bemutatják, hogyan hitelesítés hozzáadása a mobil ügyfelek segítségével a [kiszolgáló által vezérelt folyamat](../app-service/app-service-authentication-overview.md#authentication-flow):

* [Hitelesítés hozzáadása az iOS-alkalmazás][iOS]
* [Hitelesítés hozzáadása az Android-alkalmazás][Android]
* [Hitelesítés hozzáadása a Windows-alkalmazás][Windows]
* [Hitelesítés hozzáadása a Xamarin.iOS-alkalmazás][Xamarin.iOS]
* [Hitelesítés hozzáadása Xamarin.Android-alkalmazáshoz][Xamarin.Android]
* [Hitelesítés hozzáadása a Xamarin.Forms-alkalmazás][Xamarin.Forms]
* [Hitelesítés hozzáadása a Cordova-alkalmazáshoz][Cordova ]

A következő erőforrások használja, ha szeretné használni a [ügyfél által vezérelt folyamat](../app-service/app-service-authentication-overview.md#authentication-flow) az Azure Active Directory:

* [Az Active Directory Authentication Library használata iOS-hez][ADAL-iOS]
* [Használja az Active Directory hitelesítési könyvtár androidhoz][ADAL-Android]
* [A Windows és a Xamarin használható az Active Directory hitelesítési könyvtár][ADAL-dotnet]

A következő erőforrások használja, ha szeretné használni a [ügyfél által vezérelt folyamat](../app-service/app-service-authentication-overview.md#authentication-flow) a Facebook-on:

* [A Facebook SDK használata iOS-hez](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

A következő erőforrások használja, ha szeretné használni a [ügyfél által vezérelt folyamat](../app-service/app-service-authentication-overview.md#authentication-flow) a Twitteren:

* [Twitter háló használata iOS-hez](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

A következő erőforrások használja, ha szeretné használni a [ügyfél által vezérelt folyamat](../app-service/app-service-authentication-overview.md#authentication-flow) a Google:

* [A Google-bejelentkezés SDK használata iOS-hez](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova ]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
