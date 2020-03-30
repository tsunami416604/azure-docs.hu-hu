---
title: A Google Firebase felhőalapú üzenetküldésének konfigurálása az Azure Értesítési központokban | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhat azure-értesítési központot a Google Firebase Cloud Messaging beállításaival.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 1adbce654bc5c057270df9a874911731a0135034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127471"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>A Google Firebase beállításainak konfigurálása értesítési központhoz az Azure Portalon

Ez a cikk bemutatja, hogyan konfigurálhatja a Google Firebase Cloud Messaging (FCM) beállításait egy Azure-értesítési központ hoz az Azure Portalon.  

## <a name="prerequisites"></a>Előfeltételek
Ha még nem hozott létre értesítési központot, hozzon létre egyet most. További információ: [Azure-értesítési központ létrehozása az Azure Portalon.](create-notification-hub-portal.md) 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>A Google Firebase felhőalapú üzenetküldésének (FCM) konfigurálása

Az alábbi eljárás a Google Firebase Cloud Messaging (FCM) értesítési központ beállításainak konfigurálásához szükséges lépéseket tartalmazza: 

1. Az Azure Portalon az **Értesítési központ** lapon válassza a **Google (GCM/FCM)** lehetőséget a bal oldali menüben. 
2. Illessze be a korábban mentett FCM-projekt **API-kulcsát.** 
3. Kattintson a **Mentés** gombra. 

   ![Képernyőkép, amely bemutatja, hogyan kell beállítani az értesítési központokat a Google FCM-hez](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>További lépések
Az Értesítések központ és a Google Firebase felhőalapú üzenetküldés használatával az Android-eszközökre történő értesítések leküldéses útmutatóját az Értesítési központok és a [Google FCM használatával](notification-hubs-android-push-notification-google-fcm-get-started.md)című témakörben találja.

