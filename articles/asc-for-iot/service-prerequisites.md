---
title: Az IoT-Előfeltételek előzetes ASC |} A Microsoft Docs
description: Minden, a részletek szükséges készült IoT-szolgáltatás Előfeltételek ASC használatának első lépései.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 213e6a95484b5f6953f8423474953125f8739015
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541827"
---
# <a name="asc-for-iot-prerequisites"></a>Az IoT-Előfeltételek ASC

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk az ASC IoT service, a kezdéshez szükséges és alapvető fogalmait bővebben tájékozódhat a szolgáltatás különböző építőelemei magyarázatot. 

## <a name="minimum-requirements"></a>Minimális követelmények

- IoT Hub standard szintű csomag
    - RBAC szerepkör **tulajdonosa** szintű jogosultságok 
- [Log Analytics-munkaterület](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Az Azure Security Center (ajánlott)
    - Bár az Azure Security Center használata csak egy javaslatot, és nem követelmény, anélkül, akkor megtekintheti az egyéb Azure-erőforrások belül az IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>ASC IoT-szolgáltatás használata

IoT-elemzések és jelentéskészítés ASC érhetők el az Azure IoT Hub és az Azure Security Center használatával. ASC engedélyezése az Azure IoT hub, a fiók IoT **tulajdonosa** szintű jogosultságok megadása kötelező. Miután engedélyezte a ASC az IoT hub IOT, IoT-elemzések az ASC jelennek meg, a **biztonsági** funkció az Azure IoT Hub és az as **IoT** az Azure Security Centerben. 

## <a name="supported-service-regions"></a>Támogatott szolgáltatási régiók 

Az IoT ASC IoT hub a következő Azure-régióban jelenleg támogatott:
  - USA középső régiója
  - Northern Europe
  - Délkelet-Ázsia

## <a name="wheres-my-iot-hub"></a>Hol található a saját IoT Hub?

Ellenőrizze az IoT Hub hely megkezdése előtt, a szolgáltatás rendelkezésre állásának ellenőrzése. 

1. Nyissa meg az IoT hubnak. 
2. Kattintson az **Áttekintés** elemre. 
3. Ellenőrizze a megadott helyre megegyezik az egyik a [szolgáltatási régiók támogatott](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Ügynökök által támogatott platformok 

IoT-ügynökök ASC egyre nagyobb számban elérhető eszközöket és platformokat támogatja. Tekintse meg a [támogatott platformok listáját](select-deploy-agent.md) ellenőrizheti a meglévő vagy tervezett hálózatieszköz-könyvtár.  

## <a name="next-steps"></a>További lépések
- [Áttekintés](overview.md)
- [A szolgáltatás engedélyezése](quickstart-onboard-iot-hub.md)
- [ASC IOT – gyakori kérdések](resources-frequently-asked-questions.md)
- [Az IoT-riasztásokhoz ASC ismertetése](concept-security-alerts.md)
