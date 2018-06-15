---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: zivraf
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: 9666a8fde808981dd798ff712b96a7c620c9003a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29958894"
---
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>Ütemezett karbantartás miatt a portál nézet virtuális gépek

A tervezett karbantartások wave ütemezve van, és a rendszer értesítéseket küld, ha a közelgő karbantartásokról wave által befolyásolt virtuális gépek listájának figyelheti meg. 

Az Azure-portálon, és keresse meg a virtuális gépek karbantartás ütemezve.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Kattintson a bal oldali navigációs **virtuális gépek**.

3. A virtuális gépek ablaktáblán kattintson a **oszlopok** gombra kattintva nyissa meg az elérhető oszlopok listája.

4. Válassza ki, és adja hozzá a következő oszlopokat:

   **Karbantartási** -karbantartási állapota látható a virtuális gép számára. A lehetséges értékek a következők:
      
      | Érték | Leírás |
      |-------|-------------|
      | Az első lépések | A virtuális gép önkiszolgáló karbantartási időszakban van, amelyben Ön is kezdeményezheti a karbantartást. Lásd az alábbi karbantartási indításához a virtuális gépen | 
      | Ütemezett | A virtuális gép karbantartásra van ütemezve, tehát Ön nem kezdeményezheti a karbantartást. Ebben a nézetben az automatikus ütemezett ablak kiválasztásával vagy a virtuális Gépre kattintva ismerje meg a karbantartási időszak | 
      | Befejeződött | Sikeresen kezdeményezett és karbantartása befejeződött a virtuális gépen. | 
      | Kihagyva| Ön kiválasztotta a karbantartás indítását, de a művelet nem sikerült. Csak akkor használhatják az önkiszolgáló karbantartási lehetőséget. A virtuális Gépet újra kell majd indítani az Azure-ban az ütemezett karbantartási fázisban kell. | 

   **Karbantartási proaktív** -látható, az időszak, amikor karbantartási önálló elindíthatja a virtuális gépeken.
   
   **Ütemezett karbantartás** -látható, az időszak, amikor Azure karbantartás végrehajtásához a virtuális gép újraindul. 




## <a name="notification-and-alerts-in-the-portal"></a>Értesítések és riasztások a portálon

Azure e-mailt küld az előfizetés tulajdonosa és a társtulajdonosok csoport tervezett karbantartás ütemezését kommunikál. Adhat hozzá további címzetteket és csatornák kommunikáció által Azure tevékenység napló riasztásokat hoznak létre. További információ: [előfizetés tevékenység figyelése az Azure tevékenységnapló] (.. / articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válasszon ki **figyelő**. 
3. Az a **figyelője – tevékenységnapló** ablaktáblán válassza előbb **riasztások**.
4. Az a **Monitor - riasztás** ablaktáblán kattintson a **+ Hozzáadás figyelmeztetés a napló**.
5. Végezze el az adatokat a **tevékenység napló riasztás hozzáadása** lapon, és ellenőrizze, hogy a következő beállította **feltételek**: **típus**: karbantartási **állapota**: (Nincs beállítva állapota aktív vagy megoldott) összes **szint**: összes
    
Napló Tevékenységriasztásokat konfigurálásával kapcsolatos további tudnivalókért lásd: [napló riasztások tevékenység létrehozása](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Karbantartási elindítani a virtuális Gépet a portálon

A virtuális gép részletei megnézi, miközben lesz karbantartási kapcsolatos további részletek megtekintéséhez.  
Felső részén a virtuális gép részletes nézete egy új értesítés szalagon lesz hozzáadva, ha a virtuális gép egy tervezett karbantartási wave szerepel. Emellett egy új beállítás kerül indítsa el a karbantartási, amikor lehetséges. 


Kattintson a további részleteket a karbantartás lapon tekintse meg a tervezett karbantartások a karbantartási értesítést. Ott meg fogja tudni **indítsa el a karbantartási** a virtuális gépen.

Karbantartás megkezdése után a virtuális gépet újra kell indítani, és a karbantartási állapota frissülni fog az eredmény megfelelően néhány percen belül.

Ha nem fogadta a ahol megkezdheti a karbantartási időszak, továbbra is kell látni a az ablak, amikor a virtuális Gépet újra lesz indítva az Azure-ban. 
