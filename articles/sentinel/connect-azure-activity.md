---
title: Csatlakozás Azure-tevékenységi adatait Azure Sentinel-előzetes verzió |} A Microsoft Docs
description: Ismerje meg, hogyan Azure Sentinel-csatlakozás az Azure-tevékenységi adatait.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d0cc13227bfe02594a57a7fb0ba8ee1cb3383d56
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785159"
---
# <a name="connect-data-from-azure-activity-log"></a>Adatok csatlakoztatása az Azure-tevékenységnapló

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Naplóinak streamelheti [Azure tevékenységnapló](../azure-monitor/platform/activity-logs-overview.md) be Azure Sentinel-egyetlen kattintással. A tevékenységnapló egy előfizetési napló, amely az Azure-beli előfizetés-szintű eseményeket betekintést nyújt. Ez magában foglalja az adatok az Azure Resource Manager frissítéseket a Service Health-események operatív adatok széles. A tevékenységnapló használatával megadhatja, hogy a ", mit ki, és mikor" írási műveletet (PUT, POST, DELETE) tett erőforrásokra az előfizetésben. A művelet és az egyéb releváns tulajdonságok állapotát is ismernie is. A tevékenységnapló olvasási (GET) műveleteket, illetve a klasszikus erőforrások műveletei nem tartalmaz / "RDFE" modellt. 


## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdai vagy biztonsági rendszergazdai jogosultsággal rendelkező felhasználó


## <a name="connect-to-azure-activity-log"></a>Csatlakozás Azure-tevékenységnapló

1. Az Azure-Sentinel, válassza **adatösszekötők** és kattintson a **Azure tevékenységnapló** csempére.

2. A tevékenység az Azure log ablaktáblán jelölje ki a kívánt streamelése az Azure Sentinel-előfizetések. 

3. Kattintson a **Connect** (Csatlakozás) gombra.

4. A megfelelő sémát használ a Log Analytics az Azure tevékenységriasztásokat, keresse meg **AzureActivity**.


 

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerkedhetett az Azure-tevékenységnapló csatlakozni az Azure-Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
