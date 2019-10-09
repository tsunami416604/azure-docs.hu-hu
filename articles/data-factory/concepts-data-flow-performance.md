---
title: Az adatfolyam teljesítményének és hangolási útmutatójának leképezése Azure Data Factoryban | Microsoft Docs
description: Ismerje meg azokat a főbb tényezőket, amelyek hatással vannak a Azure Data Factory adatforgalmának teljesítményére.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 10/07/2019
ms.openlocfilehash: 9db1b96cb495fd0de452091da79ab61f7ae59118
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030713"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Adatfolyamatok teljesítményének és hangolási útmutatójának leképezése

A Azure Data Factory adatforgalmának leképezése egy kód nélküli felületet biztosít az adatátalakítások méretekben történő megtervezéséhez, üzembe helyezéséhez és előkészítéséhez. Ha nem ismeri a leképezési adatfolyamatokat, tekintse meg a [leképezési adatfolyam áttekintése](concepts-data-flow-overview.md)című témakört.

Az ADF UX-ből származó adatfolyamatok tervezésekor és tesztelésekor ügyeljen arra, hogy a hibakeresési módot váltson át úgy, hogy valós időben hajtsa végre az adatfolyamatokat anélkül, hogy a fürt bemelegítésére kellene várnia. További információ: [hibakeresési mód](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Az adatfolyam teljesítményének figyelése

A leképezési adatfolyamatok tervezésekor az egyes átalakításokat egységesen tesztelheti a konfigurációs panel adatelőnézet lapjára kattintva. Miután ellenőrizte a logikát, tesztelje az adatfolyamatot egy folyamat tevékenységében. Vegyen fel egy végrehajtási adatfolyam-tevékenységet, és használja a hibakeresés gombot az adatfolyam teljesítményének teszteléséhez. Az adatfolyam végrehajtási tervének és teljesítmény-profiljának megnyitásához kattintson a "műveletek" alatt található szemüvegek ikonra a folyamat kimenet lapján.

Adatfolyam- ![figyelő](media/data-flow/mon002.png "adatáramlási figyelője 2")

 Ezekkel az adatokkal megbecsülheti az adatáramlás teljesítményét a különböző méretű adatforrások esetében. További információ: a [leképezési adatok forgalmának figyelése](concepts-data-flow-monitoring.md).

Adatfolyam- ![figyelési](media/data-flow/mon003.png "adatfolyam figyelője 3")

 A folyamat-hibakeresési műveletek esetében körülbelül egy percen belül üzembe helyezési idő szükséges a teljes teljesítmény-számításokban a meleg fürthöz. Az alapértelmezett Azure Integration Runtime inicializálásakor a Felpörgetési idő körülbelül 5 percet is igénybe vehet.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Azure Integration Runtime számítási méretének növelése

A több magot tartalmazó Integration Runtime növeli a Spark számítási környezetekben lévő csomópontok számát, és nagyobb feldolgozási teljesítményt biztosít az adatok olvasásához, írásához és átalakításához.
* Ha azt szeretné, hogy a feldolgozási sebesség magasabb legyen a bemeneti sebességnél, próbálkozzon egy **számítási optimalizált** fürttel.
* Ha a memóriában további adatok gyorsítótárazására van szükség, próbálkozzon a **memóriával optimalizált** fürttel.

![Új IR]új(media/data-flow/ir-new.png "IR")

Integration Runtime létrehozásával kapcsolatos további információkért lásd: [Integration Runtime Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>A hibakeresési fürt méretének növeléséhez

Alapértelmezés szerint a hibakeresés bekapcsolása az alapértelmezett Azure Integration runtimet fogja használni, amely minden egyes adatfeldolgozó esetében automatikusan létrejön. Ez az alapértelmezett Azure IR nyolc mag, egy illesztőprogram-csomópont esetében négy, a munkavégző csomópont esetében négy pedig az általános számítási tulajdonságok használatával van beállítva. Ha nagyobb mennyiségű adattesztet használ, a hibakeresési fürt méretének növeléséhez hozzon létre egy Azure IR nagyobb konfigurációval, és válassza ezt az új Azure IR a hibakeresési lehetőség bekapcsolásakor. Ennek hatására az ADF ezt a Azure IR használja az adatelemzési és folyamat-hibakereséshez az adatfolyamatokkal.

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimalizálás Azure SQL Database és Azure SQL Data Warehouse

### <a name="partitioning-on-source"></a>Particionálás a forráson

1. Lépjen az **optimalizálás** lapra, és válassza a **particionálás beállítása** lehetőséget.
1. Válassza a **forrás**lehetőséget.
1. A **partíciók száma**területen állítsa be a kapcsolatok maximális számát az Azure SQL-adatbázishoz. Nagyobb beállítást is kipróbálhat, ha párhuzamos kapcsolatot szeretne létesíteni az adatbázissal. Bizonyos esetekben azonban előfordulhat, hogy a gyorsabb teljesítmény korlátozott számú kapcsolattal jár.
1. Válassza ki, hogy egy adott tábla vagy lekérdezés alapján kívánja-e particionálni.
1. Ha az **oszlop**lehetőséget választotta, válassza ki a partíció oszlopot.
1. Ha a **lekérdezés**lehetőséget választotta, adjon meg egy olyan lekérdezést, amely megfelel az adatbázis-tábla particionálási sémájának. Ez a lekérdezés lehetővé teszi, hogy a forrás-adatbázis motorja kihasználja a partíciók eltávolítását. A forrás-adatbázis táblái nem szükségesek particionálni. Ha a forrás még nincs particionálva, az ADF továbbra is az adatparticionálást fogja használni a Spark átalakítási környezetben a forrás-átalakításban kiválasztott kulcs alapján.

![Forrásoldali](media/data-flow/sourcepart3.png "forrás része")

### <a name="source-batch-size-input-and-isolation-level"></a>Forrás köteg mérete, bemenete és elkülönítési szintje

A forrás-átalakítás **forrás beállításai** területen a következő beállítások befolyásolhatják a teljesítményt:

* A Batch-méret arra utasítja az ADF-et, hogy az adatkészletekben tárolja a memóriában tárolt adatkészleteket. A köteg mérete választható beállítás, és a számítási csomópontokon kifogyhat az erőforrások, ha nem megfelelően vannak kiválasztva.
* A lekérdezés beállítása lehetővé teszi a sorok szűrését a forrásban, mielőtt az adatfolyamot megérkeznek a feldolgozáshoz. Ez gyorsabbá teheti a kezdeti adatgyűjtést. Ha lekérdezést használ, hozzáadhat opcionális lekérdezési mutatókat az Azure SQL-ADATBÁZIShoz, például az olvasás nem véglegesített műveletekhez.
* A nem véglegesített olvasási művelet gyorsabb lekérdezési eredményeket biztosít a forrás átalakításakor

![Forrás](media/data-flow/source4.png "Forrás")

### <a name="sink-batch-size"></a>Fogadó köteg mérete

Az adatfolyamatok soron belüli feldolgozásának elkerüléséhez állítsa be a **Batch-méretet** az Azure SQL db és az Azure SQL DW mosogatók beállítások lapján. Ha a köteg mérete be van állítva, az ADF a megadott méret alapján dolgozza fel a kötegekben lévő adatbázis-írási műveleteket.

![Fogadó](media/data-flow/sink4.png "Fogadó")

### <a name="partitioning-on-sink"></a>Particionálás a fogadón

Még ha nincs is particionálva az adatai a célhelyen, a javasolt, hogy az adatai particionálva legyenek a fogadó átalakításban. A particionált adatmennyiség gyakran gyorsabb betöltést eredményez, így az összes kapcsolat egyetlen csomópont/partíció használatára van kényszerítve. Nyissa meg a fogadó optimalizálás lapját, és válassza a *ciklikus multiplexelés* particionálása lehetőséget a fogadóba írni kívánt partíciók ideális számának kiválasztásához.

### <a name="disable-indexes-on-write"></a>Indexek letiltása íráskor

A folyamat során adjon hozzá egy [tárolt eljárási tevékenységet](transform-data-using-stored-procedure.md) az adatfolyam tevékenysége előtt, amely letiltja a fogadóban írt céltábla indexeit. Az adatfolyam tevékenysége után adjon hozzá egy másik tárolt eljárási tevékenységet, amely engedélyezi ezeket az indexeket.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Az Azure SQL-adatbázis és a DW méretének növeléséhez

Ütemezze a forrás átméretezését, és az Azure SQL DB-t és a DW-t a folyamat futtatása előtt, növelje az átviteli sebességet, és csökkentse az Azure-szabályozást a DTU korlátainak elérése után. A folyamat befejezése után méretezze át az adatbázisokat a normál futtatási sebességre.

### <a name="azure-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Csak az Azure SQL DW esetében] Adatok tömeges betöltésének használata az átmeneti használatával

Ha el szeretné kerülni a sorok közötti beszúrást a DW-be, jelölje be az **előkészítés engedélyezése** a fogadó beállításaiban lehetőséget, hogy az ADF a következőt használja: [autobase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). A Base lehetővé teszi az ADF számára az adatok tömeges betöltését.
* Amikor egy folyamatból hajtja végre az adatfolyam-tevékenységet, ki kell választania egy blobot vagy ADLS Gen2 tárolóhelyet az adatoknak a tömeges betöltés során történő előkészítéséhez.

## <a name="optimizing-for-files"></a>Fájlok optimalizálása

Minden átalakításnál beállíthatja azt a particionálási sémát, amelyet az adatelőállítót használni kíván az optimalizálás lapon.
* Kisebb fájlok esetén előfordulhat, hogy az *önálló partíciók* kiválasztásával időnként jobb és gyorsabb is működhet, mint a kis méretű fájlok particionálását kérő Spark.
* Ha nem rendelkezik elegendő információval a forrásadatok közül, válassza a *ciklikus multiplexelés* particionálás lehetőséget, és állítsa be a partíciók számát.
* Ha az adatai olyan oszlopokkal rendelkeznek, amelyek megfelelő kivonatoló kulcsok lehetnek, válassza a *kivonatoló particionálás*lehetőséget.

Ha az adatelőnézet és a folyamat hibakeresése során hibakeresést végez, a fájl alapú forrás adatkészletek korlátja és mintavételezési mérete csak a visszaadott sorok számára vonatkozik, nem pedig a sorok olvasására. Ez hatással lehet a hibakeresési végrehajtás teljesítményére, és valószínűleg a folyamat meghibásodását okozza.
* A hibakeresési fürtök alapértelmezésben kisméretű egycsomópontos fürtök, ezért ajánlott kis méretű fájlokat használni a hibakereséshez. Nyissa meg a hibakeresési beállításokat, és mutasson az adatai egy kis részhalmazára egy ideiglenes fájl használatával.

    ![Hibakeresési beállítások](media/data-flow/debugsettings3.png "hibakeresési beállításai")

### <a name="file-naming-options"></a>Fájl elnevezési beállításai

Az átalakított adatforgalomnak a blob vagy ADLS-fájlok tárolására való írásának leggyakoribb módja. A fogadóban ki kell választania egy adatkészletet, amely egy tárolóra vagy mappára mutat, nem egy elnevezett fájl. Mivel a leképezési adatfolyam a Spark használatával hajtja végre a végrehajtást, a kimenet több fájlra van felosztva a particionálási séma alapján.

Egy közös particionálási séma a _kimenet egyetlen fájlba_való kiválasztására szolgál, amely az összes kimeneti rész fájlját egyesíti egyetlen fájlba a fogadóban. Ehhez a művelethez a kimenetnek egyetlen fürtcsomópont egyetlen partícióján kell lennie. Ha sok nagyméretű forrásfájlt egyetlen kimeneti fájlba egyesít, elfogyhat a fürt csomópontjának erőforrásai.

A számítási csomópont erőforrásainak kimerítése érdekében tartsa meg az alapértelmezett, optimalizált sémát az adatfolyamban, és adjon hozzá egy másolási tevékenységet a folyamatában, amely egyesíti az összes részét a kimeneti mappából egy új egyetlen fájlba. Ez a technika elkülöníti a fájlok egyesítésének műveletét, és ugyanazt az eredményt éri el, mint a _kimenet beállítása egyetlen fájlba_.

### <a name="looping-through-file-lists"></a>Áthurkolás a fájlok listájáról

Ha a forrás-átalakítás több fájlban is megismétli a több fájlra való átirányítást, akkor a leképezési folyamat jobban végrehajtódik Javasoljuk, hogy használjon helyettesítő karaktereket vagy fájlneveket a forrás-átalakításban. Az adatfolyam-folyamat gyorsabban fog történni, mivel lehetővé teszi a hurok a Spark-fürtön belüli megkötését. További információ: [helyettesítő karakter a forrás-átalakításban](data-flow-source.md#file-based-source-options).

Ha például az 2019-es számú adatfájlok listáját szeretné feldolgozni a Blob Storage egyik mappájában, az alábbi helyettesítő karakter használható a forrás-átalakításban.

```DateFiles/*_201907*.txt```

A helyettesítő karakterek használatával a folyamat csak egy adatfolyam-tevékenységet fog tartalmazni. Ez jobb teljesítményt nyújt, mint a blob-tárolón végzett keresés, amely az összes egyező fájlon megismétli a ForEach-t egy folyamaton belüli végrehajtási adatáramlási tevékenységgel.

## <a name="next-steps"></a>További lépések

Tekintse meg a teljesítménnyel kapcsolatos egyéb adatfolyam-cikkeket:

- [Adatfolyam optimalizálása lap](concepts-data-flow-overview.md#optimize)
- [Adatfolyam-tevékenység](control-flow-execute-data-flow-activity.md)
- [Adatfolyamok teljesítményének figyelése](concepts-data-flow-monitoring.md)
