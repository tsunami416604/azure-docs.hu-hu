---
title: Az Azure karbantartási ütemezések (előzetes verzió) |} A Microsoft Docs
description: Karbantartási ütemezés lehetővé teszi, hogy az ügyfelek számára a szükséges ütemezett karbantartási események az Azure SQL Data Warehouse szolgáltatás által használt vezethet be új funkciók, frissítések és javítások körül megtervezése.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 09/20/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: c2ed79673af3563ae62f516057a174770cda99e9
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427862"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Szolgáltatási hírek és karbantartási kezelése a karbantartási ütemezések használatával

Már az Azure SQL Data Warehouse karbantartási ütemezés előzetes verzióban érhető el. Ez a funkció együttműködik a Service Health tervezett karbantartásával kapcsolatos értesítések erőforrás állapotának ellenőrzése figyelése és az Azure SQL Data Warehouse karbantartási Feladatütemező szolgáltatás.

Karbantartási ütemezés kiválasztása egy olyan időkeretet, ha az új funkciók, frissítések és javítások fogadásához kényelmes használhatja. Válasszon egy elsődleges és másodlagos karbantartási időszak egy 7 napos időszakon belül. Például egy elsődleges ablakában szombat, 22:00 vasárnapig 01:00 és a egy másodlagos ablakot, szerda 19:00, 22:00. Az SQL Data Warehouse nem tudja végrehajtani a karbantartási az elsődleges karbantartási időszak alatt, ha azt a karbantartás megpróbálja újra a másodlagos karbantartási időszak alatt.

Minden újonnan létrehozott Azure SQL Data Warehouse példányok lesz egy rendszer által meghatározott karbantartási ütemezés alkalmaz a telepítés alatt. Az ütemezés szerkesztheti, amint a telepítés nem fejeződik.

Az egyes karbantartási időszakok 3-8 óra is lehet. Karbantartási az időtartamon belül bármikor fordulhat elő. Egy rövid kapcsolat megszakadása, a szolgáltatás telepíti az új kódot az adattárház számíthat. 

A funkció előzetes verzióban az elsődleges és másodlagos windows különálló nap tartományokon belül azonosítható. Az ütemezett karbantartási időszakok belül minden karbantartási művelet befejeződik. Nincs karbantartás előzetes értesítés nélkül a megadott karbantartási időszakon kívül kerül sor. Ha az adattárháza szüneteltetve van egy ütemezett karbantartás során, azt a felfüggesztési művelet során frissülnek.  


## <a name="alerts-and-monitoring"></a>Riasztások és figyelés

Integráció a Service Health-értesítések és a Resource Health ellenőrzése figyelő értesüljön a közelgő karbantartási tevékenység lehetővé teszi. Az új automation kihasználja az Azure Monitor. Eldöntheti, hogyan szeretne értesítést kapni a közelgő karbantartási események. Is döntse el, melyik automatizált folyamatok segítségével kezelheti az állásidő, és minimalizálja a műveletek.

Egy 24 órás előzetes értesítés megelőzi az összes karbantartási események. Példány állásidő minimalizálása érdekében győződjön meg arról, hogy az adattárház nincs hosszú ideig futó tranzakció a kiválasztott karbantartási időszak előtt. Amikor elindul a karbantartási, minden aktív munkamenet meg lesz szakítva. Noncommitted tranzakció vissza lesz állítva, és az adatraktár fog tapasztalni a kapcsolat rövid adatvesztést. Értesítést is küld az adattárházra karbantartás befejezése után azonnal. 

Ha karbantartási kerül sor, de az SQL Data Warehouse nem tudja végrehajtani a karbantartási idő alatt előzetes értesítést kapott, kap egy lemondási értesítés. Karbantartási futása ezután úgy folytatódik a következő ütemezett karbantartási időszak során.
 
Az összes aktív karbantartási események megjelennek a **Service Health - tervezett karbantartási** szakaszban. A Service Health előzmények magában foglalja egy teljes rekordot a múltban történt eseményekről. Egy aktív események karbantartás az Azure Service Health-ellenőrzés portál irányítópultján keresztül követheti nyomon.

### <a name="maintenance-schedule-availability"></a>Karbantartási ütemezés rendelkezésre állása

Akkor is, ha a karbantartási ütemezés nem érhető el a kiválasztott régióban, megtekintheti, és bármikor a karbantartási ütemezés szerkesztése. Ha a karbantartási ütemezés elérhetővé válik az Ön régiójában, az azonosított ütemezés azonnal aktívvá válik az adattárházra.


## <a name="next-steps"></a>További lépések

- [További](viewing-maintenance-schedule.md) karbantartási ütemezés megtekintése. 
- [További](changing-maintenance-schedule.md) karbantartási ütemezés módosítása.
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) létrehozása, megtekintése és riasztások kezelése az Azure Monitor használatával kapcsolatban.
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) kapcsolatos naplóriasztási szabály vonatkozó webhook-műveletek.
- [További](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure Service Health szolgáltatással kapcsolatos.







