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
ms.openlocfilehash: 73304e191242725c80204efb132c26aede9ce7e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80127312"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>A Windows leküldéses értesítési szolgáltatás beállításainak konfigurálása a Azure Portal

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
Az Azure Notification Hubs és a Windows leküldéses értesítési szolgáltatás (WNS) használatával Univerzális Windows-platform alkalmazásokra vonatkozó értesítések küldésének részletes ismertetését lásd: [értesítések küldése UWP alkalmazásokba az azure Notification Hubs használatával](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


