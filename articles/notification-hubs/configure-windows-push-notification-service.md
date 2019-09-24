---
title: A Windows leküldéses értesítési szolgáltatás konfigurálása az Azure Notification Hubsban | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure Notification hub Windows leküldéses értesítési szolgáltatásának beállításait.
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
ms.openlocfilehash: a7f7734d97cd67c133ff0cedc3ef2376967bcdf4
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212409"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Az értesítési központ Windows leküldéses értesítési szolgáltatásának (WNS) beállításainak konfigurálása a Azure Portal
Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Notification hub Windows Notification Service (WNS) beállításait a Azure Portal használatával.  

## <a name="prerequisites"></a>Előfeltételek
Ha még nem hozott létre egy értesítési központot, hozzon létre egyet most. További információ: [Azure Notification hub létrehozása a Azure Portalban](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>A Windows leküldéses értesítési szolgáltatás (WNS) konfigurálása

Az alábbi eljárás az értesítési központ Windows leküldéses értesítési szolgáltatásának (WNS) beállításainak konfigurálásához nyújt útmutatást: 

1. A Azure Portal az **értesítési központ** lapon válassza a **Windows (WNS)** lehetőséget a bal oldali menüben.
2. Adja meg a **csomag biztonsági azonosítójának** és **biztonsági kulcsának**értékeit.
3. Kattintson a **Mentés** gombra.

   ![Képernyőfelvétel a csomag biztonsági AZONOSÍTÓjának és biztonsági kulcsának mezőiről](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>További lépések
Az Azure-Notification Hubs és a Windows leküldéses értesítési szolgáltatás (WNS) használatával történő értesítések küldésére vonatkozó részletes Univerzális Windows-platform útmutatásért lásd: [értesítések küldése UWP alkalmazásokba az Azure Notification használatával Hubok](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


