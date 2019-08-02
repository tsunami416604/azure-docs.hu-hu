---
title: Az adatbázis teljesítményének figyelése Intelligent Insights-Azure SQL Databasesal | Microsoft Docs
description: A Azure SQL Database Intelligent Insights a beépített intelligenciával folyamatosan figyeli az adatbázis-használatot mesterséges intelligenciával, és felderíti a gyenge teljesítményt okozó zavaró eseményeket.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: d77557f6d5d6a7bb741b9ff6caa4ea8f76db19c0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567870"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance"></a>Intelligent Insights AI használata az adatbázis teljesítményének figyeléséhez és hibakereséséhez

Azure SQL Database Intelligent Insights segítségével megtudhatja, mi történik a SQL Database és a felügyelt példány adatbázisának teljesítményében.

A Intelligent Insights a beépített intelligenciával folyamatosan figyeli az adatbázis-használatot a mesterséges intelligencián, és felismeri a gyenge teljesítményt okozó zavaró eseményeket. Az észlelést követően a rendszer részletes elemzést végez, amely a probléma intelligens értékelésével létrehoz egy diagnosztikai naplót. Ez az értékelés az adatbázis teljesítményével kapcsolatos probléma okának elemzését, és ahol lehetséges, a teljesítménnyel kapcsolatos javításokra vonatkozó javaslatokat tartalmaz.

## <a name="what-can-intelligent-insights-do-for-you"></a>Mit tehet Intelligent Insights

A Intelligent Insights az Azure beépített intelligenciának egyedülálló funkciója, amely a következő értéket biztosítja:

- Proaktív figyelés
- Testreszabott teljesítmény-felismerés
- Az adatbázis-teljesítmény romlásának korai észlelése
- Az észlelt problémák kiváltó okának elemzése
- Teljesítmény-javító javaslatok
- Több százezer adatbázisra kibővíthető képesség
- Pozitív hatással van a DevOps-erőforrásokra és a teljes tulajdonlási díjakra

## <a name="how-does-intelligent-insights-work"></a>Hogyan működik Intelligent Insights

Intelligent Insights elemzi az adatbázis teljesítményét, ha összehasonlítja az elmúlt órában az elmúlt hét napos alapkonfiguráció számítási feladatait. Az adatbázis-munkaterhelés olyan lekérdezésekből áll, amelyek úgy vannak meghatározva, hogy az adatbázis teljesítményének legjelentősebb részét, például a legtöbbször ismétlődő és legnagyobb lekérdezéseket. Mivel az egyes adatbázisok egyediek a szerkezettől, az adatoktól, a használattól és az alkalmazástól függően, az egyes generált számítási feladatok egyediek és egyediek az egyes példányok esetében. Intelligent Insights a munkaterhelés alapkonfigurációtól függetlenül az abszolút működési küszöbértékeket is figyeli, és észleli a túlzott várakozási idő, a kritikus kivételek és a teljesítményre hatással lehet a lekérdezési parameterizations kapcsolatos problémákat.

Ha a teljesítmény romlása problémát észlelt több megfigyelt metrika alapján mesterséges intelligencia használatával, az elemzés elvégzése történik. A diagnosztikai naplót intelligens betekintéssel generáljuk, hogy mi történik az adatbázissal. A Intelligent Insights segítségével egyszerűen nyomon követheti az adatbázis teljesítményét a megoldás első megjelenése után. A rendszer minden észlelt problémát az életciklusa alapján követ nyomon a kezdeti probléma észlelése és a teljesítmény javításának ellenőrzése után.

![Adatbázis-teljesítmény elemzésének munkafolyamata](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

Az adatbázis-teljesítménnyel kapcsolatos problémák méréséhez és észleléséhez használt mérőszámok lekérdezési időtartamon, időtúllépési kérelmeken, túlzott várakozási időpontokon és hibás kérelmeken alapulnak. A metrikákkal kapcsolatos további információkért tekintse meg [](sql-database-intelligent-insights.md#detection-metrics) a jelen dokumentum észlelési mérőszámok című szakaszát.

Azonosított SQL Database a teljesítmény romlását a diagnosztikai naplóban a következő tulajdonságokkal rendelkező intelligens bejegyzésekből kell rögzíteni:

| Tulajdonság             | Részletek              |
| :------------------- | ------------------- |
| Adatbázis-információk | Metaadatok egy olyan adatbázisról, amelyen a rendszer betekintést észlelt, például egy erőforrás-URI-t. |
| Megfigyelt időtartam tartománya | Az észlelt elemzés időszakának kezdési és befejezési időpontja. |
| Érintett mérőszámok | A betekintést kiváltó mérőszámok: <ul><li>A lekérdezés időtartama növekszik [másodperc].</li><li>Túlzott várakozás [másodperc].</li><li>Időtúllépési kérelmek [százalék].</li><li>Hiba miatti kérelmek [százalék].</li></ul>|
| Hatás értéke | A mért metrika értéke. |
| Érintett lekérdezések és hibakódok | Lekérdezési kivonat vagy hibakód. Ezek az érintett lekérdezések egyszerű összekapcsolására használhatók. A lekérdezés időtartamának növekedését, a várakozási időt, az időtúllépési számokat vagy a hibakódokat tartalmazó mérőszámok vannak megadva. |
| Észlelések | Az esemény során azonosított észlelés az adatbázisban. 15 észlelési minta van. További információ: [az adatbázis teljesítményével kapcsolatos hibák elhárítása Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Alapvető okok elemzése | Az ember által olvasható formátumban azonosított probléma okának elemzése. Egyes bepillantások teljesítmény-javító javaslatot is tartalmazhatnak, ahol lehetséges. |
|||

A Intelligent Insights és a Azure SQL Analytics használatának gyakorlati áttekintéséhez, valamint a tipikus használati forgatókönyvek esetében tekintse meg a beágyazott videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights a SQL Database teljesítménnyel kapcsolatos problémák felderítésében és hibaelhárításában. A Intelligent Insights a SQL Database és a felügyelt példányok adatbázisával kapcsolatos hibák elhárításához tekintse meg [a Azure SQL Database teljesítményével kapcsolatos hibák elhárítása a Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)használatával című témakört.

## <a name="configure-intelligent-insights"></a>Intelligent Insights konfigurálása

A Intelligent Insights kimenete egy intelligens teljesítménnyel kapcsolatos diagnosztikai napló. Ez a napló több módon is felhasználható – az Azure SQL Analytics, az Azure Event Hubs és az Azure Storage szolgáltatásba, vagy egy harmadik féltől származó termékbe való továbbítással.

- A termékkel [Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) használatával megtekintheti az információkat a Azure Portal felhasználói felületén. Ez az integrált Azure-megoldás, amely a legfontosabb módszer az ismeretek megtekintésére.
- A termék használata az Azure Event Hubs az egyéni figyelési és riasztási forgatókönyvek fejlesztéséhez
- A terméket az Azure Storage szolgáltatással használhatja az egyéni alkalmazások fejlesztéséhez, például az egyéni jelentéskészítéshez, a hosszú távú adatarchiváláshoz és így tovább.

A Intelligent Insights integrációja más termékekkel Azure SQL Analytics, az Azure Event hub, az Azure Storage vagy a harmadik féltől származó termékek fogyasztásra történő használatának első engedélyezése Intelligent Insights naplózás (a "SQLInsights" log) a diagnosztika során az adatbázis beállítások panelje, majd a Intelligent Insights naplózási adatnaplóba való továbbítása az egyik termékbe.

További információ a Intelligent Insights naplózásának engedélyezéséről, valamint a naplófájlok egy felhasználható termékre való továbbításának konfigurálásáról: [Azure SQL Database metrikák és diagnosztikai naplózás](sql-database-metrics-diag-logging.md).

### <a name="set-up-with-azure-sql-analytics"></a>Beállítás a Azure SQL Analytics

Azure SQL Analytics a megoldás grafikus felhasználói felületet, jelentéskészítési és riasztási képességeket biztosít az adatbázis teljesítményére vonatkozóan, valamint a Intelligent Insights diagnosztikai napló adatait.

> [!TIP]
> Gyors első lépések: A Intelligent Insights használatával történő használatának legegyszerűbb módja, ha Azure SQL Analytics, amely grafikus felhasználói felületet biztosít az adatbázis teljesítményével kapcsolatos problémákhoz. Vegyen fel Azure SQL Analytics megoldást a piactéren belül, hozzon létre egy munkaterületet ebben a megoldásban, majd minden egyes adatbázishoz, amely számára engedélyezni kívánja a Intelligent Insightsét, konfigurálja az adatbázis diagnosztikai Beállítások paneljén a "SQLInsights Azure SQL Analytics munkaterülete.
>

Az előfeltétel, hogy a piactéren Azure SQL Analytics a Azure Portal irányítópulthoz, és a munkaterület létrehozásához lásd: [Azure SQL Analytics konfigurálása](../azure-monitor/insights/azure-sql.md#configuration)

Ha Intelligent Insightst szeretne használni a Azure SQL Analytics, konfigurálja Intelligent Insights naplózási adatokat az előző lépésben létrehozott Azure SQL Analytics munkaterületre, és tekintse meg a [Azure SQL Database metrikák és diagnosztika naplózása](sql-database-metrics-diag-logging.md)című témakört.

Az alábbi példa egy Azure SQL Analyticson keresztül megtekintett Intelligent Insights mutat be:

![Intelligent Insights jelentés](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Beállítás a Event Hubs

Ha Intelligent Insightst szeretne használni a Event Hubs, konfigurálja Intelligent Insights naplózási adatait, hogy a rendszer továbbítsa a Event Hubsba, lásd: [stream Azure Diagnostics-naplók a Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md).

Ha Event Hubst szeretne használni az egyéni figyelés és a riasztások beállításához, tekintse meg a [Mi a teendő a metrikákkal és a diagnosztikai naplókkal foglalkozó](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs)témakört Event Hubs.

### <a name="set-up-with-azure-storage"></a>Beállítás az Azure Storage-ban

Ha Intelligent Insightst szeretne használni a Storage szolgáltatással, konfigurálja Intelligent Insights naplófájlok adatfolyamként való továbbítását az [Azure Storage-ba](sql-database-metrics-diag-logging.md#stream-into-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Intelligent Insights napló egyéni integrációja

Ha a Intelligent Insightst harmadik féltől származó eszközökkel szeretné használni, vagy egyéni riasztási és figyelési fejlesztésre van használatban, tekintse meg [a Intelligent Insights adatbázis-diagnosztika naplójának használata](sql-database-intelligent-insights-use-diagnostics-log.md)című részt.

## <a name="detection-metrics"></a>Észlelési metrikák

A Intelligent Insightst létrehozó modellek észleléséhez használt metrikák a figyelésen alapulnak:

- Lekérdezés időtartama
- Időtúllépési kérelmek
- Túlzott várakozási idő
- Hiba történt a kérelmeknél

A lekérdezés időtartama és az időtúllépési kérelmek elsődleges modellként használatosak az adatbázis-munkaterhelés teljesítményével kapcsolatos problémák észlelése érdekében. Ezeket azért használják, mert közvetlenül mérik, hogy mi történik a munkaterhelés esetében. A számítási feladatok teljesítményének összes lehetséges esetének észlelése érdekében a túlzott várakozási idő és a hibás kimenő kérelmek további modellként használatosak a munkaterhelés teljesítményét befolyásoló problémák jelzéséhez.

A rendszer automatikusan megtekinti a számítási feladatok változásait, és megváltoztatja az adatbázisra irányuló lekérdezési kérelmek számát, hogy dinamikusan meghatározza a normál és az általános adatbázis-teljesítmény küszöbértékeit.

Az összes mérőszámot egy tudományosan származtatott adatmodellben, amely az észlelt teljesítménnyel kapcsolatos problémákat kategorizálja, különböző kapcsolatokban tekinti át. Az intelligens betekintés által biztosított információk a következők:

- Az észlelt teljesítménnyel kapcsolatos probléma részletei.
- Az észlelt probléma kiváltó okának elemzése.
- Javaslatok a figyelt SQL-adatbázis teljesítményének növeléséhez, ahol lehetséges.

## <a name="query-duration"></a>Lekérdezés időtartama

A lekérdezés időtartamának romlási modellje elemzi az egyes lekérdezéseket, és észleli a lekérdezés fordításához és végrehajtásához szükséges időt a teljesítménnyel kapcsolatos alaptervhez képest.

Ha SQL Database beépített intelligencia jelentős növekedést észlel a lekérdezések fordításában vagy a lekérdezés végrehajtási idején, amely hatással van a munkaterhelés teljesítményére, a lekérdezések lekérdezési időtartamának romlási problémái lesznek megjelölve.

A Intelligent Insights diagnosztikai napló kimenetében a lekérdezés kivonata lecsökkentett teljesítményű. A lekérdezési kivonat azt jelzi, hogy a teljesítmény romlása kapcsolódott-e a lekérdezés fordításához vagy a végrehajtási idő növeléséhez, ami megnövelte a lekérdezés időtartamának időpontját.

## <a name="timeout-requests"></a>Időtúllépési kérelmek

Az időtúllépési kérelmek romlási modellje egyéni lekérdezéseket elemez, és észleli az időtúllépések növekedését a lekérdezés végrehajtási szintjén, valamint a kérelmek összesített időtúllépését az adatbázis szintjén, a teljesítmény-alapidőszakhoz képest.

Előfordulhat, hogy néhány lekérdezés időtúllépést okoz, még mielőtt elérné a végrehajtási szakaszt. A megszakított feldolgozók és a benyújtott kérelmek, SQL Database a beépített intelligenciával kapcsolatos intézkedések és az adatbázishoz tartozó összes olyan lekérdezés elemzése, amely elérte az adatbázist, függetlenül attól, hogy azok a végrehajtási fázisba kerültek-e.

Miután a futtatott lekérdezések időtúllépési száma vagy a megszakított kérelmek száma átlépi a rendszer által felügyelt küszöbértéket, a diagnosztikai naplót intelligens megállapítások töltik fel.

A generált megállapítások az időtúllépési kérelmek számát és az időtúllépési lekérdezések számát tartalmazzák. A teljesítmény romlásának jelzése a végrehajtási fázis időtúllépési növekedésével vagy az adatbázis teljes szintjének megadásával kapcsolatos. Ha az időtúllépések növekedése az adatbázis teljesítményének jelentős része, akkor ezek a lekérdezések időtúllépési teljesítménybeli romlási problémákként vannak megjelölve.

## <a name="excessive-wait-times"></a>Túlzott várakozási idő

A túlzott várakozási idő modell figyeli az egyes adatbázis-lekérdezéseket. Szokatlanul nagy lekérdezések várakozási statisztikáit észleli, amelyek átlépték a rendszer által felügyelt abszolút küszöbértékeket. Az új SQL Server funkció, a lekérdezési tároló várakozási statisztikái (sys. query_store_wait_stats) használatával megfigyelhető a következő lekérdezés túlzott várakozási idejének mérőszámai:

- Erőforrás-korlátok elérése
- Rugalmas készlet erőforrás-korlátainak elérése
- A munkavégző vagy a munkamenet szála túl sok
- Túlzott adatbázis-zárolás
- Memória nyomása
- Egyéb várakozási statisztikák

Az erőforrás-korlátok vagy a rugalmas készlet erőforrás-korlátainak elérése azt jelzi, hogy az előfizetésben vagy a rugalmas készletben elérhető erőforrások felhasználása túllépte az abszolút küszöbértékeket. Ezek a statisztikák a munkaterhelés teljesítményének csökkenését jelzik. A munkavégző vagy a munkamenet-szálak túlzott száma azt jelzi, hogy a feldolgozói szálak vagy munkamenetek száma az abszolút küszöbértékeket eredményezte. Ezek a statisztikák a munkaterhelés teljesítményének csökkenését jelzik.

A túlzott adatbázis-zárolás olyan feltételt jelöl, amelyben az adatbázis Zárolások száma abszolút küszöbértéket eredményezett. Ez a stat a munkaterhelés teljesítményének csökkenését jelzi. A memória-nyomás olyan feltétel, amelyben a memóriát kérő szálak száma abszolút küszöbértéket eredményezett. Ez a stat a munkaterhelés teljesítményének csökkenését jelzi.

Az egyéb várakozási statisztikák észlelése azt a feltételt jelzi, hogy a lekérdezési tároló várakozási statisztikájában mért különféle mérőszámok abszolút küszöbértéket mutatnak. Ezek a statisztikák a munkaterhelés teljesítményének csökkenését jelzik.

Ha a rendszer túlzott várakozási időt észlel, a rendelkezésre álló adatoktól függően a Intelligent Insights diagnosztikai napló kimenete a teljesítményre gyakorolt hatásnak és az érintett lekérdezéseknek az eredményét eredményezi mért várakozási idő.

## <a name="errored-requests"></a>Hibás kérelmek

A hibás kérelmek romlási modellje figyeli az egyes lekérdezéseket, és az alapidőszakhoz képest hibát okozó lekérdezések számának növekedését észleli. Ez a modell azokat a kritikus kivételeket is figyeli, amelyek SQL Database beépített intelligencia által kezelt abszolút küszöbértékeket. A rendszer automatikusan figyelembe veszi az adatbázisnak küldött lekérdezési kérések számát, valamint a figyelt időszakban felmerülő munkaterhelés-változásokhoz tartozó fiókokat.

Ha a hibás kérelmeknek a kérelmek teljes számához viszonyított megváltozása a számítási teljesítmény szempontjából jelentősnek számít, az érintett lekérdezések hibát jeleznek a teljesítmény romlásával kapcsolatban.

A Intelligent Insights napló a hibás kérelmek számát adja eredményül. Ez azt jelzi, hogy a teljesítmény romlása a hibás kérelmek növekedésével vagy a figyelt kritikus kivételek küszöbértékének és a teljesítmény romlásának mért idejével kapcsolatos-e.

Ha a figyelt kritikus kivételek bármelyike átlépi a rendszer által kezelt abszolút küszöbértékeket, akkor a kritikus kivétel részleteivel intelligens elemzés jön létre.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan lehet elhárítani a Intelligent Insightsokkal kapcsolatos [teljesítményproblémák SQL Database](sql-database-intelligent-insights-troubleshoot-performance.md).
- Használja a [Intelligent Insights SQL Database Performance Diagnostics](sql-database-intelligent-insights-use-diagnostics-log.md)-naplót.
- Megtudhatja, hogyan [figyelheti SQL Database az SQL Analytics használatával](../azure-monitor/insights/azure-sql.md).
- Ismerje meg, hogyan [gyűjthet és használhat adatokat az Azure-erőforrásokból](../azure-monitor/platform/diagnostic-logs-overview.md).
