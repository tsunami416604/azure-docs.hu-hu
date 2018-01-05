---
title: "Leküldéses értesítések hozzáadása iOS-alkalmazás az Azure Mobile Apps"
description: "Megtudhatja, hogyan küldhet leküldéses értesítéseket az iOS-alkalmazás az Azure Mobile Apps segítségével."
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: 
author: conceptdev
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/10/2016
ms.author: crdun
ms.openlocfilehash: 063926a5a98aeab62e191d372831a00c29b9f6d7
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="add-push-notifications-to-your-ios-app"></a>Leküldéses értesítések hozzáadása az iOS-alkalmazás
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés
Ebben az oktatóanyagban leküldéses értesítések hozzáadása a [iOS quick start] projektre, hogy egy leküldéses értesítést küld az eszköz minden alkalommal, amikor egy olyan rekordot csatlakoztatva van.

Ha nem használja a letöltött gyors üzembe helyezési kiszolgálóprojektet, szüksége lesz a leküldéses értesítési kiterjesztési csomagot. Lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) további információt.

A [iOS-szimulátorban nem támogatja a leküldéses értesítések](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Egy fizikai iOS-eszközön van szüksége, és egy [Apple Fejlesztőprogrambeli tagság](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Értesítési központ konfigurálása
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Regisztrálja az alkalmazást a leküldéses értesítésekre
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Leküldéses értesítések küldéséhez Azure konfigurálása
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Leküldéses értesítések küldéséhez háttérbeli frissítése
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz
[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Teszt leküldéses értesítések
[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Több
* Sablonok rugalmasan, a platformok közötti leküldéses értesítések és honosított leküldéses értesítések küldését. [Azure Mobile Apps készült ügyféloldali kódtár használata iOS hogyan](app-service-mobile-ios-how-to-use-client-library.md#templates) bemutatja, hogyan kell regisztrálni a sablonokat.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[iOS quick start]: app-service-mobile-ios-get-started.md
