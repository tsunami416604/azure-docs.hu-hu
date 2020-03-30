---
title: Lekérdezések figyelése
titleSuffix: Azure Cognitive Search
description: Lekérdezési metrikák figyelése a teljesítmény és az átviteli teljesítmény. A diagnosztikai naplókban lekérdezési karakterlánc-bemenetek gyűjtése és elemzése.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: a3a313ef9cd74ba901f5a6a2d82a18e3c21145dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462521"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Lekérdezéskérések figyelése az Azure Cognitive Search szolgáltatásban

Ez a cikk bemutatja, hogyan mérhető a lekérdezési teljesítmény és a kötet metrikák és diagnosztikai naplózás használatával. Azt is elmagyarázza, hogyan kell gyűjteni a lekérdezésekben használt bemeneti kifejezéseket - a szükséges információkat, amikor fel kell mérnie a keresési korpusz hasznosságát és hatékonyságát.

A metrikákba betápláló előzményadatok 30 napig megőrződnek. Hosszabb megőrzési, vagy a működési adatok és a lekérdezési karakterláncok jelentéséhez győződjön meg arról, hogy engedélyezze a [diagnosztikai beállítást,](search-monitor-logs.md) amely megadja a tárolási lehetőséget a naplózott események és metrikák megőrzéséhez.

Az adatmérés integritását maximalizálni a következő feltételeket:

+ Számlázható szolgáltatás használata (az alapszintű vagy a standard szinten létrehozott szolgáltatás). Az ingyenes szolgáltatást több előfizető osztja meg, ami bizonyos mértékű volatilitást vezet be, mivel a terhelések eltolódnak.

+ Ha lehetséges, használjon egyetlen replika és partíció, zárt és elszigetelt környezet létrehozásához. Ha több replikát használ, a lekérdezési metriák átlaga több csomópontközött, ami csökkentheti az eredmények pontosságát. Hasonlóképpen több partíció azt jelenti, hogy az adatok vannak osztva, azzal a potenciállal, hogy egyes partíciók különböző adatokat, ha indexelés is folyamatban van. A lekérdezési teljesítmény finomhangolásakor egyetlen csomópont és partíció stabilabb környezetet biztosít a teszteléshez.

> [!Tip]
> További ügyféloldali kód és az Application Insights segítségével is rögzítheti az átkattintási adatokat, hogy mélyebb betekintést nyerjen abba, hogy mi vonzza az alkalmazás felhasználóinak érdeklődését. További információt a [Keresés forgalomelemzés című témakörben talál.](search-traffic-analytics.md)

## <a name="query-volume-qps"></a>Lekérdezési kötet (QPS)

A kötetet **a másodpercenkénti keresési lekérdezések** (QPS) alapján mérik, amely egy beépített metrika, amely egy egyperces időszakon belül végrehajtott lekérdezések átlagos, darabszám, minimum vagy maximális értékként jelenthető. A mérőszámok egyperces intervallumai (TimeGrain = "PT1M") a rendszeren belül vannak rögzítve.

Gyakori, hogy a lekérdezések ezredmásodpercben futnak, így csak a másodpercként mérhető lekérdezések jelennek meg a metrikákban.

| Aggregáció típusa | Leírás |
|------------------|-------------|
| Átlag | Az átlagos másodpercek száma egy percen belül, amely alatt a lekérdezés végrehajtása történt.|
| Darabszám | Az egyperces időközön belül a naplóba kibocsátott metrikák száma. |
| Maximum | Az egy perc alatt regisztrált keresési lekérdezések másodpercenkénti száma. |
| Minimális | Az egy perc alatt regisztrált keresési lekérdezések másodpercenkénti száma.  |
| Összeg | A percben végrehajtott összes lekérdezés összege.  |

Egy percen belül például előfordulhat, hogy egy ilyen minta: egy másodperc a nagy terhelés, amely a maximális SearchQueriesPerSecond, majd 58 másodperc átlagos terhelés, és végül egy másodperc csak egy lekérdezés, amely a minimális.

Egy másik példa: ha egy csomópont 100 metrikát bocsát ki, ahol az egyes metrikák értéke 40, akkor a "Darab" értéke 100, a "Sum" értéke 400, az "Átlag" 40, a "Max" pedig 40.

## <a name="query-performance"></a>Lekérdezési teljesítmény

Az egész szolgáltatásra kiterjedő lekérdezési teljesítményt a keresési késés (a lekérdezés befejezéséhez szükséges mennyi ideig tart) és az erőforrás-versengés eredményeként eldobott szabályozott lekérdezések mérhető.

### <a name="search-latency"></a>Keresés késése

| Aggregáció típusa | Késés | 
|------------------|---------|
| Átlag | A lekérdezés átlagos időtartama ezredmásodpercben. | 
| Darabszám | Az egyperces időközön belül a naplóba kibocsátott metrikák száma. |
| Maximum | A leghosszabb ideig futó lekérdezés a mintában. | 
| Minimális | A legrövidebb futó lekérdezés a mintában.  | 
| Összesen | A mintában lévő összes lekérdezés teljes végrehajtási ideje, amely az időtartamon belül (egy perc) hajt végre.  |

Vegye figyelembe a következő példát a **keresési késés** metrikák: 86 lekérdezések mintavételezett, átlagos időtartama 23,26 ezredmásodperc. A legalább 0 azt jelzi, hogy néhány lekérdezés elmaradt. A leghosszabb ideig futó lekérdezés 1000 ezredmásodpercet vett igénybe. A teljes végrehajtási idő 2 másodperc volt.

![Késés összesítései](./media/search-monitor-usage/metrics-latency.png "Késés összesítései")

### <a name="throttled-queries"></a>Szabályozott lekérdezések

A szabályozott lekérdezések olyan lekérdezésekre hivatkoznak, amelyek a folyamat helyett eldobva vannak. A legtöbb esetben a szabályozás a szolgáltatás futtatásának normális része.  Ez nem feltétlenül azt jelzi, hogy valami baj van.

Szabályozás akkor történik, ha a kérelmek száma jelenleg feldolgozott meghaladja a rendelkezésre álló erőforrásokat. Előfordulhat, hogy a szabályozott kérelmek növekedése, ha egy replika kikerülése vagy indexelés közben. A lekérdezési és az indexelési kérelmeket is ugyanazok az erőforrások kezelik.

A szolgáltatás határozza meg, hogy az erőforrás-felhasználás alapján el kell-e dobni a kérelmeket. A memória, a processzor és a lemez I/o-n felhasznált erőforrások százalékos aránya egy adott időszakra átlagolt. Ha ez a százalék túllépegy küszöbértéket, az indexhez érkező összes kérelem szabályozása a kérelmek mennyiségének csökkentéséig. 

Az ügyféltől függően a szabályozott kérelem a következő módokon jelezhető:

+ A szolgáltatás "Túl sok kérést küld" hibaüzenetet ad vissza. Próbálkozzon újra később.” 
+ Egy szolgáltatás egy 503-as hibakódot ad vissza, amely azt jelzi, hogy a szolgáltatás jelenleg nem érhető el. 
+ Ha a portált (például a Search Explorert) használja, a lekérdezés csendben megszakad, és újra a Keresés gombra kell kattintania.

A szabályozott lekérdezések megerősítéséhez használja **a szabályozott keresési lekérdezések** metrikáját. A portálon lévő metrikákat is megismerheti, vagy létrehozhat egy riasztási metrikát az ebben a cikkben leírtak szerint. A mintavételi időközön belül eldobott lekérdezések esetén használja az *Összesen* a nem végrehajtott lekérdezések százalékos arányát.

| Aggregáció típusa | Throttling |
|------------------|-----------|
| Átlag | Az intervallumon belül eldobott lekérdezések százalékos aránya. |
| Darabszám | Az egyperces időközön belül a naplóba kibocsátott metrikák száma. |
| Maximum | Az intervallumon belül eldobott lekérdezések százalékos aránya.|
| Minimális | Az intervallumon belül eldobott lekérdezések százalékos aránya. |
| Összesen | Az intervallumon belül eldobott lekérdezések százalékos aránya. |

A **szabályozott keresési lekérdezések százaléka,** minimális, maximális, átlagos és összes, mind ugyanazt az értéket: a százalékos keresési lekérdezések, amelyek szabályozták, a keresési lekérdezések teljes száma egy perc alatt.

A következő képernyőképen az első szám a naplóba küldött mérőszámok száma (vagy a metrikák száma). További összesítések, amelyek megjelennek a tetején, vagy ha az egérmutatót a metrika felett, tartalmazza az átlagos, maximális és összesen. Ebben a példában egyetlen kérés sem esett el.

![Szabályozott összesítések](./media/search-monitor-usage/metrics-throttle.png "Szabályozott összesítések")

## <a name="explore-metrics-in-the-portal"></a>Tárj fel mutatókat a portálon

Az aktuális számok gyors áttekintéséhez a szolgáltatás áttekintése lap **Figyelés** lapján három metrika látható (**Keresési késés**, **Keresési lekérdezések másodpercenként (keresési egységenként)**, **szabályozott keresési lekérdezések százaléka**) az órákban, napokban és hetekben mért rögzített időközönként, az összesítéstípus módosításának lehetőségével.

Mélyebb feltáráshoz nyissa meg a metrikák kezelőjét a **Figyelés** menüből, hogy rétegezhesse, nagyíthassa és vizualizálhassa az adatokat a trendek vagy anomáliák feltárásához. A metrikakezelőről a [metrikadiagram létrehozásáról szóló oktatóanyag](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-metrics-explorer)kitöltésével tudhat meg többet.

1. A Figyelés csoportban **válassza a Metrikák** lehetőséget a metrikakezelő megnyitásához a keresési szolgáltatás hatókörkészletével.

1. A Metrika csoportban válasszon egyet a legördülő listából, és tekintse át az előnyben részesített típus elérhető összesítéseinek listáját. Az összesítés határozza meg, hogy az összegyűjtött értékek hogyan mintavételezése az egyes időintervallumokban.

   ![Metrikakezelő a QPS-metrika számára](./media/search-monitor-usage/metrics-explorer-qps.png "Metrikakezelő a QPS-metrika számára")

1. A jobb felső sarokban állítsa be az időintervallumot.

1. Válasszon egy vizualizációt. Az alapértelmezett érték egy vonaldiagram.

1. További összesítések rétegezése a **Metrika hozzáadása** és a különböző összesítések kiválasztásával.

1. Nagyítson rá egy érdekes területre a vonaldiagramon. Helyezze az egérmutatót a terület elejére, kattintson a bal egérgombbal, húzza a mutatót a terület másik oldalára, és engedje fel a gombot. A diagram ráközelít az adott időtartományra.

## <a name="identify-strings-used-in-queries"></a>A lekérdezésekben használt karakterláncok azonosítása

Diagnosztikai naplózás engedélyezésekor a rendszer rögzíti a lekérdezési kérelmeket az **AzureDiagnostics** táblában. Előfeltételként már engedélyeznie kell [a diagnosztikai naplózást,](search-monitor-logs.md)meg kell adnia egy naplóelemzési munkaterületet vagy egy másik tárolási lehetőséget.

1. A Figyelés csoportban válassza **a Naplók** lehetőséget egy üres lekérdezési ablak megnyitásához a Log Analytics alkalmazásban.

1. Futtassa a következő kifejezést a Query.Search műveletek kereséséhez, és adja vissza a művelet nevét, a lekérdezési karakterláncot, a lekérdezett indexet és a talált dokumentumok számát. Az utolsó két utasítás kizárja az üres vagy meghatározatlan keresésből álló lekérdezési karakterláncokat egy mintaindexen keresztül, amely csökkenti a zajt az eredményekben.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2019-05-06&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Szükség esetén állítson be oszlopszűrőt *Query_s,* ha egy adott szintaxison vagy karakterláncon szeretne keresni. Szűrhet például *egyenlő nek megfelelő).* `?api-version=2019-05-06&search=*&%24filter=HotelName`

   ![Naplózott lekérdezési karakterláncok](./media/search-monitor-usage/log-query-strings.png "Naplózott lekérdezési karakterláncok")

Bár ez a technika ad hoc vizsgálathoz működik, a jelentés létrehozása lehetővé teszi a lekérdezési karakterláncok konszolidálását és bemutatását az elemzéshez jobban elősegítő elrendezésben.

## <a name="identify-long-running-queries"></a>Hosszú ideig futó lekérdezések azonosítása

Adja hozzá az időtartam oszlopot az összes lekérdezés számának lekérni, nem csak azokat, amelyeket metrikaként vesznek fel. Az adatok rendezése azt mutatja, hogy mely lekérdezések végrehajtásához kell a leghosszabb.

1. A Figyelés csoportban válassza **a Naplók** lekérdezéséhez naplóadatok lekérdezéséhez lehetőséget.

1. Futtassa a következő lekérdezést a lekérdezések visszaadásához, időtartam szerint rendezve ezredmásodpercben. A leghosszabb ideig futó lekérdezések a tetején vannak.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Lekérdezések rendezése időtartam szerint](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Lekérdezések rendezése időtartam szerint")

## <a name="create-a-metric-alert"></a>Metrikariasztás létrehozása

A metrikariasztás egy küszöbértéket hoz létre, amelyen vagy értesítést kap, vagy előre meghatározott korrekciós műveletet indít el. 

Egy keresési szolgáltatás gyakori, hogy hozzon létre egy metrika riasztást a keresési késés és a szabályozott lekérdezések. Ha tudja, hogy a lekérdezések eldobásakor olyan megoldásokat kereshet, amelyek csökkentik a terhelést vagy növelik a kapacitást. Ha például a szabályozott lekérdezések növekedése az indexelés során, elhalaszthatja, amíg a lekérdezési tevékenység le nem csökken.

Egy adott replikapartíció-konfiguráció korlátainak lekérésekor a lekérdezési kötetküszöbértékek (QPS) riasztásainak beállítása is hasznos.

1. A Figyelés csoportban válassza a **Riasztások** lehetőséget, majd kattintson **a + Új riasztási szabály**elemre. Győződjön meg arról, hogy a keresési szolgáltatás van kiválasztva erőforrásként.

1. A Feltétel csoportban kattintson a **Hozzáadás**gombra.

1. A jellogika konfigurálása. A jel típus, válassza **ki a metrikákat,** majd válassza ki a jelet.

1. A jel kiválasztása után egy diagram segítségével megjelenítheti a korábbi adatokat, hogy megalapozott döntést hozzon a feltételek beállításának folytatásáról.

1. Ezután görgessen le az Értesítési logika elemre. A proof-of-concept, megadhat egy mesterségesen alacsony értékű tesztelési célokra.

   ![Riasztási logika](./media/search-monitor-usage/alert-logic-qps.png "Riasztási logika")

1. Ezután adjon meg vagy hozzon létre egy műveletcsoportot. Ez a válasz, hogy hívja meg, ha a küszöbérték teljesül. Lehet, hogy egy leküldéses értesítés vagy egy automatikus válasz.

1. Végül adja meg a riasztás részleteit. Nevezze el és írja le a riasztást, rendeljen hozzá egy súlyossági értéket, és adja meg, hogy a szabályt engedélyezett vagy letiltott állapotban hozza-e létre.

   ![Riasztás részletei](./media/search-monitor-usage/alert-details.png "Riasztás részletei")

Ha megadott egy e-mail értesítést, e-mailt fog kapni a "Microsoft Azure"-tól `<your rule name>`az "Azure: Aktivált súlyosság: 3" tárgysorú e-mailt.

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, tekintse át a keresési szolgáltatások figyelésének alapjait, és ismerje meg a felügyeleti képességek teljes skáláját.

> [!div class="nextstepaction"]
> [Műveletek és tevékenységek figyelése az Azure Cognitive Search szolgáltatásban](search-monitor-usage.md)