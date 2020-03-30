---
title: Leküldéses értesítések hozzáadása az iOS-hez
description: Megtudhatja, hogy az Azure Mobile Apps használatával hogyan küldhet leküldéses értesítéseket az iOS-alkalmazásnak.
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ad58ee158a2e1842c1b41db281bdd9f04b9b2ca4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461504"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Leküldéses értesítések hozzáadása az iOS-alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket ad hozzá az [iOS gyorsindítási] projekthez, hogy a rendszer leküldéses értesítést küldjön az eszköznek minden alkalommal, amikor egy rekordot beszúr.

Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, szüksége lesz a leküldéses értesítésbővítmény csomagra. További információ: [A .NET háttérkiszolgáló SDK azure mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) útmutatója.

Az [iOS-szimulátor nem támogatja a leküldéses értesítéseket.](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html) Szüksége van egy fizikai iOS-eszközre és egy [Apple Developer Program tagságra.](https://developer.apple.com/programs/ios/)

## <a name="configure-notification-hub"></a><a name="configure-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-app-for-push-notifications"></a><a id="register"></a>Alkalmazás regisztrálása leküldéses értesítésekhez

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Az Azure konfigurálása leküldéses értesítések küldésére

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-backend-to-send-push-notifications"></a><a id="update-server"></a>Háttérrendszer frissítése leküldéses értesítések küldéséhez

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="add-push-notifications-to-app"></a><a id="add-push"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a name="test-push-notifications"></a><a id="test"></a>Leküldéses értesítések tesztelése

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a name="more"></a><a id="more"></a>Továbbiak

* A sablonok rugalmasságot biztosítanak a platformfüggetlen leküldések és a honosított leküldések küldéséhez. [Az iOS-ügyfélkönyvtár használata az Azure Mobile Apps alkalmazáshoz](app-service-mobile-ios-how-to-use-client-library.md#templates) bemutatja, hogyan regisztrálhat sablonokat.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[iOS rövid útmutató]: app-service-mobile-ios-get-started.md
