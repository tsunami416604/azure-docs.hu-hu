---
title: Azure-Tevékenységnaplók összekapcsolása az Azure Sentinel szolgáltatással | Microsoft Docs
description: A stream Azure-tevékenységek egyetlen kattintással bejelentkeznek az Azure Sentinelbe. A műveletnapló rögzíti és megjeleníti az előfizetési szintű eseményeket az Azure-ban.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 88f363406ec40dfa439a52ad351501d1c64dce95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564488"
---
# <a name="connect-data-from-azure-activity-log"></a>Adatok összekapcsolása az Azure-beli tevékenység naplójából

Egyetlen kattintással továbbíthatja a naplókat az Azure-beli [tevékenységek naplójából](../azure-monitor/platform/platform-logs-overview.md) az Azure sentinelbe. A műveletnapló egy előfizetési napló, amely az Azure-on keresztül rögzíti és megjeleníti az előfizetési szintű eseményeket, Azure Resource Manager operatív adatokat Service Health események frissítéseire. A műveletnapló használatával meghatározhatja a "mit, ki és mikor" minden írási művelethez (PUT, POST, DELETE) az előfizetéséhez tartozó erőforrásokon. Megtudhatja a művelet állapotát és az egyéb kapcsolódó tulajdonságokat is. A műveletnapló nem tartalmaz olvasási (GET) műveleteket vagy műveleteket a klasszikus/"RDFE" modellt használó erőforrásokhoz. 

## <a name="prerequisites"></a>Előfeltételek

- A felhasználónak közreműködői engedélyekkel kell rendelkeznie a Log Analytics munkaterülethez.
- A felhasználónak rendelkeznie kell Reader-engedéllyel minden olyan előfizetéshez, amelynek naplóit át szeretné venni az Azure Sentinelbe.

## <a name="set-up-the-azure-activity-connector"></a>Az Azure Activity-összekötő beállítása

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők**lehetőséget. Az összekötők listájában kattintson az **Azure-tevékenység**elemre, majd a jobb alsó sarokban található **összekötő megnyitása lap** gombra.

2. Az **utasítások** lapon kattintson az Azure- **tevékenység naplófájljainak konfigurálása >** hivatkozásra.

3. Az **Azure-tevékenység napló** paneljén válassza ki azokat az előfizetéseket, amelyek naplóit az Azure Sentinel szolgáltatásba kívánja továbbítani. 

4. A jobb oldalon megnyíló előfizetés ablaktáblán kattintson a **Kapcsolódás**elemre.

5. Ha a megfelelő sémát szeretné használni Log Analytics az Azure-tevékenység riasztásai esetében, írja be a következőt `AzureActivity` : lekérdezési ablak.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható az Azure-tevékenység naplója az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerkedjen meg a fenyegetések észlelésével az Azure Sentinel használatával, [beépített](tutorial-detect-threats-built-in.md) vagy [Egyéni](tutorial-detect-threats-custom.md) szabályokkal.
