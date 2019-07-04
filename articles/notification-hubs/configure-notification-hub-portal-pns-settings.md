---
title: Az Azure Notification Hubs leküldéses értesítések beállítása |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be az Azure Notification Hubs az Azure Portalon platform notification system (PNS) beállítások segítségével.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 1037d8d4652f238f03d8e80b0c59a5f396ab5605
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445719"
---
# <a name="set-up-push-notifications-in-a-notification-hub-in-the-azure-portal"></a>Egy értesítési központ, az Azure Portalon a leküldéses értesítések beállítása

Az Azure Notification Hubs biztosít egy leküldéses összetevő, amely könnyen használható és, amely elvégzi a horizontális felskálázást. A Notification Hubs használatával bármilyen platformon (iOS, Android, Windows, Baidu), és bármely háttérrendszerből (felhőbeli vagy helyszíni) értesítések küldése. További információkért lásd: [Mi az Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

Ebben a rövid a platform notification system (PNS) beállításokat fogjuk a Notification Hubs leküldéses értesítések több platformon beállításához. Ez a rövid útmutató bemutatja, milyen lépéseket kell tennie az Azure Portalon.

Ha még nem hozott létre egy értesítési központ, hozzon létre egyet. További információkért lásd: [Azure notification hub létrehozása az Azure Portalon](create-notification-hub-portal.md). 

## <a name="apple-push-notification-service"></a>Apple Push Notification szolgáltatás

Fel az Apple Push Notification Service (APNS) beállítása:

1. Az Azure Portalon az a **értesítési központ** lapon jelölje be **Apple (APNS)** a bal oldali menüből.

1. A **hitelesítési mód**, ezek közül bármelyikre **tanúsítvány** vagy **Token**.

   a. Ha **tanúsítvány**:
   * A fájl ikonra, és válassza a *.p12* feltölteni kívánt fájlt.
   * Adjon meg egy jelszót.
   * Válassza a **Védőfal** módot. Vagy a leküldéses értesítések küldéséhez felhasználók, akik megvásárolták az alkalmazást az áruházból, válassza ki a **éles** mód.

     ![Képernyőkép az APNS-tanúsítvány konfigurálása az Azure Portalon](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Ha **Token**:

   * Adja meg a **Kulcsazonosító**, **Alkalmazásköteg-azonosító**, **Csapatazonosító**, és **Token**.
   * Válassza a **Védőfal** módot. Vagy a leküldéses értesítések küldéséhez felhasználók, akik megvásárolták az alkalmazást az áruházból, válassza ki a **éles** mód.

     ![Képernyőkép az APN szolgáltatás jogkivonat-konfiguráció az Azure Portalon](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

További információkért lásd: [leküldéses értesítések iOS-re az Azure Notification Hubs használatával](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging"></a>Google Firebase Cloud Messaging

To set up push notifications for Google Firebase Cloud Messaging (FCM):

1. Az Azure Portalon az a **értesítési központ** lapon jelölje be **Google (GCM/FCM)** a bal oldali menüből. 
2. Illessze be a **API-kulcs** a korábban mentett FCM-projekt. 
3. Kattintson a **Mentés** gombra. 

   ![A Notification Hubs konfigurálása a Google FCM bemutató képernyőkép](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

A lépések végrehajtása, ha egy a riasztás azt jelzi, hogy az értesítési központ frissítése sikeresen befejeződött. A **mentése** gomb le van tiltva. 

További információkért lásd: [leküldéses értesítések küldése Android-eszközök a Notification Hubs és a Google FCM használatával](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Windows leküldéses értesítéseket kezelő szolgáltatása

Fel Windows leküldéses értesítési szolgáltatásának (WNS) beállítása:

1. Az Azure Portalon az a **értesítési központ** lapon jelölje be **Windows (WNS)** a bal oldali menüből.
2. Adjon meg értéket a **csomag biztonsági azonosítója** és **biztonsági kulcs**.
3. Kattintson a **Mentés** gombra.

   ![A csomag biztonsági azonosítója és a biztonsági kulcs mezők bemutató képernyőkép](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

További információ: [küldhetők értesítések UWP-alkalmazásokba az Azure Notification Hubs használatával](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>A Microsoft leküldéses értesítési szolgáltatás a Windows Phone

A Windows Phone állítsa be a Microsoft leküldéses értesítési szolgáltatásának (MPNS): 

1. Az Azure Portalon az a **értesítési központ** lapon jelölje be **Windows Phone (MPNS)** a bal oldali menüből.
1. Vagy nem hitelesített vagy hitelesített leküldéses értesítések engedélyezése:

   a. Nem hitelesített leküldéses értesítések engedélyezéséhez jelölje be **nem hitelesített leküldés engedélyezése** > **mentése**.

      ![Képernyőkép a nem hitelesített leküldéses értesítések engedélyezése](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Hitelesített leküldéses értesítések engedélyezése:
      * Válassza az eszköztár **tanúsítvány feltöltése**.
      * A fájl ikonra, és válassza ki a tanúsítványfájl.
      * Adja meg a tanúsítványhoz tartozó jelszót.
      * Kattintson az **OK** gombra.
      * Az a **Windows Phone (MPNS)** lapon jelölje be **mentése**.

További információkért lásd: [leküldéses értesítések Windows Phone-alkalmazások, a Notification Hubs használatával](notification-hubs-windows-mobile-push-notifications-mpns.md).
      

## <a name="baidu-android-china"></a>Baidu (Android China)

Baidu leküldéses értesítések beállítása:

1. Az Azure Portalon az a **értesítési központ** lapon jelölje be **Baidu (Android China)** a bal oldali menüből. 
2. Adja meg a **Api-kulcs** a Baidu felhőalapú leküldéses projekt Baidu-konzoljában beszerzett. 
3. Adja meg a **titkos kulcs** a Baidu felhőalapú leküldéses projekt Baidu-konzoljában beszerzett. 
4. Kattintson a **Mentés** gombra. 

    ![Képernyőkép a Notification hubs használatával, amely megjeleníti a Baidu (Android China) konfigurációját, a leküldéses értesítések](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

A lépések végrehajtása, ha egy a riasztás azt jelzi, hogy az értesítési központ frissítése sikeresen befejeződött. A **mentése** gomb le van tiltva. 

További információkért lásd: [első lépésként az Notification Hubs baiduval való használatával](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>További lépések
Ebből a gyors útmutatóból megtudhatta, hogyan platform notification system beállítások egy értesítési központ konfigurálása az Azure Portalon. 

Leküldéses értesítések küldése a különböző platformok kapcsolatos további tudnivalókért lásd: ezek az oktatóanyagok:

- [Leküldéses értesítések küldése iOS-eszközök az APNS és a Notification Hubs használatával](notification-hubs-ios-apple-push-notification-apns-get-started.md)
- [Leküldéses értesítések küldése Android-eszközök a Notification Hubs és a Google FCM használatával](notification-hubs-android-push-notification-google-fcm-get-started.md)
- [Leküldéses értesítések küldéséhez egy Windows-eszközön futó UWP-alkalmazás](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
- [Egy Windows Phone 8-alkalmazást, az MPNS leküldéses értesítések küldése](notification-hubs-windows-mobile-push-notifications-mpns.md)
- [Leküldéses értesítések a Notification Hubs és a Baidu felhőalapú leküldéses használatával](notification-hubs-baidu-china-android-notifications-get-started.md)
