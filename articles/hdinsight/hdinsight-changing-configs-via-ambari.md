---
title: Az Apache Ambari – Azure HDInsight-fürt konfigurációjának optimalizálása
description: Az Apache Ambari webes felhasználói felület használatával konfigurálhatja és a HDInsight-fürtök optimalizálása.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: ashish
ms.openlocfilehash: 82995f2cc8facac9bef6f8c84c9667775ac81463
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51038518"
---
# <a name="use-ambari-to-optimize-hdinsight-cluster-configurations"></a>HDInsight-fürt konfigurációjának optimalizálása az Ambari használatával

HDInsight Apache Hadoop-fürtöket kínál a nagy méretű adatok feldolgozása alkalmazásokhoz. Kezelését, megfigyelését és ezek bonyolult, több csomópontos fürtök optimalizálása kihívást jelenthet. [Az Apache Ambari](http://ambari.apache.org/) egy webes felület, kezelni és megfigyelni a HDInsight Linux-fürtöket.  A Windows-fürtök esetén használja az Ambari [REST API-val](hdinsight-hadoop-manage-ambari-rest-api.md).

Az Ambari webes kezelőfelületen bevezetésért lásd: [kezelése a HDInsight-fürtök az Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md)

Jelentkezzen be az Ambari, `https://CLUSTERNAME.azurehdidnsight.net` a fürt hitelesítő adataival. A kezdeti képernyőn megjelenik egy áttekintő irányítópult.

![Ambari-irányítópult](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

Az Ambari webes Felülettel gazdagépek, szolgáltatások, riasztások, konfigurációk és nézetek kezelésére használható. Az Ambari hozzon létre egy HDInsight-fürt, szolgáltatások frissítése, platformok és verziók kezelése, leszerelése vagy recommission gazdagépek vagy szolgáltatások hozzáadása a fürt nem használható.

## <a name="manage-your-clusters-configuration"></a>A fürt konfigurációjának kezelése

Konfigurációs beállítások segítségével egy adott szolgáltatás tökéletesítéséhez. A szolgáltatás konfigurációs beállításainak módosításához válassza ki a szolgáltatást, az a **szolgáltatások** oldalsáv (a bal oldalon), majd lépjen a **Configs** lapon a szolgáltatás részletei lapon.

![Szolgáltatások oldalsáv](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Módosítsa a NameNode Java. generace

A NameNode Java. generace például a terhelés a fürt, a fájlok számát és a blokkok számú számos tényezőtől függ. Az alapértelmezett méret 1 GB-os nagyszerűen működik a legtöbb fürt, bár egyes számítási feladatok több vagy kevesebb memóriát igényelhet. 

A NameNode Java. generace módosítása:

1. Válassza ki **HDFS** oldalsávon a szolgáltatások, és keresse meg a **Configs** fülre.

    ![HDFS-konfiguráció](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

1. Keresse meg a beállítás **NameNode Java. generace**. Is használhatja a **szűrő** szövegmezőbe írja be, és a egy adott beállítást keres. Válassza ki a **toll** a beállítás neve melletti ikonra.

    ![NameNode Java. generace](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

1. Írja be az új értéket a szövegmezőbe, és nyomja le az **Enter** menteni a módosítást.

    ![Szerkesztés NameNode Java. generace](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

1. A NameNode Java halommemória mérete 1 GB-os megváltozott 2 GB-ra.

    ![Szerkesztett NameNode Java. generace](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. A módosítások mentéséhez kattintson a zöld a **mentése** gombra a konfiguráció képernyő felső részén.

    ![Változtatások mentése](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="hive-optimization"></a>Hive-optimalizálás

A következő szakaszok ismertetik a konfigurációs beállítások teljes Hive teljesítményének optimalizálásához.

1. Hive-konfigurációs paraméterek módosításához válassza **Hive** szolgáltatások oldalsávon.
1. Keresse meg a **Configs** fülre.

### <a name="set-the-hive-execution-engine"></a>Állítsa be a Hive-végrehajtó motor

Hive biztosít két végrehajtási motorok: MapReduce és a tezben futtatja. Tez gyorsabb, mint a MapReduce. HDInsight Linux-fürtöket Tez végrehajtómotor alapértelmezett rendelkezik. A végrehajtó motor módosítása:

1. A Hive **Configs** fülre, írja be a **végrehajtóprogramja** szót a Szűrő mezőbe.

    ![Keresőmotor-végrehajtás](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

1. A **optimalizálási** tulajdonság alapértelmezett értéke **Tez**.

    ![Optimalizálás - Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Leképező hangolása

Hadoop próbál felosztása (*térkép*) több fájlt és a létrejövő folyamat az egyetlen fájl fájlok párhuzamosan. Leképező száma attól függ, hogy a megszakítások számát. Az alábbi két konfigurációs paramétereket meghajtó a Tez végrehajtómotor a megszakítások száma:

* `tez.grouping.min-size`: Csoportosított valószínűségét, 16 MB-ot (16,777,216 bájt) alapértelmezett értékkel mérete alacsonyabb korlátot.
* `tez.grouping.max-size`: A felső határ csoportosított valószínűségét, mérete 1 GB (1,073,741,824 bájt) alapértelmezett értékkel.

Teljesítmény tapasztalatok, mint csökkentése mindkét javíthatja a késés, nagyobb átviteli sebességet növelheti ezeket a paramétereket.

Például beállítása négy eseményleképező feladatok adatméret 128 MB-ot, akkor állítania mindkét paraméter 32 MB-ra minden (33,554,432 bájt).

1. A korlát paraméterek módosításához lépjen a **Configs** a Tez szolgáltatás lapján. Bontsa ki a **általános** panelen, és keresse meg a `tez.grouping.max-size` és `tez.grouping.min-size` paramétereket.

1. Mindkét paraméter beállítása **33,554,432** bájtok (32 MB).

    ![Tez csoportosítási méretek](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Ezek a változások Tez-feladatok hatással a kiszolgáló között. Az optimális eredmény lekéréséhez válassza ki a megfelelő paraméterértékek.

### <a name="tune-reducers"></a>Csökkentő hangolása

ORC és Snappy egyaránt kínál a nagy teljesítményű. Van azonban, Hive előfordulhat, hogy túl kevés csökkentő alapértelmezés szerint szűk keresztmetszetek.

Tegyük fel például, van egy 50 GB-os bemeneti adatok méretét. 1 GB-os, hogy az adatok ORC Snappy tömörítést formázása. Hive becslése szükség csökkentő száma: (leképező bemenet bájtok száma / `hive.exec.reducers.bytes.per.reducer`).

Az alapértelmezett beállításokkal az ebben a példában 4 csökkentő.

A `hive.exec.reducers.bytes.per.reducer` paraméter megadja a feldolgozott nyomáscsökkentő bájtok száma. Az alapértelmezett érték: 64 MB-ot. Le ezt az értéket hangolása növeli a párhuzamosságot és javíthatja a teljesítményt. Hangolási túl alacsony sikerült túl sok csökkentő esetlegesen negatívan befolyásoló teljesítmény is előállíthat. Ez a paraméter az adott követelmények, a tömörítési beállítások és más környezeti tényezők alapján.

1. A paraméter módosítani, keresse meg a Hive **Configs** lapra, és keresse meg a **nyomáscsökkentő adatot** paraméter a beállítások lapon.

    ![Nyomáscsökkentő adatot](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
1. Válassza ki **szerkesztése** módosítsa a 128 MB-ra (134,217,728 bájt) értéket, és nyomja le az **Enter** mentéséhez.

    ![/ Nyomáscsökkentő – szerkesztett adatokat](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Egy bemeneti mérete 1 024 MB, 128 MB-nyi adatot nyomáscsökkentő, az adott nincsenek 8 csökkentő (1024/128).

1. Értékkel a **nyomáscsökkentő adatot** paraméter csökkentő, kedvezőtlen hatással lenne a lekérdezési teljesítmény nagy számú eredményezhet. Korlátozza a maximális számát csökkentő, állítsa `hive.exec.reducers.max` egy megfelelő értékre. Az alapértelmezett érték: 1009.

### <a name="enable-parallel-execution"></a>Párhuzamos végrehajtás engedélyezése

A Hive-lekérdezések végrehajtása egy vagy több lépésben történik. A független szakaszokra párhuzamosan is futtatható, ha, amely növeli a lekérdezések teljesítményét.

1.  Ahhoz, hogy a párhuzamos lekérdezés-végrehajtás, keresse meg a Hive **Config** lapra, és keresse meg a `hive.exec.parallel` tulajdonság. Az alapértelmezett értéke FALSE (hamis). Módosítsa az értéket Igaz értékre, és nyomja le az **Enter** mentheti az értéket.
 
1.  Korlátozza az egyidejűleg futtatandó feladatok számát, módosítsa a `hive.exec.parallel.thread.number` tulajdonság. Az alapértelmezett értéke 8.

    ![Hive-exec párhuzamos](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Engedélyezze a vektorizációt

Hive feldolgozza az adatokat soronként. Vektorizációt 1024 sorok helyett egy olyan sor egységekben dolgozza fel az adatokat a Hive egyszerre irányítja. Csak akkor alkalmazható, az ORC fájlformátumba vektorizációt.

1. Ahhoz, hogy egy vectorized lekérdezés-végrehajtás, keresse meg a Hive **Configs** lapra, és keresse meg a `hive.vectorized.execution.enabled` paraméter. Az alapértelmezett értéke igaz, a Hive 0.13.0 vagy újabb.
 
1. Ahhoz, hogy a lekérdezés csökkentse oldalára vectorized végrehajtását, állítsa be a `hive.vectorized.execution.reduce.enabled` paraméter igaz. Az alapértelmezett értéke FALSE (hamis).

    ![Hive vectorized végrehajtás](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Költség-alapú optimalizálási (CBO) engedélyezése

Alapértelmezés szerint a Hive, egy optimális lekérdezés végrehajtási terv található szabályai követi. Költségalapú optimalizálási (CBO) értékeli ki a lekérdezés végrehajtása több tervek költség rendel minden terv, majd egy lekérdezést a legolcsóbb csomag határozza meg.

Ahhoz, hogy CBO, keresse meg a Hive **Configs** lapra, és keressen rá a `parameter hive.cbo.enable`, a váltógomb, majd váltson **a**.

![CBO config](./media/hdinsight-changing-configs-via-ambari/cbo.png)

Az alábbi kiegészítő konfigurációs paramétereket Hive-lekérdezés teljesítménye növelhető, ha a CBO engedélyezve van:

* `hive.compute.query.using.stats`

    Ha igaz értékű, Hive használ választ például egyszerű lekérdezési statisztika a metaadattár tárolt `count(*)`.

    ![CBO stats](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Ha engedélyezve van a CBO oszlopstatisztika jönnek létre. Hive oszlopstatisztika, amelyeket a rendszer a metaadattár, optimalizálni a lekérdezéseket használ. Az egyes oszlopok oszlop statisztikai adatainak beolvasása hosszabb időt vesz igénybe, ha az oszlopok száma túl magas. Ha értéke HAMIS, ez a beállítás letiltja a metaadattár beolvasásakor oszlopok statisztikáit.

    ![Hive-stats set oszlopstatisztikák](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Egyszerű partíción statisztikáit, például a sorok, az adatok mérete és a fájlméret száma metaadattár vannak tárolva. Ha igaz értéke esetén a statisztikák vannak mavenből lekért metaadattár partíció. Hamis érték esetén a fájl mérete beolvassa a fájlrendszerből, és a sor séma beolvassa a sorok száma.

    ![Hive-stats set partíció stats](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Köztes tömörítés engedélyezése

Térkép feladatok létrehozása a nyomáscsökkentő feladatok által használt köztes fájlok. Köztes tömörítés a köztes mérete csökken.

Hadoop-feladatok rendszerint bottlenecked i/o. Adatok tömörítése i/o- és általános hálózati átvitel is gyorsabb.

A rendelkezésre álló tömörítési típusok a következők:

| Formátum | Eszköz | Algoritmus | Fájlkiterjesztés | Felosztható? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | .GZ | Nem |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Igen |
| LZO | Lzop | LZO | .lzo | Igen, ha indexelt |
| Snappy | – | Snappy | Snappy | Nem |

Általános szabály a tömörítéses módszer felosztható, akkor fontos, ellenkező esetben a nagyon kevés leképező létrejön. Ha a bemeneti adatokat a szöveg, `bzip2` a legjobb lehetőség. ORC formátum Snappy a leggyorsabb tömörítési beállítás.

1. Ahhoz, hogy a köztes tömörítés, keresse meg a Hive **Configs** lapra, és állítsa a `hive.exec.compress.intermediate` paraméter igaz. Az alapértelmezett értéke FALSE (hamis).

    ![Hive exec compress köztes](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]
    > A köztes fájlok tömörítése, válassza ki a tömörítési kodek költsége alacsonyabb CPU-még akkor is, ha a kodeket nem rendelkezik egy nagy tömörítési kimeneti.

1. A köztes tömörítési kodeket, adja hozzá az egyéni tulajdonság `mapred.map.output.compression.codec` , a `hive-site.xml` vagy `mapred-site.xml` fájlt.

1. Egyéni beállítás hozzáadásához:

    a. Keresse meg a Hive **Configs** lapra, és válassza a **speciális** fülre.

    b. Alatt a **speciális** lapon keresse meg és bontsa ki a **egyéni hive-hely** ablaktáblán.

    c. Kattintson a hivatkozásra **tulajdonság hozzáadása** az egyéni hive – hely panel alján.

    d. Adja meg a tulajdonság hozzáadása ablak `mapred.map.output.compression.codec` kulcsként és `org.apache.hadoop.io.compress.SnappyCodec` értékeként.

    e. Kattintson a **Hozzáadás** parancsra.

    ![Egyéni Hive-tulajdonság](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Ez lesz fájltömörítés a köztes használó Snappy tömörítést. Miután hozzáadta a tulajdonság, az egyéni hive-hely ablaktáblán jelenik meg.

    > [!NOTE]
    > Ezzel az eljárással módosítja a `$HADOOP_HOME/conf/hive-site.xml` fájlt.

### <a name="compress-final-output"></a>Végeredmény tömörítése

A végeredmény Hive is tömöríthető.

1. A végeredmény Hive tömöríteni, keresse meg a Hive **Configs** lapra, és állítsa a `hive.exec.compress.output` paraméter igaz. Az alapértelmezett értéke FALSE (hamis).

1. Válassza ki a kimeneti tömörítési kodeket, adja hozzá a `mapred.output.compression.codec` egyéni tulajdonság egyéni hive-hely megjelenítő, az előző szakasz 3. lépésben leírtak szerint.

    ![Egyéni Hive-tulajdonság](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Spekulatív végrehajtás kockázatának csökkentése engedélyezése

Spekulatív végrehajtás kockázatának csökkentése annak érdekében, hogy észlelni és tiltólistára a lassú lefutású feladat követőt, miközben a teljes feladat-végrehajtási egyéni feladat eredményei optimalizálásával ismétlődő feladatok bizonyos számú indít.

Spekulatív végrehajtás kockázatának csökkentése nem kapcsolható be a bemeneti nagy mennyiségű hosszú ideig futó MapReduce feladatok.

* Ahhoz, hogy spekulatív végrehajtás kockázatának csökkentése, keresse meg a Hive **Configs** lapra, és állítsa a `hive.mapred.reduce.tasks.speculative.execution` paraméter igaz. Az alapértelmezett értéke FALSE (hamis).

    ![Hive mapred csökkentheti a feladatok spekulatív végrehajtás kockázatának csökkentése](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>A dinamikus partíciók hangolása

Hive lehetővé teszi, hogy a dinamikus partíciók létrehozása, ha a rekordok beszúrása egy táblázat minden egyes partíción számítógépnevekről nélkül. Ez a hatékony funkció, bár eredményezhet nagy számú partíciót és a egy nagy mennyiségű fájlt az egyes partíciók létrehozása.

1. A Hive, a dinamikus partíciók, ehhez a `hive.exec.dynamic.partition` lehet, hogy a paraméter értéke true (alapértelmezett).

1. Módosítsa a dinamikus partíció módot *szigorú*. Legalább egy partíciót szigorú módban nem lehet statikus. Ez megakadályozza, hogy a partíció-szűrő a WHERE záradék nélkül lekérdezések, *szigorú* megakadályozza, hogy a lekérdezések, amelyek az összes partíció beolvasása. Keresse meg a Hive **Configs** lapra, és állítsa `hive.exec.dynamic.partition.mode` való **szigorú**. Az alapértelmezett érték **nonstrict**.
 
1. Létrehozandó dinamikus partíciók számának korlátozásához, módosítsa a `hive.exec.max.dynamic.partitions` paraméter. Az alapértelmezett érték: 5000-es.
 
1. A dinamikus partíciók száma csomópontonként teljes számának korlátozásához, módosítsa `hive.exec.max.dynamic.partitions.pernode`. Az alapértelmezett érték: 2000.

### <a name="enable-local-mode"></a>Helyi mód engedélyezése

Helyi mód lehetővé teszi, hogy a Hive feladat feladatokat végez, egyetlen gépen vagy más néven egyetlen folyamat. Ez javítja a lekérdezési teljesítményt, ha a bemeneti adatok kicsi, és lekérdezésekhez feladatok elindítása gyűjteményértékelést kelljen végezni a teljes lekérdezés végrehajtása jelentős részéért használ fel.

Helyi mód engedélyezéséhez vegye fel a `hive.exec.mode.local.auto` paramétert az egyéni hive – hely panel, a 3. lépésében leírtak a [köztes tömörítésének engedélyezéséhez a](#enable-intermediate-compression) szakaszban.

![Hive-végrehajtási mód helyi automatikus](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Készlet egyetlen MapReduce MultiGROUP szerint

Ha ez a tulajdonság értéke true, közös csoportosítási kulcsokkal MultiGROUP a lekérdezés egyetlen MapReduce feladatot hoz létre.  

Ez a viselkedés engedélyezéséhez vegye fel a `hive.multigroupby.singlereducer` egyéni hive – hely panel, 3. lépésében leírtak paramétert a [köztes tömörítésének engedélyezéséhez a](#enable-intermediate-compression) szakaszban.

![Hive állította egyetlen MapReduce MultiGROUP](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>További Hive-optimalizálást

A következő szakaszokban további Hive kapcsolatos optimalizálást is megadhatja.

#### <a name="join-optimizations"></a>Csatlakozás az optimalizálási lehetőségek

A Hive, az alapértelmezett illesztési típus egy *shuffle illesztési*. A Hive speciális leképező olvassa a bemeneti, és egy illesztési kulcs/érték pár köztes fájlok küldik. Hadoop rendezi, és egyesíti a párok a shuffle szakaszban. Ebben a szakaszban shuffle drága. Válassza a jobb oldali illesztési adatai alapján jelentősen javíthatja a teljesítményt.

| Csatlakozás típusa | Mikor: | Hogyan | Hive-beállítások | Megjegyzések |
| -- | -- | -- | -- | -- |
| Shuffle illesztés | <ul><li>Alapértelmezett választás</li><li>Mindig működik.</li></ul> | <ul><li>A tábla egyik részét olvassa be</li><li>Gyűjtők és illesztési kulcs rendezése</li><li>Minden egyes csökkentse egy gyűjtő küld</li><li>Csatlakozás a csökkentse oldalon történik</li></ul> | Nincs jelentős Hive beállítása szükséges | Minden alkalommal működik |
| Térkép illesztés | <ul><li>Egy tábla illeszkednek a memóriában</li></ul> | <ul><li>Beolvassa a kis táblák memória kivonatoló táblába</li><li>A nagy méretű fájl keresztül Streamek</li><li>Minden egyes rekord kivonattábla csatlakozik</li><li>Önálló eseményleképező szerint vannak illesztések</li></ul> | `hive.auto.confvert.join=true` | Rendkívül gyors, de korlátozott |
| Rendezés egyesítési gyűjtőbe | Ha mindkét táblázatot: <ul><li>Azonos rendezve</li><li>Azonos bucketed</li><li>Csatlakozás a rendezett/bucketed oszlop alapján</li></ul> | Minden egyes folyamat: <ul><li>A gyűjtő olvas minden táblához</li><li>Feldolgozza a legalacsonyabb értékű</li></ul> | `hive.auto.convert.sortmerge.join=true` | Nagyon hatékony |

#### <a name="execution-engine-optimizations"></a>Végrehajtási motort optimalizálási lehetőségek

A Hive-végrehajtó motor optimalizálásához további javaslatok:

| Beállítás | Ajánlott | HDInsight alapértelmezett |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | Igaz értéket a biztonságosabb, lassabb; = FALSE = gyorsabban | hamis |
| `tez.am.resource.memory.mb` | A legtöbb 4 GB-os felső határérték | Automatikusan beállított |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="pig-optimization"></a>A Pig-optimalizálás

A Pig-tulajdonságok is lehet módosítani az Ambari webes Felülettel Pig Lekérdezések finomhangolása. A Pig-tulajdonságokat az Ambari Pig tulajdonságainak módosítása közvetlenül módosítja a `/etc/pig/2.4.2.0-258.0/pig.properties` fájlt.

1. Pig-tulajdonságok módosításához nyissa meg a Pig **Configs** lapra, és ezután bontsa ki a **speciális pig-tulajdonságok** ablaktáblán.

1. Keresse meg, és állítsa vissza a módosítani kívánt tulajdonság értékének módosítása.

1. Válassza ki **mentése** mentse az új értéket az ablak felső jobb oldalán. Egyes tulajdonságok szükség lehet a szolgáltatás újraindítását.

    ![Speciális pig-tulajdonságok](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]
> A munkamenet-szintű beállításokat alaprekordban szereplők felülírásához a `pig.properties` fájlt.

### <a name="tune-execution-engine"></a>Végrehajtóprogramja hangolása

Két végrehajtási motorok Pig-parancsfájlok végrehajtása érhetők el: a MapReduce és a tezben futtatja. Tez optimalizált motor, és jóval gyorsabb, mint a MapReduce.

1. A végrehajtó motor módosítása a a **speciális pig-tulajdonságok** ablaktáblán keresse meg a tulajdonság `exectype`.

1. Az alapértelmezett érték **MapReduce**. Módosítsa a következőre **Tez**.


### <a name="enable-local-mode"></a>Helyi mód engedélyezése

Hasonló a Hive, helyi mód gyorsabb feladatok viszonylag kisebb mennyiségű adatot szolgál.

1. A helyi mód engedélyezéséhez állítsa `pig.auto.local.enabled` való **igaz**. Az alapértelmezett értéke FALSE (hamis).

1. Feladatok és a egy bemeneti adatok mérete kisebb, mint a `pig.auto.local.input.maxbytes` tulajdonság értéke kisebb feladatokat kell tekinteni. Az alapértelmezett érték: 1 GB.


### <a name="copy-user-jar-cache"></a>Másolja a felhasználói jar-gyorsítótár

A Pig másolja át az UDF-EK számára szükséges egy elosztott gyorsítótár elérhetővé teheti őket a feladat csomópontok JAR-fájlok. A JAR-fájlok kivételével nem gyakran változnak. Ha engedélyezve van, a `pig.user.cache.enabled` beállítás lehetővé teszi, hogy el kell helyezni egy gyorsítótárat is használható ugyanazon felhasználó által futtatott feladatok újra fel JAR-fájlok kivételével. Ez egy kisebb feladat teljesítmény növekedését eredményezi.

1. Ha engedélyezni szeretné, állítsa be `pig.user.cache.enabled` igaz értékre. Az alapértelmezett érték FALSE (hamis).

1. Állítsa be a kiinduló elérési útját a gyorsítótárazott JAR-fájlok kivételével, állítsa `pig.user.cache.location` az az alapútvonal. Az alapértelmezett érték `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>A memóriabeállításokat teljesítményének optimalizálása

A következő memóriabeállításokat segítségével, a Pig-parancsfájl teljesítményének optimalizálásához.

* `pig.cachedbag.memusage`: A tulajdonságcsomag számára lefoglalt memória mennyisége. A tulajdonságcsomag rekordok gyűjteménye. Egy rekord egy rendezett készlet mezőket, és az adatok az adott mező kitöltése. Ha az adatok egy tasakban meghaladja a lefoglalt memória, azt kiömlött lemezre. Az alapértelmezett érték: 0.2-es, amely a rendelkezésre álló memória 20 %-os. Ez a memória közösen használja az összes csomag egy alkalmazásban.

* `pig.spill.size.threshold`: Biztosítékot nagyobb, mint a túlfolyó adatbázisméret küszöbértéke (bájtban) vannak kiömlött lemezre. Az alapértelmezett érték: 5 MB-ot.


### <a name="compress-temporary-files"></a>Ideiglenes fájlok tömörítése

A Pig ideiglenes fájlt létrehoz a feladat végrehajtása során. Az ideiglenes fájlok tömörítése eredményez, a teljesítmény nő, amikor lemezre fájlok írása és olvasása. A következő beállítások segítségével ideiglenes fájlok tömörítése.

* `pig.tmpfilecompression`: Ha az értéke igaz, lehetővé teszi az ideiglenes fájlok tömörítési. Az alapértelmezett értéke FALSE (hamis).

* `pig.tmpfilecompression.codec`: A tömörítési kodek az ideiglenes fájlok tömörítése használandó. A javasolt tömörítési kodek a LZO és a Snappy alacsonyabb CPU-felhasználás.

### <a name="enable-split-combining"></a>Engedélyezze a vegyes csoportba foglalása

Ha engedélyezve van, kis méretű fájlok mostantól kevesebb térkép feladatokhoz. Ez növeli a sok kis fájlt a feladatok hatékonyságát. Ha engedélyezni szeretné, állítsa be `pig.noSplitCombination` igaz értékre. Az alapértelmezett értéke FALSE (hamis).


### <a name="tune-mappers"></a>Leképező hangolása

A tulajdonság módosításával leképező számát szabályozza `pig.maxCombinedSplitSize`. Megadja a térkép egyetlen tevékenység által feldolgozandó adatok méretétől. Az alapértelmezett értéke a fájlrendszer alapértelmezett blokkméret. Növelje a érték hatására eseményleképező feladatok számának csökkenését.


### <a name="tune-reducers"></a>Csökkentő hangolása

A számát csökkentő paraméter alapján kiszámítja `pig.exec.reducers.bytes.per.reducer`. Alapértelmezés szerint 1 GB-os, a paraméter adja meg a feldolgozott nyomáscsökkentő bájtok száma. Korlátozza a maximális számát csökkentő, állítsa be a `pig.exec.reducers.max` 999 alapértelmezés szerint a tulajdonság.


## <a name="hbase-optimization-with-the-ambari-web-ui"></a>A HBase optimalizálása az Ambari webes Felülettel

A HBase konfigurációs van módosítani a **HBase Configs** fülre. A következő szakaszok ismertetik az egyes HBase teljesítményt befolyásoló fontos konfigurációs beállításai.

### <a name="set-hbaseheapsize"></a>HBASE_HEAPSIZE beállítása

A HBase. generace meghatározza a maximális halommemória (MB) által használandó *régió* és *fő* kiszolgálók. Az alapértelmezett érték 1000 MB. Ez a fürt számítási feladathoz tartozó úgy kell beállítani.

1. Módosításához lépjen a **speciális HBase-env** panel az a HBase **Configs** lapra, és keresse meg a `HBASE_HEAPSIZE` beállítás.

1. Módosítsa az alapértelmezett érték 5000 MB.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Olvasási számítási feladatok optimalizálása

A következő konfigurációk fontosak olvasási számítási feladatok teljesítményének javítása érdekében.

#### <a name="block-cache-size"></a>Blokk-gyorsítótár mérete

A blokk-gyorsítótárához az olvasási gyorsítótár. Annak méretét szabályozza a `hfile.block.cache.size` paraméter. Az alapértelmezett értéke 0,4, azaz az összes régióban kiszolgálómemória 40 %-kal. Minél nagyobb a gyorsítótár blokkméret, annál gyorsabban véletlenszerű olvasást lesz.

1. Módosítsa ezt a paramétert, lépjen a **beállítások** lapján a HBase **Configs** fület, és keresse meg **% RegionServer lefoglalt olvasási puffert**.

    ![A HBase blokk-gyorsítótár mérete](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
1. Az érték módosításához jelölje ki a **szerkesztése** ikonra.


#### <a name="memstore-size"></a>Kapott mérete

Az összes módosítások vannak tárolva a memóriapuffer nevű egy *kapott*. Ez növeli a teljes adatmennyiség egyetlen művelettel lemezre lehet írni, és azt a legutóbbi módosítások felgyorsítja a ezt követően a hozzáférés. A kapott méretét határozzák meg a következő két paramétert:

* `hbase.regionserver.global.memstore.UpperLimit`: A maximális százalékos aránya a kombinált kapott használó régiókiszolgálón határozza meg.

* `hbase.regionserver.global.memstore.LowerLimit`: A minimális százalékos aránya a kombinált kapott használó régiókiszolgálón határozza meg.

Véletlenszerű olvasást optimalizálására, csökkentheti a kapott alsó és felső határa.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Vizsgálatakor a lemezről beolvasott sorok száma

A `hbase.client.scanner.caching` beállítás határozza meg, olvassa el a sorok számának lemezre a `next` módszert hívja meg a beolvasáshoz.  Az alapértelmezett érték 100. A magasabb a számát, a kisebb a távoli hívások kérés érkezett az ügyfél a régiókiszolgálón gyorsabb vizsgálatok eredményez. Azonban ez is megnöveli a rendelkezésre álló memória mennyisége az ügyfélen.

![A HBase lehívott sorok száma](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]
> Az érték nincs beállítva, például úgy, hogy a hívás a következő metódus a képolvasó közötti idő nagyobb, mint a képolvasó időtúllépése. Határozza meg a képolvasó időtúllépési időtartam a `hbase.regionserver.lease.period` tulajdonság.


### <a name="optimize-write-heavy-workloads"></a>Írási számítási feladatok optimalizálása

A következő konfigurációk fontosak írási számítási feladatok teljesítményének javítása érdekében.


#### <a name="maximum-region-file-size"></a>Fájl maximális méretét

A HBase tárolja az adatokat egy belső fájlformátum, nevű *HFile*. A tulajdonság `hbase.hregion.max.filesize` egy régió egyetlen HFile mérete határozza meg.  Egy régióban két régióban van felosztva, ha egy régió összes HFiles összege nagyobb, mint ez a beállítás.
 
![A HBase HRegion maximális fájl mérete](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Minél nagyobb a fájl méretét, a minél kisebb a megszakítások számát. A fájlok mérete határozza meg, hogy a legnagyobb eredményez írási teljesítményt érték lehet növelni.


#### <a name="avoid-update-blocking"></a>Kerülje a frissítést blokkolja-e

* A tulajdonság `hbase.hregion.memstore.flush.size` határozza meg, ahol ki van ürítve kapott mérete lemezre. Alapértelmezett érték 128 MB.

* Határozza meg a Hbase régió blokk szorzó `hbase.hregion.memstore.block.multiplier`. Az alapértelmezett érték a 4. A maximális szám a 8.

* HBase letiltja a frissítések, ha a kapott (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bájtok száma.

    Az alapértelmezett értékekre flush méretű és blokk szorzó frissítések blokkolják a kapott 128 * 4 = 512 MB méretűnek. A frissítést blokkolja a count csökkentése érdekében értékének növelése `hbase.hregion.memstore.block.multiplier`.

![HBase-régióbeli blokk szorzó](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Adja meg a kapott mérete

Kapott mérete szerint van megadva a `hbase.regionserver.global.memstore.UpperLimit` és `hbase.regionserver.global.memstore.LowerLimit` paramétereket. A beállítás ezeket az értékeket egyenlő, minden más csökkenti a szünet során (is okozó gyakoribb kiürítette) ír, és írási nagyobb teljesítményt eredményez.


### <a name="set-memstore-local-allocation-buffer"></a>Állítsa be a kapott helyi foglalási puffer

A tulajdonság határozza meg kapott helyi foglalási memóriapuffer-használat `hbase.hregion.memstore.mslab.enabled`. Ha engedélyezve van (igaz), ez megakadályozza, hogy halommemória töredezettsége nagy írási művelet során. Az alapértelmezett érték: igaz.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>További lépések

* [HDInsight-fürtök az Ambari webes felhasználói felületének kezelése](hdinsight-hadoop-manage-ambari.md)
* [Az Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
