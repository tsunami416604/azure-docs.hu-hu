---
title: Az Azure értesítési központ konfigurálása a PNS-beállításai |} A Microsoft Docs
description: Ez a rövid útmutatóban megismerheti, hogyan egy értesítési központ konfigurálása az Azure Portalon a platform notification system (PNS) beállításokkal.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 7f7e4a4d75a8e118da6f026817bc4ecfcc7a60db
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314113"
---
# <a name="configure-an-azure-notification-hub-with-platform-notification-system-settings-in-the-azure-portal"></a>A platform notification system beállításai az Azure Portalon egy Azure értesítési központ konfigurálása 
Az Azure Notification Hubs egy egyszerűen használható és kibővített leküldéses értesítési alrendszert biztosít, amellyel értesítéseket küldhet bármilyen platformra (iOS, Android, Windows, Kindle, Baidu stb.) bármilyen háttérrendszerből (felhőbeli vagy helyszíni). A szolgáltatással kapcsolatos további információkért lásd: [Mi az Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

[Az Azure notification hub létrehozása az Azure portal használatával](create-notification-hub-portal.md) Ha már tette. Ez a rövid útmutatóban megismerheti, hogyan egy értesítési központ konfigurálása az Azure Portalon a platform notification system (PNS) beállításokkal.

## <a name="apple-push-notification-service-apns"></a>Apple Push Notification szolgáltatás (APNS)
1. Az a **értesítési központ** az Azure Portalon, válassza a lap **Apple (APNS)** alatt **beállítások** a bal oldali menüben.
2. Ha **tanúsítvány**, és hajtsa végre a következőket:
    1. Válassza ki a **fájl ikon**, és válassza ki a **.p12** fájlt a feltöltéshez. 
    2. Adja meg a **jelszó**.
    3. Válassza a **Védőfal** módot. Az **Éles** beállítást kizárólag akkor használja, ha olyan felhasználóknak szeretne leküldéses értesítéseket küldeni, akik megvásárolták az alkalmazást az áruházból.

        ![APNS-tanúsítvány konfigurálása az Azure Portalon](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)
3. Ha **Token**, és kövesse az alábbi lépéseket: 
    1. Adja meg a **kulcs azonosítója**, **csomagazonosítója**, **csapatazonosító**, és **token**.
    2. Válassza a **Védőfal** módot. Az **Éles** beállítást kizárólag akkor használja, ha olyan felhasználóknak szeretne leküldéses értesítéseket küldeni, akik megvásárolták az alkalmazást az áruházból.

        ![APNS token konfigurálása az Azure Portalon](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

Részletes útmutató az értesítések leküldése az iOS-eszközök Azure Notification Hubs és az Apple Push Notification Service (APNS) használatával, lásd: [ebben az oktatóanyagban](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)
1. Az a **értesítési központ** az Azure Portalon, válassza a lap **Google (GCM/FCM)** alatt **beállítások** a bal oldali menüben. 
2. Illessze be a **kiszolgálókulcs** a korábban mentett FCM-projekt. 
3. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Az Azure Notification Hubs – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Riasztások, hogy a notification hubs sikeresen frissítve az üzenet jelenik meg. A **mentése** gomb le van tiltva. 

Részletes útmutató az értesítések leküldése az Android-eszközök Azure Notification Hubs és a Google Firebase Cloud Messaging használatával, lásd: [ebben az oktatóanyagban](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service-wns"></a>Windows leküldéses értesítéseket kezelő szolgáltatása (WNS)
1. Az a **értesítési központ** az Azure Portalon, válassza a lap **Windows (WNS)** alatt **beállítások** a bal oldali menüben.
2. Adjon meg értéket a **csomag biztonsági azonosítója** és **biztonsági kulcs**.
3. Válassza az eszköztár **Save** (Mentés) elemét.

    ![A Csomag biztonsági azonosítója és a Biztonsági kulcs mezők](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)


Részletes útmutató az értesítések leküldése egy univerzális Windows Platform (UWP) alkalmazást egy Windows-eszközön fut, lásd: [ebben az oktatóanyagban](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="windows-phone---microsoft-push-notification-service"></a>Windows Phone – a Microsoft leküldéses értesítéseket kezelő szolgáltatása
1. Az a **értesítési központ** az Azure Portalon, válassza a lap **Windows Phone (MPNS)** alatt **beállítások**.
2. Ha szeretné engedélyezni, nem hitelesített leküldéses, válassza ki a **nem hitelesített leküldés engedélyezése**, és válassza ki **mentése** az eszköztáron.

    ![Azure Portal – Nem hitelesített leküldéses értesítések engedélyezése](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)
3. Ha használni szeretné a **hitelesített** leküldéses, kövesse az alábbi lépéseket:
    1. Válassza ki **tanúsítvány feltöltése** az eszköztáron.
    2. Válassza ki a **fájl ikon** , és válassza ki a tanúsítványfájl.
    3. Adja meg a **jelszó** a tanúsítványt. 
    4. Válassza ki **OK** gombra kattintva zárja be a **tanúsítvány feltöltése** lapot. 
    5. Az a **Windows Phone(MPNS)** lapon jelölje be **mentése** az eszköztáron.

Részletes útmutató az értesítések leküldése egy Windows Phone 8-alkalmazást a Microsoft Push Notification szolgáltatás (MPNS) használatával, lásd: [ebben az oktatóanyagban](notification-hubs-windows-mobile-push-notifications-mpns.md).
      
## <a name="amazon-device-messaging-adm"></a>Amazon Device Messaging (ADM)
1. Az a **értesítési központ** az Azure Portalon, válassza a lap **Amazon (ADM)** alatt **beállítások** a bal oldali menüben.
2. Adjon meg értéket a **ügyfél-azonosító** és **titkos Ügyfélkód**.
3. Válassza az eszköztár **Save** (Mentés) elemét.
    
    ![Az Azure Notification Hubs - ADM-beállítások](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

Részletes útmutató az Azure Notification Hubs leküldéses értesítések küldéséhez Kindle-alkalmazások használatával, lásd: [ebben az oktatóanyagban](notification-hubs-kindle-amazon-adm-push-notification.md).

## <a name="baidu-android-china"></a>Baidu (Android China)
1. Az a **értesítési központ** az Azure Portalon, válassza a lap **Baidu (Android China)** alatt **beállítások** a bal oldali menüben. 
2. Adja meg a **API-kulcs** , hogy a Baidu-konzolon a Baidu felhőalapú leküldéses projektben. 
3. Adja meg a **titkos kulcs** a Baidu-konzolon a Baidu felhőalapú leküldéses projekt beszerzett. 
4. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Az Azure Notification Hubs – Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)
4. Riasztások, hogy a notification hubs sikeresen frissítve az üzenet jelenik meg. A **mentése** gomb le van tiltva. 

Az Azure Notification Hubs és a Baidu felhőalapú leküldéses értesítéseket küld a teljes Útmutató: [ebben az oktatóanyagban](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>További lépések
Ebből a gyors útmutatóból megtudhatta, hogyan különböző platform notification System egy értesítési központ konfigurálása az Azure Portalon. 

Az értesítések leküldése a különböző platformok teljes részletes tudnivalókért lásd: szereplő oktatóanyagok a **oktatóanyagok** szakaszban.

- [Leküldéses értesítések küldése iOS-eszközök Azure Notification Hubs és az Apple Push Notification Service (APNS) használatával](notification-hubs-ios-apple-push-notification-apns-get-started.md).
- [Leküldéses értesítések küldése Android-eszközök Azure Notification Hubs és a Google Firebase Cloud Messaging használatával](notification-hubs-android-push-notification-google-fcm-get-started.md).
- [Leküldéses értesítések küldése egy univerzális Windows Platform (UWP) alkalmazást egy Windows-eszközön futó](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
- [Leküldéses értesítések küldése egy Windows Phone 8-alkalmazást a Microsoft Push Notification szolgáltatás (MPNS) használatával](notification-hubs-windows-mobile-push-notifications-mpns.md).
- [Leküldéses értesítések küldése Kindle-alkalmazások](notification-hubs-kindle-amazon-adm-push-notification.md).
- [Leküldéses értesítések küldése az Azure Notification Hubs és a Baidu felhőalapú leküldéses](notification-hubs-baidu-china-android-notifications-get-started.md).
