---
title: "Leküldéses értesítések hozzáadása Mobile Apps az Android alkalmazás |} Microsoft Docs"
description: "Megtudhatja, hogyan küldhet leküldéses értesítéseket az Android-alkalmazás a Mobile Apps segítségével."
services: app-service\mobile
documentationcenter: android
manager: syntaxc4
editor: 
author: ggailey777
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/12/2016
ms.author: glenga
ms.openlocfilehash: b89e9af55342d5d7473d848956996f846250b4b5
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="add-push-notifications-to-your-android-app"></a>Leküldéses értesítések Android-alkalmazás hozzáadása
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés
Ebben az oktatóanyagban leküldéses értesítések hozzáadása a [Android gyors üzembe helyezési] projektre, hogy egy leküldéses értesítést küld az eszköz minden alkalommal, amikor egy olyan rekordot csatlakoztatva van.

Ha nem használja a letöltött gyors üzembe helyezési projekt, a leküldéses értesítési kiterjesztési csomagot kell. További információkért lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Előfeltételek
A következőkre lesz szüksége:

* Egy IDE, attól függően, hogy a projekt háttér:

  * [Android Studio](https://developer.android.com/sdk/index.html) a Node.js háttérből az alkalmazás-e.
  * [A Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) vagy újabb verzióját, ha az alkalmazás a Microsoft .NET-háttérből.
* Android 2.3-as vagy újabb verzió, a Google-tárház változat 27 vagy újabb és a Google Play-szolgáltatásokra 9.0.2 vagy újabb Firebase Cloud Messaging.
* Fejezze be a [Android gyors üzembe helyezési].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>A Firebase Cloud Messaginget támogató projekt létrehozása
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Egy értesítési központ konfigurálása
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Leküldéses értesítések küldéséhez Azure konfigurálása
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>A kiszolgáló projekt leküldéses értesítések engedélyezése
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz
Ebben a szakaszban frissíti az ügyfél Android-alkalmazás leküldéses értesítések kezeléséhez.

### <a name="verify-android-sdk-version"></a>Android SDK-verziójának ellenőrzése
[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

A következő lépés, hogy telepítse a Google Play-szolgáltatásokat. Google Cloud Messaging rendelkezik néhány minimális API szintre vonatkozó követelményeinek fejlesztéshez és teszteléshez, amely a **minSdkVersion** tulajdonság a jegyzékben meg kell felelnie.

Ha egy régebbi eszközzel rendelkező teszteli, tekintse meg [beállítva fel Google Play Services SDK] annak meghatározásához, hogyan alacsony értékre állítja, és állítsa be megfelelően.

### <a name="add-google-play-services-to-the-project"></a>Google Play-szolgáltatások felvétele a projektbe
[!INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>Adja hozzá a kódot
[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>A közzétett mobilszolgáltatás alkalmazás tesztelése
Az alkalmazás közvetlen csatlakoztatása az USB-kábellel Androidos telefonnal, vagy az emulátorban a virtuális eszköz segítségével tesztelheti.

## <a name="next-steps"></a>Következő lépések
Most, hogy ez az oktatóanyag befejezése fontolja meg valamelyik az alábbi oktatóanyagok folytatása:

* [Hitelesítés hozzáadása az Android-alkalmazás](app-service-mobile-android-get-started-users.md).
  Útmutató: hitelesítés hozzáadása a todolist gyorsútmutató-projekt az Android támogatott identitásszolgáltató használatával.
* [Az Android-alkalmazás kapcsolat nélküli szinkronizálásának engedélyezése](app-service-mobile-android-get-started-offline-data.md).
  Megtudhatja, hogyan adhat offline támogatást az alkalmazást a Mobile Apps háttérből segítségével. Kapcsolat nélküli szinkronizálás, a felhasználók használhatják a mobilalkalmazás&mdash;megtekintését, hozzáadását és módosítását adatok&mdash;akkor is, ha nincs hálózati kapcsolat.

<!-- URLs -->
[Android gyors üzembe helyezési]: app-service-mobile-android-get-started.md

[beállítva fel Google Play Services SDK]:https://developers.google.com/android/guides/setup
