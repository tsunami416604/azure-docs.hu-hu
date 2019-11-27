---
title: Leküldéses értesítések beállítása az Azure Notification Hubsban | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be az Azure Notification Hubst a Azure Portal a platform Notification System (PNS) beállításainak használatával.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 951f03f581906e45946ef75742421ba27d405267
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406980"
---
# <a name="set-up-push-notifications-in-a-notification-hub-in-the-azure-portal"></a>Leküldéses értesítések beállítása egy értesítési központban a Azure Portal

Az Azure Notification Hubs egy könnyen használható leküldéses motort biztosít, amely kibővíthető. A Notification Hubs használatával küldhet értesítéseket bármilyen platformra (iOS, Android, Windows, Baidu) és bármilyen háttérből (Felhőbeli vagy helyszíni). További információ: [Mi az az Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

Ebben a rövid útmutatóban a Notification Hubs platform Notification System (PNS) beállításait fogja használni a leküldéses értesítések több platformon történő beállításához. A rövid útmutatóban a Azure Portal elvégzendő lépések láthatók.

Ha még nem hozott létre egy értesítési központot, hozzon létre egyet most. További információ: [Azure Notification hub létrehozása a Azure Portalban](create-notification-hub-portal.md). 

## <a name="apple-push-notification-service"></a>Apple Push Notification szolgáltatás

Apple Push Notification Service (APNS) beállítása:

1. A Azure Portal az **értesítési központ** lapon válassza az **Apple (APNS)** lehetőséget a bal oldali menüből.

1. A **hitelesítési mód**beállításnál válassza a **tanúsítvány** vagy a **token**lehetőséget.

   a. Ha a **tanúsítvány**lehetőséget választja:
   * Válassza ki a fájl ikont, majd válassza ki a feltölteni kívánt *. P12* -fájlt.
   * Adjon meg egy jelszót.
   * Válassza a **Védőfal** módot. Ha leküldéses értesítéseket szeretne küldeni az alkalmazást az áruházból megvásárolt felhasználóknak, válassza a **termelési** mód lehetőséget.

     ![Képernyőkép a APNS-tanúsítvány konfigurációjának Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Ha a **jogkivonat**lehetőséget választja:

   * Adja meg a **kulcs azonosítójának**, **a köteg azonosítójának**, a **csoport azonosítójának**és a **tokennek**az értékeit.
   * Válassza a **Védőfal** módot. Ha leküldéses értesítéseket szeretne küldeni az alkalmazást az áruházból megvásárolt felhasználóknak, válassza a **termelési** mód lehetőséget.

     ![Képernyőkép a APNS-jogkivonat konfigurációjának Azure Portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

További információ: [leküldéses értesítések küldése iOS-re az Azure Notification Hubs használatával](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging"></a>Google Firebase Cloud Messaging

Leküldéses értesítések beállítása a Google Firebase Cloud Messaging (FCM) szolgáltatáshoz:

1. A Azure Portal az **értesítési központ** lapon válassza a **Google (GCM/FCM)** elemet a bal oldali menüből. 
2. Illessze be a korábban mentett FCM-projekthez tartozó **API-kulcsot** . 
3. Kattintson a **Mentés** gombra. 

   ![Képernyőkép, amely bemutatja, hogyan konfigurálható Notification Hubs a Google FCM-hez](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Ha elvégezte ezeket a lépéseket, a riasztás azt jelzi, hogy az értesítési központ frissítése sikeresen megtörtént. A **Mentés** gomb le van tiltva. 

További információ: [leküldéses értesítések Android-eszközökre Notification Hubs és a Google FCM használatával](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Windows leküldéses értesítési szolgáltatás

A Windows leküldéses értesítési szolgáltatás (WNS) beállítása:

1. A Azure Portal az **értesítési központ** lapon válassza a **Windows (WNS)** lehetőséget a bal oldali menüből.
2. Adja meg a **csomag biztonsági azonosítójának** és **biztonsági kulcsának**értékeit.
3. Kattintson a **Mentés** gombra.

   ![Képernyőfelvétel a csomag biztonsági AZONOSÍTÓjának és biztonsági kulcsának mezőiről](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

További információ: [értesítések küldése UWP-alkalmazásoknak az Azure Notification Hubs használatával](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>A Microsoft leküldéses értesítési szolgáltatása a Windows Phone-telefon

A Microsoft leküldéses értesítési szolgáltatás (MPNS) beállítása a Windows Phone-telefonhoz: 

1. A Azure Portal az **értesítési központ** lapon válassza a **Windows Phone-telefon (MPNS)** elemet a bal oldali menüből.
1. Nem hitelesített vagy hitelesített leküldéses értesítések engedélyezése:

   a. A nem hitelesített leküldéses értesítések engedélyezéséhez válassza a nem **hitelesített leküldéses** > **mentésének**engedélyezése lehetőséget.

      ![A nem hitelesített leküldéses értesítések engedélyezését bemutató képernyőkép](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Hitelesített leküldéses értesítések engedélyezése:
      * Az eszköztáron válassza a **tanúsítvány feltöltése**lehetőséget.
      * Válassza ki a fájl ikont, majd válassza ki a tanúsítványfájl.
      * Adja meg a tanúsítványhoz tartozó jelszót.
      * Kattintson az **OK** gombra.
      * A **Windows Phone-telefon (MPNS)** lapon válassza a **Mentés**lehetőséget.

További információ: [leküldéses értesítések Windows Phone-telefon alkalmazások számára Notification Hubs használatával](notification-hubs-windows-mobile-push-notifications-mpns.md).
      

## <a name="baidu-android-china"></a>Baidu (Android China)

Leküldéses értesítések beállítása a Baidu számára:

1. A Azure Portal az **értesítési központ** lapon válassza a **Baidu (Android China)** lehetőséget a bal oldali menüből. 
2. Adja meg a felhőalapú Baidu-értesítés projekt Baidu-konzolján beszerzett **API-kulcsot** . 
3. Adja meg a felhőalapú Baidu-értesítés projekt Baidu-konzolján beszerzett **titkos kulcsot** . 
4. Kattintson a **Mentés** gombra. 

    ![A leküldéses értesítések Baidu (Android China) konfigurációját bemutató Notification Hubs képernyőképe](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Ha elvégezte ezeket a lépéseket, a riasztás azt jelzi, hogy az értesítési központ frissítése sikeresen megtörtént. A **Mentés** gomb le van tiltva. 

További információ: a [Notification Hubs használatának első lépései a Baidu használatával](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban megtanulta, hogyan konfigurálhatja az értesítési központ platform-értesítési rendszerbeállításait a Azure Portal. 

Az értesítések különböző platformokra való leküldésével kapcsolatos további tudnivalókért tekintse meg a következő oktatóanyagokat:

- [Leküldéses értesítések iOS-eszközökre Notification Hubs és APNS használatával](notification-hubs-ios-apple-push-notification-apns-get-started.md)
- [Leküldéses értesítések Android-eszközökre Notification Hubs és a Google FCM használatával](notification-hubs-android-push-notification-google-fcm-get-started.md)
- [Windows-eszközön futó UWP-alkalmazás leküldéses értesítései](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
- [Leküldéses értesítések Windows Phone-telefon 8 alkalmazásba a MPNS használatával](notification-hubs-windows-mobile-push-notifications-mpns.md)
- [Leküldéses értesítések Notification Hubs és felhőalapú Baidu-értesítés használatával](notification-hubs-baidu-china-android-notifications-get-started.md)
