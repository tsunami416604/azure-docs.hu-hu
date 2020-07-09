---
title: Leküldéses értesítések küldése a natív alkalmazások az Azure Notification Hubs használatával való Reagálásához egy háttér-szolgáltatáson keresztül | Microsoft Docs
description: Ismerje meg, hogyan küldhet értesítéseket az Azure Notification Hubst használó natív alkalmazások működéséhez a háttérrendszer segítségével.
author: alexeystrakh
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 06/11/2020
ms.author: alstrakh
ms.openlocfilehash: 30cf22d99a675d4525d972aeb889cec30a2e9b94
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060506"
---
# <a name="tutorial-send-push-notifications-to-react-native-apps-using-azure-notification-hubs-via-a-backend-service"></a>Oktatóanyag: leküldéses értesítések küldése a natív alkalmazások az Azure Notification Hubs használatával való Reagálásához egy háttér-szolgáltatáson keresztül  

[![Töltse le ](./media/notification-hubs-backend-service-react-native/download.png) a mintát a minta letöltéséhez](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Natív reagálás](notification-hubs-backend-service-react-native.md)

Ebben az oktatóanyagban az [Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) használatával leküldheti az értesítéseket egy olyan [natív](https://reactnative.dev/) alkalmazásra, amely az **Androidot** és az **iOS**-t célozza meg.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Ez az oktatóanyag végigvezeti a következő lépéseken:

> [!div class="checklist"]
>
> * [A leküldéses Notification Services és az Azure Notification Hubs beállítása.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Hozzon létre egy ASP.NET Core webes API-háttérbeli alkalmazást.](#create-an-aspnet-core-web-api-backend-application)
> * [Hozzon létre egy platformfüggetlen natív alkalmazást.](#create-a-cross-platform-react-native-application)
> * [A natív Android-projekt konfigurálása leküldéses értesítésekhez.](#configure-the-native-android-project-for-push-notifications)
> * [A natív iOS-projekt konfigurálása leküldéses értesítésekhez.](#configure-the-native-ios-project-for-push-notifications)
> * [Tesztelje a megoldást.](#test-the-solution)

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez a következőket kell tennie:

* [Azure-előfizetés](https://portal.azure.com) , ahol erőforrásokat hozhat létre és kezelhet.
* Egy Mac [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) telepítve (vagy egy olyan számítógép, amelyen a [Visual Studio 2019](https://visualstudio.microsoft.com/vs) fut a **.net** munkaterhelés használatával).
* Az alkalmazás futtatásának lehetősége **Android** (fizikai vagy Emulátoros eszközökön) vagy **iOS** rendszeren (csak fizikai eszközökön).

Android esetén a következőket kell tennie:

* A fejlesztő kinyitotta a fizikai eszközt vagy az emulátort *(az API 26-os vagy újabb verzióját futtatja a Google Play-szolgáltatások telepítésével)*.

IOS esetén a következőket kell tennie:

* Aktív [Apple fejlesztői fiók](https://developer.apple.com).
* A [fejlesztői fiókba regisztrált](https://help.apple.com/developer-account/#/dev40df0d9fa) fizikai iOS *-eszköz (iOS 13,0-es vagy újabb verzió)*.
* A **kulcstartóban** telepített **. P12** [fejlesztési tanúsítvány](https://help.apple.com/developer-account/#/dev04fd06d56) lehetővé teszi, hogy egy [alkalmazást fizikai eszközön futtasson](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca).

> [!NOTE]
> Az iOS-szimulátor nem támogatja a távoli értesítéseket, ezért fizikai eszközre van szükség, amikor az iOS-en vizsgálja ezt a mintát. Az oktatóanyag elvégzéséhez azonban nem kell futtatnia az alkalmazást **Androidon** és **iOS** -en is.

Az első elvekben ismertetett lépéseket a korábbi tapasztalatok nélkül is követheti. A következő szempontokat azonban élvezheti:

* [Apple fejlesztői portál](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Google Firebase-konzol](https://console.firebase.google.com/u/0/)
* [Microsoft Azure](https://portal.azure.com) [Leküldéses értesítések Microsoft Azure és küldése iOS-alkalmazásokba az Azure Notification Hubs használatával](ios-sdk-get-started.md).
* [React Native](https://reactnative.dev/docs/getting-started).

A megadott lépések a [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) és a [Visual Studio Code](https://code.visualstudio.com/download) -hoz készültek, de a [Visual Studio 2019](https://visualstudio.microsoft.com/vs)-es használatával követhető.

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Leküldéses Notification Services és az Azure Notification hub beállítása

Ebben a szakaszban a **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** és az **[Apple push Notification Services (APNS)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** beállítást kell beállítania. Ezután létrehozhatja és konfigurálhatja az értesítési központot a szolgáltatásokkal való együttműködéshez.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>ASP.NET Core webes API háttérbeli alkalmazás létrehozása

Ebben a szakaszban a [ASP.net Core webes API](https://dotnet.microsoft.com/apps/aspnet/apis) -hátterét hozza létre az [eszközök regisztrálásának](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) kezeléséhez és értesítések küldéséhez a natív Mobile alkalmazásnak.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-react-native-application"></a>Platformfüggetlen reakciós natív alkalmazás létrehozása

Ebben a szakaszban a leküldéses értesítéseket többplatformos módon végrehajtó [natív](https://reactnative.dev/) mobil alkalmazást hozhat létre.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create React Native application](../../includes/notification-hubs-backend-service-sample-app-reactnative.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>A natív Android-projekt konfigurálása leküldéses értesítésekhez

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-reactnative-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>A natív iOS-projekt konfigurálása leküldéses értesítésekhez

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-reactnative-ios.md)]

## <a name="test-the-solution"></a>A megoldás tesztelése

Most tesztelheti az értesítéseket a háttérrendszer használatával.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>További lépések

Most már rendelkeznie kell egy alapszintű, az értesítési központhoz csatlakoztatott natív alkalmazással, amely egy háttér-szolgáltatáson keresztül érhető el, és értesítéseket küldhet és fogadhat.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások

* [Az Azure Notification Hubs áttekintése](notification-hubs-push-notification-overview.md)
* [Visual Studio for Mac telepítése](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* [A Visual Studio Code telepítése](https://code.visualstudio.com/download)
* [A reakciós natív fejlesztési környezet beállítása](https://reactnative.dev/docs/environment-setup)
* [Notification Hubs SDK a háttérbeli műveletekhez](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Notification Hubs SDK a GitHubon](https://github.com/Azure/azure-notificationhubs)
* [Regisztráció az alkalmazás háttérrendszerével](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Regisztrációkezelés](notification-hubs-push-notification-registration-management.md)
* [Címkék használata](notification-hubs-tags-segment-push-message.md)
* [Egyéni sablonok használata](notification-hubs-templates-cross-platform-push-messages.md)
