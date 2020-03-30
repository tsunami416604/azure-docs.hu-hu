---
title: SQL-adatszinkronizálás figyelése az Azure Figyelő naplóival
description: Ismerje meg, hogyan figyelheti az Azure SQL Data Sync szolgáltatást az Azure Monitor naplóinak használatával
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 0ed0bd3544fff89c8230267e3d6d8826c5ae3c7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74114620"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>SQL-adatszinkronizálás figyelése az Azure Figyelő naplóival 

Az SQL Data Sync tevékenységnapló ellenőrzéséhez és a hibák és figyelmeztetések észleléséhez korábban manuálisan kellett ellenőriznie az SQL Data Sync-et az Azure Portalon, vagy használnia kellett a PowerShellt vagy a REST API-t. A jelen cikkben ismertetett lépéseket követve konfigurálhat egy egyéni megoldást, amely javítja az adatszinkronizálás figyelési élményét. Ezt a megoldást testreszabhatja a forgatókönyvnek megfelelően.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](sql-database-sync-data.md) ismertető cikket.

> [!IMPORTANT]
> Az Azure SQL Data Sync jelenleg **nem** támogatja az Azure SQL Database felügyelt példánya.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Az összes szinkronizálási csoport irányítópultjának figyelése 

A problémák megoldásához már nem kell külön-külön átnéznie az egyes szinkronizálási csoportok naplóit. Egy egyéni Azure Monitor nézet használatával figyelheti az összes szinkronizálási csoportot bármelyik előfizetéséből egy helyen. Ez a nézet az SQL Data Sync-ügyfelek számára fontos információkat jelenik meg.

![Adatszinkronizálás figyelési irányítópultja](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatikus e-mail értesítések

Már nem kell manuálisan ellenőriznie a naplót az Azure Portalon, vagy a PowerShellen vagy a REST API-n keresztül. Az [Azure Monitor naplók](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)segítségével riasztásokat hozhat létre, amelyek közvetlenül az e-mail címét, hogy az emberek, akiknek szükségük van, hogy lássa őket, ha egy hiba történik.

![Adatszinkronizálási e-mail értesítések](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Hogyan állíthatja be ezeket a figyelési funkciókat? 

Egyéni Azure Monitor-naplók figyelési megoldásának megvalósítása az SQL Data Sync számára kevesebb mint egy óra alatt az alábbi műveletek végrehajtásával:

Három összetevőt kell konfigurálnia:

-   Egy PowerShell-runbook az SQL Data Sync naplóadatainak az Azure Monitor naplóiba való betáplálására.

-   Az Azure Monitor riasztáse az e-mail értesítésekhez.

-   Egy Azure Monitor view figyeléséhez.

### <a name="samples-to-download"></a>Letölthető minták

Töltse le a következő két mintát:

-   [Adatszinkronizálási napló PowerShell-runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Adatszinkronizálás i.](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy beállította a következő dolgokat:

-   Azure Automation-fiók

-   Log Analytics-munkaterület

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShell-runbook az SQL-adatszinkronizálási napló lehívásához 

Az Azure Automationben üzemeltetett PowerShell-runbook használatával lekéri az SQL Data Sync naplóadatait, és elküldi azokat az Azure Monitor-naplókba. A mintaparancsfájl is szerepel. Előfeltételként egy Azure Automation-fiókkal kell rendelkeznie. Ezután létre kell hoznia egy runbookot, és ütemeznie kell a futtatását. 

### <a name="create-a-runbook"></a>Runbook létrehozása

A runbookok létrehozásáról az [Első PowerShell-runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell)című témakörben talál további információt.

1.  Az Azure Automation-fiók alatt válassza a **Runbooks** fület a Folyamatautomatizálás csoportban.

2.  Válassza a Runbook oka a **Runbookok** lap bal felső sarkában válassza a Runbook hozzáadása lehetőséget.

3.  Válassza **a Meglévő Runbook importálása**lehetőséget.

4.  A **Runbook-fájl**csoportban használja a megadott `DataSyncLogPowerShellRunbook` fájlt. Állítsa be a `PowerShell` **Runbook típusát** . Adjon nevet a runbooknak.

5.  Kattintson a **Létrehozás** gombra. Most már van egy runbook.

6.  Az Azure Automation-fiók alatt válassza a **Változók** lapot a Megosztott erőforrások csoportban.

7.  Válassza a **Változóhozzáadása** lehetőséget a Változók lapon. Hozzon létre egy változót a runbook utolsó végrehajtási idejének tárolására. Ha több runbookok, minden runbookhoz egy változóra van szükség.

8.  Állítsa be a `DataSyncLogLastUpdatedTime` változó nevét dátumidőként, és állítsa be a típusát.

9.  Jelölje ki a runbookot, és kattintson a lap tetején található szerkesztés gombra.

10. Hajtsa végre a fiókhoz és az SQL Data Sync konfigurációjához szükséges módosításokat. (További részletes információt a mintaparancsfájlban talál.)

    1.  Azure-adatok.

    2.  Csoport adatainak szinkronizálása.

    3.  Az Azure Monitor naplózza az adatokat. Ezeket az információkat az Azure Portalon találja | Beállítások | Csatlakoztatott források. Az Azure Monitor-naplókba való adatküldésről további információt [az Adatok küldése az Azure Monitornak naplók küldése http-adatgyűjtő API-val (előzetes verzió)](../azure-monitor/platform/data-collector-api.md)című témakörben talál.

11. Futtassa a runbookot a Teszt ablaktáblán. Ellenőrizze, hogy sikeres volt-e.

    Ha hibákat tapasztal, győződjön meg arról, hogy a legújabb PowerShell-modul telepítve van. Telepítheti a legújabb PowerShell-modult az Automation-fiók **modulok galériájába.**

12. Kattintson **a Közzététel gombra**

### <a name="schedule-the-runbook"></a>A runbook ütemezése

A runbook ütemezése:

1.  A runbook alatt válassza az **Ütemezések** lapot az Erőforrások csoportban.

2.  Válassza **az Ütemezés hozzáadása** lehetőséget az Ütemezés lapon.

3.  Válassza **az Ütemezés csatolása a runbookhoz**lehetőséget.

4.  Válassza **az Új ütemezés létrehozása lehetőséget.**

5.  Állítsa **az Ismétlődés** értékre Ismétlődés értékre, és állítsa be a kívánt időközt. Használja ugyanazt az időközt itt, a parancsfájlban és az Azure Monitor naplóiban.

6.  Kattintson a **Létrehozás** gombra.

### <a name="check-the-automation"></a>Az automatizálás ellenőrzése

Annak figyeléséhez, hogy az automatizálás a várt módon fut-e, az Automation-fiók **áttekintése** csoportban keresse meg a **Feladatstatisztika nézetet** a **Figyelés**csoportban. A könnyebb láthatóság érdekében rögzítse ezt a nézetet az irányítópulton. A runbook-show sikeres futtatása "Befejezett" és sikertelen futtatások "Sikertelen" állapotban jelennek meg.

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Azure Monitor olvasói riasztás létrehozása e-mail értesítésekhez

Az Azure Monitor naplóit használó riasztás létrehozásához tegye a következőket. Előfeltételként az Azure Monitor-naplókat egy Log Analytics-munkaterülethez kell csatolni.

1.  Az Azure Portalon válassza a **Naplókeresés lehetőséget.**

2.  Hozzon létre egy lekérdezést a hibák és figyelmeztetések szinkronizálási csoport szerinti kiválasztásához a kiválasztott intervallumon belül. Példa:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  A lekérdezés futtatása után válassza ki az **Alert parancsot**jelző csengőt.

4.  A **Riasztás létrehozása a képernyőn csoportban**válassza a **Metrikus mérés**lehetőséget.

    1.  Állítsa az összesített értéket **nagyobbra, mint**.

    2.  A **nagyobb mint**után adja meg az értesítésfogadás előtti küszöbértéket. Az adatszinkronizálásban átmeneti hibák várhatók. A zaj csökkentése érdekében állítsa a küszöbértéket 5-re.

5.  A **Műveletek csoportban**állítsa **az E-mail értesítés** "Igen" beállítását. Adja meg a kívánt e-mail címzetteket.

6.  Kattintson a **Mentés** gombra. A megadott címzettek mostantól e-mailben értesítést kapnak, ha hibák lépnek fel.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Azure-figyelő nézet létrehozása figyeléshez

Ez a lépés létrehoz egy Azure Monitor nézetet az összes megadott szinkronizálási csoport vizuális figyeléséhez. A nézet több összetevőből áll:

-   Áttekintő csempe, amely megmutatja, hogy hány hiba, sikeres és figyelmeztetés van az összes szinkronizálási csoportban.

-   Az összes szinkronizálási csoport csempéje, amely a hibák és figyelmeztetések számát jeleníti meg szinkronizálási csoportonként. A probléma nélküli csoportok nem jelennek meg ezen a csempén.

-   Minden szinkronizálási csoport csempéje, amely a hibák, sikerességek és figyelmeztetések számát, valamint a legutóbbi hibaüzeneteket mutatja.

Az Azure Monitor nézet konfigurálásához tegye a következőket:

1.  A Log Analytics munkaterület kezdőlapján válassza ki a bal oldali pluszt a **nézettervező**megnyitásához.

2.  Válassza az **Importálás** lehetőséget a nézettervező felső sávján. Ezután válassza ki a "DataSyncLogOMSView" mintafájlt.

3.  A mintanézet két szinkronizálási csoport kezelésére alkalmas. A nézet szerkesztése a forgatókönyvnek megfelelően. Kattintson a **Szerkesztés gombra,** és hajtsa végre a következő módosításokat:

    1.  Szükség szerint hozzon létre új "Donut & List" objektumokat a Galériából.

    2.  Minden csempén frissítse a lekérdezéseket az adataival.

        1.  Minden csempén szükség szerint módosítsa a TimeStamp_t intervallumot.

        2.  Az egyes szinkronizálási csoportok csempéin frissítse a szinkronizálási csoport neveket.

    3.  Minden csempén szükség szerint frissítse a címet.

4.  Kattintson a **Mentés gombra,** és a nézet készen áll.

## <a name="cost-of-this-solution"></a>Ennek a megoldásnak a költsége

A legtöbb esetben ez a megoldás ingyenes.

**Azure-automatizálás:** Előfordulhat, hogy az Azure Automation-fiók kal kapcsolatos költségek merülnek fel, a használattól függően. A feladat futási idejének első 500 perce havonta ingyenes. A legtöbb esetben ez a megoldás várhatóan kevesebb, mint 500 percet használ havonta. A díjak elkerülése érdekében ütemezze a runbook futtatását két órás vagy annál nagyobb időközzel. További információ: [Automation pricing](https://azure.microsoft.com/pricing/details/automation/).

**Az Azure Monitor naplói:** Előfordulhat, hogy az Azure Monitor-naplók a használattól függően költségekkel járnak. Az ingyenes szint napi 500 MB betöltési adatot tartalmaz. A legtöbb esetben ez a megoldás várhatóan kevesebb, mint 500 MB/nap. A használat csökkentéséhez használja a runbookban található csak hibaszűrést. Ha naponta több mint 500 MB-ot használ, frissítsen a fizetős szintre, hogy elkerülje az elemzési kockázatok leállítását a korlátozás elérésekor. További információ: [Azure Monitor naplók díjszabása.](https://azure.microsoft.com/pricing/details/log-analytics/)

## <a name="code-samples"></a>Kódminták

Töltse le a cikkben ismertetett kódmintákat a következő helyekről:

-   [Adatszinkronizálási napló PowerShell-runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Adatszinkronizálás i.](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>További lépések
További információ az SQL Data Syncről:

-   Áttekintés – [Adatok szinkronizálása több felhőbeli és helyszíni adatbázisban az Azure SQL Data Sync segítségével](sql-database-sync-data.md)
-   Adatszinkronizálás beállítása
    - A portálon – [Oktatóanyag: Az SQL Data Sync beállítása az Adatok szinkronizálásához az Azure SQL Database és a helyszíni SQL Server között](sql-database-get-started-sql-data-sync.md)
    - A PowerShell-lel
        -  [A PowerShell használata több Azure SQL-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [A PowerShell használata egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Adatszinkronizálási ügynök – [Adatszinkronizálási ügynök az Azure SQL Data Sync szolgáltatáshoz](sql-database-data-sync-agent.md)
-   Gyakorlati tanácsok – [Gyakorlati tanácsok az Azure SQL Data Sync szolgáltatáshoz](sql-database-best-practices-data-sync.md)
-   Hibaelhárítás – [Az Azure SQL Data Sync szolgáltatással kapcsolatos problémák elhárítása](sql-database-troubleshoot-data-sync.md)
-   A szinkronizálási séma frissítése
    -   Transact-SQL - [A sémamódosítások replikációjának automatizálása az Azure SQL Data Sync-ben](sql-database-update-sync-schema.md)
    -   A PowerShell használatával – [A PowerShell használatával frissítse a szinkronizálási sémát egy meglévő szinkronizálási csoportban](scripts/sql-database-sync-update-schema.md)

További információ az SQL Database-ről:

-   [Az SQL Database áttekintése](sql-database-technical-overview.md)
-   [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
