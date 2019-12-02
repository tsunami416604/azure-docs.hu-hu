---
title: Leküldéses értesítések hozzáadása iOS-hez
description: Útmutató leküldéses értesítések iOS-alkalmazásba való küldéséhez az Azure Mobile Apps használatával.
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 2864dfca72c91acd10fd6a4ee8038040ba5defc8
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668814"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Leküldéses értesítések hozzáadása iOS-alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné a felhőszolgáltatásokat a mobilalkalmazásba integrálni, regisztráljon az [App Centerbe](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket ad hozzá az [iOS – gyors útmutató] rövid útmutató projekthez, hogy a rendszer minden egyes rekord behelyezése után leküldéses értesítést küldjön az eszköznek.

Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, szüksége lesz a leküldéses értesítési bővítmény csomagra. További információ: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) útmutató.

Az [iOS-szimulátor nem támogatja a leküldéses értesítéseket](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Szüksége lesz egy fizikai iOS-eszközre és egy [Apple Developer program tagságára](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Alkalmazás regisztrálása leküldéses értesítésekhez

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Az Azure konfigurálása leküldéses értesítések küldéséhez

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>A háttér frissítése leküldéses értesítések küldéséhez

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Leküldéses értesítések tesztelése

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>További

* A sablonok rugalmasságot biztosítanak a platformok közötti leküldések és honosított leküldések küldéséhez. Az [iOS-hez készült ügyféloldali kódtár használata az Azure Mobile apps](app-service-mobile-ios-how-to-use-client-library.md#templates) bemutatja a sablonok regisztrálásának módját.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[iOS – gyors útmutató]: app-service-mobile-ios-get-started.md
