---
title: Leküldéses értesítések hozzáadása Mobile Apps Android-alkalmazáshoz |} A Microsoft Docs
description: Ismerje meg, hogyan küldhet leküldéses értesítéseket az Android-alkalmazás a Mobile Apps segítségével.
services: app-service\mobile
documentationcenter: android
manager: crdun
editor: ''
author: conceptdev
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/17/2017
ms.author: crdun
ms.openlocfilehash: 9e9f7aba49c53a1a6fcc611ed771f266eb49c883
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38547175"
---
# <a name="add-push-notifications-to-your-android-app"></a>Leküldéses értesítések hozzáadása az Android-alkalmazás
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés
Ebben az oktatóanyagban a leküldéses értesítések hozzáadása az [Android – gyorsútmutató] projekthez, hogy a leküldéses értesítést küld az eszköz minden alkalommal, amikor a rendszer beszúr egy rekordot.

Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, a leküldéses értesítési kiterjesztési csomag kell. További információkért lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Előfeltételek
A következők szükségesek:

* Az integrált fejlesztői Környezetig, attól függően, a projekt háttérrendszere:

  * [Az Android Studio](https://developer.android.com/sdk/index.html) egy Node.js-háttérrendszer az alkalmazás-e.
  * [A Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) vagy újabb, ha az alkalmazás a Microsoft .NET-háttérrendszer.
* Android 2.3-as vagy újabb, a Google-tárház változat 27-es vagy újabb verzió és a Google Play-szolgáltatások 9.0.2-es vagy újabb a Firebase Cloud Messaging esetében.
* Végezze el a [Android – gyorsútmutató].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>A Firebase Cloud Messaginget támogató projekt létrehozása
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Értesítési központ konfigurálása
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Leküldéses értesítések küldése az Azure konfigurálása
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Engedélyezze a leküldéses értesítéseket a kiszolgálói projekt
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz
Ebben a szakaszban frissítse az ügyfél Android-alkalmazás leküldéses értesítések kezeléséhez.

### <a name="verify-android-sdk-version"></a>Android SDK-verziójának ellenőrzése
[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

A következő lépés, hogy telepítse a Google Play-szolgáltatások. Firebase Cloud Messaging rendelkezik néhány minimális API-t követelményeinek fejlesztést és tesztelést, amely a **minSdkVersion** a jegyzékfájlban tulajdonságot meg kell felelnie.

Ha egy régebbi eszközzel rendelkező teszteli, tekintse meg a [Firebase hozzáadása az Android-projekt] meghatározhatja hogyan alacsony ezt az értéket, és állítsa be megfelelően.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Adja hozzá a Firebase Cloud Messaging a projekthez
[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Kód hozzáadása
[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Az alkalmazás elleni a közzétett mobilszolgáltatás tesztelése
Az alkalmazás egy Android telefont az USB-kábelen keresztül közvetlenül csatolásával, vagy az emulátorban a virtuális eszköz segítségével tesztelheti.

## <a name="next-steps"></a>További lépések
Most, hogy ebben az oktatóanyagban elvégezte, vegye figyelembe, folytatás be az alábbi oktatóanyagok egyikét:

* [Hitelesítés hozzáadása az Android-alkalmazás](app-service-mobile-android-get-started-users.md).
  Útmutató: hitelesítés hozzáadása a todolist gyorsútmutató-projekt az Android identitásszolgáltatóval támogatott.
* [Android-alkalmazások az offline szinkronizálás engedélyezése](app-service-mobile-android-get-started-offline-data.md).
  Megtudhatja, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. Offline szinkronizálás, a felhasználók kezelhessék a mobilalkalmazás&mdash;megtekintését, hozzáadását és módosítását adatok&mdash;akkor is, ha nincs hálózati kapcsolat.

<!-- URLs -->
[Android – gyorsútmutató]: app-service-mobile-android-get-started.md
[Firebase hozzáadása az Android-projekt]:https://firebase.google.com/docs/android/setup
