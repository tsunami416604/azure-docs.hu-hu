---
title: Hitelesítés és engedélyezés az Azure App Service-mobilalkalmazásokhoz |} A Microsoft Docs
description: Fogalmi referenciája és áttekintése a hitelesítési / engedélyezési kifejezetten a mobilalkalmazások az Azure App Service-funkció.
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
ms.openlocfilehash: de501b79107aafa61c489db607c37d086a5f4ed4
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408017"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Hitelesítés és engedélyezés az Azure App Service-mobilalkalmazásokhoz

Ez a cikk bemutatja, hogyan működik a hitelesítés és engedélyezés, hogy egy App Service-ben háttérrendszerrel natív mobilalkalmazások fejlesztése során. App Service biztosítja a integrált hitelesítés és engedélyezés, így mobilalkalmazásait is bejelentkezhetnek a felhasználók App Service-ben bármilyen kód átírása nélkül. Az alkalmazás védelme és a felhasználói adatok egy egyszerű megoldást kínál. 

Ez a cikk a mobilalkalmazás-fejlesztés összpontosít. Gyors használatbavétele az App Service-hitelesítés és engedélyezés az alkalmazásokba, tekintse meg a következő oktatóanyagokban [hitelesítés hozzáadása az iOS-alkalmazás] [ iOS] (vagy [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms], vagy [Cordova]). 

Az App Service-ben hitelesítési és engedélyezési működéséről további információért lásd: [hitelesítése és engedélyezése Azure App Service-ben](../app-service/app-service-authentication-overview.md).

## <a name="authentication-with-provider-sdk"></a>Hitelesítés az SDK-t szolgáltató

Után minden App Service-ben be van állítva, módosíthatja a mobilügyfelek App Service-ben bejelentkezni. Kétféleképpen itt:

* Egy-egy adott identitásszolgáltató identitás létrehozásához, és ezután hozzáférhet az App Service-közzétevő SDK használata.
* Egyetlen sornyi kódot használja, úgy, hogy a Mobile Apps-ügyfél SDK-t a felhasználók bejelentkezhetnek.

> [!TIP]
> A legtöbb alkalmazás kell használhatják a szolgáltatói SDK egységesebb felhasználói bejelentkezés során, jogkivonat-frissítések támogatási használatát, valamint egyéb előnyök, amely meghatározza a szolgáltató.
> 
> 

SDK-szolgáltatót használ, amikor is bejelentkeznek egy szolgáltatás, amelynek az operációs rendszer, az alkalmazás fut, a nagyobb mértékben integrálható. Ez a módszer egy szolgáltató token és néhány felhasználói adatokat az ügyfélen, ami sokkal jobban megkönnyíti a graph API-k használatát, és testre szabhatja a felhasználói élmény is biztosít. Alkalmanként blogok és fórumok, azt nevezzük a "client flow" vagy "ügyfél által vezérelt folyamatnak" mivel a felhasználók ügyfél bejelentkezik a kódot, és az Ügyfélkód férhet hozzá a szolgáltató jogkivonat.

Egy szolgáltató token beszerzését követően kell az App Service-ellenőrzés kell elküldeni. App Service-ben érvényesíti a jogkivonatot, miután az App Service-ben egy új App Service-jogkivonatot az ügyfélnek visszaadott hoz létre. A Mobile Apps ügyfél-SDK segédmetódusokat kezelését az exchange, és automatikusan csatolja a jogkivonat az alkalmazás háttérrendszere érkező összes kérés rendelkezik. A fejlesztők is, hogy a szolgáltató token hivatkozást.

A hitelesítési folyamat további információkért lásd: [App Service-hitelesítési folyamat](../app-service/app-service-authentication-overview.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Hitelesítési szolgáltató SDK nélkül

Ha nem szeretne egy SDK-szolgáltató beállítása, a Mobile Apps szolgáltatás az Azure App Service, hogy jelentkezzen be az Ön számára engedélyezheti. A Mobile Apps-ügyfél SDK-t fog megszakíthatja a szolgáltató webes nézet megnyitásához, és jelentkezzen be a felhasználó. Alkalmanként a blogok és fórumok nevezzük a "server flow" vagy "a kiszolgáló által vezérelt folyamatnak", mert a kiszolgáló a folyamat, amely képes bejelentkeztetni a felhasználókat kezeli, és az ügyfél-SDK soha nem kap a szolgáltató jogkivonat.

Ez a folyamat elindításához a kód a hitelesítési oktatóanyagának az egyes platformokra vonatkozó tartalmazza. A folyamat végén az ügyfél-SDK tartalmaz egy App Service-tokent, és a jogkivonat automatikusan csatlakozik az alkalmazás háttérrendszerével minden kérelemhez.

A hitelesítési folyamat további információkért lásd: [App Service-hitelesítési folyamat](../app-service/app-service-authentication-overview.md#authentication-flow). 
## <a name="more-resources"></a>További erőforrások

Az alábbi oktatóanyagok bemutatják, hogyan hitelesítés hozzáadása a mobil ügyfélprogramok használatával a [kiszolgáló által vezérelt folyamat](../app-service/app-service-authentication-overview.md#authentication-flow):

* [Hitelesítés hozzáadása az iOS-alkalmazás][iOS]
* [Hitelesítés hozzáadása az Android-alkalmazás][Android]
* [Hitelesítés hozzáadása a Windows-alkalmazáshoz][Windows]
* [Hitelesítés hozzáadása a Xamarin.iOS-alkalmazást][Xamarin.iOS]
* [Hitelesítés hozzáadása Xamarin.Android-alkalmazáshoz][Xamarin.Android]
* [Hitelesítés hozzáadása a Xamarin.Forms-alkalmazás][Xamarin.Forms]
* [Hitelesítés hozzáadása a Cordova-alkalmazás][Cordova]

Az alábbi forrásanyagokból Ha használni szeretné a [ügyfél által vezérelt folyamat](../app-service/app-service-authentication-overview.md#authentication-flow) az Azure Active Directory:

* [Az Active Directory Authentication Library használata iOS-hez][ADAL-iOS]
* [Használja az Active Directory Authentication Library for Android][ADAL-Android]
* [Használja az Active Directory Authentication Library for Windows és a Xamarin][ADAL-dotnet]

Az alábbi forrásanyagokból Ha használni szeretné a [ügyfél által vezérelt folyamat](../app-service/app-service-authentication-overview.md#authentication-flow) a Facebookhoz:

* [A Facebook-SDK használata iOS-hez](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Az alábbi forrásanyagokból Ha használni szeretné a [ügyfél által vezérelt folyamat](../app-service/app-service-authentication-overview.md#authentication-flow) a Twitter használatához:

* [Twitter-háló használata iOS-hez](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Az alábbi forrásanyagokból Ha használni szeretné a [ügyfél által vezérelt folyamat](../app-service/app-service-authentication-overview.md#authentication-flow) for Google:

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
