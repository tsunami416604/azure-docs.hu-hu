---
title: Az adatfolyam teljesítményének és hangolási útmutatójának leképezése Azure Data Factoryban | Microsoft Docs
description: Ismerje meg azokat a főbb tényezőket, amelyek hatással vannak a Azure Data Factory adatforgalmának teljesítményére a leképezési adatfolyamatok használatakor.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 8eb244a0eff1569ac27feae68104db613373463a
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992355"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Adatfolyamatok teljesítményének és hangolási útmutatójának leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A Azure Data Factory leképezési adatfolyamatok egy kód nélküli böngésző felületet biztosítanak az adatátalakítások méretezéséhez, üzembe helyezéséhez és előkészítéséhez.

> [!NOTE]
> Ha nem ismeri az ADF-leképezési adatfolyamatok általános adatait, tekintse meg a jelen cikk elolvasása előtt [az adatfolyamatok áttekintése](concepts-data-flow-overview.md) című témakört.
>

> [!NOTE]
> Ha az ADF felhasználói felületéről fejleszti és teszteli az adatfolyamatokat, ügyeljen arra, hogy a hibakeresési kapcsolót kapcsolja be úgy, hogy valós időben tudja végrehajtani az adatfolyamatokat anélkül, hogy a fürt bemelegítésére kellene várnia.
>

![Hibakeresés gomb](media/data-flow/debugb1.png "Hibakeresés")

## <a name="monitor-data-flow-performance"></a>Adatfolyamok teljesítményének figyelése

A leképezési adatfolyamatok a böngészőben történő tervezésekor az egyes átalakításokat az egyes átalakítások alsó beállítások paneljének adatelőnézet fülére kattintva is kipróbálhatja. A következő lépés az, hogy az adatfolyamatot teljes körűen tesztelje a folyamat-tervezőben. Vegyen fel egy végrehajtási adatfolyam-tevékenységet, és használja a hibakeresés gombot az adatfolyam teljesítményének teszteléséhez. A folyamat ablakának alsó ablaktábláján egy szemüveg ikon jelenik meg a "műveletek" alatt:

![Adatfolyam-figyelő](media/data-flow/mon002.png "Adatfolyam-figyelő 2")

Erre az ikonra kattintva megjelenik az adatfolyam végrehajtási terve és az azt követő teljesítmény profilja. Ezekkel az adatokkal megbecsülheti az adatáramlás teljesítményét a különböző méretű adatforrások esetében. Vegye figyelembe, hogy az általános teljesítmény-számításokban 1 percet is igénybe vehet a fürt feladatának végrehajtásához szükséges idő, és ha az alapértelmezett Azure Integration Runtime használja, előfordulhat, hogy 5 percet kell felvennie a fürt Felpörgetési idejére is.

![Adatfolyam-figyelés](media/data-flow/mon003.png "Adatfolyam-figyelő 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimalizálás Azure SQL Database és Azure SQL Data Warehouse

![Forrás része](media/data-flow/sourcepart3.png "Forrás része")

### <a name="partition-your-source-data"></a>A forrásadatok particionálása

* Lépjen az "optimalizálás" elemre, és válassza a "forrás" lehetőséget. Állítsa be egy adott tábla oszlopát vagy típusát egy lekérdezésben.
* Ha az "oszlop" lehetőséget választotta, válassza ki a partíció oszlopot.
* Állítsa be az Azure SQL-ADATBÁZISsal létesített kapcsolatok maximális számát is. Nagyobb beállítást is kipróbálhat, ha párhuzamos kapcsolatot szeretne létesíteni az adatbázissal. Bizonyos esetekben azonban előfordulhat, hogy a gyorsabb teljesítmény korlátozott számú kapcsolattal jár.
* A forrás-adatbázis táblái nem szükségesek particionálni.
* Ha olyan lekérdezést állít be a forrás-átalakításban, amely megfelel az adatbázis-táblázat particionálási sémájának, lehetővé teszi, hogy a forrás-adatbázis motorja kihasználja a partíciók eltávolítását.
* Ha a forrás még nincs particionálva, az ADF továbbra is az adatparticionálást fogja használni a Spark átalakítási környezetben a forrás-átalakításban kiválasztott kulcs alapján.

### <a name="set-batch-size-and-query-on-source"></a>Köteg méretének és lekérdezésének beállítása a forráson

![Forrás](media/data-flow/source4.png "Forrás")

* A köteg méretének beállítása arra utasítja az ADF-et, hogy sorok szerint tárolja az adatkészletekben tárolt adatkészleteket a memóriában. Ez egy választható beállítás, és a számítási csomópontokon kifogyhat az erőforrások, ha azok nem megfelelő méretűek.
* A lekérdezés beállítása lehetővé teszi a sorok szűrését a forrásnál, még mielőtt a rendszer megkezdi az adatforgalom feldolgozását, ami gyorsabbá teheti a kezdeti adatgyűjtést.
* Ha lekérdezést használ, hozzáadhat opcionális lekérdezési tippeket az Azure SQL-ADATBÁZIShoz, például a nem véglegesített OLVASÁSI művelethez.

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>Az SQL-adatkészletek forrás-átalakítási beállításainak elkülönítési szintjének beállítása

* A nem véglegesített olvasási művelet gyorsabb lekérdezési eredményeket biztosít a forrás átalakításakor

![Elkülönítési szint](media/data-flow/isolationlevel.png "Elkülönítési szint")

### <a name="set-sink-batch-size"></a>Fogadó köteg méretének beállítása

![Fogadó](media/data-flow/sink4.png "Fogadó")

* Az adatfolyamatok soron belüli feldolgozásának elkerülése érdekében állítsa be a "batch size" kifejezést az Azure SQL DB fogadó beállításainál. Ez azt jelzi, hogy az ADF az adatbázis-írásokat kötegekben dolgozza fel a megadott méret alapján.

### <a name="set-partitioning-options-on-your-sink"></a>Particionálási beállítások megadása a fogadón

* Akkor is, ha a cél Azure SQL DB-táblákban nem particionálja az adatait, lépjen az optimalizálás lapra, és állítsa be a particionálást.
* Nagyon gyakran, egyszerűen azt mondja, hogy az ADF a Spark-végrehajtási fürtök ciklikus multiplexelés-particionálását használja, és nem kényszeríti ki az összes kapcsolatot egyetlen csomópontból vagy partícióból.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>A számítási motor méretének növeléséhez Azure Integration Runtime

![Új IR](media/data-flow/ir-new.png "Új IR")

* Növelje a magok számát, ami növeli a csomópontok számát, és nagyobb feldolgozási teljesítményt biztosít az Azure SQL-adatbázis lekérdezéséhez és írásához.
* Próbálja ki a "számításra optimalizált" és a "memória optimalizálása" lehetőséget, hogy több erőforrást alkalmazzon a számítási csomópontokra.

### <a name="unit-test-and-performance-test-with-debug"></a>Az egység tesztelése és a teljesítmény tesztelése hibakereséssel

* Amikor az egység teszteli az adatforgalmat, állítsa be az "adatfolyam-hibakeresés" gombot a következőre:.
* Az adatáramlás-tervezőn belül az átalakítások adatelőnézet lapján megtekintheti az átalakítási logika eredményét.
* Az egység tesztelése az adatfolyamatokat a folyamat-tervezőből egy adatfolyamati tevékenységnek a folyamat tervezési vásznon való elhelyezésével, és a "hibakeresés" gomb használatával tesztelheti.
* A hibakeresési módban végzett tesztelés egy élő, melegen futó fürt környezetében fog működni, anélkül, hogy meg kellene várnia egy igény szerinti fürt üzembe helyezését.

### <a name="disable-indexes-on-write"></a>Indexek letiltása íráskor
* Használjon egy ADF-feldolgozási folyamat tárolt eljárási tevékenységét az adatfolyam tevékenysége előtt, amely letiltja a fogadón lévő, a fogadó táblán írt indexeket.
* Az adatfolyam tevékenysége után adjon hozzá egy másik tárolt proc-tevékenységet, amely lehetővé tette az indexek használatát.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Növelje az Azure SQL-adatbázis méretét
* Ütemezze a forrás átméretezését, és az Azure SQL DB-t a folyamat futtatása előtt állítsa be, hogy növelje az átviteli sebességet, és csökkentse az Azure-szabályozást, ha eléri a DTU határértékeket.
* A folyamat befejezését követően átméretezheti az adatbázisokat a normál futtatási sebességre.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse optimalizálása

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>Adatok tömeges betöltésének használata az átmeneti használatával

* Az adatfolyamatok soron belüli feldolgozásának elkerülése érdekében állítsa be a fogadó beállításaiban az "átmeneti" lehetőséget, hogy az ADF kihasználja a-alapú adatsorokat a DW-be. Ez arra utasítja az ADF-t, hogy használjon egy albase-t, hogy az adatok tömegesen is betölthetők legyenek.
* Amikor egy folyamatból hajtja végre az adatfolyam-tevékenységet, és az előkészítés be van kapcsolva, a tömeges betöltéshez ki kell választania az átmeneti adatok blob-tárolójának helyét.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Az Azure SQL DW méretének növelésével

* A folyamat futtatása előtt ütemezze a forrás és a fogadó Azure SQL DW átméretezését, hogy növelje az átviteli sebességet, és csökkentse az Azure-szabályozást a DWU korlátainak elérése után.

* A folyamat befejezését követően átméretezheti az adatbázisokat a normál futtatási sebességre.

## <a name="optimize-for-files"></a>Fájlok optimalizálása

* Megadhatja, hogy az ADF hány partíciót fog használni. Az egyes forrásokon & a fogadó transzformációk, valamint az egyes átalakítások esetében beállíthatja a particionálási sémát. Kisebb fájlok esetében előfordulhat, hogy a "single Partition" (egyetlen partíció) lehetőség kiválasztásával jobb és gyorsabb lehet a kis méretű fájlok particionálását kérő Spark használata.
* Ha nem rendelkezik elegendő információval a forrás adatairól, válassza a "ciklikus multiplexelés" particionálás lehetőséget, és állítsa be a partíciók számát.
* Ha megkeresi az adatokat, és úgy találja, hogy van olyan oszlopa, amely jó kivonatoló kulcs lehet, használja a kivonatoló particionálási lehetőséget.
* Ha az adatelőnézet és a folyamat hibakeresése során hibakeresést végez, vegye figyelembe, hogy a fájl alapú forrás-adatkészletek korlát-és mintavételezési mérete csak a visszaadott sorok számára vonatkozik, nem pedig az olvasott sorok száma. Ez azért fontos, mert ez hatással lehet a hibakeresési végrehajtás teljesítményére, és valószínűleg a folyamat meghibásodását okozza.
* Ne feledje, hogy a hibakeresési fürtök alapértelmezésben kisméretű egycsomópontos fürtök, ezért a hibakereséshez használjon ideiglenes kis fájlokat. Nyissa meg a hibakeresési beállításokat, és mutasson az adatai egy kis részhalmazára egy ideiglenes fájl használatával.

![Hibakeresési beállítások](media/data-flow/debugsettings3.png "Hibakeresési beállítások")

### <a name="file-naming-options"></a>Fájl elnevezési beállításai

* Az átalakított adatokat az ADF-leképezési adatfolyamatokban való írásának alapértelmezett jellege egy blob vagy ADLS társított szolgáltatással rendelkező adatkészletbe írás. Ezt az adatkészletet úgy kell beállítani, hogy egy mappára vagy tárolóra mutasson, nem egy elnevezett fájlra.
* Az adatfolyamatok a Azure Databricks Sparkot használják a végrehajtáshoz, ami azt jelenti, hogy a kimenet több fájlra lesz felosztva az alapértelmezett Spark-particionálás vagy a explicit módon kiválasztott particionálási séma alapján.
* Az ADF adatforgalmának nagyon gyakori művelete, hogy a "kimenet egyetlen fájlba" lehetőséget választja, hogy az összes kimeneti rész fájljai egyesítve legyenek egyetlen kimeneti fájlba.
* Ez a művelet azonban megköveteli, hogy a kimenet egyetlen fürtcsomópont egyetlen partícióján legyen csökkentve.
* Tartsa szem előtt ezt a népszerű lehetőséget választva. Kifogyhat a fürtcsomópontok erőforrásai, ha sok nagyméretű forrásfájlt egyesít egyetlen kimeneti fájlba.
* A számítási csomópont-erőforrások kimerítésének elkerülése érdekében az ADF-ben megtarthatja az alapértelmezett vagy explicit particionálási sémát, amely optimalizálja a teljesítményt, majd hozzáad egy későbbi másolási tevékenységet a kimeneti mappából egy új, egyetlen fájl. Ez a technika lényegében elkülöníti a fájlok egyesítésének műveletét, és ugyanazt az eredményt éri el, mint a "kimenet egyetlen fájlba" beállítás.

### <a name="looping-through-file-lists"></a>Áthurkolás a fájlok listájáról

A legtöbb esetben az ADF-ben lévő adatfolyamatok hatékonyabban futnak egy folyamattal, amely lehetővé teszi, hogy az adatfolyam forrásának átalakítása több fájlra is megismételhető legyen. Más szóval a forrásban lévő helyettesítő karakterek vagy fájllista használata javasolt az adatforgalomban, mint a fájlok nagy listájának megismétlése a folyamat ForEach használatával, amely minden egyes iteráción végrehajt egy végrehajtási adatfolyamatot. Az adatfolyam folyamata gyorsabban elvégezhető, mivel lehetővé teszi, hogy a hurok az adatfolyamaton belül is megtörténjen.

Ha például az 2019-es számú adatfájlok listáját szeretném feldolgozni egy Blob Storage mappában, akkor nagyobb teljesítményre lehet szükség a folyamat végrehajtásához, és egy olyan helyettesítő karaktert kell használnia a forrásban, mint az alábbi. :

```DateFiles/*_201907*.txt```

Ez jobb teljesítményt nyújt, mint a blob Store-ban egy olyan folyamat, amely az összes egyező fájlon megismétli az összes megfeleltetett fájlt egy olyan ForEach használatával, amelyen belül végrehajtja az adatfolyam-tevékenységet.

## <a name="next-steps"></a>További lépések

Tekintse meg a teljesítménnyel kapcsolatos egyéb adatfolyam-cikkeket:

- [Adatfolyam optimalizálása lap](concepts-data-flow-optimize-tab.md)
- [Adatfolyam-tevékenység](control-flow-execute-data-flow-activity.md)
- [Adatfolyamok teljesítményének figyelése](concepts-data-flow-monitoring.md)
