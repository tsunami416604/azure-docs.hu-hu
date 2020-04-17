---
title: Az Apache Ambari a fürtkonfigurációk optimalizálásához - Azure HDInsight
description: Az Apache Ambari webes felhasználói felületének használatával konfigurálhatja és optimalizálhatja az Azure HDInsight-fürtöket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: c88882175ff256300dee486e680a9b63e9a65c99
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532498"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Az Apache Ambari használata a HDInsight fürtkonfigurációinak optimalizálására

A HDInsight [Apache Hadoop-fürtöket](./hadoop/apache-hadoop-introduction.md) biztosít a nagyméretű adatfeldolgozó alkalmazásokhoz. Ezek az összetett többcsomópontos fürtök kezelése, figyelése és optimalizálása kihívást jelenthet. [Az Apache Ambari](https://ambari.apache.org/) egy webes felület a HDInsight Linux-fürtök kezelésére és figyelésére.  Windows-fürtök esetén használja az [Ambari REST API-t.](hdinsight-hadoop-manage-ambari-rest-api.md)

Az Ambari webfelhasználói felület használatának bemutatása a [HDInsight-fürtök kezelése az Apache Ambari Web felhasználói felületének használatával című témakörben található.](hdinsight-hadoop-manage-ambari.md)

Jelentkezzen be az Ambari-ba `https://CLUSTERNAME.azurehdidnsight.net` a fürt hitelesítő adataival. A kezdeti képernyő egy áttekintő irányítópultot jelenít meg.

![Megjelent az Apache Ambari felhasználói irányítópultja](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

Az Ambari webes felhasználói felülete a gazdagépek, szolgáltatások, riasztások, konfigurációk és nézetek kezelésére szolgál. Az Ambari nem használható HDInsight-fürt vagy frissítési szolgáltatások létrehozására. A halmok és verziók, a leszerelt vagy újraüzembe helyezés gazdagépek, illetve szolgáltatások hozzáadása a fürthöz nem kezelhetők.

## <a name="manage-your-clusters-configuration"></a>A fürt konfigurációjának kezelése

A konfigurációs beállítások segítenek egy adott szolgáltatás hangolásában. A szolgáltatás konfigurációs beállításainak módosításához válassza ki a szolgáltatást a **Szolgáltatások** oldalsávon (a bal oldalon). Ezután keresse meg a **Configs** lapot a szolgáltatás részleteit tartalmazó lapon.

![Apache Ambari szolgáltatások oldalsáv](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>NameNode Java halommemória méretének módosítása

A NameNode Java halommemória mérete számos tényezőtől függ, például a fürt terhelése. Is, a fájlok száma, és a blokkok száma. Az alapértelmezett 1 GB-os méret a legtöbb fürtesetében jól működik, bár egyes munkaterhelések több vagy kevesebb memóriát igényelhetnek.

A NameNode Java halommemória méretének módosítása:

1. Válassza a **HDFS** elemet a Szolgáltatások oldalsávján, és keresse meg a **Configs** lapot.

    ![Apache Ambari HDFS-konfiguráció](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Keresse meg a **NameNode Java halommemória méretét.** A **szűrőszövegmezővel** is írhat, és megkereshetegy adott beállítást. Válassza a beállítás neve melletti **toll** ikont.

    ![Apache Ambari NameNode Java halommemória mérete](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Írja be az új értéket a szövegmezőbe, majd nyomja le az **Enter billentyűt** a módosítás mentéséhez.

    ![Ambari Edit NameNode Java halommemória mérete1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. A NameNode Java halommemória mérete 1 GB-ra változik 2 GB-ról.

    ![Szerkesztett NameNode Java halommemória mérete2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Mentse a módosításokat a konfigurációs képernyő tetején található zöld **Mentés** gombra kattintva.

    !["Apache Ambari menteni konfigurációk"](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="apache-hive-optimization"></a>Apache Hive optimalizálás

A következő szakaszok ismertetik a konfigurációs beállításokat az Apache Hive általános teljesítményének optimalizálásához.

1. A Hive konfigurációs paramétereinek módosításához válassza a **Hive** lehetőséget a Szolgáltatások oldalsávján.
1. Nyissa meg a **Configs** lapot.

### <a name="set-the-hive-execution-engine"></a>A Hive végrehajtási motorbeállítása

Hive két végrehajtási motorok: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) és [Apache TEZ](https://tez.apache.org/). Tez gyorsabb, mint a MapReduce. A HDInsight Linux-fürtök alapértelmezett végrehajtási motorja tez. A végrehajtási motor módosítása:

1. A Hive **Configs** lapon írja be a **végrehajtási motort** a szűrőmezőbe.

    ![Apache Ambari Keresőmotor](./media/hdinsight-changing-configs-via-ambari/ambari-search-execution.png)

1. Az **Optimalizálás** tulajdonság alapértelmezett értéke **Tez**.

    ![Optimalizálás - Apache Tez motor](./media/hdinsight-changing-configs-via-ambari/optimization-apache-tez.png)

### <a name="tune-mappers"></a>Mappers hangolása

Hadoop megpróbálja megosztani (*térkép)* egy fájlt több fájlt, és feldolgozni a kapott fájlokat párhuzamosan. A leképezők száma a felosztások számától függ. A következő két konfigurációs paraméter hajtja a Tez végrehajtási motor felosztásainak számát:

* `tez.grouping.min-size`: A csoportosított felosztás méretének alsó határa, 16 MB (16 777 216 bájt) alapértelmezett értékkel.
* `tez.grouping.max-size`: A csoportosított felosztás méretének felső határa, 1 GB alapértelmezett értékkel (1 073 741 824 bájt).

Teljesítmény-irányelvként csökkentse mindkét paramétert a késés javítása érdekében, növelje a nagyobb átviteli teljesítményt.

Ha például négy leképező feladatot szeretne beállítani 128 MB-os adatmérethez, mindkét paramétert 32 MB-ra kell állítani (33 554 432 bájt).

1. A korlát paramétereinek módosításához keresse meg a Tez szolgáltatás **Configs lapját.** Bontsa ki az `tez.grouping.max-size` **Általános** panelt, és keresse meg a paramétereket `tez.grouping.min-size` és a paramétereket.

1. Állítsa mindkét paramétert **33 554 432** bájtra (32 MB).

    ![Apache Ambari Tez csoportosítási méretei](./media/hdinsight-changing-configs-via-ambari/apache-tez-grouping-size.png)

Ezek a változások a kiszolgálón lévő összes Tez-feladatot érintik. Az optimális eredmény eléréséhez válassza ki a megfelelő paraméterértékeket.

### <a name="tune-reducers"></a>Hangzatos hangzatos

[Az Apache ORC](https://orc.apache.org/) és [a Snappy](https://google.github.io/snappy/) egyaránt nagy teljesítményt nyújt. Azonban a Hive alapértelmezés szerint túl kevés szűkítővel rendelkezik, ami szűk keresztmetszeteket okoz.

Tegyük fel például, hogy a bemeneti adatok mérete 50 GB. A Snappy tömörítésű ORC formátumú adatok 1 GB.That data in ORC format with Snappy compression is 1 GB. A Hive a következőképpen becsüli meg a szükséges szűkítők számát: `hive.exec.reducers.bytes.per.reducer`(a leképezőkbe bemeneti bájtok száma / ).

Az alapértelmezett beállításokkal ez a példa négy szűkítő.

A `hive.exec.reducers.bytes.per.reducer` paraméter a szűkítőnként feldolgozott bájtok számát adja meg. Az alapértelmezett érték 64 MB. Az érték lehangolása növeli a párhuzamosságot, és javíthatja a teljesítményt. Tuning túl alacsony is okozhat túl sok szűkítők, potenciálisan hátrányosan befolyásolja a teljesítményt. Ez a paraméter az adott adatkövetelményeken, a tömörítési beállításokon és más környezeti tényezőkön alapul.

1. A paraméter módosításához keresse meg a Hive **Configs** fület, és keresse meg a **Data per Reducer** paramétert a Beállítások lapon.

    ![Apache Ambari adatok szűkítőnként](./media/hdinsight-changing-configs-via-ambari/ambari-data-per-reducer.png)

1. Válassza a **Szerkesztés** lehetőséget a 128 MB (134 217 728 bájt) érték módosításához, majd nyomja le az **Enter** billentyűt a mentéshez.

    ![Ambari Data per Szűkítő - szerkesztett](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    1024 MB bemeneti méret, 128 MB-os szűkítőnkénti adattal, nyolc szűkítő (1024/128).

1. A **Data per Reducer** paraméter helytelen értéke nagyszámú szűkítőt eredményezhet, ami hátrányosan befolyásolja a lekérdezés teljesítményét. A szűkítők maximális számának `hive.exec.reducers.max` korlátozásához állítsa be a megfelelő értéket. Az alapértelmezett érték 1009.

### <a name="enable-parallel-execution"></a>Párhuzamos végrehajtás engedélyezése

A Hive-lekérdezés végrehajtása egy vagy több szakaszban történik. Ha a független szakaszok párhuzamosan futtathatók, az növeli a lekérdezés iményteljesítményét.

1. A párhuzamos lekérdezések végrehajtásának engedélyezéséhez keresse meg a `hive.exec.parallel` Hive **Config** lapot, és keresse meg a tulajdonságot. Az alapértelmezett érték a hamis. Módosítsa az értéket igaz értékre, majd nyomja le az **Enter billentyűt** az érték mentéséhez.

1. A párhuzamosan futtatandó feladatok számának `hive.exec.parallel.thread.number` korlátozásához módosítsa a tulajdonságot. Az alapértelmezett érték 8.

    ![Apache Hive exec párhuzamos kijelző](./media/hdinsight-changing-configs-via-ambari/apache-hive-exec-parallel.png)

### <a name="enable-vectorization"></a>Vektorizálás engedélyezése

A Hive soronként dolgozza fel az adatokat. A vektorizálás arra utasítja a Hive-t, hogy az adatokat 1024 sorból álló blokkokban dolgozza fel, nem pedig egyszerre egy sorban. A vektorizálás csak az ORC fájlformátumra alkalmazható.

1. Vektoros lekérdezések végrehajtásának engedélyezéséhez keresse meg a Hive **Configs** lapot, és keresse meg a `hive.vectorized.execution.enabled` paramétert. Az alapértelmezett érték a 0.13.0-s vagy újabb hive-értékre igaz.

1. A lekérdezés csökkentési oldalának vektoros `hive.vectorized.execution.reduce.enabled` végrehajtásának engedélyezéséhez állítsa a paramétert true értékre. Az alapértelmezett érték a hamis.

    ![Apache Hive vektorizált végrehajtás](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Költségalapú optimalizálás engedélyezése (CBO)

Alapértelmezés szerint a Hive szabályokat követ egy optimális lekérdezés-végrehajtási terv megkereséséhez. A költségalapú optimalizálás (CBO) több lekérdezés végrehajtására irányuló tervet értékel ki. És hozzárendeli a költség minden terv, majd meghatározza a legolcsóbb tervet, hogy egy lekérdezést.

A CBO engedélyezéséhez keresse meg a **Hive** > **konfigurációs** > **beállítások at,** és keresse meg a **Költségalapú optimalizáló engedélyezése**lehetőséget, majd kapcsolja be a váltógombot **Be**beállításra.

![A HDInsight költségalapú optimalizálója](./media/hdinsight-changing-configs-via-ambari/hdinsight-cbo-config.png)

A következő további konfigurációs paraméterek növelik a Hive-lekérdezés teljesítményét, ha a CBO engedélyezve van:

* `hive.compute.query.using.stats`

    Ha igaz értékre van állítva, a Hive a metatárolójában tárolt statisztikákat használja az olyan egyszerű lekérdezések megválaszolására, mint `count(*)`a.

    ![Apache Hive számítási lekérdezés statisztika használatával](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Az oszlopstatisztika akkor jön létre, ha a CBO engedélyezve van. Hive a metatárolóban tárolt oszlopstatisztikákat használja a lekérdezések optimalizálásához. Az egyes oszlopok oszlopstatisztikáinak beolvasása hosszabb időt vesz igénybe, ha az oszlopok száma magas. Ha hamis, ez a beállítás letiltja az oszlopstatisztikák beolvasását a metatárolóból.

    ![Apache Hive statisztika készlet oszlop statisztika](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Az alapvető partícióstatisztikák, például a sorok száma, az adatok mérete és a fájlméret a metatárban tárolódnak. Ha értéke igaz, a partíció statisztika lekéri metastore. Ha hamis, a fájlméretet a fájlrendszerből olvassa be. És a sorok száma a sorsémából kerül beolvasásra.

    ![Hive-statisztika készlet partíció statisztika](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Köztes tömörítés engedélyezése

A leképezési feladatok köztes fájlokat hoznak létre, amelyeket a szűkítő feladatok használnak. A köztes tömörítés csökkenti a köztes fájlméretet.

Hadoop munkák általában I / O szűk keresztmetszetű. Az adatok tömörítése felgyorsíthatja az I/O-t és a teljes hálózati átvitelt.

A rendelkezésre álló tömörítési típusok a következők:

| Formátum | Eszköz | Algoritmus | Fájlkiterjesztés | Hasadható? |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | Deflate | `.gz` | Nem |
| Bzip2 között | Bzip2 között | Bzip2 között |`.bz2` | Igen |
| LZO | `Lzop` | LZO | `.lzo` | Igen, ha indexelt |
| Snappy | N/A | Snappy | Snappy | Nem |

Általános szabály, hogy a tömörítési módszer splittable fontos, különben kevés leképező jön létre. Ha a bemeneti `bzip2` adatok szöveg, a legjobb megoldás. OrC formátum esetén a Snappy a leggyorsabb tömörítési lehetőség.

1. A köztes tömörítés engedélyezéséhez keresse meg a Hive `hive.exec.compress.intermediate` **Configs** lapot, majd állítsa a paramétert true értékre. Az alapértelmezett érték a hamis.

    !["Hive exec compress intermediate"](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > A köztes fájlok tömörítéséhez válasszon egy alacsonyabb CPU-költségű tömörítési kodeket, még akkor is, ha a kodek nem rendelkezik nagy tömörítési kimenettel.

1. A köztes tömörítési kodek `mapred.map.output.compression.codec` beállításához adja hozzá az egyéni tulajdonságot a `hive-site.xml` fájlhoz. `mapred-site.xml`

1. Egyéni beállítás hozzáadása:

    a. Nyissa meg a **Hive** > **Configs** > **Advanced** > Custom**struktúrawebhelyet.**

    b. Válassza a **Tulajdonság hozzáadása lehetőséget...** az Egyéni struktúrahely ablaktábla alján.

    c. A Tulajdonság hozzáadása ablakban adja `mapred.map.output.compression.codec` `org.apache.hadoop.io.compress.SnappyCodec` meg kulcsként és értékként.

    d. Válassza a **Hozzáadás** lehetőséget.

    !['Apache Hive egyéni tulajdonság hozzáadása'](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Ez a beállítás a köztes fájlt a Snappy tömörítéssel tömöríti. A tulajdonság hozzáadása után megjelenik az egyéni struktúrahely ablaktáblán.

    > [!NOTE]  
    > Ez az eljárás `$HADOOP_HOME/conf/hive-site.xml` módosítja a fájlt.

### <a name="compress-final-output"></a>Végső kimenet tömörítése

A végleges Hive kimenet is tömöríthető.

1. A végleges Hive-kimenet tömörítéséhez keresse meg a Hive `hive.exec.compress.output` **Configs** lapot, majd állítsa a paramétert true értékre. Az alapértelmezett érték a hamis.

1. A kimeneti tömörítési kodek kiválasztásához adja hozzá az `mapred.output.compression.codec` egyéni tulajdonságot az egyéni struktúrahely ablaktáblához, az előző szakasz 3.

    ![Apache Hive egyéni tulajdonság hozzáadása2](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Spekulatív végrehajtás engedélyezése

A spekulatív végrehajtás bizonyos számú ismétlődő feladatot indít el a lassan futó feladatkövető listájának észlelésére és megtagadására. Miközben javítja a teljes feladat végrehajtását az egyes feladateredmények optimalizálásával.

Spekulatív végrehajtás nem kell bekapcsolni a hosszú ideig futó MapReduce feladatok nagy mennyiségű bemenettel.

* A spekulatív végrehajtás engedélyezéséhez keresse meg a Hive **Configs** fület, majd állítsa a `hive.mapred.reduce.tasks.speculative.execution` paramétert true értékre. Az alapértelmezett érték a hamis.

    !["Hive mapred csökkenti feladatok spekulatív végrehajtás"](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Dinamikus partíciók finomhangolása

Hive lehetővé teszi a dinamikus partíciók létrehozása, amikor rekordok at egy táblába, anélkül, hogy minden partíciót. Ez a képesség egy erős funkció. Bár ez azt eredményezheti, hogy a partíciók nagy száma. És a nagy számú fájlt minden partíciót.

1. Ahhoz, hogy a Hive `hive.exec.dynamic.partition` dinamikus partíciókat, a paraméter értékének igaznak kell lennie (az alapértelmezett).

1. Módosítsa a dinamikus partíciós módot *szigorúra.* Szigorú módban legalább egy partíciónak statikusnak kell lennie. Ez a beállítás megakadályozza, hogy a WHERE záradékban a partíciószűrő nélküli lekérdezések, azaz a *szigorúak* megakadályozzák az összes partíciót átkelő lekérdezéseket. Nyissa meg a Hive **Configs** `hive.exec.dynamic.partition.mode` lapot, majd állítsa **a szigorúra.** Az alapértelmezett érték **nem szigorú.**

1. A létrehozandó dinamikus partíciók számának korlátozásához módosítsa a `hive.exec.max.dynamic.partitions` paramétert. Az alapértelmezett érték 5000.

1. A dinamikus partíciók csomópontonkénti teljes `hive.exec.max.dynamic.partitions.pernode`számának korlátozásához módosítsa a t. Az alapértelmezett érték 2000.

### <a name="enable-local-mode"></a>Helyi mód engedélyezése

A helyi mód lehetővé teszi a Hive számára, hogy egyetlen gépen végezze el a feladat összes feladatát. Vagy néha egyetlen folyamat. Ez a beállítás javítja a lekérdezési teljesítményt, ha a bemeneti adatok kicsik. És a lekérdezések indítási feladatok indításának többletterhelése a teljes lekérdezés-végrehajtás jelentős százalékát használja fel.

A helyi mód engedélyezéséhez adja hozzá a `hive.exec.mode.local.auto` paramétert az Egyéni struktúrahely panelhez, ahogy azt a [köztes tömörítés engedélyezése](#enable-intermediate-compression) szakasz 3.

![Apache Hive exec mód helyi automatikus](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Egyetlen mapreduce multigroup by beállítása

Ha ez a tulajdonság igaz értékre van állítva, a közös csoportonkénti kulcsokkal rendelkező MultiGROUP BY lekérdezés egyetlen MapReduce feladatot hoz létre.  

A viselkedés engedélyezéséhez `hive.multigroupby.singlereducer` adja hozzá a paramétert az Egyéni struktúrahely ablaktáblához, ahogy azt a [köztes tömörítés engedélyezése](#enable-intermediate-compression) szakasz 3.

![Hive készlet egyetlen MapReduce MultiGROUP BY](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>További Hive-optimalizálások

A következő szakaszok további Hive-val kapcsolatos optimalizálásokat írnak le, amelyeket beállíthat.

#### <a name="join-optimizations"></a>Csatlakozás optimalizáláshoz

A Hive alapértelmezett illesztési típusa a *véletlen sorrendű illesztés*. A Hive speciális leképezőolvassa be a bemenetet, és egy köztes fájlhoz illesztési kulcsot/értékpárt bocsát ki. A Hadoop rendezi és összeolvasztja ezeket a párokat egy véletlen sorrendben. Ez a shuffle szakasz drága. A megfelelő illesztés kiválasztása az adatok alapján jelentősen javíthatja a teljesítményt.

| Csatlakozás típusa | mikor | Hogyan | Hive-beállítások | Megjegyzések |
| --- | --- | --- | --- | --- |
| Véletlen sorrendű illesztés | <ul><li>Alapértelmezett választási lehetőség</li><li>Mindig működik</li></ul> | <ul><li>Olvasás az egyik tábla egy részéből</li><li>Vödrök és rendezés a Join billentyűn</li><li>Egy-egy gyűjtőt küld minden csökkentéshez</li><li>Az illesztés a Csökkentés oldalon történik</li></ul> | Nincs szükség jelentős Hive-beállításra | Minden alkalommal működik |
| Térképes illesztés | <ul><li>Egy asztal elfér a memóriában</li></ul> | <ul><li>Kis méretű táblázat beolvasása a memóriakivonat-táblába</li><li>Adatfolyamok a nagy fájl egy részén</li><li>Minden rekordot egyesít a kivonattáblázatból</li><li>Csatlakozik a mapper egyedül</li></ul> | `hive.auto.confvert.join=true` | Gyors, de korlátozott |
| Körgyűjtő rendezése | Ha mindkét tábla: <ul><li>Ugyanúgy rendezve</li><li>Bucketed ugyanaz</li><li>Csatlakozás a rendezett/gyűjtő oszlophoz</li></ul> | Minden folyamat: <ul><li>Minden táblából beolvas egy gyűjtőt</li><li>A legalacsonyabb értékű sort dolgozza fel</li></ul> | `hive.auto.convert.sortmerge.join=true` | Hatékony |

#### <a name="execution-engine-optimizations"></a>Végrehajtási motor optimalizálása

További javaslatok a Hive végrehajtási motor optimalizálásához:

| Beállítás | Ajánlott | HDInsight alapértelmezett |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | Igaz = biztonságosabb, lassabb; false = gyorsabb | hamis |
| `tez.am.resource.memory.mb` | 4 GB felső határ a legtöbb | Automatikus an-tuned |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Apache Pig optimalizálás

[Az Apache Pig](https://pig.apache.org/) tulajdonságai az Ambari webes felhasználói felületről módosíthatók a Pig-lekérdezések finomhangolásához. Az Ambari Pig tulajdonságainak módosítása közvetlenül módosítja a fájlban lévő `/etc/pig/2.4.2.0-258.0/pig.properties` Pig-tulajdonságokat.

1. A Pig tulajdonságainak módosításához keresse meg a Pig **Configs** lapot, majd bontsa ki a **Speciális sertéstulajdonságok** ablaktáblát.

1. Keresse meg, ne fűzzön megjegyzést, és módosítsa a módosítani kívánt tulajdonság értékét.

1. Az új érték mentéséhez válassza a **Mentés** gombot az ablak jobb felső részén. Egyes tulajdonságok a szolgáltatás újraindítását igényelhetik.

    ![Fejlett Apache sertés tulajdonságai](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)

> [!NOTE]  
> A munkamenet-szintű beállítások felülbírálják `pig.properties` a fájl tulajdonságértékeit.

### <a name="tune-execution-engine"></a>Végrehajtási motor hangolása

Két végrehajtási motor áll rendelkezésre a Pig parancsfájlok végrehajtásához: MapReduce és Tez. Tez egy optimalizált motor, és sokkal gyorsabb, mint MapReduce.

1. A végrehajtási motor módosításához keresse meg a tulajdonságot `exectype`a **Speciális sertéstulajdonságok** ablaktáblában.

1. Az alapértelmezett érték **MapReduce**. Változtasd meg **Tez-re.**

### <a name="enable-local-mode"></a>Helyi mód engedélyezése

A Hive-hoz hasonlóan a helyi mód a feladatok felgyorsítására szolgál viszonylag kisebb mennyiségű adattal.

1. A helyi mód engedélyezéséhez állítsa `pig.auto.local.enabled` a **true**értéket. Az alapértelmezett érték a hamis.

1. A `pig.auto.local.input.maxbytes` tulajdonságértéknél kisebb bemeneti adatméretű feladatok kis feladatoknak minősülnek. Az alapértelmezett érték 1 GB.

### <a name="copy-user-jar-cache"></a>Felhasználói tár gyorsítótárának másolása

A Pig átmásolja az UDF-fájlok által igényelt JAR-fájlokat egy elosztott gyorsítótárba, hogy elérhetővé tegye őket a feladatcsomópontok számára. Ezek az üvegek nem változnak gyakran. Ha engedélyezve `pig.user.cache.enabled` van, a beállítás lehetővé teszi, hogy az üvegek gyorsítótárba kerüljenek, hogy újra felhasználhassák őket az ugyanazon felhasználó által futtatott feladatokhoz. Ez a beállítás a munka teljesítményének kismértékű növekedését eredményezi.

1. Az engedélyezéshez állítsa igaz értékre. `pig.user.cache.enabled` Az alapértelmezett érték hamis.

1. A gyorsítótárazott üvegek alapelérési útjának beállításához állítsa az `pig.user.cache.location` alapelérési utat. A mező alapértelmezett értéke: `/tmp`.

### <a name="optimize-performance-with-memory-settings"></a>A teljesítmény optimalizálása memóriabeállításokkal

A következő memóriabeállítások segíthetnek optimalizálni a Pig parancsfájlok teljesítményét.

* `pig.cachedbag.memusage`: A táskának adott memória mennyisége. A táska a tuples gyűjteménye. A toldalék mezőrendezett, a mező pedig adat. Ha a táskában lévő adatok túlvannak az adott memórián, akkor a lemezre ömlenek. Az alapértelmezett érték 0,2, ami a rendelkezésre álló memória 20 százalékát jelenti. Ez a memória meg oszlik az összes csomag egy alkalmazás.

* `pig.spill.size.threshold`: A kiömlési méretküszöbnél nagyobb zsákok (bájtban) kiömlenek a lemezre. Az alapértelmezett érték 5 MB.

### <a name="compress-temporary-files"></a>Ideiglenes fájlok tömörítése

A Pig ideiglenes fájlokat hoz létre a feladat végrehajtása során. Az ideiglenes fájlok tömörítése teljesítménynövekedést eredményez a fájlok lemezre olvasásakor vagy írásakor. Az ideiglenes fájlok tömörítéséhez a következő beállítások használhatók.

* `pig.tmpfilecompression`: Ha igaz, ideiglenes fájltömörítést tesz lehetővé. Az alapértelmezett érték a hamis.

* `pig.tmpfilecompression.codec`: Az ideiglenes fájlok tömörítéséhez használt tömörítő kodek. Az ajánlott tömörítési kodekek [lzo](https://www.oberhumer.com/opensource/lzo/) és snappy az alacsonyabb CPU-használat.

### <a name="enable-split-combining"></a>Felosztásegyesítés engedélyezése

Ha engedélyezve van, a kis fájlok kevesebb leképezési feladathoz lesznek kombinálva. Ez a beállítás javítja a sok kis fájllal rendelkező feladatok hatékonyságát. Az engedélyezéshez állítsa igaz értékre. `pig.noSplitCombination` Az alapértelmezett érték a hamis.

### <a name="tune-mappers"></a>Mappers hangolása

A leképezők számát a tulajdonság módosításával `pig.maxCombinedSplitSize`lehet szabályozni. Ez a tulajdonság az egyetlen leképezési feladat által feldolgozandó adatok méretét adja meg. Az alapértelmezett érték a fájlrendszer alapértelmezett blokkmérete. Ennek az értéknek a növelésével kevesebb leképező feladat jelenik meg.

### <a name="tune-reducers"></a>Hangzatos hangzatos

A szűkítők számát a paraméter `pig.exec.reducers.bytes.per.reducer`alapján számítja ki a program. A paraméter megadja a szűkítőnként feldolgozott bájtok számát, alapértelmezés szerint 1 GB.The parameter specifies the number of byte processed per reducer, alapértelmezés szerint 1 GB. A szűkítők maximális számának `pig.exec.reducers.max` korlátozásához állítsa be a tulajdonságot a 999-es értékszerint.

## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Apache HBase optimalizálás az Ambari webes felhasználói felülettel

[Az Apache HBase](https://hbase.apache.org/) konfigurációja a **HBase Configs** lapjáról módosul. A következő szakaszok ismertetik a HBase teljesítményét befolyásoló fontos konfigurációs beállításokat.

### <a name="set-hbase_heapsize"></a>HBASE_HEAPSIZE beállítása

A HBase halommemória-méret a megabájtban *régiónként* és *főkiszolgálókonként* használandó halommemória maximális mennyiségét adja meg. Az alapértelmezett érték 1000 MB. Ezt az értéket a fürt munkaterheléséhez kell hangolni.

1. A módosításhoz keresse meg a HBase Konfigurációs lap **Speciális HBase-env** ablaktábláját, és keresse meg a **Configs** `HBASE_HEAPSIZE` beállítást.

1. Módosítsa az alapértelmezett értéket 5000 MB-ra.

    !['Apache Ambari HBase memóriahalomlerakás'](./media/hdinsight-changing-configs-via-ambari/ambari-hbase-heapsize.png)

### <a name="optimize-read-heavy-workloads"></a>Az olvasási nagy terhelésű munkaterhelések optimalizálása

A következő konfigurációk fontosak az olvasási nagy számítási feladatok teljesítményének javítása érdekében.

#### <a name="block-cache-size"></a>Gyorsítótár méretének blokkolása

A blokkgyorsítótár az olvasási gyorsítótár. A méretet a `hfile.block.cache.size` paraméter szabályozza. Az alapértelmezett érték 0,4, amely a teljes régiókiszolgáló memóriájának 40 százaléka. Minél nagyobb a blokkgyorsítótár mérete, annál gyorsabb lesz a véletlenszerű olvasás.

1. A paraméter módosításához keresse meg a HBase **konfigurációs** lap **Beállítások** lapját, majd keresse meg **a RegionServer olvasási pufferek számára lefoglalt %ját.**

    ![Az Apache HBase memóriablokk-gyorsítótárának mérete](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)

1. Az érték módosításához kattintson a **Szerkesztés** ikonra.

#### <a name="memstore-size"></a>Memstore mérete

Az összes szerkesztés a memóriapufferben, az úgynevezett *Memstore-ban*tárolódik. Ez a puffer növeli a lemezre egyetlen műveletben írható adatok teljes mennyiségét. Emellett felgyorsítja a hozzáférést a legutóbbi szerkesztésekhez. A Memstore méretét a következő két paraméter határozza meg:

* `hbase.regionserver.global.memstore.UpperLimit`: A Memstore kombinált régiókiszolgáló maximális százalékos arányát határozza meg.

* `hbase.regionserver.global.memstore.LowerLimit`: A Memstore által kombinált régiókiszolgáló minimális százalékos arányát határozza meg.

A véletlenszerű olvasásra való optimalizáláshoz csökkentheti a Memstore felső és alsó határait.

#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>A lemezről történő beolvasáskor beolvasott sorok száma

A `hbase.client.scanner.caching` beállítás határozza meg a lemezről `next` beolvasott sorok számát, amikor a metódust képolvasón hívják meg.  Az alapértelmezett érték 100. Minél nagyobb a szám, annál kevesebb távoli hívás érkezett az ügyféltől a régiókiszolgálóra, ami gyorsabb vizsgálatokat eredményez. Ez a beállítás azonban növeli az ügyfélre nehezedő memórianyomást is.

![Apache HBase lekért sorok száma](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Ne állítsa be az értéket úgy, hogy a következő módszer meghívása között a lapolvasón töltött idő túlnagy időtúltöltés. A lapolvasó idő-kiutazásának `hbase.regionserver.lease.period` időtartamát a tulajdonság határozza meg.

### <a name="optimize-write-heavy-workloads"></a>Írási nehéz számítási feladatok optimalizálása

A következő konfigurációk fontosak az írási nehéz számítási feladatok teljesítményének javítása érdekében.

#### <a name="maximum-region-file-size"></a>A terület fájlméretének maximális mérete

A HBase az adatokat belső fájlformátumban, *hfile formátumban*tárolja. A `hbase.hregion.max.filesize` tulajdonság egy régió egyetlen HFile fájljának méretét határozza meg.  Egy régió két régióra van felosztva, ha egy régió összes HFiles fájljának összege nagyobb, mint ez a beállítás.

!['Apache HBase HRegion maximális fájlméret'](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Minél nagyobb a területfájl mérete, annál kisebb a felosztások száma. A fájlméret növelésével meghatározhatja a maximális írási teljesítményt eredményező értéket.

#### <a name="avoid-update-blocking"></a>A frissítés blokkolásának elkerülése

* A `hbase.hregion.memstore.flush.size` tulajdonság határozza meg a Memstore lemezre ürítésének méretét. Az alapértelmezett méret 128 MB.

* A HBase régióblokk-szorzót `hbase.hregion.memstore.block.multiplier`a. Az alapértelmezett érték a 4. A megengedett maximális érték 8.

* A HBase blokkolja a frissítéseket, ha a Memstore (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bájt.

    A flush méret és a blokkszorzó alapértelmezett értékeivel a rendszer blokkolja a frissítéseket, ha a Memstore mérete 128 * 4 = 512 MB. A frissítésblokkolás számának csökkentéséhez `hbase.hregion.memstore.block.multiplier`növelje a értékét.

![Apache HBase régióblokk-szorzó](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)

### <a name="define-memstore-size"></a>Memstore méretének meghatározása

A Memstore méretét a `hbase.regionserver.global.memstore.UpperLimit` `hbase.regionserver.global.memstore.LowerLimit` és a paraméterek határozzák meg. Ha ezeket az értékeket egyenlőre állítja be, az csökkenti a szünetek írási (szintén gyakoribb kiürítést) alatti szüneteket, és növeli az írási teljesítményt.

### <a name="set-memstore-local-allocation-buffer"></a>A Memstore helyi foglalási puffere

A Memstore helyi foglalási pufferének használatát a tulajdonság `hbase.hregion.memstore.mslab.enabled`határozza meg. Ha engedélyezve van (igaz), ez a beállítás megakadályozza a halommemória töredezettségét a nehéz írási művelet során. Az alapértelmezett érték az igaz.

![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>További lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes felhasználói felületével](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
