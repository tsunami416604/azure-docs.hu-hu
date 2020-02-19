---
title: Hitelesítés és engedélyezés
description: A Azure App Service hitelesítési/engedélyezési funkciójának fogalmi referenciája és áttekintése, különösen a Mobile apps esetében.
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 4a9ef62178b9a58fa8703413a09114a617d1d239
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459463"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Hitelesítés és engedélyezés Azure App Service Mobile apps esetén

Ez a cikk azt ismerteti, hogyan működik a hitelesítés és az engedélyezés, ha natív Mobile apps-t fejleszt App Service háttérrel. A App Service integrált hitelesítést és engedélyezést biztosít, így a Mobile apps a App Serviceban lévő kódok módosítása nélkül is képes aláírni a felhasználókat. Egyszerű módszert biztosít az alkalmazás védelme és a felhasználónkénti adatmennyiség használata. 

Ez a cikk a Mobile apps fejlesztésére koncentrál. A Mobile apps App Service hitelesítésének és engedélyezésének gyors megkezdéséhez tekintse meg az alábbi oktatóanyagok az iOS-alkalmazáshoz (vagy az [Android], a [Windows], [Xamarin.iOS], a [Xamarin.Android], a [Xamarin.Forms]vagy a [Cordova]) való [hitelesítés hozzáadását][iOS] ismertető témakört. 

A hitelesítés és az engedélyezés App Serviceban való működésével kapcsolatos információkért lásd: [hitelesítés és engedélyezés a Azure app Serviceban](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Hitelesítés szolgáltatói SDK-val

Miután minden App Service konfigurálva van, módosíthatja a mobil ügyfeleket, hogy bejelentkezzenek App Serviceba. Két megközelítés van:

* Olyan SDK-t használjon, amelyet egy adott identitás-szolgáltató tesz közzé a személyazonosság azonosításához, majd a App Servicehoz való hozzáféréshez.
* Használjon egy sor kódot, hogy a Mobile Apps ügyfél-SDK be tudja jelentkezni a felhasználókba.

> [!TIP]
> A legtöbb alkalmazásnak rendelkeznie kell egy szolgáltatói SDK-val, hogy következetesebb felhasználói élményt kapjon a felhasználók bejelentkeznek, a jogkivonat-frissítési támogatás használatához, valamint a szolgáltató által megadott egyéb előnyök beszerzéséhez.
> 
> 

Ha szolgáltatói SDK-t használ, a felhasználók bejelentkezhetnek olyan élménybe, amely szorosabban integrálódik az alkalmazás által futtatott operációs rendszerbe. Ez a módszer szolgáltatói jogkivonatot és néhány felhasználói információt is biztosít az ügyfélen, ami sokkal egyszerűbbé teszi a Graph API-k felhasználását és a felhasználói élmény testreszabását. Alkalmanként a blogok és a fórumok esetében az úgynevezett "ügyfél-folyamat" vagy "ügyfél által irányított folyamat", mivel a kód az ügyfélen jelentkezik a felhasználók számára, és az ügyfél kódja hozzáfér a szolgáltatói jogkivonathoz.

A szolgáltatói jogkivonat beszerzése után az ellenőrzéshez App Service kell elküldeni. Miután App Service érvényesíti a jogkivonatot, App Service létrehoz egy új App Service tokent, amelyet a rendszer visszaadott az ügyfélnek. A Mobile Apps Client SDK rendelkezik segítő módszerekkel az Exchange kezeléséhez, és automatikusan csatolja a jogkivonatot az alkalmazás összes kéréséhez. A fejlesztők a szolgáltatói jogkivonatra mutató hivatkozást is megtarthatnak.

A hitelesítési folyamattal kapcsolatos további információkért lásd: [app Service hitelesítési folyamat](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Hitelesítés szolgáltatói SDK nélkül

Ha nem szeretne szolgáltatói SDK-t beállítani, engedélyezheti a Azure App Service Mobile Apps funkciójának bejelentkezni. A Mobile Apps Client SDK egy webes nézetet nyit meg a választott szolgáltatóhoz, és bejelentkezik a felhasználóval. A blogok és fórumok időnként a "kiszolgálói folyamatnak" vagy a "kiszolgáló által irányított folyamatnak" nevezik, mivel a kiszolgáló felügyeli a felhasználókat, és az ügyfél-SDK soha nem fogadja el a szolgáltatói jogkivonatot.

A folyamat elindítására szolgáló kód az egyes platformokhoz tartozó hitelesítési oktatóanyag részét képezi. A folyamat végén az ügyfél-SDK App Service tokent tartalmaz, és a jogkivonat automatikusan csatolva lesz az alkalmazás-háttérbeli összes kérelemhez.

A hitelesítési folyamattal kapcsolatos további információkért lásd: [app Service hitelesítési folyamat](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>További segédanyagok

A következő oktatóanyagok azt mutatják be, hogyan adhat hozzá hitelesítést a mobil ügyfelekhez a [kiszolgáló által irányított folyamat](../app-service/overview-authentication-authorization.md#authentication-flow)használatával:

* [Hitelesítés hozzáadása iOS-alkalmazáshoz][iOS]
* [Hitelesítés hozzáadása az Android-alkalmazáshoz][Android]
* [Hitelesítés hozzáadása a Windows-alkalmazáshoz][Windows]
* [Hitelesítés hozzáadása a Xamarin. iOS-alkalmazáshoz][Xamarin.iOS]
* [Hitelesítés hozzáadása a Xamarin. Android-alkalmazáshoz][Xamarin.Android]
* [Hitelesítés hozzáadása a Xamarin. Forms alkalmazáshoz][Xamarin.Forms]
* [Hitelesítés hozzáadása a Cordova-alkalmazáshoz][Cordova]

Használja az alábbi erőforrásokat, ha a Azure Active Directory [ügyfél által irányított folyamatot](../app-service/overview-authentication-authorization.md#authentication-flow) szeretné használni:

* [Az iOS-es Active Directory-hitelesítési tár használata][ADAL-iOS]
* [Az Android rendszerhez készült Active Directory-hitelesítési tár használata][ADAL-Android]
* [A Windows és a Xamarin Active Directory-hitelesítési tár használata][ADAL-dotnet]

Használja a következő erőforrásokat, ha a Facebook-hoz készült [ügyfél-irányított folyamatot](../app-service/overview-authentication-authorization.md#authentication-flow) szeretné használni:

* [Az iOS-hez készült Facebook SDK használata](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Használja a következő erőforrásokat, ha az [ügyfél által irányított folyamatot](../app-service/overview-authentication-authorization.md#authentication-flow) szeretné használni a Twitteren:

* [Az iOS-hez készült Twitter-háló használata](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Használja a következő erőforrásokat, ha a Google-hoz készült [ügyfél-átirányítású folyamatot](../app-service/overview-authentication-authorization.md#authentication-flow) szeretné használni:

* [Az iOS-hez készült Google bejelentkezési SDK használata](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

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
