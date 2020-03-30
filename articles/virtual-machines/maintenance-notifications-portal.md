---
title: A portál használata karbantartási értesítésekhez
description: Tekintse meg az Azure-ban futó virtuális gépek karbantartási értesítéseit, és indítsa el az önkiszolgáló karbantartást a portál használatával.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 46fcc825ac49f0181ac74e9c3e2deaea577f3329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115738"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Tervezett karbantartási értesítések kezelése a portál használatával

**Ez a cikk linuxos és Windows-os virtuális gépekre vonatkozik.**

A [tervezett karbantartási](maintenance-notifications.md) hullám ütemezése után ellenőrizheti az érintett virtuális gépek listáját. 

Használhatja az Azure Portalon, és keresse meg a karbantartásra ütemezett virtuális gépek.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. A bal oldali navigációs sávon kattintson a **Virtuális gépek gombra.**

3. A Virtuális gépek ablaktáblán válassza az **Oszlopok szerkesztése** gombot az elérhető oszlopok listájának megnyitásához.

4. Jelölje ki és adja hozzá a következő oszlopokat:

   **Karbantartási állapot**: A virtuális gép karbantartási állapotát jeleníti meg. A lehetséges értékek a következők:
      
      | Érték | Leírás |
      |-------|-------------|
      | Indítás most | A virtuális gép az önkiszolgáló karbantartási ablakban, amely lehetővé teszi, hogy saját maga kezdeményezze a karbantartást. Lásd alább, hogyan kell elindítani a karbantartást a virtuális gépen. | 
      | Ütemezett | A virtuális gép karbantartásra van ütemezve, tehát Ön nem kezdeményezheti a karbantartást. A karbantartási időszakról a Karbantartás - Ütemezett ablak kiválasztásával ebben a nézetben, vagy a virtuális gépre kattintva ismerheti meg. | 
      | Már frissítve | A virtuális gép már frissítve van, és jelenleg nincs szükség további műveletre. | 
      | Próbálkozzon később | Ön sikertelenkarbantartást kezdeményezett. Az önkiszolgáló karbantartási lehetőséget egy későbbi időpontban is használhatja. | 
      | Próbálkozzon újra | A korábban sikertelen, saját kezdeményezésű karbantartást újra próbálkozhat. | 
      | - | A virtuális gép nem része a tervezett karbantartási hullám. |
      

   **Karbantartás – Önkiszolgáló ablak**: Megmutatja az időablakot, amikor önbeindíthatja a karbantartást a virtuális gépeken.
   
   **Karbantartás – Ütemezett ablak:** Azt az időablakot jeleníti meg, amikor az Azure a karbantartás befejezéséhez karbantartja a virtuális gépét. 



## <a name="notification-and-alerts-in-the-portal"></a>Értesítések és riasztások a portálon

Az Azure közli a tervezett karbantartás ütemezését azáltal, hogy e-mailt küld az előfizetés tulajdonosának és társtulajdonosai csoportnak. Azure-tevékenységnapló-riasztások létrehozásával további címzetteket és csatornákat adhat hozzá ehhez a kommunikációhoz. További információt a [Tevékenységnapló-értesítések létrehozása a szolgáltatásértesítésekhez](../azure-monitor/platform/alerts-activity-log-service-notifications.md)című témakörben talál.

Győződjön meg arról, hogy az **eseménytípust** **tervezett karbantartásként,** a **Szolgáltatásokat** pedig **virtuálisgép-méretezési készletként** és/vagy **virtuális gépként**állította be.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Karbantartás indítása a virtuális gépen a portálról

A virtuális gép részleteinek megtekintése közben további karbantartással kapcsolatos részleteket láthat.  
A virtuális gép részletes nézetének tetején egy új értesítési menüszalag kerül hozzáadásra, ha a virtuális gép szerepel egy tervezett karbantartási hullámban. Ezenkívül egy új lehetőség is hozzáadódik a karbantartás megkezdéséhez, ha lehetséges. 


Kattintson a karbantartási értesítésre a karbantartási oldal megtekintéséhez a tervezett karbantartás további részleteit. Innen a virtuális gép **karbantartását is elindíthatja.**

A karbantartás megkezdése után a virtuális gép megmarad, és a karbantartási állapot néhány percen belül frissül, hogy tükrözze az eredményt.

Ha lemaradt az önkiszolgáló ablakról, továbbra is láthatja az ablakot, amikor a virtuális gép az Azure által karbantartott lesz. 


## <a name="next-steps"></a>További lépések

A tervezett karbantartást az [Azure CLI](maintenance-notifications-cli.md) vagy a [PowerShell](maintenance-notifications-powershell.md)használatával is kezelheti.