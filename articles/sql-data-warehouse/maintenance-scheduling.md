---
title: Azure-karbantartási ütemtervek (előzetes verzió) | Microsoft Docs
description: A karbantartási ütemezés lehetővé teszi, hogy az ügyfelek megtervezzék a Azure SQL Data Warehouse szolgáltatás által az új funkciók, frissítések és javítások elvégzéséhez szükséges ütemezett karbantartási eseményeket.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 07/16/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 3875106e8c6301c95bc8d0fbce6a1c0400d07f78
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278124"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Karbantartási ütemtervek használata a szolgáltatások frissítéseinek és karbantartásának kezeléséhez

A karbantartási ütemtervek mostantól minden Azure SQL Data Warehouse régióban elérhetők. Ez a szolgáltatás integrálja a Service Health tervezett karbantartási értesítéseket, Resource Health ellenőrző figyelőt és a Azure SQL Data Warehouse karbantartási ütemezési szolgáltatást.

A karbantartási ütemezés használatával kiválaszthatja az időablakot, amikor az új funkciók, frissítések és javítások fogadására alkalmas. Egy hét napos időszakon belül kiválaszthatja az elsődleges és a másodlagos karbantartási időszakot. Ilyen például a 22:00 – vasárnap 01:00-es és a 22:00-es, valamint az 19:00-as számú másodlagos ablak. Ha SQL Data Warehouse nem tudja végrehajtani a karbantartást az elsődleges karbantartási időszak alatt, akkor a másodlagos karbantartási időszak alatt újra próbálkozik a karbantartással. A szolgáltatás karbantartása az elsődleges és a másodlagos Windowsban is előfordulhat. Az összes karbantartási művelet gyors befejezésének biztosítása érdekében a DW400 (c) és az alacsonyabb adattárház-rétegek a karbantartási időszakon kívül is elvégezhetik a karbantartást.

Az újonnan létrehozott Azure SQL Data Warehouse példányok egy rendszer által meghatározott karbantartási ütemtervtel lesznek alkalmazva az üzembe helyezés során. Az ütemtervet az üzembe helyezés befejeződése után is szerkesztheti.

Minden karbantartási időszak három – nyolc óra lehet. A karbantartás az ablakon belül bármikor megtörténhet. A karbantartás megkezdésekor a rendszer minden aktív munkamenetet megszakít, és a nem véglegesített tranzakciókat visszaállítja. A kapcsolatnak több rövid veszteséget kell várnia, mivel a szolgáltatás új kódot telepít az adattárházba. A karbantartás befejezése után azonnal értesítést kap az adattárházban

Ennek a funkciónak a használatához külön napi tartományokban kell megadnia egy elsődleges és egy másodlagos ablakot. Minden karbantartási műveletnek az ütemezett karbantartási időszakokon belül kell futnia. Előzetes értesítés nélkül nem kerül sor karbantartásra a megadott karbantartási időszakon kívül. Ha az adattárház egy ütemezett karbantartás során szünetel, a rendszer a folytatási művelet során frissíti.  

## <a name="alerts-and-monitoring"></a>Riasztások és figyelés

Service Health értesítésekkel való integráció és a Resource Health-ellenőrzési figyelő lehetővé teszi, hogy az ügyfelek tájékoztassanak a közelgő karbantartási tevékenységekről. Az új automatizálás kihasználja Azure Monitor. Eldöntheti, hogyan szeretné értesíteni a közelgő karbantartási eseményekről. Azt is eldöntheti, hogy mely automatizált folyamatok segíthetnek az állásidők kezelésében, és csökkentheti a műveletekre gyakorolt hatást.

Egy 24 órás előzetes értesítés az összes karbantartási esemény előtt, a DW400c és az alacsonyabb szintek aktuális kivételével. A példányok leállásának minimalizálásához győződjön meg arról, hogy az adattárház nem rendelkezik hosszan futó tranzakciókkal a választott karbantartási időszak előtt.

> [!NOTE]
> Abban az esetben, ha egy kritikus idejű frissítést kell telepíteni, a speciális értesítési idők jelentősen csökkenthetők.

Ha előzetes értesítés érkezett arról, hogy a karbantartás megtörténik, de SQL Data Warehouse nem tudja végrehajtani a karbantartást, akkor a rendszer lemondási értesítést fog kapni. A karbantartás ekkor folytatódik a következő ütemezett karbantartási időszakban.

Az összes aktív karbantartási esemény megjelenik a **Service Health tervezett karbantartási** szakaszban. A Service Health előzményei a múltbeli események teljes számát tartalmazzák. Az aktív esemény során a Azure Service Health-ellenőrzés portál irányítópultján figyelheti a karbantartást.

### <a name="maintenance-schedule-availability"></a>Karbantartási ütemterv rendelkezésre állása

Még ha a karbantartási ütemezés nem érhető el a kiválasztott régióban, bármikor megtekintheti és szerkesztheti a karbantartási ütemtervet. Ha a karbantartási ütemezés elérhetővé válik a régióban, az azonosított ütemezés azonnal aktív lesz az adattárházban.

## <a name="next-steps"></a>További lépések

- [További](viewing-maintenance-schedule.md) információ a karbantartási ütemterv megtekintéséről.
- [További](changing-maintenance-schedule.md) információ a karbantartási ütemterv módosításáról.
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) információ a riasztások létrehozásáról, megtekintéséről és kezeléséről Azure monitor használatával.
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) információ a napló riasztási szabályaival kapcsolatos webhook-műveletekről.
- [További információ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Műveleti csoportok létrehozása és kezelése.
- [További](https://docs.microsoft.com/azure/service-health/service-health-overview) információ a Azure Service Healthról.
