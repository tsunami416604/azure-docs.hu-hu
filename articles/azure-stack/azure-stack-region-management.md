---
title: Régiók kezelése az Azure Stackben |} A Microsoft Docs
description: Régiók kezelése az Azure Stack áttekintése.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: sethm
ms.reviewer: efemmano
ms.openlocfilehash: 401b81ceb7ab71528a4ad11bc7d8944b4d732933
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078865"
---
# <a name="region-management-in-azure-stack"></a>Régiók kezelése az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack régiókban, ahol a logikai entitások-csoportból áll, a hardver-erőforrások az Azure Stack-infrastruktúrát alkotó koncepcióját használja. Régiók kezelése, belül sikeresen működik az Azure Stack-infrastruktúra szükséges összes erőforrást is megtalálhatja.

Egy integrált rendszer központi telepítéséhez (a továbbiakban egy *Azure Stack-felhőben*) lekérdezhetővé teszi akár egyetlen régión. Minden egyes Azure Stack Development Kit rendelkezik egy régiót nevű **helyi**. Ha telepít egy második Azure Stackkel integrált rendszereknél, vagy beállíthat egy másik példánya a csomagban található külön hardvert, az Azure Stack-felhőben egy másik régióban.

## <a name="information-available-through-the-region-management-tile"></a>A régió felügyeleti csempén keresztül érhetők el információk

Az Azure Stack szolgáltatásban elérhető Eszközkezelési lehetőségeivel régió készletével rendelkezik a **régiók kezelése** csempére. Ez a csempe az Azure Stack operátorait, az alapértelmezett irányítópult, a felügyeleti portálon érhető el. Ez a csempe keresztül figyelheti és frissítése az Azure Stack-régió és annak összetevői, amelyek régióspecifikus.

 ![A régió felügyeleti csempe](media/azure-stack-manage-region/image1.png)

 Ha rákattint egy régiót a régió felügyeleti csempén, érhető el a következő információkat:

  ![A régió felügyeleti panel ablaktáblát leírása](media/azure-stack-manage-region/image2.png)

1. **Az erőforrások menüjének**. Itt, hozzáférhet az adott infrastruktúra felügyeleti területeket, és megtekintheti, és felhasználói erőforrások, például a storage-fiókok és a virtuális hálózatok kezelése.

2. **Riasztások**. Ez a rendszerszintű riasztásokat sorolja fel, és ismerteti az egyes riasztások.

3. **Frissítések**. Itt megtekintheti a jelenlegi verziója az Azure Stack-infrastruktúra, az elérhető frissítéseket és a frissítési előzményeket. Az integrált rendszer frissítheti is.

4. **Erőforrás-szolgáltatók**. Erőforrás-szolgáltatók az a hely kezeléséhez a felhasználó funkciói az Azure Stack futtatásához szükséges összetevőket. Mindegyik erőforrás-szolgáltató olyan felügyeleti megoldást tartalmaz. Ez a tapasztalat riasztások tartalmazhatnak a szolgáltató, mérőszámok és más felügyeleti képességek jellemző az erőforrás-szolgáltató.

5. **Infrastruktúra-szerepkörök**. Infrastruktúra-szerepkörök az Azure Stack futtatásához szükséges összetevőket. Csak az infrastruktúra-szerepkör, amely a jelentés a riasztások jelennek meg. A szerepkör kiválasztásával megtekintheti a riasztásokat, a szerepkör és a szerepkörpéldányok, ezt a szerepkört futtató társított.

## <a name="next-steps"></a>További lépések

- [A figyelő állapotát és a riasztások az Azure Stackben](azure-stack-monitor-health.md)
- [Az Azure Stack frissítéseinek kezelése](azure-stack-updates.md)
