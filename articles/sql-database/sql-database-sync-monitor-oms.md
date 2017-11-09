---
title: "Az Azure SQL adatok szinkronban Naplóelemzési OMS figyelése |} Microsoft Docs"
description: "Útmutató: Azure SQL adatszinkronizálás figyelje az OMS szolgáltatáshoz"
services: sql-database
ms.date: 11/7/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: e1099d2cd7eeccbe76d762028a0c5d5f95f53026
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="monitor-azure-sql-data-sync-preview-with-oms-log-analytics"></a>A figyelő az Azure SQL adatszinkronizálás (előzetes verzió) az OMS szolgáltatáshoz 

Ellenőrizze az SQL adatszinkronizálás tevékenységnapló, és a hibák és figyelmeztetések észleléséhez, korábban kellett SQL adatszinkronizálás manuálisan ellenőrizze az Azure portálon, vagy PowerShell vagy a REST API-t használja. Kövesse a cikkben egy egyéni megoldás, amely javítja az adatok szinkronizálása figyelésének lehetőségével konfigurálásához. Ez a megoldás a forgatókönyvnek megfelelően testre.

SQL adatszinkronizálás áttekintését lásd: [adatok szinkronizálásának több felhőalapú és helyszíni adatbázisokat az Azure SQL adatszinkronizálás (előzetes verzió)](sql-database-sync-data.md).

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>A szinkronizálási csoportok a figyelési irányítópult 

Már nincs szüksége a nézze át az egyes szinkronizálási csoportok külön-külön és azokban megkereshetik a problémák a naplókat. Egy egyéni OMS (Operations Management Suite) nézet használatával figyelheti a szinkronizálási csoportok bármelyik az előfizetések egy helyen. Ez a nézet a fontos információkat SQL adatszinkronizálás ügyfelek hibaelhárításra használható.

![Adatok szinkronizálása figyelési irányítópult](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.jpg)

## <a name="automated-email-notifications"></a>Az automatikus E-mail értesítések

Már nincs szüksége a részletek a naplóban manuálisan az Azure portál vagy PowerShell vagy a REST API használatával. Használatával a [OMS Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), ugorjon az e-mail címét, amely hiba esetén megjelenítéséhez szükséges riasztásokat hozhat létre.

![Adatok szinkronizálása értesítő e-mailek](media/sql-database-sync-monitor-oms/sync-email-notifications.jpg)

## <a name="how-do-you-set-this-up"></a>Hogyan hajtsa végre ezt lehet beállítani? 

A felügyeleti megoldás az SQL adatszinkronizálás kevesebb, mint egy óra alatt a következő művelet végrehajtásával egyéni OMS megvalósításához.

3 összetevők konfigurálása lesz szüksége:

-   Naplóadatok adatszinkronizálás SQL hírcsatornát, hogy OMS PowerShell runbook.

-   Értesítő e-mailek OMS szolgáltatáshoz értesítést.

-   Az OMS nézetben a figyelésre.

### <a name="samples-to-download"></a>Töltse le a minták

Töltse le a következő két minták:

-   [Adatok szinkronizálási napló PowerShell-forgatókönyv](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Adatok szinkronizálási napló OMS megtekintése](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Előfeltételek

Ellenőrizze, hogy meg van adva a következő műveleteket:

-   Egy Azure Automation-fiók

-   A Naplóelemzési kapcsolódó OMS-munkaterület

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShell-forgatókönyv lekérni az SQL-adatok szinkronizálása naplója 

Húzza az SQL adatszinkronizálás naplóadatokat, és elküldi a OMS található Azure Automation PowerShell-forgatókönyv használatával. Egy minta parancsfájlt része. Előfeltételként szükség van egy Azure Automation-fiókra. Akkor szükséges, hozzon létre egy runbookot, és az ütemezés futtatásához. 

### <a name="create-a-runbook"></a>Runbook létrehozása

Egy runbook létrehozásával kapcsolatos további információkért lásd: [az első PowerShell runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Az Azure Automation-fiókjában, válassza ki a **Runbookok** lapon a folyamatok automatizálásához.

2.  Válassza ki **adja hozzá a Runbookok** , a Runbookok lap bal felső sarkában.

3.  Válassza ki **meglévő forgatókönyv importálása**.

4.  A **Runbook fájl**, használja az adott `DataSyncLogPowerShellRunbook` fájlt. Állítsa be a **runbooktípusba** , `PowerShell`. Nevezze el a runbookot.

5.  Kattintson a **Létrehozás** gombra. Most már rendelkezik egy runbookot.

6.  Az Azure Automation-fiók alatt válassza ki a **változók** lapon a megosztott erőforrások.

7.  Válassza ki **változó hozzáadása** a változók oldalon. Hozzon létre egy változót, a runbook legutóbbi végrehajtásának időpontja tárolására van szükségünk. Ha több runbook, a rendszer minden runbook kell egy változó.

8.  Állítsa be a változó neve, mint a `DataSyncLogLastUpdatedTime` és annak típusát, a DateTime típusú érték.

9.  Válassza ki a runbookot, és kattintson a Szerkesztés gombra az oldal tetején.

10. Végezze el a fiókját és az SQL Data Sync konfigurációja a szükséges módosításokat. (Lásd: a minta-parancsfájl részletes információt.)

    1.  Az Azure adatokat.

    2.  Szinkronizálási adatait.

    3.  OMS-adatokat. Ezek az információk megtalálhatók a OMS-portálon |} Beállítások |} Csatlakoztatott adatforrások. Adatok küldése a Naplóelemzési kapcsolatos további információért lásd: [adatokat küldeni a HTTP adatait gyűjtője API-t (nyilvános előzetes verzió) Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api).

11. A runbook futtatása a ablaktáblán. Ellenőrizze, hogy sikeres volt-e.

    Ha hibákat, győződjön meg arról, hogy a legújabb PowerShell-modul telepítve van. Ehhez a **modulok Gallery** az Automation-fiókban.

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

Figyelheti, hogy a várt módon fut az automation **áttekintése** az automation-fiókban található a **Projekt statisztika** meg a **figyelés**. Ez egyszerűen megtekinthetők az irányítópulton rögzítheti. A runbook megjelenítése "Befejezettként" sikeres kísérletei és futtatása nem sikerült megjeleníteni "Sikertelen".

## <a name="create-an-oms-log-reader-alert-for-email-notifications"></a>Értesítő e-mailek OMS napló olvasó riasztás létrehozása

Riasztás létrehozása, amely OMS Naplóelemzési használ, a következő lehetőségekkel. Előfeltételként kell egy OMS-munkaterület kapcsolódó Naplóelemzési rendelkezik.

1.  Válassza ki az OMS-portálon **naplófájl-keresési**.

2.  Hozzon létre egy lekérdezést a kiválasztott időtartamon belül a hibák és figyelmeztetések szinkronizálási csoport kiválasztásához. Példa:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  A lekérdezés futtatása után válassza ki, amely szerint a harang **riasztási**.

4.  A **létrehozása riasztás alapján**, jelölje be **metrikus**.

    1.  Adja meg az összesített értéket **nagyobb, mint**.

    2.  Miután **nagyobb, mint**, adja meg a küszöbérték milyen értesítéseket kapni. Átmeneti hibák adatszinkronizálás vár. Azt javasoljuk, hogy 5 zaj csökkentése érdekében megadhatja a küszöbértéket.

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

    3.  Onn egyes csempére, a cím frissítéséhez, igény szerint.

4.  Kattintson a **mentése** , és készen áll a nézetet.

## <a name="cost-of-this-solution"></a>Ez a megoldás költsége

A legtöbb esetben ez a megoldás felszabadul.

**Azure Automation szolgáltatásbeli:** előfordulhat, hogy az Azure Automation-fiók, attól függően, hogy a használati költségeit. A feladat-futásidő havonta az első 500 perc szabadon. A legtöbb esetben ez a megoldás várható havi 500 percnél kevesebb használja. Díjak elkerülése érdekében a runbook futtatását, hogy legalább két órás ütemezni. További információk: [Automation árképzési](https://azure.microsoft.com/pricing/details/automation/).

**OMS Naplóelemzési:** ingyenes OMS attól függően, hogy miképpen lehet. Ingyenes szint 500 MB naponkénti feldolgozott adatokat tartalmazza. A legtöbb esetben ez a megoldás várható betöltési naponta 500 MB-nál kevesebb. A használati csökkentéséhez a csak hiba szűrés használatához a runbookokban tartalmazott. Ha több mint 500 MB naponkénti használ, váltson annak elkerülése érdekében a korlátozás elérésekor leállítása Analytics fizetős csomagra. További információk: [Naplóelemzési árképzési](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Kódminták

Töltse le a következő helyekről cikkben leírt mintakódok:

-   [Adatok szinkronizálási napló PowerShell-forgatókönyv](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Adatok szinkronizálási napló OMS megtekintése](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Következő lépések
vagy SQL adatszinkronizálás, kapcsolatos további információért lásd:

-   [Szinkronizálja az adatokat több felhőalapú és helyszíni adatbázisokat az Azure SQL adatszinkronizálás](sql-database-sync-data.md)
-   [Ismerkedés az Azure SQL-adatok szinkronizálása](sql-database-get-started-sql-data-sync.md)
-   [Az Azure SQL adatszinkronizálás problémák elhárítása](sql-database-troubleshoot-data-sync.md)

-   PowerShell-példák bemutatják, hogyan konfigurálja az SQL adatszinkronizálás befejezése:
    -   [A PowerShell szolgáltatás használatával több Azure SQL-adatbázisok közötti szinkronizálása](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Egy Azure SQL-adatbázis és a helyszíni SQL Server-adatbázisok közötti szinkronizálása a PowerShell használatával](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Töltse le az SQL Data szinkronizálási REST API dokumentációja](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

SQL-adatbázis kapcsolatos további információkért lásd:

-   [SQL-adatbázis – áttekintés](sql-database-technical-overview.md)
-   [Adatbázis életciklusának kezelésére](https://msdn.microsoft.com/library/jj907294.aspx)
