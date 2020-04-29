---
title: SQL-adatszinkronizálás figyelése Azure Monitor naplókkal
description: Ismerje meg, hogyan figyelhetők meg az Azure SQL-adatszinkronizálás Azure Monitor naplók használatával
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 5f5980f74b24cd972d43e9b05d4a5d623e6e3d2f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383690"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>SQL-adatszinkronizálás figyelése Azure Monitor naplókkal 

A SQL-adatszinkronizálási tevékenység naplójának ellenőrzéséhez és a hibák és figyelmeztetések észleléséhez előzőleg ellenőriznie kell SQL-adatszinkronizálás manuálisan a Azure Portal, vagy a PowerShell vagy a REST API használatával. Az ebben a cikkben ismertetett lépéseket követve konfigurálhat egy egyéni megoldást, amely javítja az adatszinkronizálás figyelésének élményét. A megoldás testreszabható úgy, hogy illeszkedjen a forgatókönyvhöz.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](sql-database-sync-data.md) ismertető cikket.

> [!IMPORTANT]
> Az Azure SQL-adatszinkronizálás jelenleg **nem** támogatja Azure SQL Database felügyelt példányok használatát.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Az összes szinkronizálási csoport figyelési irányítópultja 

Többé nem kell megvizsgálnia az egyes szinkronizálási csoportok naplóit a problémák kereséséhez. Az egyes előfizetések összes szinkronizálási csoportját egy helyen, egyéni Azure Monitor nézet használatával figyelheti. Ez a nézet felfedi a SQL-adatszinkronizálás ügyfelek számára fontos információkat.

![Adatszinkronizálás monitorozási irányítópultja](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatikus e-mail-értesítések

Többé nem kell manuálisan ellenőriznie a naplót a Azure Portal vagy a PowerShell vagy a REST API használatával. [Azure monitor naplók](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)használatával olyan riasztásokat hozhat létre, amelyek közvetlenül azon személyek e-mail-címeire mutatnak, akiknek hiba esetén meg kell tekinteniük őket.

![Adatszinkronizálási értesítő e-mailek](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Hogyan állíthatja be ezeket a figyelési funkciókat? 

A következő műveletek végrehajtásával egyéni Azure Monitor naplók figyelési megoldását implementálhatja SQL-adatszinkronizálás számára kevesebb mint egy órán belül:

Három összetevőt kell konfigurálnia:

-   Egy PowerShell-runbook, amely a naplóba SQL-adatszinkronizálás naplózza az adatAzure Monitor naplókba.

-   Az e-mail-értesítések Azure Monitor riasztása.

-   A figyelés Azure Monitor nézete.

### <a name="samples-to-download"></a>Letölthető minták

Töltse le a következő két mintát:

-   [Adatszinkronizálási napló PowerShell-Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Adatszinkronizálási Azure Monitor nézet](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a következő dolgokat állította be:

-   Egy Azure Automation fiók

-   Log Analytics-munkaterület

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShell-Runbook SQL-adatszinkronizálás napló beszerzéséhez 

A Azure Automationban üzemeltetett PowerShell-runbook lekéréséhez és az Azure Monitor naplókba való elküldéséhez használja a SQL-adatszinkronizálás naplózási szolgáltatásait A rendszer egy minta parancsfájlt is tartalmaz. Előfeltételként Azure Automation fiókkal kell rendelkeznie. Ezután létre kell hoznia egy runbook, és ütemeznie kell a futtatását. 

### <a name="create-a-runbook"></a>Runbook létrehozása

A runbook létrehozásával kapcsolatos további információkért tekintse meg [az első PowerShell-runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  A Azure Automation fiók alatt válassza a **runbookok** lapot a folyamat automatizálása alatt.

2.  Válassza a **Runbook hozzáadása** elemet a runbookok lap bal felső sarkában.

3.  Válassza **a meglévő Runbook importálása**lehetőséget.

4.  A **Runbook fájl**alatt használja a megadott `DataSyncLogPowerShellRunbook` fájlt. Állítsa a **Runbook típust a következőre** : `PowerShell`. Adja meg a runbook nevét.

5.  Kattintson a **Létrehozás** gombra. Most már rendelkezik egy runbook.

6.  A Azure Automation fiók alatt válassza a **változók** fület a megosztott erőforrások területen.

7.  Válassza a **változó hozzáadása** lehetőséget a változók lapon. Hozzon létre egy változót, amely a runbook utolsó végrehajtási idejét tárolja. Ha több runbookok rendelkezik, minden egyes runbook egy változóra van szükség.

8.  Állítsa be a változó nevét `DataSyncLogLastUpdatedTime` , és állítsa be a típusát datetime értékre.

9.  Válassza ki a runbook, és kattintson a lap tetején található Szerkesztés gombra.

10. Végezze el a fiókhoz és a SQL-adatszinkronizálás konfigurációhoz szükséges módosításokat. (Részletesebb információ a minta parancsfájlban található.)

    1.  Azure-információk.

    2.  Szinkronizálási csoport adatai.

    3.  Azure Monitor naplózza az adatokat. Az információk megkeresése az Azure Portalon | Beállítások | Csatlakoztatott források. Az adatok Azure Monitor naplókba való küldésével kapcsolatos további információkért lásd: [adatok küldése Azure monitor naplókba a http-adatgyűjtő API-val (előzetes verzió)](../azure-monitor/platform/data-collector-api.md).

11. Futtassa a runbook a teszt ablaktáblán. Győződjön meg arról, hogy a művelet sikeres volt.

    Ha hibákat észlel, ellenőrizze, hogy telepítve van-e a legújabb PowerShell-modul. A legújabb PowerShell-modult az Automation-fiók **modulok galériájában** telepítheti.

12. Kattintson a **Közzététel** gombra

### <a name="schedule-the-runbook"></a>A runbook beosztása

A runbook ütemezhet:

1.  A runbook alatt válassza az erőforrások területen az **ütemtervek** lapot.

2.  Válassza az **ütemterv hozzáadása** lehetőséget az ütemtervek lapon.

3.  Válassza **az ütemterv csatolása a runbook**lehetőséget.

4.  Válassza **az új ütemterv létrehozása lehetőséget.**

5.  Állítsa be az **ismétlődést** ismétlődőre, és állítsa be a kívánt időközt. Használja ugyanazt az intervallumot itt, a parancsfájlban és a Azure Monitor naplókban.

6.  Kattintson a **Létrehozás** gombra.

### <a name="check-the-automation"></a>Az Automation ellenõrzése

Ha szeretné megfigyelni, hogy az automatizálás a várt módon fut-e, az Automation-fiók **áttekintése** alatt keresse meg a **figyelés**területen található **feladatok statisztikai** nézetét. A nézet rögzítése az irányítópulton az egyszerű megtekintés érdekében. A runbook sikeres futtatása "befejezett", és a sikertelen futtatások "sikertelen" megjelenítést mutatnak.

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Azure Monitor olvasói riasztás létrehozása e-mailes értesítésekhez

Azure Monitor naplókat használó riasztás létrehozásához tegye a következőket. Előfeltételként Azure Monitor naplókat kell összekapcsolni egy Log Analytics munkaterülettel.

1.  A Azure Portal válassza a **naplóbeli keresés**lehetőséget.

2.  Hozzon létre egy lekérdezést a hibák és figyelmeztetések kiválasztásához a kiválasztott intervallumon belül a szinkronizálási csoport alapján. Például:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  A lekérdezés futtatása után válassza ki azt a harangot, amely a **riasztást**mondja.

4.  **A riasztás alapja**alapján területen válassza a **metrika mérése**elemet.

    1.  Állítsa az összesítő értéket **nagyobbra, mint**.

    2.  A **nagyobb**értéket követően adja meg a küszöbértéket az értesítések fogadása előtt. Átmeneti hibák várhatóak az adatszinkronizálás során. A zaj csökkentése érdekében állítsa 5 értékre a küszöbértéket.

5.  A **műveletek**területen adja meg az **e-mailes értesítést** az "igen" értékre. Adja meg a kívánt e-mail-címzetteket.

6.  Kattintson a **Save** (Mentés) gombra. A megadott címzettek mostantól kapnak e-mail-értesítéseket, amikor hibák történnek.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Azure Monitor nézet létrehozása a figyeléshez

Ez a lépés egy Azure Monitor nézetet hoz létre az összes megadott szinkronizálási csoport vizuális figyeléséhez. A nézet több összetevőt tartalmaz:

-   Egy áttekintő csempe, amely azt mutatja, hogy az összes szinkronizálási csoport hány hibát, sikert és figyelmeztetést tartalmaz.

-   Az összes szinkronizálási csoport csempéje, amely a hibák és figyelmeztetések számát jeleníti meg szinkronizálási csoportonként. A probléma nélküli csoportok nem jelennek meg ezen a csempén.

-   Az egyes szinkronizálási csoportok csempéje, amely a hibák, a sikerek és a figyelmeztetések számát, valamint a legutóbbi hibaüzeneteket mutatja.

A Azure Monitor nézet konfigurálásához tegye a következőket:

1.  A Log Analytics munkaterület kezdőlapján kattintson a bal oldalon található plusz gombra a **Tervező nézet**megnyitásához.

2.  Válassza az **Importálás** lehetőséget a Tervező nézet felső sávján. Ezután válassza ki a "DataSyncLogOMSView" nevű mintát.

3.  A minta nézet két szinkronizálási csoport kezelésére szolgál. A nézet szerkesztésével illeszkedjen a forgatókönyvhöz. Kattintson a **Szerkesztés** gombra, és végezze el a következő módosításokat:

    1.  Szükség szerint hozzon létre új "fánk & List" objektumokat a gyűjteményből.

    2.  Az egyes csempék esetében frissítse a lekérdezéseket az adataival.

        1.  Az egyes csempén módosítsa a TimeStamp_t intervallumot igény szerint.

        2.  Az egyes szinkronizálási csoportok csempéi esetében frissítse a szinkronizálási csoportok nevét.

    3.  Az egyes csempék esetében szükség szerint frissítse a címet.

4.  Kattintson a **Save (Mentés** ) gombra, és a nézet készen áll.

## <a name="cost-of-this-solution"></a>A megoldás díja

A legtöbb esetben ez a megoldás ingyenes.

**Azure Automation:** A használattól függően a Azure Automation-fiókkal kapcsolatos költségek is felmerülhetnek. Havonta a feladatok futtatásának első 500 perce ingyenes. A legtöbb esetben ez a megoldás várhatóan kevesebb mint 500 percet vesz igénybe havonta. A díjak elkerüléséhez ütemezze a runbook úgy, hogy két órán belül fusson. További információ: az [Automation díjszabása](https://azure.microsoft.com/pricing/details/automation/).

**Naplók Azure monitor:** A használattól függően előfordulhat, hogy a Azure Monitor naplókhoz kapcsolódó költségeket kell fizetnie. Az ingyenes csomag naponta 500 MB betöltött adatot tartalmaz. A legtöbb esetben ez a megoldás várhatóan kevesebb, mint 500 MB-ot tölt be naponta. A használat csökkentése érdekében használja a runbook található, csak hibákra vonatkozó szűrést. Ha naponta több mint 500 MB-ot használ, frissítsen a fizetős szintre, hogy elkerülje az elemzések kockázatát a korlátozás elérésekor. További információ: [Azure monitor naplók díjszabása](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Kódminták

Töltse le a cikkben ismertetett kódot a következő helyekről:

-   [Adatszinkronizálási napló PowerShell-Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Adatszinkronizálási Azure Monitor nézet](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>További lépések
További információ az SQL Data Syncről:

-   Áttekintés – az [adatszinkronizálás több felhőalapú és helyszíni adatbázis között az Azure SQL-adatszinkronizálás](sql-database-sync-data.md)
-   Adatszinkronizálás beállítása
    - A portálon – [oktatóanyag: SQL-adatszinkronizálás beállítása az Azure SQL Database és a helyszíni SQL Server közötti adatszinkronizáláshoz](sql-database-get-started-sql-data-sync.md)
    - A PowerShell-lel
        -  [A PowerShell használata több Azure SQL-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [A PowerShell használata egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Adatszinkronizálási ügynök – [Az Azure SQL-adatszinkronizálás adatszinkronizálási ügynöke](sql-database-data-sync-agent.md)
-   Ajánlott eljárások – [ajánlott eljárások az Azure SQL-adatszinkronizálás](sql-database-best-practices-data-sync.md)
-   Hibaelhárítás – [Az Azure SQL-adatszinkronizálás hibáinak elhárítása](sql-database-troubleshoot-data-sync.md)
-   A szinkronizálási séma frissítése
    -   A Transact-SQL- [ben – automatizálja a séma változásainak az Azure-ban való replikálását SQL-adatszinkronizálás](sql-database-update-sync-schema.md)
    -   A PowerShell [használatával – egy meglévő szinkronizálási csoportban lévő szinkronizálási séma frissítéséhez használja a PowerShellt](scripts/sql-database-sync-update-schema.md) .

További információ az SQL Database-ről:

-   [Az SQL Database áttekintése](sql-database-technical-overview.md)
-   [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
