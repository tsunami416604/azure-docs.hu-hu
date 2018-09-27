---
title: Az Azure karbantartási ütemezések (előzetes verzió) |} A Microsoft Docs
description: Karbantartási ütemezés lehetővé teszi a felhasználóknak szerte a szükséges ütemezett karbantartási események az Azure SQL Data warehouse szolgáltatást használja, vezethet be új funkciók, frissítések és javítások megtervezéséhez.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 09/20/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 85e8327d1cac17059b2e91b1ea21becc23002c8e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228387"
---
# <a name="using-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Karbantartási ütemezések használatával kezelheti a szolgáltatás frissítések és karbantartás

Már az Azure SQL Data Warehouse karbantartási ütemezés előzetes verzióban érhető el. Ez az új szolgáltatás zökkenőmentesen integrálható a Service Health tervezett karbantartásával kapcsolatos értesítések erőforrás állapotának ellenőrzése figyelése és az Azure SQL Data Warehouse karbantartási Feladatütemező szolgáltatás.

Karbantartási ütemezés lehetővé teszi a egy olyan időkeretet meghatározhatja, mikor célszerű a új funkciók, a frissítések és javítások. Ügyfelek választ ki egy elsődleges és másodlagos karbantartási időszak egy 7 napos időszakban, azaz a szombat 22:00 – vasárnap (elsődleges) 01:00 és 19:00 – 22:00 szerdán (másodlagos). Ha nem tudja végrehajtani a karbantartási az elsődleges karbantartási időszak alatt, a másodlagos karbantartási időszak során újra megkíséreljük.

Minden újonnan létrehozott Azure SQL Data Warehouse példányok lesz egy rendszer által meghatározott karbantartási ütemezés alkalmaz a telepítés alatt. Az ütemezés szerkesztheti, amint a telepítés nem fejeződik.

Az egyes karbantartási időszakok a 3. és 8 óra közötti lehet, a 3hrs jelenleg folyamatban van a legrövidebb elérhető lehetőség. Karbantartási fordulhat elő, az időtartamon belül bármikor, és egy rövid kapcsolat megszakadása, a szolgáltatás telepíti az új kódot az adattárház számíthat. 

A funkció előzetes verzióban az ügyfelek számára, hogy azonosítsa az elsődleges és másodlagos windows különálló nap tartományokon belül arra kérjük.   
Az ütemezett karbantartási időszakok belül minden karbantartási műveleteket kell elvégezni, és nincs karbantartási kerül sor, előzetes értesítés nélkül a megadott karbantartási időszakon kívüli. Ha az adattárháza szüneteltetve van egy ütemezett karbantartás során, azt a felfüggesztési művelet során frissülnek.  


## <a name="alerts-and-monitoring"></a>Riasztások és figyelés

Szolgáltatás állapotára vonatkozó értesítések zökkenőmentes integrációjával és a Resource health ellenőrizze a figyelő lehetővé teszi, hogy az ügyfelek számára, hogy értesüljön a közelgő karbantartási tevékenység. Az új automation kihasználja az Azure Monitor, és lehetővé teszi a felhasználóknak meghatározhatja azok szeretne értesítést kapni a közelgő karbantartási események és amely automatizált folyamatok legyen elindítva állásidő kezeléséhez, és minimalizálja a műveleteket.

Az összes karbantartási események egy 24 órás előzetes értesítés előzi meg. Példány állásidő minimalizálása érdekében győződjön meg egyetlen hosszú ideig futó tranzakció sem az adattárházra megkezdése előtt a kiválasztott karbantartási időszak. Amikor elindul a karbantartási minden aktív munkamenet meg lesz szakítva, nem véglegesített tranzakció vissza lesz állítva, és az adatraktár fog tapasztalni a kapcsolat rövid adatvesztést. Értesíteni fogjuk után azonnal karbantartási feladatok elvégzése az adattárházra. 

Ha, hogy a karbantartási kerül sor, de nem lehet végrehajtani a karbantartási idő alatt előzetes értesítést kapott, kapni fog egy lemondási értesítés. Karbantartási futása ezután úgy folytatódik a következő ütemezett karbantartási időszak során.
 
Összes aktív karbantartási esemény jelenik meg a "Service Health - tervezett karbantartási" szakaszban. Egy teljes rekordot a múltban történt eseményekről szolgáltatás állapotelőzmények részeként lesznek megőrizve. Karbantartás az Azure Service Health-ellenőrzés portál irányítópultján keresztül figyelhető egy aktív esemény során.

### <a name="maintenance-schedule-availability"></a>Karbantartási ütemezés rendelkezésre állása

Akkor is, ha a karbantartási ütemezés még nem érhető el a kiválasztott régióban, továbbra is megtekintheti és szerkesztheti a bármikor a karbantartási ütemezéshez. Amikor karbantartási ütemezés elérhetővé válik az Ön régiójában, az azonosított ütemezés azonnal aktívvá válik az adattárházra.


## <a name="next-steps"></a>További lépések

- [További](viewing-maintenance-schedule.md) karbantartási ütemezés megtekintése 
- [További](changing-maintenance-schedule.md) karbantartási ütemezés módosítása
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) létrehozása, megtekintése és használata az Azure Monitor riasztások kezelése
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) kapcsolatos naplóriasztási szabály vonatkozó Webhook-műveletek
- [További](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure Service Health szolgáltatással kapcsolatos







