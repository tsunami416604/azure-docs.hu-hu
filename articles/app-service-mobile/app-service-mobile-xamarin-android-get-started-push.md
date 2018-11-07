---
title: Leküldéses értesítések hozzáadása Xamarin.Android-alkalmazáshoz |} A Microsoft Docs
description: Az Azure App Service és az Azure Notification Hubs használata leküldéses értesítések küldésére Xamarin.Android-alkalmazáshoz
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 71cb39465bd6c180861325ab451aa11be14a80e0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51231669"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Leküldéses értesítések hozzáadása Xamarin.Android-alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban a leküldéses értesítések hozzáadása az [Xamarin.Android rövid](app-service-mobile-windows-store-dotnet-get-started.md) projekthez, hogy a leküldéses értesítést küld az eszköz minden alkalommal, amikor a rendszer beszúr egy rekordot.

Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, szüksége lesz a leküldéses értesítési kiterjesztési csomag. További információkért lásd: a [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) útmutató.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a telepítés:

* Aktív Google-fiók. A egy Google-fiókot regisztrálhatnak [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [A Google Cloud Messaging Client összetevő](http://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Egy értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Engedélyezze a Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Leküldéses kéréseket küldhet az Azure konfigurálása

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Leküldéses értesítések küldéséhez a kiszolgálói projekt frissítése

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Az ügyfélprojekt a leküldéses értesítések konfigurálása

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Leküldéses értesítések kód hozzáadása az alkalmazáshoz

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Teszt leküldéses értesítések az alkalmazásban

Az alkalmazást az emulátorban a virtuális eszköz segítségével tesztelheti. Nincsenek további konfigurációs lépéseket az emulátor futtatásához szükséges.

1. A virtuális eszköz rendelkeznie kell a Google API-k, az Android virtuális eszközt (AVD) Manager célként beállítva.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Google-fiók hozzáadása az Android-eszközre kattintva **alkalmazások** > **beállítások** > **fiók hozzáadása**, majd kövesse az utasításokat.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. A todolist alkalmazást előtt futtassa, és a egy új teendő elem beszúrása. Ennek során egy értesítési ikon jelenik meg az értesítési területen. A teljes szöveg a bejelentés megtekintése az értesítési fiókot is megnyithatja.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
