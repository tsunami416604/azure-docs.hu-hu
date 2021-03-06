---
title: Adatfolyamok leképezése
description: Az adatfolyamatok Azure Data Factoryban való leképezésének áttekintése
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/10/2020
ms.openlocfilehash: 01ee890fe77abebfdec8d3d643773b42cb54187e
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606181"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Az adatfolyamatok leképezése Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="what-are-mapping-data-flows"></a>Mik azok a leképezési adatfolyamok?

Az adatfolyamatok leképezése vizuálisan tervezett adatátalakítások Azure Data Factoryban. Az adatforgalom lehetővé teszi, hogy az adatmérnökök programkód írása nélkül fejlesszenek Adatátalakítási logikát. Az eredményül kapott adatfolyamatok a kibővített Apache Spark-fürtöket használó Azure Data Factory folyamatokon belüli tevékenységekként lesznek végrehajtva. Az adatfolyam-tevékenységek a meglévő Azure Data Factory ütemezési, vezérlési, folyamat-és figyelési képességek használatával is működőképesek.

Az adatforgalom leképezése teljes körű vizuális élményt biztosít, és nincs szükség kódolásra. Az adatfolyamatok az ADF által felügyelt végrehajtási fürtökön futnak a kibővíthető adatfeldolgozáshoz. Azure Data Factory kezeli az adatáramlási feladatok összes fordítását, az elérési út optimalizálását és végrehajtását.

## <a name="getting-started"></a>Első lépések

Az adatfolyamatok a gyári erőforrások ablaktábláról jönnek létre, például a folyamatok és az adatkészletek. Adatfolyamat létrehozásához válassza ki a **gyári erőforrások** melletti pluszjelet, majd válassza az **adatfolyam** lehetőséget. 

![Új adatfolyam](media/data-flow/new-data-flow.png)

Ez a művelet végigvezeti az adatáramlási vászonon, ahol létrehozhatja az átalakítási logikát. A forrás-átalakítás konfigurálásának megkezdéséhez válassza a **forrás hozzáadása** lehetőséget. További információ: forrás- [átalakítás](data-flow-source.md).

## <a name="authoring-data-flows"></a>Adatfolyamatok készítése

A leképezési folyamat egy egyedi szerzői vászon, amely megkönnyíti az átalakítási logika kiépítése. Az adatfolyam-vászon három részből áll: a felső sáv, a gráf és a konfigurációs panel. 

![Képernyőfelvétel: az adatáramlási vászon a felső sáv, a gráf és a konfigurációs panel feliratú.](media/data-flow/canvas-1.png "Vászon")

### <a name="graph"></a>Graph

A gráf megjeleníti az átalakítási adatfolyamot. Megjeleníti a forrásadatok vonalát, mivel az egy vagy több mosogatóba áramlik. Új forrás hozzáadásához válassza a **forrás hozzáadása** elemet. Új átalakítás hozzáadásához válassza a meglévő átalakítás jobb alsó sarkában látható plusz jelre. További információ [az adatfolyam-gráf kezeléséről](concepts-data-flow-manage-graph.md).

![A képernyőfelvétel a vászon Graph részét jeleníti meg egy keresési szövegmezővel.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Konfigurációs panel

A konfigurációs panel megjeleníti az aktuálisan kiválasztott átalakításhoz tartozó beállításokat. Ha nincs kiválasztva átalakítás, az adatfolyamatot jeleníti meg. A teljes adatfolyam-konfigurációban paramétereket adhat hozzá a **Parameters (paraméterek** ) lapon. További információ: [az adatfolyam paramétereinek leképezése](parameters-data-flow.md).

Minden átalakítás legalább négy konfigurációs lapot tartalmaz.

#### <a name="transformation-settings"></a>Átalakítási beállítások

Az egyes átalakítások konfigurációs paneljének első lapja az adott átalakításra vonatkozó beállításokat tartalmazza. További információ: az átalakítás dokumentációs lapja.

![Forrás beállításai lap](media/data-flow/source1.png "Forrás beállításai lap")

#### <a name="optimize"></a>Optimalizálás

Az **optimalizálás** lap a particionálási sémák konfigurálásához szükséges beállításokat tartalmazza. Az adatfolyamatok optimalizálásával kapcsolatos további tudnivalókért tekintse meg az [adatfolyamok teljesítményének feltérképezése című útmutatót](concepts-data-flow-performance.md).

![A képernyőképen az optimalizálás lap látható, amely tartalmazza a partíciós beállítást, a partíció típusát és a partíciók számát.](media/data-flow/optimize.png)

#### <a name="inspect"></a>Vizsgálata

Az **ellenőrzés** lapon megtekintheti az átalakítás alatt álló adatfolyam metaadatait. Megtekintheti az oszlopok számát, a megváltoztatott oszlopokat, a hozzáadott oszlopokat, az adattípusokat, az oszlopok sorrendjét és az oszlopok hivatkozásait. A **vizsgálat** a metaadatok csak olvasható nézete. Nem kell engedélyezni a hibakeresési módot a metaadatok megjelenítéséhez a **vizsgálat** ablaktáblán.

![Vizsgálata](media/data-flow/inspect1.png "Vizsgálata")

Amikor átalakításokon keresztül módosítja az adatok alakját, a metaadatok változásai a **vizsgálat** panelen jelennek meg. Ha nincs definiált séma a forrás-átalakításban, akkor a metaadatok nem lesznek láthatók a **vizsgálat** ablaktáblán. A metaadatok hiánya gyakori a séma-drift forgatókönyvekben.

#### <a name="data-preview"></a>Adatelőnézet

Ha a hibakeresési mód be van kapcsolva, az **adatelőnézet** lap interaktív pillanatképet biztosít az adatokról az egyes transzformációk során. További információ: az [adatelőnézet hibakeresési módban](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Felső sáv

A felső sáv olyan műveleteket tartalmaz, amelyek hatással vannak a teljes adatfolyamra, például a mentésre és az érvényesítésre. Az átalakítási logikában megtekintheti a mögöttes JSON-kódot és adatfolyam-parancsfájlt is. További információért olvassa el az adatfolyam- [parancsfájlt](data-flow-script.md).

## <a name="available-transformations"></a>Elérhető átalakítások

Az elérhető átalakítások listájának megtekintéséhez tekintse meg a [leképezési adatfolyam-átalakítás áttekintését](data-flow-transformation-overview.md) .

## <a name="data-flow-activity"></a>Adatfolyam-tevékenység

A leképezési adatfolyamatok az ADF-folyamatokon belül működnek az [adatfolyam tevékenységgel](control-flow-execute-data-flow-activity.md). Az összes felhasználónak meg kell határoznia, hogy melyik integrációs modult használja, és adja át a paraméter értékeit. További információ: az [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)ismertetése.

## <a name="debug-mode"></a>Hibakeresési mód

A hibakeresési mód lehetővé teszi az egyes átalakítási lépések eredményének interaktív megjelenítését az adatfolyamatok létrehozásakor és hibakeresése során. A hibakeresési munkamenet az adatfolyam-logika összeállításakor és az adatfolyamatok hibakeresésének futtatásakor is használható az adatfolyam-tevékenységekkel. További információt a [hibakeresési mód dokumentációjában](concepts-data-flow-debug-mode.md)talál.

## <a name="monitoring-data-flows"></a>Adatfolyamatok figyelése

Az adatáramlás leképezése integrálható a meglévő Azure Data Factory Figyelési képességeivel. Az adatfolyam-figyelés kimenetének megismeréséhez lásd: a [leképezési adatfolyamatok figyelése](concepts-data-flow-monitoring.md).

A Azure Data Factory csapat létrehozta a [teljesítmény-hangolási útmutatót](concepts-data-flow-performance.md) , amellyel optimalizálhatja az Adatáramlások végrehajtási idejét az üzleti logikája kiépítése után.

## <a name="available-regions"></a>Elérhető régiók

= = = = = = = Az adatfolyamatok leképezése az ADF következő régióiban érhető el:

| Azure-régió | Adatfolyamatok az ADF-ben |
| ------------ | ----------------- |
|  Ausztrália középső régiója | |
| Ausztrália 2. középső régiója | |
| Kelet-Ausztrália | ✓ |
| Délkelet-Ausztrália   | ✓ |
| Dél-Brazília  | ✓ |
| Közép-Kanada | ✓ |
| Közép-India | ✓ |
| USA középső régiója    | ✓ |
| Kelet-Kína |      |
| Kelet-Kína 2  |   |
| Kína nem regionális | |
| Észak-Kína |     |
| Észak-Kína 2 | |
| Kelet-Ázsia | ✓ |
| USA keleti régiója   | ✓ |
| USA 2. keleti régiója | ✓ |
| Közép-Franciaország | ✓ |
| Dél-Franciaország  | |
| Közép-Németország (szuverén) | |
| Németország – régiófüggetlen (szuverén) | |
| Észak-Németország (nyilvános) | |
| Északkelet-Németország (szuverén) | |
| Középnyugat-Németország (nyilvános) |  |
| Kelet-Japán | ✓ |
| Nyugat-Japán |  |
| Dél-Korea középső régiója | ✓ |
| Dél-Korea déli régiója | |
| USA északi középső régiója  | ✓ |
| Észak-Európa  | ✓ |
| Kelet-Norvégia | |
| Norvégia nyugati régiója | |
| Dél-Afrika északi régiója    | ✓ |
| Dél-Afrika nyugati régiója |  |
| USA déli középső régiója  | |
| Dél-India | |
| Délkelet-Ázsia    | ✓ |
| Észak-Svájc |   |
| Nyugat-Svájc | |
| UAE középső régiója | |
| Észak-Egyesült Arab |  |
| Az Egyesült Királyság déli régiója  | ✓ |
| Az Egyesült Királyság nyugati régiója |     |
| US DoD – Középső régió | |
| US DoD – Kelet | |
| USA-beli államigazgatás – Arizona |      |
| US Gov – régiófüggetlen | |
| USA-beli államigazgatás – Texas | |
| USA-beli államigazgatás – Virginia |     |
| USA nyugati középső régiója |     |
| Nyugat-Európa   | ✓ |
| Nyugat-India | |
| USA nyugati régiója   | ✓ |
| USA 2. nyugati régiója | ✓ |

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan hozhat létre [forrás-átalakítást](data-flow-source.md).
* Megtudhatja, hogyan hozhat létre adatfolyamatokat [hibakeresési módban](concepts-data-flow-debug-mode.md).
