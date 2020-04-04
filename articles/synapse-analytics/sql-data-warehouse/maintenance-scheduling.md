---
title: A Synapse SQL-készlet karbantartási ütemezése
description: A karbantartási ütemezés lehetővé teszi az ügyfelek számára, hogy megtervezhetjék a szükséges ütemezett karbantartási eseményeket, amelyeket az Azure Synapse Analytics használ az új funkciók, frissítések és javítások bevezetéséhez.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 43fc32e910c51e8b70e15aa49584a18e5b703fca
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631581"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>A karbantartási ütemezések használata a szolgáltatásfrissítések és -karbantartás kezeléséhez

A karbantartási ütemezés i. szolgáltatás integrálja a szolgáltatásállapot-tervezett karbantartási értesítéseket, az erőforrás-állapot-ellenőrző figyelőt és a Synapse SQL-készlet (adattárház) karbantartási ütemezési szolgáltatását az Azure Synapse Analytics szolgáltatásban.

A karbantartási ütemezés segítségével válasszon egy időablakot, amikor az új funkciók, frissítések és javítások fogadása kényelmes. Egy hétnapos időszakon belül ki kell választania egy elsődleges és egy másodlagos karbantartási időszakot, minden ablaknak külön napi tartományon belül kell lennie.

Például ütemezhet egy szombati 22:00 és vasárnap 01:00 óra között érvényes elsődleges ablakot, majd egy másodlagos, szerda 19:00 és 22:00 óra között tartandó időszakot. Ha a karbantartás nem hajtható végre az elsődleges karbantartási időszak alatt, akkor a másodlagos karbantartási időszak alatt újra megpróbálja a karbantartást. A szolgáltatás karbantartása esetenként az elsődleges és a másodlagos ablakokban is előfordulhat. Az összes karbantartási művelet gyors befejezésének biztosítása érdekében a DW400c és az alacsonyabb adattárházszintek a kijelölt karbantartási időszakon kívül is befejezhetik a karbantartást.

Minden újonnan létrehozott adattárház-példány hoz létre egy rendszer által definiált karbantartási ütemezést a telepítés során. Az ütemezés szerkeszthető, amint a központi telepítés befejeződött.

Bár a karbantartási időszak három és nyolc óra között lehet, ez nem jelenti azt, hogy az adattárház offline állapotban lesz az időtartam alatt. Karbantartás bármikor előfordulhat az ablakon belül, és akkor számíthat egyetlen leválasztása ebben az időszakban tartó ~5 -6 mins, mint a szolgáltatás telepíti az új kódot az adattárházba. A DW400c és az alacsonyabb szint a karbantartási időszak alatt különböző időpontokban több rövid hálózati veszteséget tapasztalhat. A karbantartás megkezdésekor az összes aktív munkamenet megszakad, és a nem véglegesített tranzakciók visszalesznek állítva. A példányállás minimalizálása érdekében győződjön meg arról, hogy az adattárház nem rendelkezik hosszú ideig futó tranzakciókkal a kiválasztott karbantartási időszak előtt.

Minden karbantartási műveletnek be kell fejeződnie a megadott karbantartási időszakokon belül, kivéve, ha időérzékeny frissítést kell üzembe helyeznünk. Ha az adattárház egy ütemezett karbantartás során szünetel, akkor a folytatási művelet során frissül. Az adattárház karbantartásának befejezése után azonnal értesítést kap.

## <a name="alerts-and-monitoring"></a>Figyelmeztetések és megfigyelések

A Szolgáltatásállapot-értesítésekkel és az Erőforrás-állapot-ellenőrző figyelővel való integráció lehetővé teszi az ügyfelek számára, hogy folyamatosan tájékoztassák a közelgő karbantartási tevékenységről. Ez az automatizálás kihasználja az Azure Monitor előnyeit. Eldöntheti, hogyan szeretne értesítést kapni a közelgő karbantartási eseményekről. Azt is kiválaszthatja, hogy mely automatizált folyamatok segítenek az állásidő kezelésében és a működési hatás minimalizálásában.

A 24 órás előzetes értesítés megelőzi az összes karbantartási események, amelyek nem a DWC400c és az alacsonyabb szintek.

> [!NOTE]
> Abban az esetben, ha egy időkritikus frissítést kell üzembe helyeznünk, a speciális értesítési idő jelentősen csökkenhet.

Ha előzetes értesítést kapott arról, hogy a karbantartás ra kerül sor, de a karbantartás nem hajtható végre az értesítés ben töltött időszak alatt, akkor lemondási értesítést kap. A karbantartás ezután a következő ütemezett karbantartási időszakban folytatódik.

Minden aktív karbantartási esemény megjelenik a **Szolgáltatás állapot - Tervezett karbantartás** szakaszban. A Szolgáltatás-állapot előzményei a múltbeli események teljes rekordját tartalmazzák. Az Azure Service Health Check portal irányítópultján keresztül figyelheti a karbantartást egy aktív esemény során.

### <a name="maintenance-schedule-availability"></a>Karbantartási ütemezés rendelkezésre állása

Még akkor is, ha a karbantartási ütemezés nem érhető el a kiválasztott régióban, bármikor megtekintheti és szerkesztheti a karbantartási ütemezést. Amikor a karbantartási ütemezés elérhetővé válik a régióban, az azonosított ütemezés azonnal aktívvá válik a Synapse SQL-készletben.

## <a name="view-a-maintenance-schedule"></a>Karbantartási ütemezés megtekintése

Alapértelmezés szerint az összes újonnan létrehozott adattárház-példány egy nyolc órás elsődleges és másodlagos karbantartási időszakot alkalmaz az üzembe helyezés során. Amint azt fentebb jeleztük, módosíthatja az ablakokat, amint a telepítés befejeződött. A megadott karbantartási időszakokon kívül nem fog karbantartás zajlani előzetes értesítés nélkül.

A Synapse SQL-készletre alkalmazott karbantartási ütemezés megtekintéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Jelölje ki a megtekinteni kívánt Szinapsze SQL-készletet.
3. A kijelölt Synapse SQL-készlet megnyílik az áttekintő panelen. Az adatraktárra alkalmazott karbantartási ütemezés a **Karbantartási ütemezés**alatt jelenik meg.

![Áttekintő panel](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Karbantartási ütemezés módosítása

A karbantartási ütemezés bármikor frissíthető vagy módosítható. Ha a kiválasztott példány aktív karbantartási cikluson megy keresztül, a beállítások mentésre kerülnek. A következő azonosított karbantartási időszakban aktívvá válnak. [További információ](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) az adattárház figyeléséről egy aktív karbantartási esemény során.

## <a name="identifying-the-primary-and-secondary-windows"></a>Az elsődleges és másodlagos ablakok azonosítása

Az elsődleges és másodlagos ablakoknak külön naptartományokkal kell rendelkezniük. Erre példa a kedd–csütörtök elsődleges ablaka, valamint a szombat–vasárnap ablak másodlagos ablaka.

A Synapse SQL-készlet karbantartási ütemezésének módosításához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Jelölje ki a frissíteni kívánt Szinapsze SQL-készletet. Az oldal megnyílik az áttekintő panelen.
Nyissa meg a lapot a karbantartási ütemezés beállításaihoz, ha kiválasztja a **Karbantartási ütemezés összegzése** hivatkozást az áttekintő panelen. Vagy válassza a **Karbantartási ütemezés** lehetőséget a bal oldali erőforrásmenüben.

    ![Áttekintő panel beállítások](./media/maintenance-scheduling/maintenance-change-option.png)

3. Az oldal tetején található beállításokkal azonosíthatja az elsődleges karbantartási időszak előnyben részesített napi tartományát. Ez a beállítás határozza meg, hogy az elsődleges ablak hétköznap vagy hétvégén következik-e be. A kijelölés frissíti a legördülő értékeket.
Az előzetes verzió során előfordulhat, hogy egyes régiók még nem támogatják a rendelkezésre álló **Napi** beállítások teljes készletét.

   ![Karbantartási beállítások panel](./media/maintenance-scheduling/maintenance-settings-page.png)

4. Válassza ki a kívánt elsődleges és másodlagos karbantartási időszakokat a legördülő listák segítségével:
   - **Nap**: A kijelölt ablakban a karbantartás előnyben részesített napja.
   - **Kezdési időpont**: A karbantartási időszak preferált kezdési időpontja.
   - **Időablak**: Az időablak preferált időtartama.

   A panel alján található **Összegzés ütemezése** terület a kiválasztott értékek alapján frissül.
  
5. Kattintson a **Mentés** gombra. Megjelenik egy üzenet, amely megerősíti, hogy az új ütemezés aktív.

   Ha olyan régióban ment egy ütemezést, amely nem támogatja a karbantartásütemezést, a következő üzenet jelenik meg. A beállítások mentésre kerülnek, és aktívvá válnak, amint a funkció elérhetővé válik a kiválasztott régióban.

   ![Üzenet a régió elérhetőségéről](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>További lépések

- [További információ](../../azure-monitor/platform/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a riasztások létrehozásáról, megtekintéséről és kezeléséről az Azure Monitor használatával.
- [További információ](../..//azure-monitor/platform/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a naplóriasztási szabályok webhook-műveletekről.
- [További információ](../..//azure-monitor/platform/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Műveletcsoportok létrehozása és kezelése.
- [További információ](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) az Azure Service Health szolgáltatásról.
