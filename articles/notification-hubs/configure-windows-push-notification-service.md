---
title: A Windows leküldéses értesítési szolgáltatáskonfigurálása az Azure Értesítési központokban | Microsoft dokumentumok
description: Megtudhatja, hogyan konfigurálhatja a Windows leküldéses értesítési szolgáltatás beállításait egy Azure-értesítési központhoz.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127312"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>A Windows leküldéses értesítési szolgáltatás beállításainak konfigurálása az Azure Portalon

Ez a cikk bemutatja, hogyan konfigurálhatja a Windows értesítési szolgáltatás (WNS) beállításait egy Azure értesítési központ az Azure Portal használatával.  

## <a name="prerequisites"></a>Előfeltételek
Ha még nem hozott létre értesítési központot, hozzon létre egyet most. További információ: [Azure-értesítési központ létrehozása az Azure Portalon.](create-notification-hub-portal.md) 

## <a name="configure-windows-push-notification-service-wns"></a>A Windows leküldéses értesítési szolgáltatás (WNS) konfigurálása

Az alábbi eljárás a Windows leküldéses értesítési szolgáltatás (WNS) értesítési központ beállításainak konfigurálásának lépéseit tartalmazza: 

1. Az Azure Portalon az **Értesítési központ** lapon válassza a **Windows (WNS)** lehetőséget a bal oldali menüben.
2. Adja meg a **csomag biztonsági azonosítójának** és **biztonsági kulcsának értékeit.**
3. Kattintson a **Mentés** gombra.

   ![Képernyőkép a Csomag biztonsági azonosítója és a biztonsági kulcs mezőiről](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>További lépések
Az Értesítések küldése az Azure Értesítési központok és a Windows leküldéses értesítési szolgáltatás (WNS) használatával az értesítések univerzális Windows-platformalkalmazásokba történő leküldése lépésenkénti útmutatóját az [Értesítések küldése az UWP-alkalmazásoknak az Azure Értesítési központok használatával című](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)témakörben találja.


