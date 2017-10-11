---
title: "Leküldéses értesítések hozzáadása Xamarin.Android-alkalmazáshoz |} Microsoft Docs"
description: "Azure App Service és az Azure Notification Hubs használata leküldéses értesítések küldésére a Xamarin.Android-alkalmazás"
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.openlocfilehash: c3757d56fb1792092710740dc5ffbd64f18730cf
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Leküldéses értesítések hozzáadása Xamarin.Android-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés
Ebben az oktatóanyagban leküldéses értesítések hozzáadása a [Xamarin.Android gyors üzembe helyezési](app-service-mobile-windows-store-dotnet-get-started.md) projektre, hogy egy leküldéses értesítést küld az eszköz minden alkalommal, amikor egy olyan rekordot csatlakoztatva van.

Ha nem használja a letöltött gyors üzembe helyezési kiszolgálóprojektet, szüksége lesz a leküldéses értesítési kiterjesztési csomagot. Lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) további információt.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyaghoz az alábbiakra lesz szükség:

* Aktív Google-fiók. A Google-fiókja, regisztrálhat [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
* [A Google Cloud Messaging Client összetevő](http://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Egy értesítési központ konfigurálása
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Engedélyezze a Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Leküldéses kérelmek küldésére Azure konfigurálása
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Frissítés a kiszolgáló projekt leküldéses értesítések küldéséhez
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Az ügyfél-projektjét, amely a leküldéses értesítések konfigurálása
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Leküldéses értesítések kód hozzáadása az alkalmazáshoz
[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Teszt leküldéses értesítések az alkalmazásban
Az alkalmazást az emulátorban a virtuális eszköz segítségével tesztelheti. Nincsenek az emulátor futtatásához szükséges további konfigurációs lépéseket.

1. Győződjön meg arról, üzembe vagy hibakeresés egy virtuális eszközön, amely rendelkezik a Google API-k, célként beállítva, az Android virtuális eszközt (AVD) kezelő az alábbiak szerint vannak.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)
2. A Google-fiók kattintva vegyen fel új Android-eszköz **alkalmazások** > **beállítások** > **fiók hozzáadása**, majd kövesse a megjelenő utasításokat.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)
3. Futtassa a todolist alkalmazást előtt, és egy új teendő elem beszúrása. Ebben az esetben egy értesítési ikon jelenik meg az értesítési területen. Úgy is megnyithatja az értesítési fiókot, az értesítés a teljes szöveg megtekintéséhez.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
