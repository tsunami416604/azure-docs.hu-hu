---
title: Hitelesítés és engedélyezés
description: Az Azure App Service hitelesítési/engedélyezési szolgáltatásának általános áttekintése és áttekintése, különösen a mobilalkalmazásokhoz.
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 4a9ef62178b9a58fa8703413a09114a617d1d239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459463"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Hitelesítés és engedélyezés mobilalkalmazásokhoz az Azure App Service-ben

Ez a cikk ismerteti, hogyan működik a hitelesítés és az engedélyezés, ha natív mobilalkalmazások at fejleszt az App Service háttérrendszerrel. Az App Service integrált hitelesítést és engedélyezést biztosít, így a mobilalkalmazások anélkül jelentkezhetnek be a felhasználókba, hogy módosítanák a kódot az App Service-ben. Ez egy egyszerű módja annak, hogy megvédje az alkalmazást, és a felhasználónkénti adatokkal való munkavégzést. 

Ez a cikk a mobilalkalmazás-fejlesztésre összpontosít. Az App Service-hitelesítés és -engedélyezés gyors megkezdéséhez olvassa el az alábbi oktatóanyagok [egyikét: Hitelesítés hozzáadása az iOS-alkalmazáshoz][iOS] (vagy [Android,] [Windows,] [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]vagy [Cordova]). 

A hitelesítés és engedélyezés működéséről az App Service-ben a [Hitelesítés és engedélyezés az Azure App Service szolgáltatásban](../app-service/overview-authentication-authorization.md)című témakörben talál tájékoztatást.

## <a name="authentication-with-provider-sdk"></a>Hitelesítés szolgáltatós SDK-val

Miután minden konfigurálva van az App Service-ben, módosíthatja a mobilügyfeleket, hogy jelentkezzenek be az App Service szolgáltatással. Két megközelítés van itt:

* Használjon egy SDK-t, amelyet egy adott identitásszolgáltató tesz közzé az identitás létrehozásához, majd az App Service-hez való hozzáféréshez.
* Használjon egyetlen kódsort, hogy a Mobilalkalmazások ügyfél SDK-ja bejelentkezhessen a felhasználókhoz.

> [!TIP]
> A legtöbb alkalmazás nak egy szolgáltató SDK-t kell használnia, hogy konzisztensebb felhasználói élményt kapjon a felhasználók bejelentkezésekor, a tokenfrissítési támogatás használatához, és hogy a szolgáltató által megadott egyéb előnyöket kapjon.
> 
> 

Szolgáltató SDK használata esetén a felhasználók bejelentkezhetnek egy olyan élménybe, amely szorosabban integrálódik az alkalmazás által futtatott operációs rendszerrel. Ez a módszer egy szolgáltatói jogkivonatot és néhány felhasználói információt is biztosít az ügyfélen, ami sokkal könnyebbé teszi a grafikon API-k felhasználását és a felhasználói élmény testreszabását. Alkalmanként a blogok és fórumok, ez a továbbiakban az "ügyfél-folyamat" vagy "ügyfél-irányított folyamat", mert a kódot az ügyfél jelek a felhasználók, és az ügyfélkód hozzáfér a szolgáltató jogkivonatot.

A szolgáltatói jogkivonat beszerzése után el kell küldeni az App Service-nek ellenőrzésre. Miután az App Service érvényesíti a jogkivonatot, az App Service létrehoz egy új App Service-jogkivonatot, amely visszakerül az ügyfélnek. A Mobile Apps ügyfél SDK rendelkezik segítő módszerek kel a csere kezeléséhez, és automatikusan csatolja a jogkivonatot az összes kérelmet az alkalmazás háttérrendszeréhez. A fejlesztők a szolgáltatói jogkivonatra mutató hivatkozást is megtarthatnak.

A hitelesítési folyamattal kapcsolatos további információkért lásd: [App Service hitelesítési folyamat.](../app-service/overview-authentication-authorization.md#authentication-flow) 

## <a name="authentication-without-provider-sdk"></a>Hitelesítés szolgáltató nélkül SDK

Ha nem szeretne szolgáltatóSDK-t beállítani, engedélyezheti, hogy az Azure App Service mobilalkalmazások funkciója jelentkezzen be. A Mobile Apps ügyfél SDK megnyit egy webes nézetet az Ön által választott szolgáltató számára, és bejelentkezik a felhasználóba. Alkalmanként a blogok és fórumok, ez az úgynevezett "kiszolgáló iflow" vagy "kiszolgáló által irányított folyamat", mert a kiszolgáló kezeli a folyamatot, amely aláírja a felhasználók, és az ügyfél SDK soha nem kapja meg a szolgáltató token.

A folyamat elindításához szükséges kód az egyes platformok hitelesítési oktatóanyaga tartalmazza. A folyamat végén az ügyfél SDK rendelkezik egy App Service-jogkivonattal, és a jogkivonat automatikusan csatlakozik az alkalmazás háttérrendszeréhez tartozó összes kérelemhez.

A hitelesítési folyamattal kapcsolatos további információkért lásd: [App Service hitelesítési folyamat.](../app-service/overview-authentication-authorization.md#authentication-flow) 
## <a name="more-resources"></a>További erőforrások

A következő oktatóanyagok bemutatják, hogyan adhat hitelesítést a mobilügyfelekhez a [kiszolgáló által irányított folyamat](../app-service/overview-authentication-authorization.md#authentication-flow)használatával:

* [Hitelesítés hozzáadása az iOS-alkalmazáshoz][iOS]
* [Hitelesítés hozzáadása az Android-alkalmazáshoz][Android]
* [Hitelesítés hozzáadása a Windows-alkalmazáshoz][Windows]
* [Hitelesítés hozzáadása a Xamarin.iOS alkalmazáshoz][Xamarin.iOS]
* [Hitelesítés hozzáadása a Xamarin.Android alkalmazáshoz][Xamarin.Android]
* [Hitelesítés hozzáadása a Xamarin.Forms alkalmazáshoz][Xamarin.Forms]
* [Hitelesítés hozzáadása a Cordova alkalmazáshoz][Cordova]

A következő erőforrásokat használja, ha az Ügyfél [által irányított folyamatot](../app-service/overview-authentication-authorization.md#authentication-flow) szeretné használni az Azure Active Directoryhoz:

* [Az iOS Active Directory hitelesítési könyvtárának használata][ADAL-iOS]
* [Az Android Active Directory hitelesítési könyvtárának használata][ADAL-Android]
* [A Windows és a Xamarin Active Directory hitelesítési könyvtárának használata][ADAL-dotnet]

Ha a Facebook [ügyféloldali folyamatát](../app-service/overview-authentication-authorization.md#authentication-flow) szeretné használni, használja a következő erőforrásokat:

* [Az iOS-es Facebook SDK használata](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Használja a következő erőforrásokat, ha az [ügyfél által irányított folyamatot](../app-service/overview-authentication-authorization.md#authentication-flow) szeretné használni a Twitterhez:

* [A Twitter Fabric iOS-hez való használata](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

A következő erőforrásokat használja, ha a Google [ügyféláltal irányított folyamatát](../app-service/overview-authentication-authorization.md#authentication-flow) szeretné használni:

* [A Google bejelentkezési SDK használata iOS-hez](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
