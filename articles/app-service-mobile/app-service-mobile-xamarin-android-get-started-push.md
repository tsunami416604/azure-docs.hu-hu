---
title: Leküldéses értesítések hozzáadása Xamarin.Android-alkalmazáshoz
description: Ismerje meg, hogyan küldhet leküldéses értesítéseket az Azure App Service és az Azure Értesítési központok használatával a Xamarin.Android alkalmazásnak.
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5657be0dbaeb46f8f899a9b4a2f8ba9b4fe9ebaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249307"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Leküldéses értesítések hozzáadása Xamarin.Android-alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket ad hozzá a [Xamarin.Android gyorsindítási](app-service-mobile-windows-store-dotnet-get-started.md) projekthez, hogy a rendszer minden rekord beszúrásakor leküldéses értesítést küldjön az eszköznek.

Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, szüksége lesz a leküldéses értesítésbővítmény csomagra. További információt a [.NET háttérkiszolgáló SDK azure-beli mobilalkalmazásokhoz útmutatója](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag a következőket igényli:

* Aktív Google-fiók. A google-fiókot a [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)regisztrálhatja.
* [Google Cloud Messaging Ügyfélösszetevő](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="enable-firebase-cloud-messaging"></a><a id="register"></a>A Firebase Cloud Messaging engedélyezése

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Az Azure konfigurálása leküldéses kérelmek küldésére

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a><a id="update-server"></a>A kiszolgálóprojekt frissítése leküldéses értesítések küldéséhez

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-the-client-project-for-push-notifications"></a><a id="configure-app"></a>Az ügyfélprojekt konfigurálása leküldéses értesítésekhez

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a name="add-push-notifications-code-to-your-app"></a><a id="add-push"></a>Leküldéses értesítéskód hozzáadása az alkalmazáshoz

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test-push-notifications-in-your-app"></a><a name="test"></a>Leküldéses értesítések tesztelése az alkalmazásban

Az alkalmazást az emulátorban lévő virtuális eszköz használatával tesztelheti. Az emulátoron való futtatáshoz további konfigurációs lépések szükségesek.

1. A virtuális eszköznek rendelkeznie kell a Google API-k célként való beállításával az Android virtuális eszköz (AVD) kezelőjében.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Adjon hozzá egy Google-fiókot az Android-eszközhöz az **Alkalmazások** > **beállításai** > **fiók hozzáadása**elemre kattintva, majd kövesse az utasításokat.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Futtassa a todolista alkalmazást a korábban, és szúrjon be egy új teendőelemet. Ezúttal egy értesítési ikon jelenik meg az értesítési területen. Az értesítési fiók megnyitásával megtekintheti az értesítés teljes szövegét.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
