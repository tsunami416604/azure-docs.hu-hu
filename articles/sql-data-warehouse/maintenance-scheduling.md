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
ms.openlocfilehash: 0e5df583112bbb6db9651004e5deaceb6b5b9d12
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958898"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Karbantartási ütemtervek használata a szolgáltatások frissítéseinek és karbantartásának kezeléséhez

A karbantartási ütemtervek mostantól minden Azure SQL Data Warehouse régióban elérhetők. A karbantartási ütemezési funkció a Service Health tervezett karbantartási értesítéseket, Resource Health ellenőrző figyelőt és a Azure SQL Data Warehouse karbantartási ütemezési szolgáltatást integrálja.

A karbantartási ütemezés használatával kiválaszthatja az időablakot, amikor az új funkciók, frissítések és javítások fogadására alkalmas. Egy hét napos időszakon belül kiválaszthatja az elsődleges és a másodlagos karbantartási időszakot. Ennek a funkciónak a használatához külön napi tartományokban kell megadnia egy elsődleges és egy másodlagos ablakot.

Megadhatja például, hogy a 22:00-es és a Sunday 01:00-es elsődleges ablak is beütemezzen, majd egy másodlagos 19:00 ablakot is ütemezzen a 22:00-re. Ha SQL Data Warehouse nem tudja végrehajtani a karbantartást az elsődleges karbantartási időszak alatt, akkor a másodlagos karbantartási időszak alatt újra próbálkozik a karbantartással. A szolgáltatás karbantartása az elsődleges és a másodlagos Windows rendszerben is előfordulhat. Az összes karbantartási művelet gyors befejezésének biztosítása érdekében a DW400 (c) és az alacsonyabb adattárház-rétegek a karbantartási időszakon kívül is elvégezhetik a karbantartást.

Az újonnan létrehozott Azure SQL Data Warehouse példányok egy rendszer által meghatározott karbantartási ütemtervtel lesznek alkalmazva az üzembe helyezés során. Az ütemtervet az üzembe helyezés befejeződése után is szerkesztheti.

Minden karbantartási időszak három és nyolc óra között lehet. A karbantartás az ablakon belül bármikor megtörténhet. A karbantartás megkezdésekor a rendszer minden aktív munkamenetet megszakít, és a nem véglegesített tranzakciókat visszaállítja. A kapcsolatnak több rövid veszteséget kell várnia, mivel a szolgáltatás új kódot telepít az adattárházba. Az adatraktár-karbantartás befejeződése után azonnal értesítést kap.

 Minden karbantartási műveletnek az ütemezett karbantartási időszakokon belül kell futnia. A megadott karbantartási időszakokon kívül nem fog karbantartás zajlani előzetes értesítés nélkül. Ha az adattárház egy ütemezett karbantartás során szünetel, a rendszer a folytatási művelet során frissíti. 

## <a name="alerts-and-monitoring"></a>Riasztások és figyelés

Service Health értesítésekkel való integráció és a Resource Health-ellenőrzési figyelő lehetővé teszi, hogy az ügyfelek tájékoztassanak a közelgő karbantartási tevékenységekről. Az új automatizálás kihasználja Azure Monitor. Eldöntheti, hogyan szeretné értesíteni a közelgő karbantartási eseményekről. Emellett kiválaszthatja, hogy mely automatizált folyamatok segítenek az állásidő kezelésében és a működési hatás minimalizálásában.

A 24 órás előzetes értesítés megelőzi a DWC400c és az alacsonyabb rétegekhez nem tartozó karbantartási eseményeket. A példányok leállásának minimalizálásához győződjön meg arról, hogy az adattárház nem rendelkezik hosszan futó tranzakciókkal a választott karbantartási időszak előtt.

> [!NOTE]
> Abban az esetben, ha egy kritikus idejű frissítést kell telepíteni, a speciális értesítési idők jelentősen csökkenthetők.

Ha előzetes értesítés érkezett arról, hogy a karbantartás megtörténik, de SQL Data Warehouse nem tudja végrehajtani a karbantartást, akkor a rendszer lemondási értesítést fog kapni. A karbantartás ekkor folytatódik a következő ütemezett karbantartási időszakban.

Az összes aktív karbantartási esemény megjelenik a **Service Health tervezett karbantartási** szakaszban. A Service Health előzményei a múltbeli események teljes számát tartalmazzák. Az aktív esemény során a Azure Service Health-ellenőrzés portál irányítópultján figyelheti a karbantartást.

### <a name="maintenance-schedule-availability"></a>Karbantartási ütemterv rendelkezésre állása

Még ha a karbantartási ütemezés nem érhető el a kiválasztott régióban, bármikor megtekintheti és szerkesztheti a karbantartási ütemtervet. Ha a karbantartási ütemezés elérhetővé válik a régióban, az azonosított ütemezés azonnal aktív lesz az adattárházban.

## <a name="view-a-maintenance-schedule"></a>Karbantartási ütemterv megtekintése 

### <a name="portal"></a>Portál

Alapértelmezés szerint minden újonnan létrehozott Azure SQL Data Warehouse-példányra egy, az üzembe helyezés során alkalmazott nyolcórás elsődleges és másodlagos karbantartási időszak vonatkozik. A fentiekben leírtak szerint a Windowst a közeljövőben is megváltoztathatja. A megadott karbantartási időszakokon kívül nem fog karbantartás zajlani előzetes értesítés nélkül.

Az adattárház karbantartási ütemezésének megtekintéséhez hajtsa végre az alábbi lépéseket:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  Válassza ki a megtekinteni kívánt adatraktárat. 
3.  A kiválasztott adattárház megnyílik az Áttekintés panelen. Az adatraktárra alkalmazott karbantartási ütemterv a **karbantartási ütemterv**alatt jelenik meg.

![Áttekintés panel](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Karbantartási ütemterv módosítása 

### <a name="portal"></a>Portál
A karbantartási ütemtervet bármikor frissítheti vagy módosíthatja. Ha a kiválasztott példány aktív karbantartási cikluson halad át, a rendszer menti a beállításokat. A következő azonosított karbantartási időszak alatt lesznek aktívak. [További](https://docs.microsoft.com/azure/service-health/resource-health-overview) információ az adattárház figyeléséről aktív karbantartási esemény során. 

### <a name="identifying-the-primary-and-secondary-windows"></a>Az elsődleges és a másodlagos ablakok azonosítása

Az elsődleges és a másodlagos Windowsnak külön napi tartományokkal kell rendelkeznie. Ilyen például a kedd – csütörtök elsődleges ablak és a szombat – vasárnap egy másodlagos ablak.

Az adattárház karbantartási ütemtervének módosításához hajtsa végre a következő lépéseket:
1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  Válassza ki a frissíteni kívánt adatraktárat. Megnyílik az oldal az Áttekintés panelen. 
3.  Nyissa meg a karbantartási ütemterv beállításainak lapját. Ehhez válassza a **karbantartási ütemterv (előzetes verzió) összefoglalás** hivatkozást az Áttekintés panelen. Vagy válassza a **karbantartási ütemterv** lehetőséget a bal oldali erőforrás menüjében.  

    ![Áttekintő panel beállításai](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Azonosítsa az elsődleges karbantartási időszak előnyben részesített napját az oldal tetején található beállítások használatával. Ez a beállítás határozza meg, hogy az elsődleges ablak a hétköznapokon vagy a hétvégén fog történni. A kiválasztott érték frissíti a legördülő listát. Az előzetes verzió során előfordulhat, hogy egyes régiók még nem támogatják a rendelkezésre álló **napi** beállítások teljes készletét.

   ![Karbantartási beállítások panel](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Válassza ki a kívánt elsődleges és másodlagos karbantartási időszakokat a legördülő lista használatával:
   - **Nap**: Az előnyben részesített nap a kiválasztott ablak karbantartásának elvégzéséhez.
   - **Kezdés időpontja**: A karbantartási időszak előnyben részesített kezdési ideje.
   - **Időablak**: Az időablak előnyben részesített időtartama.

   A panel alján található **ütemezett összefoglalás** terület a kiválasztott értékek alapján frissül. 
  
6. Kattintson a **Mentés** gombra. Megjelenik egy üzenet, amely megerősíti, hogy most már aktív az új ütemterv. 

   Ha olyan régióban ment egy ütemezést, amely nem támogatja a karbantartási ütemezést, akkor a következő üzenet jelenik meg. A rendszer menti a beállításokat, és aktívvá válik, ha a funkció elérhetővé válik a kiválasztott régióban.    

   ![A régió elérhetőségével kapcsolatos üzenet](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>További lépések
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) információ a riasztások létrehozásáról, megtekintéséről és kezeléséről Azure monitor használatával.
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) információ a napló riasztási szabályaival kapcsolatos webhook-műveletekről.
- [További információ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Műveleti csoportok létrehozása és kezelése.
- [További](https://docs.microsoft.com/azure/service-health/service-health-overview) információ a Azure Service Healthról.