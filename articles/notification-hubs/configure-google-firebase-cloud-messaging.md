---
title: Google Firebase Cloud Messaging az Azure Notification Hubs konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálja az Azure notification hub Google Firebase Cloud Messaging beállításokkal.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: d200f5746d5f7c62ff3a52873d5d5d6c3e1322dc
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488371"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>Egy értesítési központ Google Firebase Cloud Messaging (FCM) beállításainak konfigurálása az Azure Portalon
Ez a cikk bemutatja, hogyan Google Firebase Cloud Messaging (FCM) az Azure notification hub beállításainak konfigurálása az Azure portal használatával.  

## <a name="prerequisites"></a>Előfeltételek
Ha még nem hozott létre egy értesítési központ, hozzon létre egyet. További információkért lásd: [Azure notification hub létrehozása az Azure Portalon](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Configure Google Firebase Cloud Messaging (FCM)

Az alábbi eljárás biztosít egy értesítési központ Google Firebase Cloud Messaging (FCM) beállítások konfigurálásának lépései: 

1. Az Azure Portalon az a **értesítési központ** lapon jelölje be **Google (GCM/FCM)** a bal oldali menüben. 
2. Illessze be a **API-kulcs** a korábban mentett FCM-projekt. 
3. Kattintson a **Mentés** gombra. 

   ![A Notification Hubs konfigurálása a Google FCM bemutató képernyőkép](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>További lépések
A részletes oktatóanyag az értesítések leküldése az Android-eszközök Azure Notification Hubs és a Google Firebase Cloud Messaging használatával, lásd: [leküldéses értesítések küldése Android-eszközök a Notification Hubs és a Google FCM használatával ](notification-hubs-android-push-notification-google-fcm-get-started.md).

