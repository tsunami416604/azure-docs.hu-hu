---
title: Azure-Tevékenységnaplók összekapcsolása az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az Azure-tevékenységek az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 107bbed8aea692a76f6d5bf6bbf1da26277b2c85
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156573"
---
# <a name="connect-data-from-azure-activity-log"></a>Adatok összekapcsolása az Azure-beli tevékenység naplójából



Egyetlen kattintással továbbíthatja a naplókat az Azure-beli [tevékenységek naplójából](../azure-monitor/platform/platform-logs-overview.md) az Azure sentinelbe. A műveletnapló egy előfizetési napló, amely betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekre. Ez sokféle adatot foglal magában az Azure Resource Manager üzemeltetési adataitól a Service Health-események frissítéseiig. A műveletnapló használatával meghatározhatja a "mit, ki és mikor" minden írási művelethez (PUT, POST, DELETE) az előfizetésében lévő erőforrásokon. Emellett megismerheti a művelet állapotát és az egyéb kapcsolódó tulajdonságokat is. A műveletnapló nem tartalmaz olvasási (GET) műveleteket vagy műveleteket a klasszikus/"RDFE" modellt használó erőforrásokhoz. 


## <a name="prerequisites"></a>Előfeltételek

- Log Analytics munkaterülethez közreműködői jogosultsággal rendelkező felhasználó 


## <a name="connect-to-azure-activity-log"></a>Kapcsolódás az Azure Activity log-hoz

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson az **Azure-tevékenység napló** csempére.

2. Az Azure-tevékenység napló paneljén válassza ki azokat az előfizetéseket, amelyeket az Azure Sentinelbe szeretne továbbítani. 

3. Kattintson a **Connect** (Csatlakozás) gombra.

4. Ha az Azure-tevékenységgel kapcsolatos riasztások esetében a Log Analytics vonatkozó sémát szeretné használni, keresse meg a **AzureActivity**.


 

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható az Azure-tevékenység naplója az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
