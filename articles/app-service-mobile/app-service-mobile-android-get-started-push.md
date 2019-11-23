---
title: Leküldéses értesítések hozzáadása az Android-alkalmazáshoz Mobile Apps használatával | Microsoft Docs
description: Ismerje meg, hogyan küldhet leküldéses értesítéseket az Android-alkalmazásokba a Mobile Apps használatával.
services: app-service\mobile
documentationcenter: android
manager: crdun
editor: ''
author: elamalani
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 539859ba73c8a26d6f7e8f25b9e7453d987a52bd
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389012"
---
# <a name="add-push-notifications-to-your-android-app"></a>Leküldéses értesítések hozzáadása az Android-alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné a felhőszolgáltatásokat a mobilalkalmazásba integrálni, regisztráljon az [App Centerbe](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket ad hozzá az [Android – első lépések] projekthez, hogy a rendszer minden egyes rekord behelyezése után leküldéses értesítést küldjön az eszköznek.

Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, szüksége lesz a leküldéses értesítési bővítmény csomagra. További információ: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

A következőkre lesz szüksége:

* IDE, a projekt hátterétől függően:

  * [Android Studio](https://developer.android.com/sdk/index.html) , ha az alkalmazás Node. js-háttérrel rendelkezik.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) vagy újabb verzió, ha az alkalmazás Microsoft .net háttérrel rendelkezik.
* Android 2,3 vagy újabb verzió, a Google repository változat 27-ös vagy újabb verziója, valamint a Google Play Services 9.0.2 vagy újabb verziója a Firebase Cloud Messaging szolgáltatáshoz.
* Fejezze be az [Android – első lépések].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>A Firebase Cloud Messaginget támogató projekt létrehozása

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Az Azure konfigurálása leküldéses értesítések küldéséhez

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Leküldéses értesítések engedélyezése a kiszolgálói projekthez

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz

Ebben a szakaszban a leküldéses értesítések kezeléséhez frissíti az ügyfél Android-alkalmazását.

### <a name="verify-android-sdk-version"></a>Az Android SDK verziójának ellenőrzése

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

A következő lépés a Google Play-szolgáltatások telepítése. A Firebase Cloud Messaging szolgáltatáshoz a fejlesztéshez és teszteléshez minimálisan szükséges API-szintű követelmények szükségesek, amelyeknek a jegyzékfájl **minSdkVersion** tulajdonságának meg kell felelnie.

Ha egy régebbi eszközzel végzi a tesztelést, tekintse meg a [Firebase hozzáadása az Android-projekthez] című témakört, és határozza meg, hogyan állíthatja be ezt az értéket, és megfelelően állíthatja be.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Firebase Cloud Messaging hozzáadása a projekthez

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Kód hozzáadása

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Az alkalmazás tesztelése a közzétett Mobile Service-ben

Az alkalmazást az Android-telefonok USB-kábellel való közvetlen csatolásával, vagy az emulátorban található virtuális eszköz használatával tesztelheti.

## <a name="next-steps"></a>Következő lépések

Most, hogy elvégezte az oktatóanyagot, tekintse át a következő oktatóanyagok egyikét:

* [Hitelesítés hozzáadása az Android-alkalmazáshoz](app-service-mobile-android-get-started-users.md).
  Megtudhatja, hogyan adhat hozzá hitelesítést a ToDoList gyors üzembe helyezési projekthez az Androidon egy támogatott identitás-szolgáltató használatával.
* [Az Android-alkalmazás offline szinkronizálásának engedélyezése](app-service-mobile-android-get-started-offline-data.md).
  Megtudhatja, hogyan adhat offline támogatást az alkalmazáshoz egy Mobile Apps háttér használatával. A kapcsolat nélküli szinkronizálással a felhasználók a Mobile apps szolgáltatással kezelhetik az adat&mdash;t&mdash;megtekinteni, hozzáadni vagy módosítani, még akkor is, ha nincs hálózati kapcsolat.

<!-- URLs -->
[Android – első lépések]: app-service-mobile-android-get-started.md
[Firebase hozzáadása az Android-projekthez]: https://firebase.google.com/docs/android/setup
