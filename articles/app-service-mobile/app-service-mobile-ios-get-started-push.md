---
title: Leküldéses értesítések hozzáadása iOS-alkalmazás az Azure Mobile Apps
description: Ismerje meg, hogyan küldhet leküldéses értesítéseket az iOS-alkalmazás az Azure Mobile Apps segítségével.
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: ''
author: conceptdev
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/10/2016
ms.author: crdun
ms.openlocfilehash: b84990d053d92b9408e119b8aba28c8b9f8b046e
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818998"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Leküldéses értesítések hozzáadása iOS-alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban a leküldéses értesítések hozzáadása az [iOS rövid] projekthez, hogy a leküldéses értesítést küld az eszköz minden alkalommal, amikor a rendszer beszúr egy rekordot.

Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, szüksége lesz a leküldéses értesítési kiterjesztési csomag. További információkért lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) útmutató.

A [az iOS-szimulátor nem támogatja a leküldéses értesítések](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Kell egy fizikai iOS-eszköz és a egy [Apple Fejlesztőprogrambeli tagság](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Alkalmazás regisztrálása leküldéses értesítésekhez

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Leküldéses értesítések küldése az Azure konfigurálása

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Leküldéses értesítések küldéséhez háttérbeli frissítése

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Leküldéses értesítések hozzáadása alkalmazáshoz

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Teszt leküldéses értesítések

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Több

* Sablonok lehetővé teszik, többplatformos leküldések és honosított leküldéses értesítést küldhet. [Azure Mobile Apps-Klienskódtárának használata iOS hogyan](app-service-mobile-ios-how-to-use-client-library.md#templates) bemutatja, hogyan regisztrálhat a sablonokat.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[iOS rövid]: app-service-mobile-ios-get-started.md
