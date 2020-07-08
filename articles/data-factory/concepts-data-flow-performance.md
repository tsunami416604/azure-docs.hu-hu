---
title: Az adatfolyam teljesítményének és hangolási útmutatójának leképezése
description: Ismerje meg azokat a főbb tényezőket, amelyek hatással vannak a Azure Data Factory adatforgalmának teljesítményére.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 07/06/2020
ms.openlocfilehash: 1c63568418f21da0556ced0d004e04e7909118fb
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042628"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Adatfolyamatok teljesítményének és hangolási útmutatójának leképezése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory adatforgalmának leképezése egy kód nélküli felületet biztosít az adatátalakítások méretekben történő megtervezéséhez, üzembe helyezéséhez és előkészítéséhez. Ha nem ismeri a leképezési adatfolyamatokat, tekintse meg a [leképezési adatfolyam áttekintése](concepts-data-flow-overview.md)című témakört.

Az ADF UX-ből származó adatfolyamatok tervezésekor és tesztelésekor ügyeljen arra, hogy a hibakeresési módot váltson át úgy, hogy valós időben hajtsa végre az adatfolyamatokat anélkül, hogy a fürt bemelegítésére kellene várnia. További információ: [hibakeresési mód](concepts-data-flow-debug-mode.md).

Ebből a videóból megtekintheti az adatfolyamatokkal való adatátalakítással kapcsolatos néhány minta időzítést:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Az adatfolyam teljesítményének figyelése

A leképezési adatfolyamatok tervezésekor az egyes átalakításokat egységesen tesztelheti a konfigurációs panel adatelőnézet lapjára kattintva. Miután ellenőrizte a logikát, tesztelje az adatfolyamatot egy folyamat tevékenységében. Vegyen fel egy végrehajtási adatfolyam-tevékenységet, és használja a hibakeresés gombot az adatfolyam teljesítményének teszteléséhez. Az adatfolyam végrehajtási tervének és teljesítmény-profiljának megnyitásához kattintson a "műveletek" alatt található szemüvegek ikonra a folyamat kimenet lapján.

![Adatfolyam-figyelő](media/data-flow/mon002.png "Adatfolyam-figyelő 2")

 Ezekkel az adatokkal megbecsülheti az adatáramlás teljesítményét a különböző méretű adatforrások esetében. További információ: a [leképezési adatok forgalmának figyelése](concepts-data-flow-monitoring.md).

![Adatfolyam-figyelés](media/data-flow/mon003.png "Adatfolyam-figyelő 3")

 A folyamat-hibakeresési műveletek esetében körülbelül egy percen belül üzembe helyezési idő szükséges a teljes teljesítmény-számításokban a meleg fürthöz. Az alapértelmezett Azure Integration Runtime inicializálásakor a Felpörgetési idő körülbelül 5 percet is igénybe vehet.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Azure Integration Runtime számítási méretének növelése

A több magot tartalmazó Integration Runtime növeli a Spark számítási környezetekben lévő csomópontok számát, és nagyobb feldolgozási teljesítményt biztosít az adatok olvasásához, írásához és átalakításához. Az ADF-adatfolyamatok a Sparkot használják a számítási motorhoz. A Spark-környezet nagyon jól működik a memóriára optimalizált erőforrásokon.

Javasoljuk, hogy a legtöbb számítási feladathoz **optimalizált memóriát** használjon. A memóriában több adattal is tárolhatók, és a memóriában lévő hibák csökkenthetők. Az optimalizált memória magasabb árat mutat, mint a számítási optimalizált érték, de valószínűleg gyorsabb átalakítási sebességet és sikeres folyamatokat eredményez. Ha az adatfolyamatok végrehajtása során a memóriával kapcsolatos hibákat tapasztal, váltson a memóriára optimalizált Azure IR konfigurációra.

A **számításra optimalizált számítási** funkció elegendő a korlátozott számú adatsor hibakereséséhez és adatmegjelenítéséhez. A számítási feladatokra optimalizált számítások valószínűleg nem lesznek végrehajtva az éles munkaterhelések esetében is.

![Új IR](media/data-flow/ir-new.png "Új IR")

Integration Runtime létrehozásával kapcsolatos további információkért lásd: [Integration Runtime Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>A hibakeresési fürt méretének növeléséhez

Alapértelmezés szerint a hibakeresés bekapcsolása az alapértelmezett Azure Integration runtimet fogja használni, amely minden egyes adatfeldolgozó esetében automatikusan létrejön. Ez az alapértelmezett Azure IR nyolc mag, egy illesztőprogram-csomópont esetében négy, a munkavégző csomópont esetében négy pedig az általános számítási tulajdonságok használatával van beállítva. Ha nagyobb mennyiségű adattesztet használ, a hibakeresési fürt méretének növeléséhez hozzon létre egy Azure IR nagyobb konfigurációval, és válassza ezt az új Azure IR a hibakeresési lehetőség bekapcsolásakor. Ennek hatására az ADF ezt a Azure IR használja az adatelemzési és folyamat-hibakereséshez az adatfolyamatokkal.

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>A fürt számítási indítási idejének csökkentése ÉLETTARTAMmal

Az adatáramlás tulajdonságainál az Azure IR egy tulajdonsága, amely lehetővé teszi, hogy a gyári számítási erőforrások készletét elbírja a gyár számára. Ezzel a készlettel szekvenciálisan elküldheti az adatfolyam-tevékenységeket a végrehajtáshoz. A készlet létrehozása után minden további teendő 1-2 percet vesz igénybe, hogy az igény szerinti Spark-fürt végrehajtsa a feladatot. Az erőforráskészlet kezdeti beállítása körülbelül 6 percet vesz igénybe. Itt adhatja meg, hogy mennyi ideig kívánja fenntartani az erőforráskészletet az élettartam (TTL) beállításban.

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>Optimalizálás Azure SQL Database és Azure SQL Data Warehouse szinapszis számára

### <a name="partitioning-on-source"></a>Particionálás a forráson

1. Lépjen az **optimalizálás** lapra, és válassza a **particionálás beállítása** lehetőséget.
1. Válassza a **forrás**lehetőséget.
1. A **partíciók száma**területen állítsa be a kapcsolatok maximális számát az Azure SQL-adatbázishoz. Nagyobb beállítást is kipróbálhat, ha párhuzamos kapcsolatot szeretne létesíteni az adatbázissal. Bizonyos esetekben azonban előfordulhat, hogy a gyorsabb teljesítmény korlátozott számú kapcsolattal jár.
1. Válassza ki, hogy egy adott tábla vagy lekérdezés alapján kívánja-e particionálni.
1. Ha az **oszlop**lehetőséget választotta, válassza ki a partíció oszlopot.
1. Ha a **lekérdezés**lehetőséget választotta, adjon meg egy olyan lekérdezést, amely megfelel az adatbázis-tábla particionálási sémájának. Ez a lekérdezés lehetővé teszi, hogy a forrás-adatbázis motorja kihasználja a partíciók eltávolítását. A forrás-adatbázis táblái nem szükségesek particionálni. Ha a forrás még nincs particionálva, az ADF továbbra is az adatparticionálást fogja használni a Spark átalakítási környezetben a forrás-átalakításban kiválasztott kulcs alapján.

![Forrás része](media/data-flow/sourcepart3.png "Forrás része")

> [!NOTE]
> Egy jó útmutató a forráshoz tartozó partíciók számának kiválasztásához a Azure Integration Runtime számára beállított magok száma alapján, és az érték szorzása öt értékkel. Így például, ha a ADLS-mappákban több fájlt alakít át, és egy 32 Magos Azure IR fog használni, a célként megadott partíciók száma 32 x 5 = 160 partíció.

### <a name="source-batch-size-input-and-isolation-level"></a>Forrás köteg mérete, bemenete és elkülönítési szintje

A forrás-átalakítás **forrás beállításai** területen a következő beállítások befolyásolhatják a teljesítményt:

* A Batch-méret arra utasítja az ADF-et, hogy a Spark-memóriában lévő készletekben tárolja az adatsorokat a soron belüli sorok helyett. A köteg mérete választható beállítás, és a számítási csomópontokon kifogyhat az erőforrások, ha nem megfelelően vannak kiválasztva. Ha ezt a tulajdonságot nem állítja be, akkor a Spark cache batch alapértelmezett értékeit fogja használni.
* A lekérdezés beállítása lehetővé teszi a sorok szűrését a forrásban, mielőtt az adatfolyamot megérkeznek a feldolgozáshoz. Ez gyorsabbá teheti a kezdeti adatgyűjtést. Ha lekérdezést használ, hozzáadhat opcionális lekérdezési mutatókat az Azure SQL-ADATBÁZIShoz, például az olvasás nem véglegesített műveletekhez.
* A nem véglegesített olvasási művelet gyorsabb lekérdezési eredményeket biztosít a forrás átalakításakor

![Forrás](media/data-flow/source4.png "Forrás")

### <a name="sink-batch-size"></a>Fogadó köteg mérete

Az adatfolyamatok soron belüli feldolgozásának elkerüléséhez állítsa be a **Batch-méretet** az Azure SQL db és az Azure SQL DW mosogatók beállítások lapján. Ha a köteg mérete be van állítva, az ADF a megadott méret alapján dolgozza fel a kötegekben lévő adatbázis-írási műveleteket. Ha ezt a tulajdonságot nem állítja be, akkor a Spark cache batch alapértelmezett értékeit fogja használni.

![Sink (Fogadó)](media/data-flow/sink4.png "Sink (Fogadó)")

### <a name="partitioning-on-sink"></a>Particionálás a fogadón

Még ha nincs is particionálva az adatai a célhelyen, a javasolt, hogy az adatai particionálva legyenek a fogadó átalakításban. A particionált adatmennyiség gyakran gyorsabb betöltést eredményez, így az összes kapcsolat egyetlen csomópont/partíció használatára van kényszerítve. Nyissa meg a fogadó optimalizálás lapját, és válassza a *ciklikus multiplexelés* particionálása lehetőséget a fogadóba írni kívánt partíciók ideális számának kiválasztásához.

### <a name="disable-indexes-on-write"></a>Indexek letiltása íráskor

A folyamat során adjon hozzá egy [tárolt eljárási tevékenységet](transform-data-using-stored-procedure.md) az adatfolyam tevékenysége előtt, amely letiltja a fogadóban írt céltábla indexeit. Az adatfolyam tevékenysége után adjon hozzá egy másik tárolt eljárási tevékenységet, amely engedélyezi ezeket az indexeket. Az adatbázis-fogadóban az előfeldolgozási és a feldolgozás utáni parancsfájlokat is használhatja.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Az Azure SQL-adatbázis és a DW méretének növeléséhez

Ütemezze a forrás átméretezését, és az Azure SQL DB-t és a DW-t a folyamat futtatása előtt, növelje az átviteli sebességet, és csökkentse az Azure-szabályozást a DTU korlátainak elérése után. A folyamat befejezése után méretezze át az adatbázisokat a normál futtatási sebességre.

* Az SQL DB forrásoldali tábla 887k-sorokkal és 74-oszlopokkal, egyetlen származtatott oszlop-átalakítással rendelkező SQL DB-táblázattal körülbelül 3 percet vesz igénybe a memória optimalizált 80-Core debug Azure IRs használatával.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Csak az Azure szinapszis SQL DW esetében] Adatok tömeges betöltésének használata az átmeneti használatával

Ha el szeretné kerülni a sorok közötti beszúrást a DW-be, jelölje be az **előkészítés engedélyezése** a fogadó beállításaiban lehetőséget, hogy az ADF a következőt használja: [autobase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). A Base lehetővé teszi az ADF számára az adatok tömeges betöltését.
* Amikor egy folyamatból hajtja végre az adatfolyam-tevékenységet, ki kell választania egy blobot vagy ADLS Gen2 tárolóhelyet az adatoknak a tömeges betöltés során történő előkészítéséhez.

* Az 74 oszlopokat tartalmazó 421Mb-fájl forrása egy szinapszis-táblához, és egy származtatott oszlop transzformációja körülbelül 4 percet vesz igénybe a memória optimalizált 80-Core debug Azure IRs használatával.

## <a name="optimizing-for-files"></a>Fájlok optimalizálása

Minden átalakításnál beállíthatja azt a particionálási sémát, amelyet az adatelőállítót használni kíván az optimalizálás lapon. Célszerű először a fájl alapú mosogatókat tesztelni az alapértelmezett particionálás és optimalizálás megtartása mellett. Ha kihagyja a particionálást az "aktuális particionálás" értékre a fájl célhelyén, a Spark lehetővé teszi a számítási feladatok megfelelő alapértelmezett particionálását. Az alapértelmezett particionálás 128 MB/s partíciót használ.

* Kisebb fájlok esetében előfordulhat, hogy a kevesebb partíció kiválasztásával időnként jobb és gyorsabb lehet a kis méretű fájlok particionálása, mint a Spark megkérdezése.
* Ha nem rendelkezik elegendő információval a forrásadatok közül, válassza a *ciklikus multiplexelés* particionálás lehetőséget, és állítsa be a partíciók számát.
* Ha az adatai olyan oszlopokkal rendelkeznek, amelyek megfelelő kivonatoló kulcsok lehetnek, válassza a *kivonatoló particionálás*lehetőséget.

* Az 74 oszlopokat tartalmazó 421Mb-fájlból és egy származtatott oszlop-átalakításból álló file Source (forrás), amely körülbelül 2 percet vesz igénybe a memória optimalizált 80-Core hibakeresés Azure IRs használatával.

Ha az adatelőnézet és a folyamat hibakeresése során hibakeresést végez, a fájl alapú forrás adatkészletek korlátja és mintavételezési mérete csak a visszaadott sorok számára vonatkozik, nem pedig a sorok olvasására. Ez hatással lehet a hibakeresési végrehajtás teljesítményére, és valószínűleg a folyamat meghibásodását okozza.
* A hibakeresési fürtök alapértelmezésben kisméretű egycsomópontos fürtök, ezért ajánlott kis méretű fájlokat használni a hibakereséshez. Nyissa meg a hibakeresési beállításokat, és mutasson az adatai egy kis részhalmazára egy ideiglenes fájl használatával.

    ![Hibakeresési beállítások](media/data-flow/debugsettings3.png "Hibakeresési beállítások")

### <a name="file-naming-options"></a>Fájl elnevezési beállításai

Az átalakított adatforgalomnak a blob vagy ADLS-fájlok tárolására való írásának leggyakoribb módja. A fogadóban ki kell választania egy adatkészletet, amely egy tárolóra vagy mappára mutat, nem egy elnevezett fájl. Mivel a leképezési adatfolyam a Spark használatával hajtja végre a végrehajtást, a kimenet több fájlra van felosztva a particionálási séma alapján.

Egy közös particionálási séma a _kimenet egyetlen fájlba_való kiválasztására szolgál, amely az összes kimeneti rész fájlját egyesíti egyetlen fájlba a fogadóban. Ehhez a művelethez a kimenetnek egyetlen fürtcsomópont egyetlen partícióján kell lennie. Ha sok nagyméretű forrásfájlt egyetlen kimeneti fájlba egyesít, elfogyhat a fürt csomópontjának erőforrásai.

A számítási csomópont erőforrásainak kimerítése érdekében tartsa meg az alapértelmezett, optimalizált sémát az adatfolyamban, és adjon hozzá egy másolási tevékenységet a folyamatában, amely egyesíti az összes részét a kimeneti mappából egy új egyetlen fájlba. Ez a technika elkülöníti a fájlok egyesítésének műveletét, és ugyanazt az eredményt éri el, mint a _kimenet beállítása egyetlen fájlba_.

### <a name="looping-through-file-lists"></a>Áthurkolás a fájlok listájáról

Ha a forrás-átalakítás több fájlban is megismétli a több fájlra való átirányítást, akkor a leképezési folyamat jobban végrehajtódik Javasoljuk, hogy használjon helyettesítő karaktereket vagy fájlneveket a forrás-átalakításban. Az adatfolyam-folyamat gyorsabban fog történni, mivel lehetővé teszi a hurok a Spark-fürtön belüli megkötését. További információ: [helyettesítő karakter a forrás-átalakításban](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Ha például az 2019-es számú adatfájlok listáját szeretné feldolgozni a Blob Storage egyik mappájában, az alábbi helyettesítő karakter használható a forrás-átalakításban.

```DateFiles/*_201907*.txt```

A helyettesítő karakterek használatával a folyamat csak egy adatfolyam-tevékenységet fog tartalmazni. Ez jobb teljesítményt nyújt, mint a blob-tárolón végzett keresés, amely az összes egyező fájlon megismétli a ForEach-t egy folyamaton belüli végrehajtási adatáramlási tevékenységgel.

Az egyes párhuzamos üzemmódokhoz tartozó folyamat több fürtöt fog elkészíteni a minden végrehajtott adatfolyam-tevékenységhez. Ez nagy számú párhuzamos végrehajtást eredményezhet az Azure-szolgáltatás szabályozásához. Azonban a folyamaton belüli végrehajtási adatfolyamatok használata az egyes szekvenciális készletekhez a folyamatban a szabályozás és az erőforrás-kimerülés elkerülését eredményezi. Ez Data Factory kényszeríti az egyes fájlok egymást követő adatfolyamként történő végrehajtását.

Azt javasoljuk, hogy ha mindegyiket egy adatfolyamattal együtt használja egymás után, akkor a Azure Integration Runtime élettartam beállítását kell használnia. Ennek az az oka, hogy minden fájl a teljes 5 perces fürt indítási idejét veszi fel az iteráción belül.

### <a name="optimizing-for-cosmosdb"></a>Optimalizálás a CosmosDB

Az átviteli sebesség és a Batch tulajdonságainak beállítása a CosmosDB-tárolók esetében csak az adott adatfolyamnak egy folyamat adatfolyam-tevékenységből való végrehajtásakor lép érvénybe. Az eredeti gyűjtemény beállításait a CosmosDB az adatfolyam-végrehajtás után fogja tiszteletben venni.

* Köteg mérete: kiszámítja az adatainak durva sorszámát, és győződjön meg arról, hogy a rowSize * batch mérete kisebb, mint 2 000 000. Ha igen, növelje a köteg méretét, hogy jobb teljesítményt kapjon
* Átviteli sebesség: állítsa be a nagyobb átviteli sebesség beállítását, hogy a dokumentumok gyorsabban CosmosDB. Ne feledje, hogy a magasabb szintű RU-költségek nagy adatátviteli beállításokon alapulnak.
*   Írási átviteli sebesség költségvetése: olyan értéket használjon, amely kisebb, mint a percenkénti összes RUs. Ha nagy számú Spark-partícióval rendelkező adatfolyamot tartalmaz, a költségvetés átviteli sebességének beállítása nagyobb egyensúlyt tesz lehetővé a partíciók között.

## <a name="join-and-lookup-performance"></a>Csatlakozás és keresési teljesítmény

Az adatfolyamok teljesítményének kezelése nagyon gyakori művelet, amelyet az adatátalakítások életciklusa során fog elvégezni. Az ADF-ben az adatfolyamok nem igénylik, hogy az összekapcsolások előtt rendezze az adatokat, mivel ezek a műveletek a Sparkban található kivonatoló illesztések. Azonban a jobb teljesítmény kihasználható a "szórás" illesztési optimalizálással, amely az illesztésekre, a meglévő és a keresési átalakításokra vonatkozik.

Ezzel a lépéssel elkerülhető, hogy az illesztési kapcsolat mindkét oldalának tartalmát lenyomva a Spark-csomópontra kerül. Ez jól működik a hivatkozási keresésekhez használt kisebb táblák esetében is. A csomópont memóriájában esetleg nem illeszkedő nagyobb táblák nem jó jelöltek a szórásos optimalizáláshoz.

A sok illesztési művelettel rendelkező adatforgalomhoz ajánlott konfiguráció az optimalizálás "automatikus" értékre állítása a "szórás" beállításnál, és a ***memória optimalizált*** Azure Integration Runtime konfigurációjának használata. Ha a memóriában hibák léptek fel, vagy az adatfolyam-végrehajtás során a szórási időtúllépések is fennállnak, kikapcsolhatja a szórásos optimalizálást. Ez azonban lassabban fogja végrehajtani az adatfolyamatokat. Ha szeretné, a pushdown csak a csatlakozás bal vagy jobb oldalán, vagy mindkettőn keresztül utasíthatja el az adatfolyamot.

![Szórási beállítások](media/data-flow/newbroad.png "Szórási beállítások")

Egy másik illesztési optimalizálás az összekapcsolások összekapcsolása oly módon, hogy elkerülje a Spark tendenciáját a több illesztés megvalósításában. Ha például belefoglalja az illesztési feltételekben szereplő literál értékeket, a Spark azt láthatja, hogy először egy teljes Descartes-szorzatot kell végrehajtania, majd ki kell szűrnie az illesztett értékeket. Ha azonban gondoskodni szeretne arról, hogy az összekapcsolási feltétel mindkét oldalán legyen oszlopos érték, elkerülheti a Spark által okozott Descartes-szorzatot, és javíthatja az illesztések és az adatfolyamatok teljesítményét.

## <a name="next-steps"></a>További lépések

Tekintse meg a teljesítménnyel kapcsolatos egyéb adatfolyam-cikkeket:

- [Adatfolyam optimalizálása lap](concepts-data-flow-overview.md#optimize)
- [Adatfolyam-tevékenység](control-flow-execute-data-flow-activity.md)
- [Adatfolyamok teljesítményének figyelése](concepts-data-flow-monitoring.md)
