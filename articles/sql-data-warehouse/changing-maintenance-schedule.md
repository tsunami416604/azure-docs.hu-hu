---
title: Az Azure karbantartási ütemezések (előzetes verzió) |} A Microsoft Docs
description: Karbantartási ütemezés lehetővé teszi, hogy az ügyfelek számára a szükséges ütemezett karbantartási események az Azure SQL Data Warehouse szolgáltatás által használt vezethet be új funkciók, frissítések és javítások körül megtervezése.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/15018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 75384aed8c354cf2d549d92cdb75f87038f33ab8
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713265"
---
# <a name="change-a-maintenance-schedule"></a>Karbantartási ütemezés módosítása 

## <a name="portal"></a>Portál
Karbantartási ütemezés frissíthetők vagy bármikor módosítható. Ha a kiválasztott példány egy aktív karbantartási folyamata alatt tartózkodnak, a rendszer menti a beállításokat. Akkor lesz aktívvá válik a következő azonosított karbantartási időszak során. [További](https://docs.microsoft.com/azure/service-health/resource-health-overview) egy aktív karbantartási események során az adatraktár figyelésével kapcsolatos. 

Karbantartási ütemezések használ, ki kell választania a két karbantartási időszakok egy 7 napos időszakban. Az egyes karbantartási időszakok 3-8 óra is lehet. Karbantartási is egy karbantartási időszakban bármikor előfordulhatnak, de nem megy végbe, előzetes értesítés nélkül idő időszakon kívül. A kapcsolat rövid adatvesztést is, a szolgáltatás telepíti az új kódot az adattárház lesz tapasztalható. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Az elsődleges és másodlagos windows azonosítása

Az elsődleges és másodlagos windows kell rendelkeznie időtartomány külön. Lehet például egy elsődleges ablak kedd – csütörtök, és a egy másodlagos ablak szombat – vasárnap.

A data warehouse-hoz a karbantartási ütemezés módosításához kövesse az alábbi lépéseket:
1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  Válassza ki a frissíteni kívánt adatraktár. Az oldal megnyílik az áttekintési panelen. 
3.  Nyissa meg a karbantartási ütemezés beállításai oldal kiválasztásával a **karbantartási ütemezés (előzetes verzió) összegzés** hivatkozás az áttekintési panelen. Vagy válassza ki a **karbantartási ütemezés** lehetőséget a bal oldali erőforrás menüben.  

    ![Áttekintés panel beállításai](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Azonosítják az előnyben részesített naptartomány az elsődleges karbantartási időszak a beállításokat az oldal tetején. Ez a beállítás határozza meg, ha az elsődleges ablak történik, a hét napja vagy a hétvégén. A kijelölt frissíteni fogja a legördülő értékeket. Előzetes verzióban az egyes régiókban előfordulhat, hogy még nem támogatja az elérhető a teljes körű **nap** beállítások.

   ![Karbantartási beállítások panel](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. A legördülő lista használatával válassza ki a kívánt elsődleges és másodlagos karbantartási időszakok:
   - **Nap**: előnyben részesített napi karbantartás végrehajtásához a kijelölt időszakban.
   - **Kezdési idő**: a karbantartási időszak előnyben részesített kezdési idejét.
   - **Időtartomány**: előnyben részesített az időtartomány időtartama.

   A **ütemezés összegzése** területet a panel alján, frissül a kiválasztott értékek alapján. 
  
6. Kattintson a **Mentés** gombra. Megjelenik egy üzenet, igazolja, hogy az új ütemezés most már aktív. 

   Ha egy ütemezést egy régióban, amely nem támogatja a karbantartási ütemezés menti, a következő üzenet jelenik meg. A beállítások mentett és aktív válnak, amikor a szolgáltatás elérhetővé válik a kiválasztott régióban.    

   ![Régiónkénti elérhetőség szóló üzenetet](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>További lépések
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) kapcsolatos naplóriasztási szabály vonatkozó webhook-műveletek.
- [További](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure Service Health szolgáltatással kapcsolatos.


