---
title: "Azure-készletben régió felügyeleti |} Microsoft Docs"
description: "Azure verem régió management áttekintése."
services: azure-stack
documentationcenter: 
author: efemmano
manager: dsavage
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: efemmano
ms.openlocfilehash: d1310f0cb9a820366ab8712a782785e955a24134
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="region-management-in-azure-stack"></a>Azure-készletben régió kezelése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure verem rendelkezik a régiókban, amely magában foglalja az Azure-verem infrastruktúrát alkotó hardver-erőforrások logikai entitások fogalmát. Belül régió felügyeleti található összes erőforrást az Azure-verem infrastruktúra életciklus sikeresen működéséhez szükséges.

Egy integrált rendszer központi telepítése (a továbbiakban egy *Azure verem felhő*) teszi ki egy régiót. Minden Azure verem szoftverfejlesztői készlet rendelkezik egy régió tartozik, nevű **helyi**. Ha telepít egy második integrált Azure verem rendszer, vagy egy másik példánya a csomagban található külön beállítása, a verem Azure felhőben egy másik régióban.

## <a name="information-available-through-the-region-management-tile"></a>Információk a régió felügyeleti csempe keresztül érhetők el
Az Azure verem tartozik egy régiót az elérhető Eszközkezelési lehetőségeivel a **régió felügyeleti** csempére. Ez a csempe nem érhető el az alapértelmezett irányítópulton a felügyeleti portál Azure verem operátor. Ez a csempe keresztül figyelheti és frissítheti az Azure-verem régiójában és az összetevők, amelyek régióspecifikus.

 ![A régió felügyeleti csempe](media/azure-stack-manage-region/image1.png)

 Ha egy régiót a régió felügyeleti csempén gombra kattint, érhető el a következő információkat:

  ![A terület-felügyelet panel ablaktáblái leírása](media/azure-stack-manage-region/image2.png)

1. **Az erőforrás menü**. Itt, hozzáférhet az adott infrastruktúra felügyeleti területek, és megtekintheti, és felhasználói erőforrások, például a storage-fiókok és a virtuális hálózatok kezeléséhez.

2. **Riasztások**. Ez a csempe rendszerszintű riasztásokat sorolja fel, és egyes vonatkozó riasztási részleteit.

3. **Frissítések**. Ez a csempe megtekintheti a verem Azure infrastruktúra aktuális verzióját.

4. **Erőforrás-szolgáltató**. Erőforrás-szolgáltató az a hely kezeléséhez a bérlő által kínált funkciót közzétevő Azure verem futtatásához szükséges összetevőket. Mindegyik erőforrás-szolgáltató egy felügyeleti élményt nyújtson tartalmaz. Ez a felület riasztások például az adott szolgáltató, metrikákat és más felügyeleti képességek jellemző az erőforrás-szolgáltató.
 
5. **Infrastruktúra-szerepkörök**. Infrastruktúra-szerepkörök olyan Azure verem futtatásához szükséges összetevőket. Csak az infrastruktúra-szerepkörök riasztásokat hoznak vannak felsorolva. Egy szerepkör kattintva tekintheti meg a megfelelő szerepkört és a szerepkörpéldányok, ezt a szerepkört futtató tartozó riasztásokat. Bár a funkció elindításához, indítsa újra, vagy állítsa le az infrastruktúra-szerepkör példánya, tegye **nem** ehhez development kit környezetben. Ezek a beállítások készültek, csak egy több csomópontos környezetben, ahol van egy infrastruktúra-szerepkör egynél több szerepkörpéldányt. A szerepkör példánya (különösen AzS-Xrp01) a csomagban újraindít rendszer instabil.

## <a name="next-steps"></a>Következő lépések
[A figyelő állapotát és az Azure-készletben riasztások](azure-stack-monitor-health.md)

[Azure-készletben frissítések kezelése](azure-stack-updates.md)






