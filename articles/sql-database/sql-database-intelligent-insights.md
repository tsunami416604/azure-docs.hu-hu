---
title: Intelligent Insights – Azure SQL Database-adatbázis teljesítményének figyelése |} A Microsoft Docs
description: Intelligent Insights az Azure SQL Database beépített intelligenciával folyamatosan mesterséges intelligencia révén az adatbázis-használat figyeléséhez, és észleli azokat a káros eseményeket, amelyek gyenge teljesítményhez vezethetnek használ.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: e87cd3742a5dcd52c3a447bb66ea9089c18d5aee
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214681"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance"></a>Mesterséges Intelligencia használatával monitorozásának és hibaelhárításának intelligens elemzési adatbázis-teljesítmény

Az Azure SQL Database Intelligent Insights lehetővé teszi, hogy mi történik az SQL Database és a felügyelt példány az adatbázis teljesítményét.

Intelligent Insights használ beépített intelligenciával folyamatosan mesterséges intelligencia révén az adatbázis-használat figyeléséhez, és észleli azokat a káros eseményeket, amelyek gyenge teljesítményhez vezethetnek. Ha azt észleli, részletes elemzés történik, amely az intelligens értékelése a probléma egy diagnosztikai naplót hoz létre. Az értékelés a kiváltó okok elemzése az adatbázis teljesítményét probléma áll, és ahol lehetséges, javaslatokat, a teljesítmény.

## <a name="what-can-intelligent-insights-do-for-you"></a>Intelligent Insights Teendők az Ön számára

Intelligent Insights egy egyedi képesség az Azure beépített intelligenciával, amely a következő értéket kínálja:

- Proaktív figyelés
- Testre szabott teljesítmény elemzéseihez
- Adatbázis-teljesítmény romlását korai észlelése
- Alapvető okok problémák észlelhetők
- Javaslatok a teljesítmény fokozása
- Horizontális felskálázás funkció a több százezer adatbázis
- Pozitív hatással lehet a DevOps-erőforrások és a teljes birtoklási költség

## <a name="how-does-intelligent-insights-work"></a>Hogyan működik az Intelligent Insights

Intelligent Insights összehasonlítja az adatbázis feladata az elmúlt hét nap alapkonfiguráció számítási feladatok az elmúlt órában a elemzi az adatbázis teljesítményét. Adatbázis-tevékenységprofil lekérdezések az adatbázis teljesítményét, például: ismétlődő és a legnagyobb lekérdezések a legjelentősebb minősül tevődik össze. Mivel minden adatbázis egyedi struktúra, adatok, használati, és alkalmazás alapján, minden egyes számítási feladatok alapterv, amely akkor jön létre, csak az egyedi egy egyedi példányára. Intelligent Insights, a számítási feladatok alapkonfiguráció független is abszolút működési küszöbérték figyeli, és észleli a hibákat túlzott várakoznia, kritikus kivételeket és lekérdezési parameterizations teljesítményt befolyásoló problémák.

Miután egy teljesítményprobléma teljesítménycsökkenés több megfigyelt metrikák a mesterséges intelligencia használatával észlel, elemzési történik. Az intelligens adatforgalmáról mi történik az adatbázis egy diagnosztikai napló jön létre. Intelligent Insights megkönnyíti az adatbázis teljesítményét a probléma megoldásáig az első megjelenés nyomon. Minden egyes azt észlelte, hogy probléma van és nyomon követni a életciklus kezdeti probléma észlelése és a teljesítmény fokozása ellenőrzése annak befejezését. Frissítések a diagnosztikai naplóban találhatók 15 percenként.

![Adatbázis teljesítménye az árelemzési munkafolyamat](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

A metrikák mérni, és az adatbázis teljesítménybeli problémák észleléséhez használt lekérdezés időtartama, időkorlát kérelmek, túlzott mértékű várakozási időt és hibát eredményező kérések alapulnak. A metrikák további információkért lásd: a [észlelési metrikák](sql-database-intelligent-insights.md#detection-metrics) szakasz ebben a dokumentumban.

Azonosítja a teljesítmény romlását rögzíti a diagnosztikai naplóban intelligens bejegyzéseket, amelyek a következő tulajdonságok állnak az SQL Database:

| Tulajdonság             | Részletek              |
| :------------------- | ------------------- |
| adatbázis-információ | Egy adatbázis, amelyre egy elemzést észlelt, például egy erőforrás-URI metaadatait. |
| Megfigyelt időtartomány | Az észlelt insight időszak kezdési és befejezési idő. |
| Érintett metrika | Létrejön egy elemzést okozó metrikák: <ul><li>A lekérdezés időtartamának növelése [másodperc].</li><li>Túl sok várakozó [másodperc].</li><li>Lejárt kérések [százalék].</li><li>Hibát eredményező kimenő kérelmek [százalék].</li></ul>|
| Hatás érték | Metrika értékének mérni. |
| Érintett lekérdezések és hibakódok | A lekérdezés kivonata vagy hibakód. Ezek a könnyen korrelációját, ha az érintett lekérdezések használhatók. Lekérdezés időtartamának növelése, várakozási idő, időkorlát számát, vagy hibakódok álló metrikákat. |
| Észlelések | Az észlelési azonosítani az adatbázist egy eseményt az alatt. Nincsenek 15 észlelési mintákat. További információkért lásd: [adatbázis teljesítményproblémák megoldásában az Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Alapvető okok elemzése | Alapvető okok az emberek számára olvasható formátumban azonosított probléma. Elkészítette az elemzéseket a teljesítmény fokozása javaslat tartalmazhatja, ahol csak lehetséges. |
|||

A gyakorlati áttekintése az Azure SQL Analytics Intelligent Insights használatával és a jellemző használati forgatókönyvei: a beágyazott videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights a szabadban, ragyogó felderítése és az SQL Database teljesítménnyel kapcsolatos problémák elhárítása. Az SQL Database és a felügyelt példány database teljesítménnyel kapcsolatos problémáinak elhárítása Intelligent Insights használatához lásd: [teljesítményproblémák elhárítása Azure SQL Database intelligens elemzésekkel](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="configure-intelligent-insights"></a>Intelligent Insights konfigurálása

Az Intelligent Insights kimenete egy intelligens diagnosztika teljesítménynaplóban. Ez a napló - streameli az Azure SQL Analytics az Azure Event Hubs és az Azure storage segítségével többféle módon használhatók fel vagy harmadik féltől származó terméket.

- A termék használata [Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) elemzések megtekintése az Azure portal felhasználói felületén keresztül. Ez egy, az integrált Azure-megoldás, és a leggyakoribb módja elemzések megtekintése.
- A termék használatához az Azure Event Hubs egyéni monitorozási és riasztási forgatókönyvek fejlesztéséhez
- A termék használatához és az Azure storage az egyéni alkalmazásfejlesztéshez, ilyen például egyéni adatokról szóló jelentéseket, hosszú távú archiválás és így tovább.

Intelligent Insights más termékekkel Azure SQL Analytics, Azure Event Hub, az Azure storage vagy harmadik féltől származó termékek felhasználásra integrációja első engedélyezése Intelligent Insights naplózás (a "SQLInsights" napló) található a diagnosztika keresztül történik egy adatbázist, és az Intelligent Insights ezután konfigurálása (beállítások) panelén ezeket a termékeket egy közzétett adatok naplózása.

Intelligent Insights naplózás engedélyezése és konfigurálása a fogyasztó termékhez is streamelhetők naplóadatok kapcsolatos további információkért lásd: [Azure SQL Database-metrikák és diagnosztikai célú naplózásának](sql-database-metrics-diag-logging.md).

### <a name="set-up-with-azure-sql-analytics"></a>Az Azure SQL Analytics beállítása

Az Azure SQL Analytics megoldás biztosítja a grafikus felhasználói felület, jelentéskészítési és riasztási funkciókat az adatbázis teljesítményét, és az Intelligent Insights diagnosztikai naplója adatait.

> [!TIP]
> Gyors bevezetés: ki az alapoktól való használatakor az Intelligent Insights beszerzése a legegyszerűbb módja az, hogy a vele együtt az Azure SQL Analytics, amely adatbázis teljesítményproblémák grafikus felhasználói felületet biztosít. A marketplace-ről az Azure SQL Analytics megoldás hozzáadása, hozzon létre egy munkaterületet a megoldás belül, és az Intelligent Insights engedélyezni kívánt minden egyes adatbázishoz, adja meg a diagnosztikai beállítások paneljén látható az adatbázis "SQLInsights" bejelentkezés adatfolyamként a az Azure SQL Analytics munkaterületén.
>

Üzem előtti követelmény, hogy rendelkezik Azure SQL Analytics hozzáadott a marketplace-ről az Azure portal Irányítópultjára, és hozzon létre egy munkaterületet, lásd: [konfigurálása az Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md#configuration)

Az Azure SQL Analytics Intelligent Insights használatához konfigurálja az Intelligent Insights naplóadatokat az előző lépésben létrehozott Azure SQL Analytics-munkaterületnek is streamelhetők lásd [Azure SQL Database metrikák és diagnosztikai naplózás](sql-database-metrics-diag-logging.md).

Az alábbi példa bemutatja, hogy az Azure SQL Analytics az Intelligent Insights tekinthetők meg:

![Intelligent Insights jelentés](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Állítsa be az Event hubs szolgáltatással

Az Event Hubs Intelligent Insights használatához konfigurálja az Intelligent Insights naplóadatok streamelés az Event hubs szolgáltatásban, lásd: [Stream Azure diagnostics logs szolgáltatásba és az Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).

Az Event Hubs segítségével egyéni monitorozási és riasztási beállítása, lásd: [mit kell tenni a metrikák és diagnosztikai naplók az Event Hubs](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Állítsa be az Azure Storage

Intelligent Insights használata a Storage, konfigurálja az Intelligent Insights naplóadatok Storage streamként, lásd: [Azure Storage-bA Stream](sql-database-metrics-diag-logging.md#stream-into-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Egyéni integrációkat intelligens elemzési napló

Harmadik féltől származó eszközökkel, és így egyéni riasztási és fejlődés figyelése Intelligent Insights használatához lásd: [használja az adatbázis teljesítményét Intelligent Insights diagnosztikai naplója](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Észlelési metrikák

Észlelési modellek, amelyek létrehozzák az Intelligent Insights használt mérőszámok alapján figyelése:

- Lekérdezés időtartama
- Kérelem időtúllépése
- Túl sok várakozási idő
- Hibát eredményező kérések ki

Lekérdezés időtartama és időtúllépése kérelmek használják elsődleges modelleket a problémák az adatbázis számítási feladatok teljesítményére. Használhatók, mert ezek közvetlenül mérik, hogy mi történik, a számítási feladatok. Észleli a számítási feladatok teljesítményére, minden lehetséges esetben teljesítményromlását tapasztalja, túlzott mértékű várakozási időt, és kibővített hibát eredményező kérések jelzi a problémákat, amelyek a számítási feladatok teljesítményére hatással, további modellek szolgálnak.

A rendszer automatikusan tekinti a számítási feladatok módosításait, és a lekérdezési kérelmek száma az adatbázishoz való dinamikusan változásai döntse el, normál és out-egyaránt az adatbázis teljesítmény-küszöbértékeket.

A metrikák mindegyikét együttese, amely kategorizálja a minden egyes teljesítménybeli problémát észlelt tudományosan származtatott adatok modellel különböző kapcsolatban. Egy intelligens elemzést keresztül adatokat tartalmazza:

- Részletes információk a teljesítménybeli problémát észlelt.
- A kiváltó okok elemzése az észlelt problémát.
- Javaslatokat tesz a megfigyelt SQL-adatbázis teljesítményének növelése érdekében ahol csak lehetséges.

## <a name="query-duration"></a>Lekérdezés időtartama

A lekérdezési időtartam teljesítménycsökkenése modell egyes lekérdezések elemzi, és észleli a növekedés fordításához és a egy lekérdezést a teljesítmény alapkonfigurációval összevetett szükséges időt.

Ha az SQL Database beépített intelligenciával jelentősen növelheti a lekérdezés fordításához vagy lekérdezés végrehajtási idő, amely hatással van a számítási feladatok teljesítményére észlel, ezeket a lekérdezéseket, a lekérdezés időtartama vannak-e megjelölve teljesítménycsökkenési hibák.

Az intelligens elemzési diagnosztikai naplót jelenít meg a lekérdezés romlik a teljesítmény lekérdezés kivonata. A lekérdezés kivonata azt jelzi, hogy a teljesítményromlást volt kapcsolatos lekérdezés fordításához vagy végrehajtási idő növekedése, ami nagyobb lekérdezési időtartamát.

## <a name="timeout-requests"></a>Kérelem időtúllépése

Az időtúllépési kérelmek teljesítménycsökkenés modell elemzi az egyes lekérdezések, és növelése észleli a lekérdezés végrehajtási szintjén időtúllépések és az általános kérés időtúllépések, az adatbázis szintjén, a teljesítmény alapkonfiguráció időszak képest.

A lekérdezések némelyikének előfordulhat, hogy időtúllépés elérnék a végrehajtási fázis előtt is. Az azt jelenti, hogy a megszakított munkavállalók és a kérelmek az SQL Database beépített intelligenciával méri és elemzi az összes olyan lekérdezést, amely az adatbázis elérte a készülékeiken a végrehajtási fázisba, vagy nincs-e.

Után végrehajtott lekérdezések időtúllépések száma vagy a megszakított kérelem feldolgozók száma átlép a rendszer által felügyelt küszöbértéket, intelligens elemzési diagnosztikai naplót megjelenik.

Az insights jön létre a lejárt kérések száma és időkorlátot lekérdezések száma tartalmaznak. Arra utalhat, hogy a teljesítményromlást kapcsolódó időtúllépési növelését a végrehajtási szakaszában, vagy az általános adatbázis-szint biztosítja. A növekedés időtúllépéseket sikertelennek az adatbázis teljesítményének jelentős, ha ezeket a lekérdezéseket, időkorlát teljesítménycsökkenési hibák vannak megjelölve.

## <a name="excessive-wait-times"></a>Túl sok várakozási időt

A túlzott várakozási idő modell figyeli az egyes adatbázis-lekérdezések. Azt észleli, amelyek áthaladnak a rendszer által felügyelt abszolút küszöbértékek szokatlanul magas lekérdezés várakozási statisztikái. A következő lekérdezés túl sok várakozni metrikák jelennek meg az új SQL Server szolgáltatást, a Query Store várakozási statisztikái (sys.query_store_wait_stats) használatával:

- Ért el erőforráskorlátok
- Ért el a rugalmas készlet erőforráskorlátok
- Túl sok dolgozó vagy munkamenet szálak
- Túl sok adatbázis zárolása
- Rendelkezésre álló memória mennyisége
- Más várakozási statisztikái

Ért el erőforráskorlátok vagy a rugalmas készlet erőforráskorlátok jelöl, hogy egy adott előfizetés vagy a rugalmas készlet rendelkezésre álló erőforrások fogyasztásának átlépni abszolút küszöbértékeket. Statisztikánkat a számítási feladatok teljesítménycsökkenés jelzik. Feldolgozó vagy munkamenet szálak száma azt jelzi, hogy egy feltételt, amelyben a munkaszálak vagy által kezdeményezett munkamenetek száma túllépte a abszolút küszöbértékeket. Statisztikánkat a számítási feladatok teljesítménycsökkenés jelzik.

Túl sok adatbázis-zárolást azt jelzi, hogy egy feltételt, amelyben a zárolások az adatbázisok száma elért abszolút küszöbértékeket. Ez az állapot azt jelzi, egy számítási feladat teljesítménycsökkenés. Egy feltétel, a memória kérő szálak száma biztosít, amelyben nyomás memóriát túllépte az egy abszolút küszöbértéket. Ez az állapot azt jelzi, egy számítási feladat teljesítménycsökkenés.

Más várakozási statisztikái észlelési azt jelzi, hogy egy feltételt, amelyben a Query Store várakozási statisztikái keresztül mért vegyes metrikák túllépte az abszolút küszöbértéket. Statisztikánkat a számítási feladatok teljesítménycsökkenés jelzik.

Miután túlzott várakozási időt észlelése esetén érhető el, az adatoktól függően a Intelligent Insights diagnosztikai jelentkezzen kimenetek kivonatok romlik a teljesítmény, a lekérdezések Várakozás, végrehajtás okozó metrikák részleteit hatású és az érintett lekérdezések és mért várakozási idő.

## <a name="errored-requests"></a>Hibát eredményező kérések

A hibát eredményező kérések teljesítménycsökkenés modell, figyelők egyéni lekérdezések és növelését észleli, hogy ki a hibát eredményező képest az alapkonfiguráció időszak lekérdezések száma. Ez a modell is figyeli a kritikus kivételek, amelyek áthaladnak a beépített intelligenciával az SQL Database által kezelt abszolút küszöbértékeket. A rendszer automatikusan tekinti a figyelt időszakban az adatbázis és a fiókok számára bármilyen számítási feladat változását követő végzett lekérdezésekre vonatkozó kérelmek száma.

A hibát eredményező kérések kérelmek teljes száma képest mért növekedése a számítási feladatok teljesítményére is jelentős sikertelennek, amikor a rendszer megjelölt érintett lekérdezések hibát eredményező kérések teljesítménycsökkenési hibák.

Az Intelligent Insights napló jelenít meg a hibát eredményező kérések száma. Azt jelzi, hogy a teljesítményromlást volt kapcsolatos-e hibát eredményező kérések növekedését, vagy egy figyelt kritikus küszöbérték és a teljesítmény romlását mért időt határainak átlépése.

A figyelt kritikus kivételek adatbázisközi a abszolút küszöbértékeket a rendszer kezeli, a kritikus kivétel részletei jön létre egy intelligens elemzés.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [intelligens elemzésekkel SQL Database teljesítménnyel kapcsolatos problémáinak elhárítása](sql-database-intelligent-insights-troubleshoot-performance.md).
- Használja a [Intelligent Insights az SQL Database teljesítményét diagnosztikai napló](sql-database-intelligent-insights-use-diagnostics-log.md).
- Ismerje meg, hogyan [figyeli az SQL Database használatával az SQL Analytics](../log-analytics/log-analytics-azure-sql.md).
- Ismerje meg, hogyan [gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
