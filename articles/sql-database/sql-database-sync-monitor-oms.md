---
title: Az Azure SQL Data szinkronban (előzetes verzió) Naplóelemzési figyelése |} Microsoft Docs
description: Log Analytics segítségével megtudhatja, hogyan figyelése Azure SQL adatszinkronizálás (előzetes verzió)
services: sql-database
ms.date: 04/01/2018
ms.topic: conceptual
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 4c684337a904fe6984e6b5cd5cf88ef8eb976cb6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650247"
---
# <a name="monitor-sql-data-sync-preview-with-log-analytics"></a>A figyelő SQL adatok szinkronban (előzetes verzió) Naplóelemzési 

Ellenőrizze az SQL adatszinkronizálás tevékenységnapló, és a hibák és figyelmeztetések észleléséhez, korábban kellett SQL adatszinkronizálás manuálisan ellenőrizze az Azure portálon, vagy PowerShell vagy a REST API-t használja. Kövesse a cikkben egy egyéni megoldás, amely javítja az adatok szinkronizálása figyelésének lehetőségével konfigurálásához. Ez a megoldás a forgatókönyvnek megfelelően testre.

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync előzetes verziójával végzett adatszinkronizálást](sql-database-sync-data.md) ismertető cikket.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>A szinkronizálási csoportok a figyelési irányítópult 

Már nincs szüksége a nézze át az egyes szinkronizálási csoportok külön-külön és azokban megkereshetik a problémák a naplókat. Egy egyéni Naplóelemzési nézet használatával figyelheti a szinkronizálási csoportok bármelyik az előfizetések egy helyen. Ez a nézet a fontos információkat SQL adatszinkronizálás ügyfelek hibaelhárításra használható.

![Adatok szinkronizálása figyelési irányítópult](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Az automatikus E-mail értesítések

Már nincs szüksége a részletek a naplóban manuálisan az Azure portál vagy PowerShell vagy a REST API használatával. A [Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), hozhat létre riasztásokat, amelyek közvetlenül Ugrás a hiba akkor fordul elő, amikor igénylő személyek e-mail címét.

![Adatok szinkronizálása értesítő e-mailek](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Hogyan állíthatja be ezeket a figyelési szolgáltatásokat? 

A felügyeleti megoldás az SQL adatszinkronizálás kevesebb, mint egy óra alatt a következő művelet végrehajtásával egyéni Naplóelemzési megvalósításához:

Kell konfigurálnia a három összetevővel:

-   Naplóadatok adatszinkronizálás SQL hírcsatornát, hogy Naplóelemzési PowerShell runbook.

-   A Naplóelemzési riasztások értesítő e-mailek.

-   A napló Analytics nézet figyelésre.

### <a name="samples-to-download"></a>Töltse le a minták

Töltse le a következő két minták:

-   [Adatok szinkronizálási napló PowerShell-forgatókönyv](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Adatok szinkronizálási napló Analytics megtekintése](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Előfeltételek

Ellenőrizze, hogy meg van adva a következő műveleteket:

-   Egy Azure Automation-fiók

-   Log Analytics-munkaterület

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShell-forgatókönyv lekérni az SQL-adatok szinkronizálása naplója 

Húzza az SQL adatszinkronizálás naplóadatokat, és elküldi a Naplóelemzési található Azure Automation PowerShell-forgatókönyv használatával. Egy minta parancsfájlt része. Előfeltételként szükség van egy Azure Automation-fiókra. Akkor szükséges, hozzon létre egy runbookot, és az ütemezés futtatásához. 

### <a name="create-a-runbook"></a>Runbook létrehozása

Egy runbook létrehozásával kapcsolatos további információkért lásd: [az első PowerShell runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Az Azure Automation-fiókjában, válassza ki a **Runbookok** lapon a folyamatok automatizálásához.

2.  Válassza ki **hozzáadása egy Runbook** , a Runbookok lap bal felső sarkában.

3.  Válassza ki **meglévő forgatókönyv importálása**.

4.  A **Runbook fájl**, használja az adott `DataSyncLogPowerShellRunbook` fájlt. Állítsa be a **runbooktípusba** , `PowerShell`. Nevezze el a runbookot.

5.  Kattintson a **Létrehozás** gombra. Most már rendelkezik egy runbookot.

6.  Az Azure Automation-fiók alatt válassza ki a **változók** lapon a megosztott erőforrások.

7.  Válassza ki **változó hozzáadása** a változók oldalon. Hozzon létre egy változót, a runbook legutóbbi végrehajtásának időpontja tárolásához. Ha több runbook, a rendszer minden runbook kell egy változó.

8.  Állítsa be a változó neve, mint a `DataSyncLogLastUpdatedTime` és annak típusát, a DateTime típusú érték.

9.  Válassza ki a runbookot, és kattintson a Szerkesztés gombra az oldal tetején.

10. Végezze el a fiókját és az SQL Data Sync konfigurációja a szükséges módosításokat. (További részletes információkért lásd: a minta parancsfájlt.)

    1.  Az Azure adatokat.

    2.  Szinkronizálási adatait.

    3.  OMS-adatokat. Ezek az információk megtalálhatók a OMS-portálon |} Beállítások |} Csatlakoztatott adatforrások. Adatok küldése a Naplóelemzési kapcsolatos további információért lásd: [adatokat küldeni a HTTP adatait gyűjtője API-t (előzetes verzió) Naplóelemzési](../log-analytics/log-analytics-data-collector-api.md).

11. A runbook futtatása a ablaktáblán. Ellenőrizze, hogy sikeres volt-e.

    Ha hibákat, győződjön meg arról, hogy a legújabb PowerShell-modul telepítve van. A legújabb PowerShell moduljának telepítése a **modulok Gallery** az Automation-fiókban.

12. Kattintson a **közzététele**

### <a name="schedule-the-runbook"></a>A runbook ütemezése

A runbook ütemezése:

1.  A runbook alatt válassza ki a **ütemezések** az erőforrások lapon.

2.  Válassza ki **ütemezés hozzáadása** az ütemezések lapon.

3.  Válassza ki **ütemezés kapcsolása a forgatókönyvhöz**.

4.  Válassza ki **hozzon létre egy új ütemezést.**

5.  Állítsa be **ismétlődési** ismétlődő és beállítása a időközt szeretne. Itt, azonos időintervallumát használja a parancsfájlban, és az OMS Szolgáltatáshoz.

6.  Kattintson a **Létrehozás** gombra.

### <a name="check-the-automation"></a>Ellenőrizze az automatizálás

Figyelheti, hogy a várt módon fut az automation **áttekintése** az automation-fiókban található a **Projekt statisztika** meg a **figyelés**. Ez a nézet egyszerűen megtekinthetők az irányítópulton rögzítheti. A runbook megjelenítése "Befejezettként" sikeres kísérletei és futtatása nem sikerült megjeleníteni "Sikertelen".

## <a name="create-a-log-analytics-reader-alert-for-email-notifications"></a>Riasztás létrehozása, a napló Analytics olvasó értesítő e-mailek

Riasztás létrehozása, amely használja a Naplóelemzési, tegye a következőket. Előfeltételként szüksége van a Naplóelemzési munkaterület kapcsolódó Naplóelemzési.

1.  Válassza ki az OMS-portálon **naplófájl-keresési**.

2.  Hozzon létre egy lekérdezést a kiválasztott időtartamon belül a hibák és figyelmeztetések szinkronizálási csoport kiválasztásához. Példa:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  A lekérdezés futtatása után válassza ki, amely szerint a harang **riasztási**.

4.  A **létrehozása riasztás alapján**, jelölje be **metrikus**.

    1.  Adja meg az összesített értéket **nagyobb, mint**.

    2.  Miután **nagyobb, mint**, adja meg a küszöbérték milyen értesítéseket kapni. Átmeneti hibák adatszinkronizálás vár. Zaj csökkentése érdekében a küszöbérték beállítása 5.

5.  A **műveletek**, beállíthatja **e-mailes értesítés** az "Igen". Adja meg a kívánt e-mailjeik címzettjeire.

6.  Kattintson a **Save** (Mentés) gombra. A megadott címzetteknek szóló e-mail értesítések most már fogadni, a hibák esetén.

## <a name="create-an-oms-view-for-monitoring"></a>A figyeléshez OMS nézet létrehozása

Ebben a lépésben létrehoz egy OMS nézet kijelölése az összes a megadott szinkronizálási a csoportok figyelési. A nézet több összetevőt tartalmaz:

-   Az Áttekintés csempe, amely jeleníti meg, hány hibák, a sikeres és a figyelmeztetések a szinkronizálási csoportokat is.

-   Egy csempe az összes szinkronizálás csoport, amely jeleníti meg. a hibák és figyelmeztetések szinkronizálási csoportonként. Problémák nélküli csoportok nem jelennek meg erre a csempére.

-   Egy csempe az egyes szinkronizálási csoportokban hibák, a sikeres, és a figyelmeztetések és a legutóbbi hibaüzenetek számát jeleníti meg.

Az OMS-nézet konfigurálásához tegye a következőket:

1.  Az OMS kezdőlapján válassza ki a bal oldali nyissa meg a plusz a **adatforrásnézet-tervezőből**.

2.  Válassza ki **importálási** a felső sávon, az adatforrásnézet-tervezőből. Jelölje ki a "DataSyncLogOMSView" minta fájlt.

3.  A minta nézet két szinkronizálási csoportok kezelésére szolgál. Ez a nézet a forgatókönyvnek megfelelően szerkesztése. Kattintson a **szerkesztése** és a következő módosításokat:

    1.  Hozzon létre új "Fánk & List" objektumok a gyűjteményből, igény szerint.

    2.  Az összes csempe frissítése a lekérdezések adatait.

        1.  Minden lapon módosítható TimeStamp_t tetszés szerint.

        2.  Az egyes szinkronizálási csempék, a szinkronizálási csoport nevének a frissítése.

    3.  Mindegyik mozaiknál szükség szerint frissítse az a cím.

4.  Kattintson a **mentése** , és készen áll a nézetet.

## <a name="cost-of-this-solution"></a>Ez a megoldás költsége

A legtöbb esetben ez a megoldás felszabadul.

**Azure Automation szolgáltatásbeli:** előfordulhat, hogy az Azure Automation-fiók, attól függően, hogy a használati költségeit. A feladat-futásidő havonta az első 500 perc szabadon. A legtöbb esetben ez a megoldás várható havi 500 percnél kevesebb használja. Díjak elkerülése érdekében a runbook futtatását, hogy legalább két órás ütemezni. További információk: [Automation árképzési](https://azure.microsoft.com/pricing/details/automation/).

**Naplóelemzési:** ingyenes Naplóelemzési attól függően, hogy a használati lehet. Ingyenes szint 500 MB naponkénti feldolgozott adatokat tartalmazza. A legtöbb esetben ez a megoldás várható betöltési naponta 500 MB-nál kevesebb. A használati csökkentéséhez a csak hiba szűrés használatához a runbookokban tartalmazott. Ha több mint 500 MB naponkénti használ, váltson annak elkerülése érdekében a korlátozás elérésekor leállítása Analytics fizetős csomagra. További információk: [Naplóelemzési árképzési](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Kódminták

Töltse le a következő helyekről cikkben leírt mintakódok:

-   [Adatok szinkronizálási napló PowerShell-forgatókönyv](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Adatok szinkronizálási napló Analytics megtekintése](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>További lépések
További információ az SQL Data Syncről:

-   [Több felhőalapú és helyszíni adatbázis közötti adatszinkronizálás az Azure SQL Data Synckel](sql-database-sync-data.md)
-   [Az Azure SQL Data Sync beállítása](sql-database-get-started-sql-data-sync.md)
-   [Ajánlott eljárások az Azure SQL Data Synchez](sql-database-best-practices-data-sync.md)
-   [Az Azure SQL Data Synckel hibaelhárítása](sql-database-troubleshoot-data-sync.md)

-   Teljes PowerShell-példák az SQL Data Sync konfigurálásáról:
    -   [A PowerShell használata több Azure SQL Database-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [A PowerShell használata egy Azure-beli SQL Database-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Az SQL Data Sync REST API dokumentációjának letöltése](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

További információ az SQL Database-ről:

-   [Az SQL Database áttekintése](sql-database-technical-overview.md)
-   [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
