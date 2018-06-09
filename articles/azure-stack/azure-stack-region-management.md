---
title: Azure-készletben régió felügyeleti |} Microsoft Docs
description: Azure verem régió management áttekintése.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: brenduns
ms.reviewer: efemmano
ms.openlocfilehash: 0286ed9c7b3fe320b936d33fe3beaddccd6ac0fa
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247534"
---
# <a name="region-management-in-azure-stack"></a>Azure-készletben régió kezelése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure verem használja a régiókban, amely magában foglalja az Azure-verem infrastruktúrát alkotó hardver-erőforrások logikai entitások fogalmát. Régió felügyeleti belül található sikeresen működik az Azure-verem infrastruktúra szükséges összes erőforrást.

Egy integrált rendszer központi telepítése (a továbbiakban egy *Azure verem felhő*) teszi ki egy régiót. Minden Azure verem szoftverfejlesztői készlet rendelkezik egy régió tartozik, nevű **helyi**. Ha telepít egy második integrált Azure verem rendszer, vagy egy másik példánya a csomagban található külön beállítása, a verem Azure felhőben egy másik régióban.

## <a name="information-available-through-the-region-management-tile"></a>Információk a régió felügyeleti csempe keresztül érhetők el

Az Azure verem tartozik egy régiót az elérhető Eszközkezelési lehetőségeivel a **régió felügyeleti** csempére. Ez a csempe nem érhető el az alapértelmezett irányítópulton a felügyeleti portál Azure verem operátor. Ez a csempe keresztül figyelheti és frissítheti az Azure-verem régiójában és az összetevők, amelyek régióspecifikus.

 ![A régió felügyeleti csempe](media/azure-stack-manage-region/image1.png)

 Ha egy régiót a régió felügyeleti csempén gombra kattint, érhető el a következő információkat:

  ![A terület-felügyelet panel ablaktáblái leírása](media/azure-stack-manage-region/image2.png)

1. **Az erőforrás menü**. Itt, hozzáférhet az adott infrastruktúra felügyeleti területek, és megtekintheti, és felhasználói erőforrások, például a storage-fiókok és a virtuális hálózatok kezeléséhez.

2. **Riasztások**. Ez a rendszerszintű riasztásokat sorolja fel, és részleteket közöl a mindegyik vonatkozó riasztási.

3. **Frissítések**. Itt megtekintheti a verem Azure infrastruktúra, rendelkezésre álló frissítéseket és a frissítési előzményeket aktuális verzióját. Az integrált rendszert is frissítheti.

4. **Erőforrás-szolgáltató**. Erőforrás-szolgáltató az a hely kezeléséhez, a felhasználó által kínált funkciót közzétevő Azure verem futtatásához szükséges összetevőket. Mindegyik erőforrás-szolgáltató egy felügyeleti élményt nyújtson tartalmaz. Ez a felület riasztások például az adott szolgáltató, metrikákat és más felügyeleti képességek jellemző az erőforrás-szolgáltató.

5. **Infrastruktúra-szerepkörök**. Infrastruktúra-szerepkörök olyan Azure verem futtatásához szükséges összetevőket. Csak az infrastruktúra-szerepkörök riasztásokat hoznak vannak felsorolva. A szerepkör kiválasztásával megtekintheti a riasztásokat, a szerepkör és a szerepkörpéldányok, ezt a szerepkört futtató társított.

## <a name="next-steps"></a>További lépések

- [A figyelő állapotát és az Azure-készletben riasztások](azure-stack-monitor-health.md)
- [Azure-készletben frissítések kezelése](azure-stack-updates.md)
