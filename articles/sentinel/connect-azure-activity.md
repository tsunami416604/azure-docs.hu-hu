---
title: Az Azure az Azure-on Előzetesben Sentinel-adatok gyűjtése |} A Microsoft Docs
description: Ismerje meg, hogyan gyűjtheti az Azure az Azure Sentinel-adatok.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 068da0c196cba8095aca24a572eb4dfaec685c7a
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992942"
---
# <a name="collect-data-from-azure-activity-log"></a>Adatok gyűjtését az Azure-tevékenységnapló

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Naplóinak streamelheti [Azure tevékenységnapló](../azure-monitor/platform/activity-logs-overview.md) be Azure Sentinel-egyetlen kattintással. A tevékenységnapló egy előfizetési napló, amely az Azure-beli előfizetés-szintű eseményeket betekintést nyújt. Ez magában foglalja az adatok az Azure Resource Manager frissítéseket a Service Health-események operatív adatok széles. A tevékenységnapló használatával megadhatja, hogy a ", mit ki, és mikor" írási műveletet (PUT, POST, DELETE) tett erőforrásokra az előfizetésben. A művelet és az egyéb releváns tulajdonságok állapotát is ismernie is. A tevékenységnapló olvasási (GET) műveleteket, illetve a klasszikus erőforrások műveletei nem tartalmaz / "RDFE" modellt. 


## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdai vagy biztonsági rendszergazdai jogosultsággal rendelkező felhasználó


## <a name="connect-to-azure-activity-log"></a>Csatlakozás Azure-tevékenységnapló

1. Az Azure-Sentinel, válassza **adatgyűjtés** és kattintson a **Azure tevékenységnapló** csempére.

2. A tevékenység az Azure log ablaktáblán jelölje ki a kívánt streamelése az Azure Sentinel-előfizetések. 

3. Kattintson a **Connect** (Csatlakozás) gombra.

4. A megfelelő sémát használ a Log Analytics az Azure tevékenységriasztásokat, keresse meg **AzureActivity**.


 

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerkedhetett az Azure-tevékenységnapló csatlakozni az Azure-Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
