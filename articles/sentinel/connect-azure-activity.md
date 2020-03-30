---
title: Az Azure-tevékenység adatainak összekapcsolása az Azure Sentinelhez | Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze az Azure-tevékenység adatait az Azure Sentinelhez.
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
ms.openlocfilehash: 82dfcaf3394703aae531c828a1b96ad290bab798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124986"
---
# <a name="connect-data-from-azure-activity-log"></a>Adatok csatlakoztatása az Azure-tevékenységnaplóból

Egyetlen kattintással streamelheti a naplókat az [Azure-tevékenységnaplóból](../azure-monitor/platform/platform-logs-overview.md) az Azure Sentinelbe. A tevékenységnapló egy előfizetési napló, amely rögzíti és megjeleníti az azure-beli előfizetési szintű eseményeket, az Azure Resource Manager működési adataitól a Service Health-események frissítéseiig. A tevékenységnapló segítségével meghatározhatja az előfizetés erőforrásain végrehajtott "mit, ki és mikor" műveletet az előfizetés erőforrásain végrehajtott írási műveletekhez. Azt is megtudhatja, a művelet állapotát és más releváns tulajdonságokat. A tevékenységnapló nem tartalmazza a klasszikus/"RDFE" modellt használó erőforrások olvasási (GET) műveleteit vagy műveleteit. 

## <a name="prerequisites"></a>Előfeltételek

- A felhasználónak közreműködői engedélyekkel kell rendelkeznie a Log Analytics-munkaterülethez.
- A felhasználónak reader-engedélyekkel kell rendelkeznie minden olyan előfizetéshez, amelynek naplóit az Azure Sentinelbe szeretné streamelni.

## <a name="set-up-the-azure-activity-connector"></a>Az Azure-tevékenység összekötő beállítása

1. Az Azure Sentinel navigációs menüjében válassza az **Adatösszekötők**lehetőséget. Az összekötők listájában kattintson az **Azure Activity**, majd az **Összekötő lap megnyitása** gombra a jobb alsó sarokban.

2. A **Utasítások** lapon kattintson az **Azure-tevékenységnaplók konfigurálása >** hivatkozásra.

3. Az **Azure-tevékenység naplóablakában** válassza ki azokat az előfizetéseket, amelyek naplóit az Azure Sentinelbe szeretné streamelni. 

4. A jobb oldalon megnyíló előfizetési ablaktáblán kattintson a **Csatlakozás gombra.**

5. Ha a megfelelő sémát szeretné használni az Azure-tevékenységriasztási naplóban, írja be `AzureActivity` a lekérdezési ablakot.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakozhat az Azure-tevékenységnaplóhoz az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- A [beépített](tutorial-detect-threats-built-in.md) vagy [egyéni](tutorial-detect-threats-custom.md) szabályok használatával első lépések észlelheti a fenyegetéseket az Azure Sentinel segítségével.
