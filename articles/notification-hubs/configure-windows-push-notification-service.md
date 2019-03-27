---
title: Windows leküldéses értesítési szolgáltatás konfigurálása az Azure Notification Hubs |} A Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja az Azure notification hub Windows leküldéses értesítési szolgáltatás beállításait.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: c3e3f1e7df5c90c690756375ff1e1b0350c72714
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488330"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Egy értesítési központ a Windows leküldéses értesítési szolgáltatásának (WNS) beállításainak konfigurálása az Azure Portalon
Ez a cikk bemutatja, hogyan az Azure notification hub Windows értesítési szolgáltatása (WNS) beállításainak konfigurálása az Azure portal használatával.  

## <a name="prerequisites"></a>Előfeltételek
Ha még nem hozott létre egy értesítési központ, hozzon létre egyet. További információkért lásd: [Azure notification hub létrehozása az Azure Portalon](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Konfigurálja a Windows leküldéses értesítéseket kezelő szolgáltatása (WNS)

Az alábbi eljárás biztosít egy értesítési központ a Windows leküldéses értesítési szolgáltatásának (WNS) beállítások konfigurálásának lépései: 

1. Az Azure Portalon az a **értesítési központ** lapon jelölje be **Windows (WNS)** a bal oldali menüben.
2. Adjon meg értéket a **csomag biztonsági azonosítója** és **biztonsági kulcs**.
3. Kattintson a **Mentés** gombra.

   ![A csomag biztonsági azonosítója és a biztonsági kulcs mezők bemutató képernyőkép](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>További lépések
A részletes oktatóanyag az értesítések leküldése univerzális Windows-Platformos alkalmazások az Azure Notification Hubs és a Windows leküldéses értesítési szolgáltatásának (WNS) használatával, lásd: [küldhetők értesítések UWP-alkalmazásokba az Azure használatával A Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


