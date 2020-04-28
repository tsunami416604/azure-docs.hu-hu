---
title: A Google Firebase Cloud Messaging konfigurálása az Azure Notification Hubsban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy Azure Notification hub-t a Google Firebase Cloud Messaging-beállításaival.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127471"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>A Google Firebase beállításainak konfigurálása egy értesítési központhoz a Azure Portal

Ez a cikk bemutatja, hogyan konfigurálhatja a Google Firebase Cloud Messaging (FCM) beállításait egy Azure Notification hub számára a Azure Portal használatával.  

## <a name="prerequisites"></a>Előfeltételek
Ha még nem hozott létre egy értesítési központot, hozzon létre egyet most. További információ: [Azure Notification hub létrehozása a Azure Portalban](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>A Google Firebase Cloud Messaging (FCM) konfigurálása

Az alábbi eljárás lépéseit követve konfigurálhatja az értesítési központ Google Firebase Cloud Messaging (FCM) beállításait: 

1. A Azure Portal az **értesítési központ** lapon válassza a **Google (GCM/FCM)** elemet a bal oldali menüben. 
2. Illessze be a korábban mentett FCM-projekthez tartozó **API-kulcsot** . 
3. Kattintson a **Mentés** gombra. 

   ![Képernyőkép, amely bemutatja, hogyan konfigurálható Notification Hubs a Google FCM-hez](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>További lépések
Az Azure Notification Hubs és a Google Firebase Cloud Messaging használatával folytatott értesítések Android-eszközökre történő leküldésével kapcsolatos részletes útmutatásért lásd: [leküldéses értesítések az Android-eszközökre Notification Hubs és a Google FCM használatával](notification-hubs-android-push-notification-google-fcm-get-started.md).

