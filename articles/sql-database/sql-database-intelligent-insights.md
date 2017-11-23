---
title: "Adatbázis-használat, amelyen intelligens - Azure SQL Database figyelése |} Microsoft Docs"
description: "Intelligens Insights Azure SQL Database beépített funkciói folyamatosan mesterséges eszközintelligencia adatbázis-használat figyelését, valamint őket az eseményeket, amelyek miatt romolhat a teljesítmény észleléséhez használja."
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 823855d88396a14ff7e5428a12d71384cdfe95a1
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="intelligent-insights"></a>Intelligent Insights

Az Azure SQL adatbázis intelligens Insights értesíti Önt arról, mi történik az adatbázis teljesítménye.

Intelligens Insights beépített funkciói folyamatosan mesterséges eszközintelligencia adatbázis-használat figyelését, valamint őket az eseményeket, amelyek miatt romolhat a teljesítmény észleléséhez használja. Ha észlel, a részletes elemzés történik, amely egy intelligens értékelése a problémát a diagnosztika naplót hoz létre. Ez az értékelés áll egy adatbázis teljesítményprobléma kiváltó okának elemzése és, ha lehetséges, teljesítménnyel kapcsolatos fejlesztések javaslatok. 

## <a name="what-can-intelligent-insights-do-for-you"></a>Milyen műveleteket végezhetnek el intelligens Insights meg?

Intelligens Insights egy egyedi képességét az Azure beépített funkciói, amely a következő értéket:

- Proaktív figyelés
- Testreszabott teljesítménybe
- Adatbázis teljesítményének csökkenéséhez korai észlelése
- Alapvető oka észlelt problémák elemzése
- Teljesítmény fokozása javaslatok
- Horizontális felskálázás képességet a több száz akár több ezer adatbázis
- A DevOps erőforrásokhoz és a teljes birtoklási költség pozitív hatása

## <a name="how-does-intelligent-insights-work"></a>Hogyan működik az intelligens Insights?

Intelligens Insights elemzi az SQL-adatbázis teljesítményének összehasonlítva az adatbázis munkaterhelés az elmúlt órában az elmúlt hét nap alapterv áttelepítendő feladatokhoz. Adatbázis munkaterhelés lekérdezések az adatbázis teljesítményét, például a ismételt és a legnagyobb lekérdezések a legjelentősebb találhatóak tevődik össze. Mivel az egyes adatbázisok egyedi struktúra, adatok, használat, és alkalmazás alapján, minden munkaterhelés alapterv, amely akkor jön létre, csak az egyedi egyes példányra. Intelligens elemzések, a munkaterhelés kiindulási független is abszolút működési küszöbértékek figyeli, és túlzott várakozási idő, a kritikus kivételeket és a lekérdezés parameterizations teljesítményt befolyásoló problémákat észlel.

Miután a teljesítmény romlását probléma mesterséges eszközintelligencia használatával több megfigyelt metrikákat a észlel, elemzés történik. Diagnosztika a napló hoz létre az adatbázist, mi történik az intelligens egyet. Intelligens Insights megkönnyíti a megoldás csak az első megjelenésében az adatbázis teljesítményprobléma nyomon. Minden észlelt probléma kulcsban követhető nyomon a teljes életciklusuk kezdeti probléma észlelésének és a teljesítmény fokozása ellenőrzése a befejezését. Frissítések szerepelnek a diagnosztikai naplófájl 15 percenként. 

![Adatbázis teljesítményének elemzése munkafolyamat](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

A metrikák és adatbázis-teljesítménnyel kapcsolatos problémák észlelése használt lekérdezés időtartama, időtúllépés kérelmeket, túlzott várakozási idő és hiba miatt megszakadt kérelmek alapulnak. A metrikák további információkért lásd: a [észlelési metrikák](sql-database-intelligent-insights.md#detection-metrics) szakasz ebben a dokumentumban.

Meghatározott SQL-adatbázis teljesítményének degradations tárolja, amely a diagnosztika naplóra intelligens megadásával, amely a következő tulajdonságok áll:

| Tulajdonság             | Részletek              |
| :------------------- | ------------------- |
| Adatbázis-információ | Amikor egyet az észlelt, például egy erőforrás URI adatbázis metaadatait. |
| Megfigyelt időtartomány | Az észlelt insight időszak kezdő és záró idő. |
| Érintett metrikák | Egyet az generálása okozó metrikák: <ul><li>A lekérdezés időtartam növelése [másodperc].</li><li>Túl sok várakozó [másodperc].</li><li>Időtúllépés miatt megszakadt kérelmek száma a [százalék].</li><li>Munkamenetekről, kimenő kérelmek [százalék].</li></ul>|
| Ütközési értéket | A metrika méri. |
| Érintett lekérdezések és hibakódok | A lekérdezés kivonatoló vagy hibakód. Ezek használhatók az érintett lekérdezésekre könnyen összefüggéseket. Metrikák lekérdezés időtartam növelése, várakozási idő, időtúllépés számát, vagy hibakódok áll rendelkezésre állnak. |
| Észlelések | Az adatbázis azonosítsák az időre, amíg az esemény észlelése. Nincsenek 15 észlelési kombinációját. További információkért lásd: [intelligens, amelyen adatbázis teljesítménnyel kapcsolatos problémák elhárítása](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Kiváltó okának elemzése | Alapvető oka elemzése az emberek számára olvasható formátumban felismert probléma. Néhány insights tartalmazhat a teljesítmény fokozása javaslat, ahol csak lehetséges. |
|||

A diagnosztika naplóban rögzített teljesítményproblémák vannak megjelölve, egy probléma életciklus Háromállapotú egyike: "Active", "Ellenőrzése", és a "Teljes". A teljesítmény után problémát észlelt, és ítélt hosszú azt rendelkezik annyi téglára SQL Database beépített funkciói által, a probléma megjelölt "Aktív". Ha a probléma akkor tekinthető problémák elhárításáról, igazolható, és a probléma állapota "Ellenőrzése". SQL Database beépített funkciói úgy véli, hogy a probléma elhárítva, miután a probléma állapota "Complete" van megjelölt.

## <a name="use-intelligent-insights"></a>Intelligens Insights használata

Intelligens Insights egy intelligens diagnosztika Teljesítménynapló. Integrálható a felhasználás más termékekkel és az egyes alkalmazások ilyen Azure Naplóelemzés, az Azure Event Hubs és az Azure storage, vagy harmadik féltől származó termékek. 

Intelligens Insights együtt az Azure Naplóelemzés általában egy olyan webböngésző, és lehet, hogy az egyik ki az alapoktól használhatná a terméket az beszerzése a legegyszerűbben keresztül insights megtekintésére használt. Intelligens Insights együtt az Azure Event Hubs általában használt egyéni figyelés és riasztás forgatókönyvek beállítása. Intelligens insights együtt az Azure storage általában azoknak a egyéni alkalmazások fejlesztését, ilyen például egyéni jelentések, vagy esetleg archiválási és lekérése.

Integráció más termékekkel Azure Naplóelemzés intelligens Insights, Azure Event hubs Eseményközpontot, az Azure storage vagy külső gyártótól származó termékek felhasználásra első engedélyezése intelligens Insights naplózása (SQLInsights napló), és majd konfigurálásával keresztül történik Intelligens Insights oszthatók ezeket a termékeket közzétett adatok naplózása. Intelligens Insights naplózás engedélyezése és konfigurálása egy fogyasztó termékre közzétett naplóadatokat kapcsolatos további információkért lásd: [Azure SQL Database metrikák és diagnosztikai naplózás](sql-database-metrics-diag-logging.md). 

Az Azure Naplóelemzés intelligens Insights segítségével a rendszer gyakorlati áttekintése és a jellemző használati forgatókönyvei tekintse meg a beágyazott videót:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligens Insights található felderítésének, illetve az SQL Database teljesítménnyel kapcsolatos problémák elhárításakor helyezi. SQL Database teljesítményproblémáinak elhárítása intelligens Insights használatához tekintse meg a [teljesítményproblémák elhárítása az Azure SQL Database intelligens, amelyen](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="set-up-intelligent-insights-with-log-analytics"></a>A Naplóelemzési intelligens Insights beállítása 

Jelentkezzen Analytics megoldás jelentéseket készít, és az intelligens Insights képességeket riasztási diagnosztikai adatok naplózása.

A Naplóelemzési intelligens Insights használatához konfigurálnia a naplóadatok intelligens Insights szolgáltatáshoz adatfolyamként továbbítását, lásd: [Azure SQL Database metrikák és diagnosztikai naplózás](sql-database-metrics-diag-logging.md). 

A következő példa egy intelligens Insights az Azure SQL elemzés mutatja be:

![Intelligens Insights jelentés](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

Miután az intelligens Insights diagnosztikai naplófájl SQL elemzés adatfolyam adatok van konfigurálva, [figyelje az SQL-adatbázis az SQL elemzés](../log-analytics/log-analytics-azure-sql.md).

## <a name="set-up-intelligent-insights-with-event-hubs"></a>Az Event Hubs intelligens Insights beállítása

Az Event Hubs intelligens Insights használatához konfigurálnia intelligens Insights naplóadatokat Event hubs adatfolyamként továbbítását, lásd: [adatfolyam Azure diagnosztikai naplók Event hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).

Használja az Event Hubs a egyéni figyelés és riasztás, lásd: [mit kell tenni a metrikák és diagnosztika bejelentkezik az Event Hubs](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs). 

## <a name="set-up-intelligent-insights-with-storage"></a>Tároló intelligens Insights beállítása

Tároló intelligens Insights használatához konfigurálnia intelligens Insights naplóadatokat Storage adatfolyamként továbbítását, lásd: [az Azure Storage adatfolyam](sql-database-metrics-diag-logging.md#stream-into-storage).

## <a name="custom-integrations-of-intelligent-insights-log"></a>Egyéni integrációkat intelligens Insights napló

A harmadik féltől származó eszközökkel, és az egyéni riasztási és a figyelés fejlesztési intelligens Insights használatához tekintse meg a [használja az intelligens Insights adatbázis teljesítményének diagnosztikai naplófájlok](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Észlelési metrikák

Intelligens elemzések készítése észlelési modellek esetében használt mérőszámok alapján figyelése:

- lekérdezés időtartama
- Kérelem időtúllépése
- Túl sok várakozási idő
- Kimenő kérelmek hiba miatt megszakadt

Az adatbázis munkaterhelés teljesítménnyel kapcsolatos problémák észlelése elsődleges modellel lekérdezési időtartamát és időtúllépés kérelmek használatosak. Azok mire szolgál, mert azok közvetlenül mérését, a munkaterhelés, mi történik. A számítási feladat teljesítményére minden lehetséges esetben észleléséhez teljesítménycsökkenést, túl sok várnia kell, és hiba kérelmet arra használják, kiegészítő modellek, amelyek hatással vannak a számítási feladat teljesítményére vonatkozó problémákra utalnak.

A rendszer automatikusan veszi figyelembe a módosításokat az alkalmazások és a lekérdezési kérelmek száma az adatbázishoz való dinamikusan változásai normál és out-beállítás az adatbázis teljesítményének küszöbértékek meghatározásához.

A metrikák mindegyikét minősülnek együtt egy tudományosan származtatott adatmodell, amely minden észlelt teljesítményprobléma kategorizálja keresztül különböző kapcsolatban. Egy intelligens insight keresztül elérhető adatok tartalmazzák:

* A teljesítménycsökkenés oka észlelt részleteit. 
* Egy észlelte a hibát kiváltó okának elemzése. 
* Javaslatok a figyelt SQL-adatbázis teljesítményének javításával, ahol csak lehetséges.

## <a name="query-duration"></a>lekérdezés időtartama

A lekérdezés időtartama teljesítménycsökkenést modell egyes lekérdezések elemzi, és megnövelve észleli fordításához és a teljesítménybeli alapértékek képest lekérdezés végrehajtása szükséges idő.

SQL Database beépített funkciói jelentősen növelheti a lekérdezés fordításához vagy lekérdezés-végrehajtási idő, amely hatással van a számítási feladat teljesítményére azt észleli, ha ezeket a lekérdezéseket, a lekérdezés időtartama vannak megjelölve teljesítményproblémák teljesítménycsökkenést. 

Az intelligens Insights diagnosztikai naplófájl kiírja a lekérdezési teljesítmény csökkentett teljesítményű lekérdezés kivonatát. A lekérdezés kivonatoló azt jelzi, hogy a teljesítmény romlását lett kapcsolatos lekérdezés fordításához vagy végrehajtási idő száma megemelkedik, ami nagyobb lekérdezés időtartama.

## <a name="timeout-requests"></a>Kérelem időtúllépése

A időtúllépés kérelmek teljesítménycsökkenést modell egyes lekérdezések elemzi, és növekedése észleli a lekérdezés végrehajtása szinten időtúllépések és a teljes kérelem időtúllépése az adatbázis szintjén, a teljesítmény alapterv időszak képest.

A lekérdezések némelyikének előfordulhat, hogy időtúllépés érik a végrehajtási fázis előtt is. Keresztül megszakított munkavállalók kérelmek és az azt jelenti az SQL Database beépített funkciói méri, és elemzi az összes lekérdezés, amely az adatbázis elérte, hogy élvezhetik végrehajtási szintjére, vagy nem. 

Után végrehajtott lekérdezések időtúllépések számát vagy a megszakított kérelem munkavállalók keverve használ a rendszer által felügyelt küszöbértéket, a diagnosztika a napló intelligens, amelyen fel van töltve.

A generált elemzéseket időtúllépés miatt megszakadt kérelmek száma és az időtúllépés lekérdezések tartalmazhat. A teljesítmény romlását feltüntetése kapcsolódó időtúllépés növelje a végrehajtási szakaszban, vagy a teljes adatbázis szintje valósul meg. Időtúllépések növekedése az adatbázis teljesítményének jelentős akkor számít elértnek, amikor ezeket a lekérdezéseket, időtúllépés teljesítménycsökkenést teljesítményproblémák vannak megjelölve. 

## <a name="excessive-wait-times"></a>Túl sok várakozási idő

A túlzott várakozási idő állapotmodellje egyéni adatbázis-lekérdezést. Azt észleli, hogy a rendszer által felügyelt abszolút küszöbértékek túllépése szokatlanul nagy lekérdezés várakozási statisztikák. A lekérdezés túl sok várakozási idő metrikák az új SQL Server szolgáltatás, a lekérdezés tároló Várjon, amíg statisztikák (sys.query_store_wait_stats) használatával figyelhetők:

- Az erőforrás-korlátozások
- A rugalmas készlet az erőforrás-korlátok
- Túl sok munkavégző vagy munkamenet szálak száma
- Túl sok adatbázis zárolása
- Memória
- Más várakozási statisztikák

Az erőforrás-korlátozások vagy a rugalmas készlet erőforrás korlátok jelöl, hogy az előfizetés vagy a rugalmas készlet elérhető erőforrások fogyasztásának elért abszolút küszöbértékeket. Ezek a statisztikák munkaterhelés teljesítménycsökkenés jelzi. Munkavégző vagy munkamenet szálak száma azt jelzi, hogy egy feltétel, amelyben munkaszál vagy kezdeményezett munkamenetek száma átlépte abszolút küszöbértékeket. Ezek a statisztikák munkaterhelés teljesítménycsökkenés jelzi.

Túl sok adatbázis-zárolás jelöli, amelyben a zárolásokat egy adatbázis száma abszolút küszöbértékek elért feltétel. Ez az állapot azt jelzi, hogy egy munkaterhelés teljesítménycsökkenés. Nyomás egy olyan feltétel, amelyben a kért memória szálak száma engedélyezi a memória egy abszolút küszöbérték túllépése. Ez az állapot azt jelzi, hogy egy munkaterhelés teljesítménycsökkenés.

Más várakozási statisztikák észlelési azt jelzi, hogy egy feltétel, amelyen keresztül a lekérdezés tároló Várjon, amíg statisztikák mért vegyes metrikák túllépte egy abszolút küszöbértéket. Ezek a statisztikák munkaterhelés teljesítménycsökkenés jelzi.

Túl sok várakozási idő észlel, attól függően, hogy az elérhető adatok összméretével, miután a intelligens Insights diagnosztikai naplófájlok-e a teljesítmény, a végrehajtási, várjon, amíg azt okozó metrikák részleteit a csökkentett teljesítményt befolyásoló és érintett lekérdezések kimenetének kivonatok és mért várakozási idő.

## <a name="errored-requests"></a>Hiba miatt megszakadt kérelmek

A hiba miatt megszakadt kérelmek teljesítménycsökkenést modell, figyelők egyedi kérdezi le, és megnövelheti észleli, hogy munkamenetekről, kimenő hasonlítva az eredeti időszak lekérdezések száma. Ez a modell is figyeli a kritikus kivételeket, amely felügyeli az SQL Database beépített funkciói abszolút küszöbértékek túllépése. A rendszer automatikusan tekinti a lekérdezési kérelmek száma az adatbázis és a munkaterhelés módosításai fiókokhoz a figyelt időszakban.

Hiba miatt megszakadt kérelmek viszonyítva kérelmek teljes száma a mért növekedése jelentős terhelés teljesítmény akkor számít elértnek, amikor érintett lekérdezések, hiba miatt megszakadt kérelmek teljesítményének csökkenéséhez problémák vannak megjelölve.

Az intelligens Insights napló kimenete hiba miatt megszakadt kérelmek száma. Azt jelzi, hogy a teljesítmény romlását kapcsolatos volt, hiba miatt megszakadt kérelmek növekedése vagy egy figyelt kritikus küszöbérték és a teljesítmény romlását mért idejét meghaladó. 

A figyelt kritikus kivételek cross a abszolút küszöbértékeket a rendszer kezeli, egyet az intelligens jön létre, a kritikus kivétel részletes adatai.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [intelligens, amelyen SQL Database teljesítménnyel kapcsolatos problémák elhárítása](sql-database-intelligent-insights-troubleshoot-performance.md).
* Használja a [intelligens Insights SQL-adatbázis teljesítményének diagnosztikai naplófájl](sql-database-intelligent-insights-use-diagnostics-log.md).
* Megtudhatja, hogyan [figyelje az SQL-adatbázis SQL elemzés](../log-analytics/log-analytics-azure-sql.md).
* Megtudhatja, hogyan [gyűjtése és felhasználása az Azure-erőforrások naplóadatait](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).


