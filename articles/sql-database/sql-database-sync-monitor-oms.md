---
title: A Log Analytics használatával az Azure SQL Data Sync monitorozása |} A Microsoft Docs
description: Azure SQL Data Sync monitorozása a Log Analytics használatával
services: sql-database
ms.date: 04/01/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 3bad71834d11b640bbc3577bf67c10357fad36dc
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618310"
---
# <a name="monitor-sql-data-sync-with-log-analytics"></a>Az SQL Data Sync monitorozása a Log Analytics szolgáltatással 

Az SQL Data Sync a tevékenységnaplóban, és észlelheti a hibák és figyelmeztetések, korábban kellett az SQL Data Sync manuálisan ellenőrizze az Azure Portalon, vagy a PowerShell vagy a REST API segítségével. Kövesse a cikkben egy egyéni megoldás, amely javítja a figyelés felület Data Sync beállítása. Ez a megoldás saját forgatókönyvéhez igazítva testre szabhatja.

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](sql-database-sync-data.md) ismertető cikket.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Az összes szinkronizálási csoport figyelési irányítópult 

Már nincs szüksége, nézze át a naplókat a problémák kereséséhez külön-külön az egyes szinkronizálási csoportok. Bármely, egy helyen az előfizetés összes szinkronizálási csoportot egy egyéni Log Analytics-nézet használatával figyelheti meg. Ez a nézet a fontos információk az SQL Data Sync ügyfeleknek feltárásával.

![Adatok szinkronizálása figyelési irányítópult](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Az automatikus E-mail-értesítések

Már nincs szüksége a naplóban manuálisan az Azure Portalon vagy a PowerShell vagy a REST API használatával. A [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), lépjen közvetlenül az e-mail-címeket, annak a személynek, megtekintheti őket, ha hiba lép fel igénylő riasztásokat is létrehozhat.

![Data Sync e-mail-értesítések](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Hogyan állíthatja be ezeket a figyelési funkciókat? 

Egy egyéni Naplóelemzési, figyelési megoldás az SQL Data Sync kevesebb mint egy órán belül a következők végrehajtásával megvalósításához:

Mindhárom összetevő konfigurálni kell:

-   Egy SQL Data Sync naplóadatok a Log Analytics-hírcsatorna PowerShell-forgatókönyvet.

-   E-mail-értesítések a Log Analytics-riasztásból.

-   A Log Analytics-nézet figyelésre.

### <a name="samples-to-download"></a>Minták letöltése

Töltse le a következő két mintát:

-   [Adatok szinkronizálása Log PowerShell-forgatókönyv](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Adatok szinkronizálása Log Analytics-nézet](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy meg van adva az alábbiakat:

-   Azure Automation-fiók

-   Log Analytics-munkaterület

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShell-forgatókönyvet az SQL Data Sync napló beolvasása 

Az Azure Automationben tárolt a PowerShell-runbook használatával lekérheti az SQL Data Sync naplózási adatokat, majd azokat elküldi a Log Analytics. A példaszkript egy részét képezi. Egy előfeltétel szüksége lesz egy Azure Automation-fiókot. Akkor szükséges, hozzon létre egy runbookot, és ütemezheti. 

### <a name="create-a-runbook"></a>Runbook létrehozása

Egy runbook létrehozásával kapcsolatos további információkért lásd: [az első PowerShell-forgatókönyvem](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Az Azure Automation-fiók alatt válassza ki a **Runbookok** lapon a folyamatok automatizálásával.

2.  Válassza ki **forgatókönyv hozzáadása** a Runbookok lap bal felső sarkában található.

3.  Válassza ki **meglévő Runbook importálása**.

4.  A **forgatókönyvfájl**, használja az adott `DataSyncLogPowerShellRunbook` fájlt. Állítsa be a **Runbook típusa** , `PowerShell`. Nevezze el a runbookot.

5.  Kattintson a **Létrehozás** gombra. Most már rendelkezik egy runbookot.

6.  Az Azure Automation-fiók alatt válassza ki a **változók** megosztott erőforrások lapján.

7.  Válassza ki **változó hozzáadása** a változók oldalon. Hozzon létre egy változót az utolsó végrehajtási időpont a runbook tárolásához. Ha több runbook, minden egyes runbook szükség van egy változót.

8.  Állítsa be a változó nevét, `DataSyncLogLastUpdatedTime` és jeho Typu DateTime.

9.  Válassza ki a runbookot, és kattintson a Szerkesztés gombra a lap tetején.

10. Hajtsa végre a módosításokat, a fiók és az SQL Data Sync konfiguráció szükséges. (További részletes információkért lásd: a minta parancsfájl.)

    1.  Az Azure information.

    2.  Szinkronizálási csoport adatokat.

    3.  OMS-adatokat. Ezek az információk megtalálhatók a OMS-portálon |} Beállítások |} A csatlakoztatott források. A Log Analyticshez való adatküldés kapcsolatos további információk: [adatokat küldeni a HTTP-adatgyűjtő API (előzetes verzió) a Log Analytics](../log-analytics/log-analytics-data-collector-api.md).

11. A runbook futtatása a teszt panelt. Ellenőrizze, hogy sikeres volt.

    Ha hibákat észlel, győződjön meg arról, hogy a legújabb PowerShell-modul telepítve van. Telepítheti a legújabb PowerShell-modul a **Modulkatalógus** az Automation-fiókban.

12. Kattintson a **közzététele**

### <a name="schedule-the-runbook"></a>A runbook ütemezése

A runbook ütemezése:

1.  A runbook alatt válassza ki a **ütemezések** lapon az erőforrások.

2.  Válassza ki **ütemezés hozzáadása** az ütemezések lapon.

3.  Válassza ki **összekapcsolhat egy ütemezést a runbook**.

4.  Válassza ki **új ütemezés létrehozása.**

5.  Állítsa be **ismétlődési** ismétlődő és állítsa be az időközt szeretne. Intervallum itt használja, a parancsfájl, és az OMS-ben.

6.  Kattintson a **Létrehozás** gombra.

### <a name="check-the-automation"></a>Az automation ellenőrzése

Az automation alatt a vártnak megfelelően működik-e figyelése **áttekintése** az automation-fiók található a **feladatstatisztika** nézetében **figyelés**. Ez a nézet egyszerűen megtekinthetők az irányítópulton rögzítheti. Sikeres futtatások a runbook megjelenítése "Befejezettként", és sikertelen futtatások megjelenítése "Sikertelen."

## <a name="create-a-log-analytics-reader-alert-for-email-notifications"></a>Hozzon létre egy Log Analytics-olvasó riasztás E-mail értesítések

Riasztás létrehozása, amely használja a Log Analytics, tegye a következőket. Egy előfeltétel szüksége lesz a Log Analytics-munkaterület társított Log Analytics.

1.  Az OMS-portálon válassza **naplóbeli keresés**.

2.  Hozzon létre egy lekérdezést a kiválasztott időtartamon belül a hibák és figyelmeztetések által szinkronizálási csoport kiválasztásához. Példa:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  A lekérdezés futtatása után válassza a harang arról, hogy a **riasztási**.

4.  A **létrehozása riasztás alapján**válassza **Metrikamérés**.

    1.  Adja meg az összesített értéket **nagyobb, mint**.

    2.  Miután **nagyobb, mint**, adja meg a küszöbérték leteltét, mielőtt értesítést kap. Átmeneti hibák várhatóan az adatszinkronizálás. A zaj csökkentésére, megadhatja azt a küszöbértéket az 5.

5.  A **műveletek**állítsa be **e-mailes értesítés** "Igen"értéket. Adja meg a kívánt e-mail-címzettek.

6.  Kattintson a **Save** (Mentés) gombra. A megadott címzetteknek most email értesítéseket kapni, ha hibák fordulnak elő.

## <a name="create-an-oms-view-for-monitoring"></a>Figyelés az OMS nézet létrehozása

Ez a lépés létrehoz egy OMS nézet vizuálisan figyelése az összes megadott szinkronizálási csoportot. A nézet több összetevőket tartalmazza:

-   Az Áttekintés csempe, amely jeleníti meg, hány hibák, a sikeres és a figyelmeztetések összes szinkronizálási csoportot is.

-   Egy csempe az összes szinkronizálási csoportot, amely jeleníti meg. a hibák és figyelmeztetések szinkronizálási csoportonként. Problémák nélküli csoportok nem jelennek meg erre a csempére.

-   Egy csempe az összes szinkronizálási csoportból, amely hibák, sikeresen lezajlott, és a figyelmeztetések és a legutóbbi hiba üzenetek számát jeleníti meg.

Az OMS-nézet konfigurálásához tegye a következőket:

1.  Az OMS kezdőlapján válassza a plusz megnyitásához a bal oldali a **adatforrásnézet-tervezőből**.

2.  Válassza ki **importálás** Az adatforrásnézet-tervezőből, a felső sávon található. Ezután válassza ki a "DataSyncLogOMSView"-mintafájlt.

3.  A minta nézet két szinkronizálási csoportok kezelésére szolgál. Ez a nézet a saját forgatókönyvéhez igazítva szerkesztése. Kattintson a **szerkesztése** és a következő módosításokat:

    1.  Új "Fánkdiagram és lista" objektumok létrehozása a gyűjteményből, szükség szerint.

    2.  Lévő egyes csempék frissítése a lekérdezések adatait.

        1.  Az egyes csempéken a TimeStamp_t időköz igény szerint módosítható.

        2.  A csempék az összes szinkronizálási csoportból, a szinkronizálási csoport nevének a frissítése.

    3.  Az egyes csempéken frissítse a címet, igény szerint.

4.  Kattintson a **mentése** , és készen áll a nézetet.

## <a name="cost-of-this-solution"></a>Ez a megoldás költsége

A legtöbb esetben ez a megoldás használata ingyenes.

**Az Azure Automation:** előfordulhat, hogy az Azure Automation-fiókkal, attól függően, a használat költségeit. Az első 500 perc feladatfuttatási idő havi használata ingyenes. A legtöbb esetben ez a megoldás várható havi 500 perc használja. Költségek elkerülése érdekében a runbook futtatásához legalább két órás időközönként ütemezheti. További információ: [díjszabásról](https://azure.microsoft.com/pricing/details/automation/).

**A log Analytics:** előfordulhat, hogy a használat függően a Log Analytics költséggel. Az ingyenes szint biztosít a feldolgozott adatok napi 500 MB. A legtöbb esetben ez a megoldás várt, hogy kevesebb, mint 500 MB / nap. A felhasználás csökkentéséhez a hiba csak szűrés használatához a runbook szerepel. Ha több mint 500 MB / nap használ, frissítse a fizetős szint annak elkerülése érdekében az elemzés az korlát elérése után leáll. További információ: [Log Analytics díjszabása](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Kódminták

Letölteni a mintakódokat, a következő helyekről cikkben leírt:

-   [Adatok szinkronizálása Log PowerShell-forgatókönyv](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Adatok szinkronizálása Log Analytics-nézet](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>További lépések
További információ az SQL Data Syncről:

-   [Több felhőalapú és helyszíni adatbázis közötti adatszinkronizálás az Azure SQL Data Synckel](sql-database-sync-data.md)
-   [Az Azure SQL Data Sync beállítása](sql-database-get-started-sql-data-sync.md)
-   [Ajánlott eljárások az Azure SQL Data Synchez](sql-database-best-practices-data-sync.md)
-   [Az Azure SQL Data Synckel hibaelhárítása](sql-database-troubleshoot-data-sync.md)

-   Teljes PowerShell-példák az SQL Data Sync konfigurálásáról:
    -   [A PowerShell használata több Azure SQL Database-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [A PowerShell használata egy Azure-beli SQL Database-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)

További információ az SQL Database-ről:

-   [Az SQL Database áttekintése](sql-database-technical-overview.md)
-   [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
