---
title: Leküldéses értesítések hozzáadása az Android-alkalmazáshoz
description: További információ arról, hogyan küldhet leküldéses értesítéseket a Mobilalkalmazásokkal az Android-alkalmazásba.
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6fec85c028e992c15fb9503ffb599023e668c58f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459939"
---
# <a name="add-push-notifications-to-your-android-app"></a>Leküldéses értesítések hozzáadása az Android-alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket ad hozzá az [Android gyorsindítási] projektjéhez, hogy a rendszer leküldéses értesítést küldjön az eszköznek minden alkalommal, amikor egy rekordot beszúr.

Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, szüksége van a leküldéses értesítésbővítmény csomagra. További információt [a .NET háttérkiszolgáló SDK azure mobile apps szolgáltatásához című témakörben talál.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="prerequisites"></a>Előfeltételek

A következőkre van szüksége:

* Ide, a projekt háttérvégétől függően:

  * [Android Studio,](https://developer.android.com/sdk/index.html) ha ez az alkalmazás egy Node.js háttér.
  * [Visual Studio Community 2013-as](https://go.microsoft.com/fwLink/p/?LinkID=391934) vagy újabb verzió, ha az alkalmazás rendelkezik Microsoft .NET háttérrendszerrel.
* Android 2.3-as vagy újabb verzió, a Google Repository 27-es vagy újabb verziója, valamint a Google Play Services 9.0.2-es vagy újabb verziói a Firebase Cloud Messaging esetében.
* Töltse ki az [Android gyorsindítást].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>A Firebase Cloud Messaginget támogató projekt létrehozása

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Az Azure konfigurálása leküldéses értesítések küldésére

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Leküldéses értesítések engedélyezése a kiszolgálóprojekthez

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz

Ebben a szakaszban frissíti az ügyfél Android-alkalmazást a leküldéses értesítések kezeléséhez.

### <a name="verify-android-sdk-version"></a>Az Android SDK verziójának ellenőrzése

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

A következő lépés a Google Play-szolgáltatások telepítése. A Firebase Cloud Messaging rendelkezik néhány minimális API-szintű követelményt a fejlesztéshez és a teszteléshez, amelynek a manifesztben lévő **minSdkVersion** tulajdonságnak meg kell felelnie.

Ha egy régebbi eszközzel tesztel, olvassa el a [Firebase hozzáadása az Android-projekthez] című területet, hogy megállapíthassa, milyen alacsonyra állíthatja ezt az értéket, és állítsa be megfelelően.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Firebase Cloud Messaging hozzáadása a projekthez

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Kód hozzáadása

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Az alkalmazás tesztelése a közzétett mobilszolgáltatással szemben

Az alkalmazást úgy tesztelheti, hogy közvetlenül csatlakoztat egy Android telefont USB-kábellel, vagy egy virtuális eszközt használ az emulátorban.

## <a name="next-steps"></a>További lépések

Most, hogy befejezte az oktatóanyagot, fontolja meg az alábbi oktatóanyagok egyikének folytatását:

* [Adja hozzá a hitelesítést az Android-alkalmazáshoz.](app-service-mobile-android-get-started-users.md)
  Ismerje meg, hogyan adhat hozzá hitelesítést a todolista gyorsindítási projekthez Androidon egy támogatott identitásszolgáltató használatával.
* [Engedélyezze az offline szinkronizálást az Android-alkalmazásban.](app-service-mobile-android-get-started-offline-data.md)
  Megtudhatja, hogy miként adhat hozzá offline támogatást az alkalmazáshoz a mobilalkalmazások háttérrendszerével. Az offline szinkronizálással a felhasználók&mdash;akkor is kezelhetik a&mdash;mobilalkalmazásokat, hogy megtekintsék, hozzáadják vagy módosítsák az adatokat, ha nincs hálózati kapcsolat.

<!-- URLs -->
[Android gyors indítás]: app-service-mobile-android-get-started.md
[Firebase hozzáadása az Android-projekthez]:https://firebase.google.com/docs/android/setup
