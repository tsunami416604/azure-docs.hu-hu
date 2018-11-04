---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/02/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: b8d759c7b55608be780389991e6bb393f4f3fe9f
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50981524"
---
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>A portálon a karbantartásra ütemezett virtuális gépek megtekintése

Miután egy tervezett karbantartás ütemezve van, figyelheti, hogy a soron következő karbantartás által érintett virtuális gépek listáját. 

Az Azure Portalon, és keresse meg a karbantartásra ütemezett virtuális gépek.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali navigációs sávján kattintson **virtuális gépek**.

3. A virtuális gépek panelén kattintson a **oszlopok** gombra kattintva az elérhető oszlopok listájának megnyitásához.

4. Válassza ki, és adja hozzá a következő oszlopokat:

   **Karbantartási**: a virtuális gép a karbantartás állapotát jeleníti meg. A lehetséges értékek a következők:
      
      | Érték | Leírás |
      |-------|-------------|
      | Az első lépések | A virtuális gép van az önkiszolgáló karbantartási időszakot, amely lehetővé teszi a kezdeményezheti a karbantartást. Lásd az alábbi karbantartási indításához a virtuális Gépen. | 
      | Ütemezett | A virtuális gép karbantartásra van ütemezve, tehát Ön nem kezdeményezheti a karbantartást. Karbantartási időszak kiválasztásával a karbantartás - ütemezett ablakot ebben a nézetben, vagy a virtuális gépre kattintva tudhat meg. | 
      | Már frissítve | A virtuális gép már frissítve van, és jelenleg semmilyen további műveletet nem szükséges. | 
      | Újrapróbálkozás később. | A művelet nem sikerült az karbantartási kezdeményezett. Fogja tudni használni az önkiszolgáló karbantartás lehetősége egy későbbi időpontban. | 
      | Próbálkozás most | A korábban sikertelen önálló által kezdeményezett karbantartás újra. | 
      | - | A virtuális gép nem szerepel egy tervezett karbantartási műveletek. |
      

   **Karbantartás – önkiszolgáló ablak**: jeleníti meg az időtartomány, ha önálló elindítható a karbantartás a virtuális gépeken.
   
   **Karbantartás – ütemezett ablak**: jeleníti meg az időtartomány, ha Azure megőrzi a virtuális gép karbantartás elvégzéséhez. 



## <a name="notification-and-alerts-in-the-portal"></a>Értesítések és riasztások a portálon

Az Azure tervezett karbantartási ütemezés egy e-mailt küld az előfizetés tulajdonosa és a társtulajdonosok csoport kommunikál. Adhat hozzá további címzettek és csatornák a kommunikáció Azure-tevékenységi riasztások létrehozásával. További információkért lásd: [tevékenységnapló-riasztások létrehozása a szolgáltatási értesítések](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

Mindenképpen állítsa be a **eseménytípus** , **tervezett karbantartás** és **szolgáltatások** , **Virtual Machine Scale Sets** és/vagy **Virtuális gépek**
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Karbantartás indítása a portálról a virtuális gépen

Megnézi a virtuális gép adatait, miközben lesz karbantartási kapcsolatos további részletek megtekintéséhez.  
A virtuális gép részletei nézet tetején egy új értesítési menüszalag fog bővülni Ha a virtuális gép egy tervezett karbantartási műveletek is szerepel. Emellett egy új beállítás hozzáadódik indítsa el a karbantartást, amikor csak lehetséges. 


Kattintson a karbantartási értesítést, a a tervezett karbantartással kapcsolatos további részleteket a karbantartás oldalon talál. Itt meg fogja tudni **indítsa el a karbantartási** a virtuális Gépen.

Elindítja a karbantartási, miután a virtuális gép továbbra is ugyanúgy működik, és a karbantartási állapot fog frissülni, hogy az eredmény pár percen belül.

Ha lemaradt az önkiszolgáló ablak, akkor továbbra is képesek lesznek az ablakban megtekintheti, ha a virtuális gép továbbra is ugyanúgy működik, az Azure-ban. 
