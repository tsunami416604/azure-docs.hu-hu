---
title: Az adatfolyam-teljesítmény és a hangolási útmutató leképezése
description: Ismerje meg az Azure Data Factory leképezési adatfolyamainak teljesítményét befolyásoló legfontosabb tényezőket.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 18f8b0732e4af0229ff225d9c3b423e27bf342a8
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382804"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Az adatfolyamok teljesítményének és hangolási útmutatójának leképezése

Az Azure Data Factory adatfolyamainak leképezése kódmentes felületet biztosít az adatátalakítások nagy méretekben történő tervezéséhez, üzembe helyezéséhez és vezényléséhez. Ha nem ismeri az adatfolyamok leképezését, olvassa el az [Adatfolyam leképezése – áttekintés című témakört.](concepts-data-flow-overview.md)

Az ADF-ux-ből származó adatfolyamok tervezésekor és tesztelésekénél győződjön meg arról, hogy a hibakeresési módot kapcsolja be az adatfolyamok valós idejű végrehajtásához anélkül, hogy megvárna egy fürt bemelegedését. További információt a [Hibakeresési mód című témakörben talál.](concepts-data-flow-debug-mode.md)

Ez a videó az adatok adatfolyamokkal történő átalakításának néhány mintaidőzítését mutatja be:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Az adatfolyam teljesítményének figyelése

A leképezési adatfolyamok tervezésekor egyesítheti az egyes átalakításokat a konfigurációpanel adatelőnézeti fülére kattintva. Miután ellenőrizte a logikát, tesztelje az adatfolyamot végpontok között egy folyamat tevékenységeként. Adjon hozzá egy adatfolyam-tevékenység végrehajtása, és használja a Debug gombot az adatfolyam teljesítményének teszteléséhez. Az adatfolyam végrehajtási tervének és teljesítményprofiljának megnyitásához kattintson a szemüveg ikonra a folyamat kimeneti lapján található "Műveletek" alatt.

![Adatfolyam-figyelő](media/data-flow/mon002.png "Adatfolyam-figyelő 2")

 Ezen információk segítségével megbecsülheti az adatfolyam teljesítményét a különböző méretű adatforrásokkal szemben. További információ: [Mapping mapping data flows](concepts-data-flow-monitoring.md).

![Adatfolyam figyelése](media/data-flow/mon003.png "Adatfolyam-figyelő 3")

 A folyamathiba-hibakeresés hez körülbelül egy perc fürt beállítási ideje szükséges az általános teljesítményszámításokban egy meleg fürthöz. Ha az alapértelmezett Azure-integrációs futásidejű inicializálása, az üzembe helyezési idő körülbelül 5 percet is igénybe vehet.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Növekvő számítási méret az Azure-integrációs futásidőben

A több maggal rendelkező integrációs futásidő növeli a csomópontok számát a Spark számítási környezetekben, és nagyobb feldolgozási teljesítményt biztosít az adatok olvasásához, írásához és átalakításához. Az ADF-adatfolyamok a Sparkot használják a számítási motorhoz. A Spark-környezet nagyon jól működik a memória optimalizált erőforrásokon.
* Ha azt szeretné, hogy a feldolgozási arány magasabb legyen, mint a bemeneti arány, **próbálkozzon a számítási optimalizált** fürttel.
* Ha több adatot szeretne gyorsítótárazni a memóriában, próbálkozzon **a memóriaoptimalizált** fürttel. A memória optimalizált memória magonkénti ár-pont, mint a számítási optimalizált, de valószínűleg gyorsabb átalakítási sebességet eredményez.

![Új infravörös](media/data-flow/ir-new.png "Új infravörös")

Az integrációs futásidejűek létrehozásáról az [Integrációs futásidő az Azure Data Factory ban](concepts-integration-runtime.md)című témakörben talál további információt.

### <a name="increase-the-size-of-your-debug-cluster"></a>A hibakeresési fürt méretének növelése

Alapértelmezés szerint a hibakeresés bekapcsolása az alapértelmezett Azure-integrációs futásidőt fogja használni, amely automatikusan létrejön az egyes adat-előállítókhoz. Ez az alapértelmezett Azure IR van beállítva nyolc mag, négy egy illesztőprogram-csomópont és négy egy feldolgozó csomópont, általános számítási tulajdonságok használatával. A nagyobb adatokkal való tesztelés során növelheti a hibakeresési fürt méretét egy nagyobb konfigurációkkal rendelkező Azure IR létrehozásával, és válassza ezt az új Azure IR-t a hibakeresés bekapcsolásakor. Ez arra utasítja az ADF-et, hogy használja ezt az Azure IR-t az adatok előnézetéhez és a folyamat hibakereséséhez az adatfolyamokkal.

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>Fürtszámítási indítási idejének csökkentése a TTL-lel

Van egy tulajdonság az Azure IR adatfolyam-tulajdonságok alatt, amely lehetővé teszi, hogy a fürt számítási erőforrások készletének a gyárban álló készlet. Ezzel a készlettel egymás után küldhet idáig adatfolyam-tevékenységeket a végrehajtáshoz. A készlet létrehozása után minden további feladat 1-2 percet vesz igénybe, amíg az igény szerinti Spark-fürt végrehajtja a feladatot. Az erőforráskészlet kezdeti beállítása körülbelül 6 percet vesz igénybe. Adja meg, hogy mennyi időt kíván fenntartani az erőforráskészletet az élő (TTL) beállításban.

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>Optimalizálás az Azure SQL Database és az Azure SQL Data Warehouse Synapse számára

### <a name="partitioning-on-source"></a>Particionálás a forráson

1. Nyissa meg az **Optimalizálás** lapot, és válassza **a Particionálás beállítása lehetőséget**
1. Válassza a **Forrás**lehetőséget.
1. A **Partíciók száma csoportban**adja meg az Azure SQL DB-hez való kapcsolatok maximális számát. Megpróbálhat egy magasabb beállítást, hogy párhuzamos kapcsolatot létesítsen az adatbázissal. Egyes esetek azonban korlátozott számú kapcsolat esetén gyorsabb teljesítményt eredményezhetnek.
1. Adja meg, hogy egy adott táblaoszlop vagy lekérdezés szerint particionáljon-e.
1. Ha az **Oszlop**lehetőséget választotta, válassza ki a partícióoszlopot.
1. Ha a **Lekérdezés**lehetőséget választotta, adjon meg egy olyan lekérdezést, amely megfelel az adatbázistábla particionálási sémának. Ez a lekérdezés lehetővé teszi, hogy a forrás adatbázis-motor a partíció megszüntetése. A forrásadatbázis-táblákat nem kell particionálni. Ha a forrás még nincs particionálva, az ADF továbbra is használni fogja az adatparticionálást a Spark átalakítási környezetben a forrás transzformációban kiválasztott kulcs alapján.

![Forrásrész](media/data-flow/sourcepart3.png "Forrásrész")

> [!NOTE]
> Egy jó útmutató, amely segít a partíciók száma kiválasztásában a forráshoz az Azure-integrációs futásidejű hez beállított magok számától, és ezt a számot ötre szorozza meg. Így például ha az ADLS-mappákban lévő fájlok sorozatát alakítja át, és egy 32 magos Azure IR-t fog használni, a megcélzott partíciók száma 32 x 5 = 160 partíció.

### <a name="source-batch-size-input-and-isolation-level"></a>Forrásköteg-méret, bemeneti és elkülönítési szint

A forrásátalakítás **Forrásbeállításai csoportjában** a következő beállítások befolyásolhatják a teljesítményt:

* A kötegméret arra utasítja az ADF-et, hogy az adatokat a Spark memóriájában tárolja a sorok helyett. A kötegméret nem kötelező beállítás, és elfogyhatnak az erőforrások a számítási csomópontokon, ha nem megfelelően vannak méretezve. Ha nem állítja be ezt a tulajdonságot, akkor a Spark-gyorsítótárazási köteg alapértelmezett beállításait fogja használni.
* A lekérdezés beállításával szűrheti a sorokat a forrásnál, mielőtt azok feldolgozásra megérkeznének az adatfolyamba. Ez gyorsabbá teheti a kezdeti adatgyűjtést. Ha lekérdezést használ, hozzáadhat opcionális lekérdezési tippeket az Azure SQL DB-hez, például a READ UNCOMMITTED.If you use a query, you can add optional query hints for your Azure SQL DB, such as READ UNCOMMITTED.
* A nem véglegesített olvasás gyorsabb lekérdezési eredményeket biztosít a forrástranszformáción

![Forrás](media/data-flow/source4.png "Forrás")

### <a name="sink-batch-size"></a>Fogadó kötegmérete

Az adatfolyamok soronkénti feldolgozásának elkerülése érdekében állítsa be a **Kötegméretet** az Azure SQL DB és az Azure SQL DW-fogadók Beállítások lapján. Ha a kötegméret be van állítva, az ADF feldolgozza az adatbázis kötegekben történő írását a megadott méret alapján. Ha nem állítja be ezt a tulajdonságot, akkor a Spark-gyorsítótárazási köteg alapértelmezett beállításait fogja használni.

![Sink (Fogadó)](media/data-flow/sink4.png "Sink (Fogadó)")

### <a name="partitioning-on-sink"></a>Particionálás a fogadóban

Még akkor is, ha az adatok particionált a céltáblák, az ajánlott, hogy az adatok particionálva a fogadó átalakítás. A particionált adatok gyakran sokkal gyorsabb betöltést eredményeznek, és az összes kapcsolat egyetlen csomópont/partíció használatára kényszeríti. Nyissa meg a mosogató Optimalizálás lapját, és válassza a *Ciklikus robin* particionálás lehetőséget, hogy kiválassza a fogadóba írni kívánt partíciók ideális számát.

### <a name="disable-indexes-on-write"></a>Indexek letiltása íráskor

A folyamat, adjon hozzá egy [tárolt eljárás tevékenység](transform-data-using-stored-procedure.md) et, mielőtt az adatfolyam-tevékenység, amely letiltja az indexek a céltáblákon írt a mosogató. Az adatfolyam-tevékenység után adjon hozzá egy másik tárolt eljárási tevékenységet, amely lehetővé teszi ezeket az indexeket. Vagy használja az előfeldolgozási és utófeldolgozási parancsfájlok egy adatbázis-fogadóban.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Az Azure SQL DB és a DW méretének növelése

Ütemezze a forrás átméretezését, és a folyamat futtatása előtt elsüllyesztse az Azure SQL DB-t és a DW-t, hogy növelje az átviteli szintet, és minimalizálja az Azure-szabályozást, amint eléri a DTU-korlátokat. A folyamat végrehajtása befejezése után méretezze át az adatbázisokat a normál futtatási sebességre.

* SQL DB forrástábla 887k sorok és 74 oszlopok egy SQL DB tábla egyetlen származtatott oszlop átalakítása körülbelül 3 mins end-to-end memória optimalizált 80-core debug Azure IRs használatával.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Csak az Azure Synapse SQL DW] Az előkészítés használata az adatok tömeges betöltéséhez a Polybase-en keresztül

Ha el szeretné kerülni, hogy soronként beszúrja a DW-t, jelölje be **az Előkészítés engedélyezése** a fogadó beállításaiközött jelölőnégyzetet, hogy az ADF használhassa a [PolyBase parancsot.](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) A PolyBase lehetővé teszi az ADF számára az adatok tömeges betöltését.
* Amikor folyamatból hajtja végre az adatfolyam-tevékenységet, ki kell választania egy Blob vagy ADLS Gen2 tárolási helyet az adatok tömeges betöltése során történő ütemezéséhez.

* A 421 Mb-os fájl forrása 74 oszlopot tartalmaz egy szinapszis táblához, és egy származtatott oszlop átalakítása körülbelül 4 perces végponttól végpontig tart a memóriára optimalizált 80 magos Hibakeresési Azure-i RS használatával.

## <a name="optimizing-for-files"></a>Fájlok optimalizálása

Minden átalakításnál beállíthatja azt a particionálási sémát, amelyet az Optimalizálás lapon szeretne használni az adatgyárban. Ez egy jó gyakorlat, hogy az első teszt fájlalapú fogadók az alapértelmezett particionálás és optimalizálás.

* Kisebb fájlok esetén előfordulhat, hogy a kevesebb partíció kiválasztása néha jobban és gyorsabban működik, mint a Spark kérése a kis fájlok particionálására.
* Ha nincs elegendő információ a forrásadatokról, válassza a *Ciklikus multiplexelés* particionálás lehetőséget, és állítsa be a partíciók számát.
* Ha az adatok olyan oszlopokat tartalmaznak, amelyek jó kivonatoló kulcsok lehetnek, válassza *a Kivonatparticionálás lehetőséget.*

* Fájlforrás fájlfogadóval egy 421 Mb-os fájl 74 oszlop és egy származtatott oszlop átalakítása körülbelül 2 mins end-to-end memória optimalizált 80-core debug Azure IRs használatával.

Ha hibakeresést szeretne az adatok előnézetében és a folyamathibakeresésben, a fájlalapú forrásadatkészletek revansolási és mintavételi mérete csak a visszaadott sorok számára vonatkozik, az olvasott sorok számára nem. Ez hatással lehet a hibakeresési végrehajtások teljesítményére, és valószínűleg a folyamat sikertelensítéséhez vezethet.
* A hibakeresési fürtök alapértelmezés szerint kis egycsomópontos fürtök, ezért javasoljuk, hogy a hibakereséshez használjon mintafájlokat. Nyissa meg a Hibakeresési beállítások lapot, és mutasson az adatok egy kis részhalmazára egy ideiglenes fájl használatával.

    ![Hibakeresési beállítások](media/data-flow/debugsettings3.png "Hibakeresési beállítások")

### <a name="file-naming-options"></a>Fájlelnevezési beállítások

Az átalakított adatok írásának leggyakoribb módja a Blob vagy ADLS fájltároló írásai adatfolyamokban. A fogadóban ki kell jelölnie egy olyan adatkészletet, amely egy tárolóra vagy mappára mutat, nem egy elnevezett fájlra. Mivel az adatfolyam leképezése a Sparkot használja a végrehajtáshoz, a kimenet több fájlra oszlik a particionálási séma alapján.

A közös particionálási séma a _Kimenet egyetlen fájlhoz_lehetőséget választja, amely az összes kimeneti RÉSZfájlt egyetlen fájlba egyesíti a fogadóban. Ehhez a művelethez a kimenet egyetlen partícióra csökken egyetlen fürtcsomóponton. Kifogyhat a fürtcsomópont-erőforrásokból, ha sok nagy forrásfájlt egyesít egyetlen kimeneti fájlba.

A számítási csomópont-erőforrások kimerítésének elkerülése érdekében tartsa meg az alapértelmezett, optimalizált sémát az adatfolyamban, és adjon hozzá egy másolási tevékenységet a folyamathoz, amely a kimeneti mappából származó összes RÉSZfájlt egy új egyetlen fájlba egyesíti. Ez a módszer elválasztja az átalakítás műveletét a fájlegyesítéstől, és ugyanazt az eredményt éri el, mint a _Kimenet beállítása egyetlen fájlra_.

### <a name="looping-through-file-lists"></a>Ismétlés a fájllistákközött

A leképezési adatfolyam jobban fog végrehajtani, ha a forrás transzformáció több fájlon keresztül ismétlődik, ahelyett, hogy a For Each tevékenységen keresztül hurkolna. Javasoljuk, hogy használjon helyettesítő karaktereket vagy fájllistákat a forrásátalakításban. Az adatfolyam-folyamat gyorsabban fog végrehajtani, lehetővé téve a ciklus a Spark-fürtön belül történő előfordulását. További információ: [Helyettesítő karakter a Forrásátalakításban](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Ha például 2019 júliusától van egy lista az adatfájlokról, amelyeket a Blob Storage egy mappájában szeretne feldolgozni, az alábbiakban egy helyettesítő karakter t, amelyet a forrás átalakítása során használhat.

```DateFiles/*_201907*.txt```

Helyettesítő karakter használatával a folyamat csak egy adatfolyam-tevékenységet fog tartalmazni. Ez jobban fog működni, mint a Blob Store-on való keresés, amely az összes egyező fájlon végighalad egy ForEach használatával, amelyen belül adatfolyam-végrehajtási tevékenység van.

### <a name="optimizing-for-cosmosdb"></a>Optimalizálás cosmosDB-hez

Az átviteli és kötegelt tulajdonságok beállítása a CosmosDB-fogadókban csak a folyamat-adatfolyam-tevékenységből származó adatfolyam végrehajtása során lép érvénybe. Az adatfolyam-végrehajtás után a CosmosDB tiszteletben tartja az eredeti adatgyűjtési beállításokat.

* Kötegméret: Számítsa ki az adatok durva sorméretét, és győződjön meg arról, hogy a rowSize * kötegmérete kevesebb, mint kétmillió. Ha ez így van, növelje a köteg méretét, hogy jobb átviteli
* Átviteli sebessége: Itt állítson be egy nagyobb átviteli sebességet, hogy a dokumentumok gyorsabban írhassanak a CosmosDB-be. Kérjük, vegye figyelembe, hogy a magasabb ru költségek alapján a magas átviteli sebességű beállítás.
*   Átviteli költségvetés írása: Használjon olyan értéket, amely kisebb, mint a teljes rúkódások percenként. Ha nagy számú Spark-partícióval rendelkező adatfolyammal rendelkezik, a költségvetési átviteli érték beállítása nagyobb egyensúlyt tesz lehetővé a partíciók között.

## <a name="join-performance"></a>Csatlakozás teljesítményéhez

Az adatfolyamok illesztéseinek teljesítményének kezelése nagyon gyakori művelet, amelyet az adatátalakítások teljes életciklusa során el fog végezni. Az ADF-ben az adatfolyamok nem igényelnek adatokat kell rendezni az illesztések előtt, mivel ezek a műveletek a Spark kivonatillesztéseként történnek. A "Broadcast" Join optimization azonban jobb teljesítményt érhet el. Ezzel elkerülhető a véletlen sorrendű keverés, ha lenyomja az illesztési kapcsolat mindkét oldalának tartalmát a Spark-csomópontba. Ez jól működik a referencia-keresményekhez használt kisebb táblák esetében. A nagyobb táblák, amelyek nem férnek bele a csomópont memóriájába, nem jó jelöltek az adásoptimalizáláshoz.

Egy másik Join optimalizálás az, hogy az illesztések oly módon, hogy elkerüli a Spark azon tendencia, hogy végre kereszt illesztések. Ha például literális értékeket ad meg az illesztési feltételekben, a Spark azt láthatja, hogy a teljes descartes-i termék végrehajtásának követelményeként, majd kiszűri az illesztett értékeket. De ha biztosítja, hogy az illesztési feltétel mindkét oldalán oszlopértékek vannak, elkerülheti ezt a Spark által kiváltott descartes-i terméket, és javíthatja az illesztések és az adatfolyamok teljesítményét.

## <a name="next-steps"></a>További lépések

Lásd a teljesítményre vonatkozó egyéb adatfolyam-cikkeket:

- [Adatfolyam optimalizálása lap](concepts-data-flow-overview.md#optimize)
- [Adatfolyam-tevékenység](control-flow-execute-data-flow-activity.md)
- [Az adatfolyam teljesítményének figyelése](concepts-data-flow-monitoring.md)
