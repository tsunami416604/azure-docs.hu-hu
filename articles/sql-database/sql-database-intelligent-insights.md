---
title: Az adatbázisok teljesítményének figyelése az Intelligent Insights segítségével
description: Az Azure SQL Database Intelligent Insights beépített intelligenciával folyamatosan figyeli az adatbázis-használatot mesterséges intelligencián keresztül, és észleli a gyenge teljesítményt okozó zavaró eseményeket.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: d7b9ada17871dc7882209b7a8a449a8edcd61a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214075"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Az Intelligens elemzések a a mi segítségével figyelik és elhárítják az adatbázis teljesítményét (előzetes verzió)

Az Azure SQL Database Intelligent Insights segítségével megtudhatja, hogy mi történik az adatbázis teljesítményével.

Az Intelligent Insights beépített intelligenciával folyamatosan figyeli az adatbázis-használatot mesterséges intelligenciával, és észleli a gyenge teljesítményt okozó zavaró eseményeket. Észlelése után egy részletes elemzést végeznek, amely létrehoz egy Intelligent Insights erőforrásnaplót (úgynevezett SQLInsights) a probléma intelligens felmérésével. Ez az értékelés az adatbázis teljesítményével kapcsolatos probléma kiváltó okelemzéséből és lehetőség szerint a teljesítmény javítására vonatkozó javaslatokból áll.

## <a name="what-can-intelligent-insights-do-for-you"></a>Mit tehet Önért az Intelligent Insights?

Az Intelligent Insights az Azure beépített intelligenciájának egyedülálló képessége, amely a következő értéket nyújtja:

- Proaktív figyelés
- Személyre szabott teljesítményelemzés
- Az adatbázis teljesítményének romlásának korai felismerése
- Az észlelt problémák kiváltó okainak elemzése
- Teljesítményjavítási javaslatok
- Több százezer adatbázis kiskálázási képessége
- Pozitív hatás a DevOps-erőforrásokra és a teljes tulajdonlási költségre

## <a name="how-does-intelligent-insights-work"></a>Hogyan működik az Intelligent Insights

Az Intelligent Insights az adatbázis teljesítményének összehasonlításával elemzi az adatbázis-munkaterhelést az elmúlt hét napos alapszámítási feladattal. Az adatbázis-munkaterhelés az adatbázis teljesítménye szempontjából legjelentősebbnek ítélt lekérdezésekből áll, például a legismétlődőbb és legnagyobb lekérdezésekből. Mivel minden adatbázis egyedi a szerkezete, az adatai, a használata és az alkalmazása alapján, minden létrehozott számítási feladat alapkonfigurációja egyedi és egyedi az adott számítási feladatra. A számítási feladatok alapkonfigurációjának típusától független intelligens elemzési adatok is figyelik az abszolút működési küszöbértékeket, és észleli a túlzott várakozási idővel, kritikus kivételekkel és a lekérdezés paraméterezésével kapcsolatos problémákat, amelyek hatással lehetnek a teljesítményre.

Miután a teljesítményromlási problémát több megfigyelt metrikából észleli mesterséges intelligencia használatával, elemzés történik. A diagnosztikai napló jön létre egy intelligens betekintést, hogy mi történik az adatbázissal. Az Intelligent Insights segítségével az első megjelenéstől a megoldásig egyszerűen nyomon követheti az adatbázis teljesítményével kapcsolatos problémát. Minden észlelt probléma nyomon követhető az életciklusa során a kezdeti probléma észlelése és a teljesítmény javulásának ellenőrzése a befejezéséig.

![Adatbázis-teljesítményelemzési munkafolyamat](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

Az adatbázis teljesítményproblémáinak mérésére és észlelésére használt metrikák a lekérdezés időtartamán, az időtúltöltési kérelmeken, a túlzott várakozási időn és a hibás kérelmeken alapulnak. A mérőszámokkal kapcsolatos további információkért [lásd: Észlelési mutatók.](#detection-metrics)

Az SQL Database azonosított teljesítményromlásai az SQLInsights-naplóban kerülnek rögzítésre, intelligens bejegyzésekkel, amelyek a következő tulajdonságokból állnak:

| Tulajdonság | Részletek |
| :------------------- | ------------------- |
| Adatbázis adatai | Metaadatok egy olyan adatbázisról, amelyről a rendszer betekintést észlelt, például egy erőforrás URI-ját. |
| Megfigyelt időtartomány | Az észlelt betekintés időszakának kezdési és befejezési időpontja. |
| Érintett mérőszámok | Olyan mérőszámok, amelyek betekintést okoztak: <ul><li>A lekérdezés időtartamának növelése [másodperc].</li><li>Túlzott várakozás [másodperc].</li><li>Időkimenő kérelmek [százalék].</li><li>Hibatörtént a kijelentkezési kérelmek [százalék].</li></ul>|
| Hatásérték | Mért mérőszám értéke. |
| Érintett lekérdezések és hibakódok | Lekérdezési kivonat vagy hibakód. Ezek segítségével könnyen korrelál az érintett lekérdezések. Metrikák, amelyek vagy lekérdezés időtartama növelése, várakozási idő, időtúllépés számít, vagy hibakódok vannak megadva. |
| Nyomozás | Az adatbázisban az esemény idején azonosított észlelés. 15 észlelési minta létezik. További információt az [Intelligens elemzésekkel kapcsolatos adatbázis-teljesítményproblémák elhárítása című](sql-database-intelligent-insights-troubleshoot-performance.md)témakörben talál. |
| Kiváltó okok elemzése | Az ember által olvasható formátumban azonosított probléma kiváltó okának elemzése. Egyes elemzések szükség esetén teljesítményjavítási javaslatot tartalmazhatnak. |
|||

Az Intelligens elemzési adatok Azure SQL Analytics szolgáltatással való használatának gyakorlati áttekintését és a tipikus használati forgatókönyveket a következő videóban tekintheti meg:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Az Intelligent Insights az SQL Database teljesítményével kapcsolatos problémák felderítésében és elhárításában retusáló. Az Intelligens elemzési adatok használatával háríthatja el az adatbázis-teljesítménnyel kapcsolatos problémákat, olvassa el [az Azure SQL Database teljesítményével kapcsolatos problémák elhárítása az Intelligent Insights szolgáltatással című témakört.](sql-database-intelligent-insights-troubleshoot-performance.md)

## <a name="intelligent-insights-options"></a>Intelligens elemzési lehetőségek

Az Azure SQL Database-ben elérhető Intelligens insights-beállítások a következők:

| Intelligens betekintési lehetőség | Egyadatbázis- és készletezésű adatbázis-támogatás | Példányadatbázis-támogatás |
| :----------------------------- | ----- | ----- |
| **Intelligens insights konfigurálása** – Intelligens insights-elemzés konfigurálása az adatbázisokhoz. | Igen | Igen |
| **Elemzéseket továbbíthatja az Azure SQL Analytics szolgáltatásba** – az Azure SQL Analytics figyelési megoldásába továbbíthatja az elemzéseket az Azure SQL Database-hez. | Igen | Igen |
| **Elemzéseket továbbíthat az Event Hubba** – az elemzéseket az Event Hubs-ba továbbíthatja további egyéni integrációkhoz. | Igen | Igen |
| **Elemzéseket továbbítson az Azure Storage ba** – További elemzés és hosszú távú archiválás céljából az Azure Storage-ba továbbíthatja az elemzéseket. | Igen | Igen |

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Az Intelligent Insights-napló exportálásának konfigurálása

Az Intelligens elemzések kimenete elemzés céljából a számos úti cél egyikére streamelhető:

- A Log Analytics-munkaterületre streamelt kimenet az [Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) szolgáltatással használható az Azure Portal felhasználói felületén keresztül imitált információk megtekintéséhez. Ez az integrált Azure-megoldás, és a legjellemzőbb módja az elemzések megtekintésének.
- Az Azure Event Hubs-ba streamelt kimenet egyéni figyelési és riasztási forgatókönyvek fejlesztésére használható
- Az Azure Storage-ba streamelt kimenet egyéni alkalmazásfejlesztéshez használható, például egyéni jelentéskészítés, hosszú távú adatarchiválás stb.

Az Azure SQL Analytics, az Azure Event Hub, az Azure Storage vagy harmadik féltől származó termékek felhasználási szolgáltatásának integrációja először az Intelligens insights-naplózás (az "SQLInsights" napló) engedélyezésével történik az adatbázis diagnosztikai beállítások paneljén, majd az Intelligent Insights naplóadatainak konfigurálása a következő célok egyikébe való streameléshez.

Az Intelligens insights-naplózás engedélyezéséről, valamint a mérőszámok és az erőforrásnapló-adatok fogyasztó termékre való streamelésének konfigurálásáról az [Azure SQL Database metrikáiés diagnosztikai naplózása című](sql-database-metrics-diag-logging.md)témakörben talál további információt.

### <a name="set-up-with-azure-sql-analytics"></a>Beállítás az Azure SQL Analytics szolgáltatással

Az Azure SQL Analytics-megoldás grafikus felhasználói felületet, jelentéskészítési és riasztási lehetőségeket biztosít az adatbázis teljesítményével kapcsolatban az Intelligent Insights erőforrásnapló-adatok használatával.

Az Azure SQL Analytics hozzáadása az Azure Portal irányítópultjához a piactérről, és munkaterület létrehozásához [lásd: Az Azure SQL Analytics konfigurálása](../azure-monitor/insights/azure-sql.md#configuration)

Az Intelligent Insights és az Azure SQL Analytics használatával való használatához konfigurálja az Intelligent Insights naplóadatait az előző lépésben létrehozott Azure SQL Analytics-munkaterületre való streamelésre, olvassa el az [Azure SQL Database metrikáinak és diagnosztikai naplózásának.](sql-database-metrics-diag-logging.md)

A következő példa az Azure SQL Analytics szolgáltatáson keresztül megtekintett Intelligent Insights-ot mutatja be:

![Intelligens insights-jelentés](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Beállítás az Eseményközponttal

Az Intelligent Insights és az Event Hubs használatával való használatához konfigurálja az Intelligent Insights naplóadatait az Event Hubs-ba való streameléshez, olvassa el az [Azure SQL Database metrikáinak és diagnosztikai naplózásának,](sql-database-metrics-diag-logging.md) valamint [az Azure diagnosztikai naplóinak az Event Hubs ba című témakört.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Ha az Event Hubs segítségével egyéni figyelést és riasztást szeretne beállítani, olvassa [el a Mi a teendő a metrikák és diagnosztikai naplók használatával az Event Hubs-ban című témakört.](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs)

### <a name="set-up-with-azure-storage"></a>Beállítás az Azure Storage szolgáltatással

Az Intelligent Insights with Storage használatához konfigurálja az Intelligent Insights naplóadatait a Storage-ba való streameléshez, olvassa el az [Azure SQL Database metrikáinak és diagnosztikai naplózásának](sql-database-metrics-diag-logging.md) és [az Azure Storage-ba való streameléscímű témakört.](sql-database-metrics-diag-logging.md#stream-into-azure-storage)

### <a name="custom-integrations-of-intelligent-insights-log"></a>Az Intelligent Insights-napló egyéni integrációi

Az Intelligent Insights harmadik féltől származó eszközökkel való használatához, illetve egyéni riasztási és figyelési fejlesztésekhez [az Intelligens elemzések adatbázis teljesítménydiagnosztikai naplójának használata című témakörben található.](sql-database-intelligent-insights-use-diagnostics-log.md)

## <a name="detection-metrics"></a>Észlelési mutatók

Az intelligens elemzéseket generáló észlelési modellekhez használt metrikák figyelésen alapulnak:

- Lekérdezés időtartama
- Idő-elállási kérelmek
- Túlzott várakozási idő
- Hiba történt a kérelmekben

A lekérdezés időtartama és az időtúlterhelési kérelmek elsődleges modellekként szolgálnak az adatbázis-munkaterhelés teljesítményével kapcsolatos problémák észlelésében. Azért használják őket, mert közvetlenül mérik, hogy mi történik a munkaterheléssel. A számítási feladatok teljesítményének csökkenésének minden lehetséges esetének észleléséhez a túlzott várakozási idő és a hibamentes kérelmek további modellekként szolgálnak a számítási feladatok teljesítményét befolyásoló problémák jelzésére.

A rendszer automatikusan figyelembe veszi a számítási feladatok és az adatbázislekérdezési kérelmek számának változásait a normál és a nem szokványos adatbázis-teljesítményküszöbértékek dinamikus meghatározásához.

Az összes metrikák együttesen különböző kapcsolatokban egy tudományosan származtatott adatmodell, amely kategorizálja az egyes teljesítményproblémák észlelt. Az intelligens betekintés révén nyújtott információk a következők:

- A észlelt teljesítményprobléma részletei.
- A probléma kiváltó okai nak elemzése.
- Javaslatok a figyelt SQL-adatbázis teljesítményének javítására, ahol lehetséges.

## <a name="query-duration"></a>Lekérdezés időtartama

A lekérdezés időtartamának lebontása modell elemzi az egyes lekérdezések, és észleli a lekérdezés fordításához és végrehajtásához szükséges idő növekedését a teljesítmény alapértékéhez képest.

Ha az SQL Database beépített intelligenciája jelentős növekedést észlel a lekérdezésfordítási vagy lekérdezés-végrehajtási időben, amely befolyásolja a számítási feladatok teljesítményét, ezek a lekérdezések lekérdezési időtartam teljesítményromlási problémákként vannak megjelölve.

Az Intelligent Insights diagnosztikai naplója a lekérdezési kivonatot adja ki a teljesítményben csökkent. A lekérdezési kivonat azt jelzi, hogy a teljesítménycsökkenés a lekérdezés fordításához vagy a végrehajtási idő növeléséhez kapcsolódott-e, ami megnövelte a lekérdezés időtartamának időtartamát.

## <a name="timeout-requests"></a>Idő-elállási kérelmek

Az időtúltöltési kérelmek lebontási modell elemzi az egyes lekérdezéseket, és észleli az időtúlhívások növekedését a lekérdezés végrehajtási szintjén, és a teljes kérelem időtúlhívások az adatbázis szintjén, mint a teljesítmény alapidőszak.

Előfordulhat, hogy a lekérdezések egy része idővel időd, még mielőtt azok elérnék a végrehajtási szakaszban. A megszakított dolgozók és a megküldött kérelmek segítségével az SQL Database beépített intelligenciaméri, és elemzi az adatbázisba elérő lekérdezéseket, függetlenül attól, hogy eljutottak-e a végrehajtási szakaszba vagy sem.

Miután a végrehajtott lekérdezések időtúlszáma vagy a megszakított kérelemben dolgozók száma átlépi a rendszer által kezelt küszöbértéket, a diagnosztikai napló intelligens elemzésekkel van feltöltve.

A létrehozott elemzések tartalmazzák az időalapú kimenő kérelmek számát és az időkimenő lekérdezések számát. A teljesítménycsökkenés jelzése a végrehajtási szakaszban az időtúllépés növekedésével függ össze, vagy a teljes adatbázisszint meg van adva. Ha az időtúlárak növekedése jelentősnek tekinthető az adatbázis teljesítménye szempontjából, ezek a lekérdezések időtúlhasználati teljesítmény-csökkenési problémákként vannak megjelölve.

## <a name="excessive-wait-times"></a>Túlzott várakozási idő

A túlzott várakozási idő modell figyeli az egyes adatbázis-lekérdezések. Szokatlanul magas lekérdezésvárakoztatási statisztikákat észlel, amelyek átlépték a rendszer által kezelt abszolút küszöbértékeket. Az új SQL Server-szolgáltatás, a Query Store Wait Stats (sys.query_store_wait_stats) használatával a következő lekérdezési túlzott várakozási idő mérőszámok figyelhetők meg:

- Erőforráskorlátok elérése
- Rugalmas készlet erőforráskorlátainak elérése
- A dolgozó- vagy munkamenet-szálak túl nagy száma
- Túlzott adatbázis-zárolás
- Memóriaterhelés
- Egyéb várakozási statisztikák

Erőforráskorlátok vagy rugalmas készlet erőforráskorlátok elérése azt jelöli, hogy az előfizetésen vagy a rugalmas készletben rendelkezésre álló erőforrások felhasználása átlépte az abszolút küszöbértékeket. Ezek a statisztikák a számítási feladatok teljesítményének csökkenését jelzik. A dolgozói vagy munkamenet-szálak túl nagy száma olyan állapotot jelöl, amelyben a munkaszálak vagy kezdeményezett munkamenetek száma átlépte az abszolút küszöbértékeket. Ezek a statisztikák a számítási feladatok teljesítményének csökkenését jelzik.

A túlzott adatbázis-zárolás olyan állapotot jelöl, amelyben az adatbázis zárolásainak száma átlépte az abszolút küszöbértékeket. Ez a stat a számítási feladatok teljesítményének csökkenését jelzi. A memórianyomás olyan állapot, amelyben a memóriatámogatást kérő szálak száma átlépte az abszolút küszöbértéket. Ez a stat a számítási feladatok teljesítményének csökkenését jelzi.

Más várakozási statisztikák észlelése olyan állapotot jelez, amelyben a Lekérdezéstároló várakozási statisztikái által mért vegyes metrikák átlépteaz abszolút küszöbértéket. Ezek a statisztikák a számítási feladatok teljesítményének csökkenését jelzik.

A túlzott várakozási idő észlelése után a rendelkezésre álló adatoktól függően az Intelligent Insights diagnosztikai naplók naplója a teljesítményben leromlott és az érintett lekérdezések kihágása, a lekérdezések végrehajtás közben ivárakozását okozó metrikák részletei, valamint a lekérdezések végrehajtás közbeni várakozását okozó metrikák részletei, valamint mért várakozási idő.

## <a name="errored-requests"></a>Hibás kérések

A hibás kérelmek lebontási modell figyeli az egyes lekérdezések, és észleli a lekérdezések száma, amely elhibázott az alapvonali időszakhoz képest. Ez a modell is figyeli a kritikus kivételeket, amelyek átlépték az SQL Database beépített intelligencia által kezelt abszolút küszöbértékeket. A rendszer automatikusan figyelembe veszi az adatbázisba küldött lekérdezési kérelmek számát, és figyelembe veszi a figyelt időszakban bekövetkező munkaterhelés-változásokat.

Ha a hibás kérelmek mért növekedése a kérelmek teljes számához viszonyítva jelentősnek tekinthető a számítási feladatok teljesítménye szempontjából, az érintett lekérdezések hibás kérelmek teljesítménycsökkenési problémákként lesznek megjelölve.

Az Intelligent Insights naplója a hibás kérelmek számát adja ki. Azt jelzi, hogy a teljesítménycsökkenés a hibás kérelmek számának növekedésével vagy a figyelt kritikus kivételi küszöbérték ek és a teljesítménycsökkenés mért idejének átlépése miatt történt.It indicates whether the performance degradation was related to a increase in errored requests or to crossing a monitoredcritical exception threshold and measured time of the performance degradation.

Ha a figyelt kritikus kivételek bármelyike átlépi a rendszer által kezelt abszolút küszöbértékeket, a rendszer intelligens betekintést hoz létre a kritikus kivételrészleteivel.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan figyelheti az [SQL Database-t az SQL Analytics használatával.](../azure-monitor/insights/azure-sql.md)
- Ismerje meg, hogyan hárítható el az [SQL Database teljesítményproblémái az Intelligent Insights segítségével.](sql-database-intelligent-insights-troubleshoot-performance.md)
