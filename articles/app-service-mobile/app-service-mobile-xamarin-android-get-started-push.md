---
title: Leküldéses értesítések hozzáadása a Xamarin. Android-alkalmazáshoz | Microsoft Docs
description: Ismerje meg, hogyan küldhet leküldéses értesítéseket a Xamarin. Android-alkalmazásba a Azure App Service és az Azure Notification Hubs használatával
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 25e0196fec94acb363757e74fa71f666a4c9d11c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388491"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Leküldéses értesítések hozzáadása Xamarin.Android-alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket adhat hozzá a [Xamarin. Android](app-service-mobile-windows-store-dotnet-get-started.md) gyors üzembe helyezési projekthez, hogy a rendszer minden alkalommal elküldjön egy leküldéses értesítést az eszköznek.

Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, szüksége lesz a leküldéses értesítési bővítmény csomagra. További információ: az Azure-hoz [készült .net backend Server SDK használata Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) útmutató.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következő beállítások szükségesek:

* Aktív Google-fiók. Regisztrálhat egy Google-fiókot a következő címen: [accounts.Google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Google Cloud Messaging ügyfél-összetevő](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Firebase Cloud Messaging engedélyezése

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Az Azure konfigurálása leküldéses kérelmek küldéséhez

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>A kiszolgálói projekt frissítése leküldéses értesítések küldéséhez

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Az ügyfél-projekt konfigurálása leküldéses értesítésekhez

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Leküldéses értesítések kódjának hozzáadása az alkalmazáshoz

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Leküldéses értesítések tesztelése az alkalmazásban

Az alkalmazást az emulátorban található virtuális eszköz segítségével tesztelheti. Az emulátoron való futtatáshoz további konfigurációs lépések szükségesek.

1. A virtuális eszköznek a Google API-kat kell beállítania célként az Android virtuális eszköz (AVD) kezelőjében.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Vegyen fel egy Google-fiókot az Android-eszközre, ehhez kattintson az **alkalmazások** > **Beállítások** > **fiók hozzáadása**lehetőségre, majd kövesse az utasításokat.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Futtassa a ToDoList alkalmazást az előzőekben leírtak szerint, és szúrjon be egy új teendőt. Ekkor az értesítési területen megjelenik egy értesítési ikon. Az értesítés teljes szövegének megtekintéséhez nyissa meg az értesítési fiókot.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
