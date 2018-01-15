---
title: "Az Ambari - Azure HDInsight fürt konfigurációjának optimalizálása |} Microsoft Docs"
description: "Az Ambari webes felhasználói felület segítségével konfigurálhatja, és optimalizálja a HDInsight-fürtök."
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: ashish
ms.openlocfilehash: 5b3700580f593e7590360792f2b76dee79608896
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2018
---
# <a name="use-ambari-to-optimize-hdinsight-cluster-configurations"></a>A HDInsight fürt konfigurációjának optimalizálása az Ambari használatával

HDInsight Apache Hadoop-fürtök nyújt a nagy méretű adatokat feldolgozó alkalmazások számára. Kezelését, megfigyelését és ezek összetett, több csomópontot tartalmazó fürt optimalizálása kihívást jelenthet. [Apache Ambari](http://ambari.apache.org/) kezelni és megfigyelni a HDInsight Linux-fürtök egy webes felhasználói felület.  Windows-fürtök esetén használja az Ambari [REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

Bevezetés az Ambari webes felhasználói felület használatával, lásd: [kezelése HDInsight-fürtök az Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md)

Jelentkezzen be a Ambari `https://CLUSTERNAME.azurehdidnsight.net` a fürt hitelesítő adataival. A kezdeti képernyőn megjelenik az áttekintő irányítópulthoz.

![Ambari irányítópult](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

Gazdagépek, a szolgáltatások, a riasztások, a konfigurációkat és a nézetek kezelése az Ambari webes felhasználói felület használható. Ambari nem használható a HDInsight-fürtök létrehozása, szolgáltatások frissítésére, kezelni a verem és verziók, leszerelése vagy állomások recommission vagy szolgáltatások hozzáadása a fürthöz.

## <a name="manage-your-clusters-configuration"></a>A fürt konfigurációjának kezelése

Konfigurációs beállítások segítségével finomhangolhatják a egy adott szolgáltatáshoz. Az adott szolgáltatás konfigurációs beállítások módosításához válassza a szolgáltatás a **szolgáltatások** oldalsávon (a bal oldalon), majd keresse meg a **Configs** lapon a szolgáltatás részletei lapon.

![Szolgáltatások oldalsó sáv](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Módosítsa a NameNode Java halommemória mérete

A NameNode Java halommemória mérete például a terhelést a fürt, a fájlok számát és a blokkok számát számos tényezőtől függ. Az alapértelmezett méret 1 GB jól működik a legtöbb fürt, bár egyes munkaterhelések több vagy kevesebb memória lehet szükség. 

A NameNode Java halommemória mérete módosítása:

1. Válassza ki **HDFS** a szolgáltatások oldalsávon a, és keresse meg a **Configs** fülre.

    ![HDFS-konfiguráció](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

2. A beállítás található **NameNode Java halommemória mérete**. Használhatja a **szűrő** szövegmezőbe írja be, és egy adott beállítás megtalálni. Válassza ki a **toll** a beállítás neve mellett.

    ![NameNode Java halommemória mérete](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

3. Írja be az új értéket a szövegmezőbe, és nyomja le az **Enter** menteni a módosítást.

    ![NameNode Java halommemória mérete szerkesztése](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

4. A NameNode Java halommemória mérete 1 GB-os változása – 2 GB.

    ![Szerkeszthető halommemória mérete NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

5. A módosítások mentéséhez kattintson a zöld **mentése** tetején a következő konfigurációs képernyőjén gombra.

    ![Módosítások mentése](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="hive-optimization"></a>Hive optimalizálása

A következő szakaszok ismertetik a konfigurációs beállítások megfelelően általános Hive teljesítményének optimalizálásához.

1. Hive-konfigurációs paraméterek módosításához válassza **Hive** a a szolgáltatások oldalsávon.
2. Keresse meg a **Configs** fülre.

### <a name="set-the-hive-execution-engine"></a>Állítsa be a Hive-végrehajtó motor

Hive biztosít két végrehajtási motorok: MapReduce és Tez. Tez gyorsabb, mint a MapReduce. HDInsight Linux-fürtök Tez rendelkezik, mint az alapértelmezett végrehajtó motorja. A-végrehajtó motor módosítása:

1. A Hive a **Configs** fülre, írja be **végrehajtó motorja** szót a Szűrő mezőbe.

    ![Keresési végrehajtó motorja](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

2. A **optimalizálási** tulajdonság alapértelmezett értéke **Tez**.

    ![Optimalizálás - Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Mappers hangolása

Hadoop megpróbálja vágási (*térkép*) több fájlok és a létrejövő folyamat egyetlen fájl fájlok párhuzamosan. Mappers száma attól függ, hogy a megszakítások számát. A következő két konfigurációs paraméterek meghajtót a Tez végrehajtómotor a megszakítások számát:

* `tez.grouping.min-size`: Alsó határ csoportosított valószínűségét, mérete az alapértelmezett érték 16 MB (16,777,216 bájt).
* `tez.grouping.max-size`: A felső határ csoportosított valószínűségét, mérete 1 GB (1,073,741,824 bájt) alapértelmezett értéket.

A teljesítmény a legjobb megoldás, mint csökkentése mindkét késés javítása céljából a további átviteli sebesség növelése paramétert.

Például, hogy egy adatok mérete 128 MB négy leképező feladatok, állíthat mindkét paraméter 32 MB-ra egyes (33,554,432 bájt).

1. A korlát paraméterek módosításához nyissa meg a **Configs** a Tez szolgáltatás lapján. Bontsa ki a **általános** panelen, és keresse meg a `tez.grouping.max-size` és `tez.grouping.min-size` paraméterek.

2. Mindkét paraméter beállítása **33,554,432** bájt (32 MB).

    ![Tez csoportosítási méretek](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Ezeket a módosításokat minden Tez feladatokhoz befolyásolja a kiszolgáló között. Ahhoz, hogy az optimális eredmény, válassza ki a megfelelő paraméterértékeket.

### <a name="tune-reducers"></a>Szűkítő hangolása

ORC és Snappy is kínál az nagy teljesítményt. Azonban Hive állhat túl kevés szűkítő alapértelmezés szerint okozó szűk keresztmetszeteket.

Tegyük fel, hogy van egy bemeneti adatok mérete 50 GB-ot. 1 GB-os, hogy adatokat ORC klassz kis tömörítést formátumban. Hive becslése szükség szerint szűkítő száma: (mappers bemenete bájtok száma / `hive.exec.reducers.bytes.per.reducer`).

Az alapértelmezett beállításokkal az ebben a példában 4 szűkítő.

A `hive.exec.reducers.bytes.per.reducer` paraméter adja meg egy nyomáscsökkentő feldolgozott bájtok száma. Az alapértelmezett érték: 64 MB. Le ezt az értéket hangolása növeli a párhuzamosságot és javíthatja a teljesítményt. Túl kevés hangolása sikerült túl sok szűkítő, potenciálisan hátrányosan a teljesítményt befolyásoló is eredményez. Ez a paraméter az adott követelmények, a tömörítési beállítások és más környezeti tényezők alapul.

1. A paraméter módosításához nyissa meg a Hive **Configs** lapra, és keresse a **/ nyomáscsökkentő adatok** paraméter a beállítások lapon.

    ![Adatok nyomáscsökkentő száma](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
2. Válassza ki **szerkesztése** módosítsa 128 MB-ra (134,217,728 bájt) értékét, és nyomja le az **Enter** mentéséhez.

    ![/ Nyomáscsökkentő - szerkesztett adatokat](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    8 szűkítő nincsenek kap egy, 1024 MB, az adatok egy nyomáscsökkentő, 128 MB bemeneti mérete (1024/128).

3. Értékkel a **/ nyomáscsökkentő adatok** paraméter szűkítő negatívan befolyásolja a lekérdezési teljesítmény nagy számú eredményezhet. Szűkítő maximális számának korlátozásához beállítása `hive.exec.reducers.max` egy megfelelő értékre. Az alapértelmezett érték: 1009.

### <a name="enable-parallel-execution"></a>Párhuzamos végrehajtás engedélyezése

A Hive-lekérdezést végrehajtja a rendszer egy vagy több fázisból áll. A független szakaszból párhuzamosan futtatható, ha, amely növeli a lekérdezések teljesítményét.

1.  Ahhoz, hogy a párhuzamos lekérdezés-végrehajtás, keresse meg a Hive **Config** lapra, és keresse meg a `hive.exec.parallel` tulajdonság. Az alapértelmezett értéke hamis. Módosítsa a beállítást igaz, és nyomja le az **Enter** mentheti az értéket.
 
2.  A párhuzamosan futtatható feladatok száma korlátozza, módosítsa a `hive.exec.parallel.thread.number` tulajdonság. Az alapértelmezett értéke 8.

    ![Exec párhuzamos struktúra](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Vectorization engedélyezése

Hive feldolgozza az adatokat soronként. Vectorization struktúra egy olyan sor, hanem 1024 sorok blokkokban adatfeldolgozásra történő egyszerre irányítja. Vectorization tulajdonság csak az ORC formátumát vonatkozik.

1. Ahhoz, hogy egy vectorized lekérdezés-végrehajtás, keresse meg a Hive **Configs** lapra, és keresse meg a `hive.vectorized.execution.enabled` paraméter. Az alapértelmezett értéke igaz, a Hive 0.13.0 vagy újabb.
 
2. A lekérdezés csökkentse oldalának vectorized végrehajtása engedélyezéséhez állítsa a `hive.vectorized.execution.reduce.enabled` paraméter igaz értékű. Az alapértelmezett értéke hamis.

    ![Hive vectorized végrehajtás](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Költség-alapú optimalizálási (CBO) engedélyezése

Alapértelmezés szerint a Hive egy optimális lekérdezés végrehajtási terv található szabálykészlet következő. Költség-alapú optimalizálási (CBO) értékeli ki a lekérdezés végrehajtása több tervek költségekkel rendel hozzá minden egyes programra, majd határozza meg a lekérdezés végrehajtása legolcsóbb tervezi.

Ahhoz, hogy a CBO, keresse meg a Hive **Configs** lapra, és keresse meg `parameter hive.cbo.enable`, váltson a váltógomb való **a**.

![CBO config](./media/hdinsight-changing-configs-via-ambari/cbo.png)

A következő konfigurációs paramétereket Hive lekérdezés teljesítmény növelése érdekében CBO engedélyezésekor a rendszer:

* `hive.compute.query.using.stats`

    Amikor true értéke esetén Hive használja például a egyszerű lekérdezések megválaszolásához találhatók a metaadattárhoz statisztika `count(*)`.

    ![CBO statisztikák](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Oszlop statisztikai adatainak jönnek létre, ha CBO engedélyezve van. Hive használja az oszlop statisztikai adatainak optimalizálni a lekérdezéseket, metaadattárhoz tárolódnak. Az egyes oszlopok oszlop statisztikai adatainak beolvasása hosszabb időt vesz igénybe, ha az oszlopok száma túl magas. Ha értéke HAMIS, ez a beállítás letiltja a metaadattárhoz lekérdezésekor oszlop statisztikáit.

    ![Hive statisztikák set oszlop statisztikák](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Alapszintű partíció statisztikai adatainak, például a sorok, az adatok mérete és a fájlméret metaadattárhoz vannak tárolva. Ha igaz értéke esetén a statisztikák beolvasott metaadattárhoz a partíciót. Hamis érték esetén a fájlméret beolvassa a fájlrendszerből, és a sor séma beolvassa a sorok száma.

    ![Hive statisztikák set partíció statisztikák](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Köztes tömörítésének engedélyezése

Térkép feladatok létrehozása a nyomáscsökkentő feladatok által használt köztes fájlokat. Köztes tömörítési zsugorítja a köztes méretét.

Hadoop-feladatokat rendszerint i/o szűk keresztmetszetét adja ki. Az adatok tömörítése és a teljes hálózati átviteli felgyorsítható.

A rendelkezésre álló tömörítési típusok a következők:

| Formátum | Eszköz | Algoritmus | Fájlnévkiterjesztés | Feloszthatók? |
| -- | -- | -- | -- | -- |
| A gzip | A gzip | DEFLATE | .GZ | Nem |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Igen |
| LZO | Lzop | LZO | .lzo | Igen, ha indexelt |
| klassz kis | – | klassz kis | klassz kis | Nem |

Általános szabályként feloszthatók tömörítés, akkor fontos, ellenkező esetben nagyon kevés mappers jön létre. Ha a bemeneti adatok szöveg, `bzip2` az ajánlott beállítás. ORC formátumnál Snappy a leggyorsabb tömörítési beállítás.

1. Ahhoz, hogy a köztes tömörítés, keresse meg a Hive **Configs** lapot, és utána állítsa be a `hive.exec.compress.intermediate` paraméter igaz értékű. Az alapértelmezett értéke hamis.

    ![Hive exec compress köztes](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]
    > A köztes a fájlok tömörítésével, válassza ki a tömörítési kodek költsége alacsonyabb CPU akkor is, ha a kodek nem rendelkezik egy nagy tömörítési kimeneti.

2. A köztes tömörítési kodek megadásához adja hozzá az egyéni tulajdonság `mapred.map.output.compression.codec` számára a `hive-site.xml` vagy `mapred-site.xml` fájlt.

3. Egyéni beállítás hozzáadása:

    a. Keresse meg a Hive **Configs** fülre, és válassza ki a **speciális** fülre.

    b. Az a **speciális** lapon található, és bontsa ki a **egyéni hive-hely** ablaktáblán.

    c. Kattintson a hivatkozásra **tulajdonság hozzáadása** az egyéni hive-hely ablaktábla alján.

    d. Adja meg a tulajdonság hozzáadása ablak `mapred.map.output.compression.codec` kulcsként és `org.apache.hadoop.io.compress.SnappyCodec` értékeként.

    e. Kattintson a **Hozzáadás** parancsra.

    ![Egyéni tulajdonság struktúra](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Ez tömöríti a köztes fájlt az klassz kis tömörítésével lehetett. A tulajdonság hozzáadása után az egyéni hive-hely ablaktáblán jelenik meg.

    > [!NOTE]
    > Ezzel az eljárással módosítja az `$HADOOP_HOME/conf/hive-site.xml` fájlt.

### <a name="compress-final-output"></a>Végső kimenetet tömörítése

A Hive kimenetként is lehet tömörített.

1. A végső Hive kimeneti tömörítéséhez, keresse meg a Hive **Configs** lapot, és utána állítsa be a `hive.exec.compress.output` paraméter igaz értékű. Az alapértelmezett értéke hamis.

2. A kimeneti tömörítési kodek mellett dönt, vegye fel a `mapred.output.compression.codec` egyéni tulajdonság egyéni hive-hely megjelenítő, az előző szakasz 3. lépésben leírtak szerint.

    ![Egyéni tulajdonság struktúra](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Spekulatív végrehajtási engedélyezése

Spekulatív végrehajtási elindítja egy bizonyos számú észlelése és a teljes feladat-végrehajtás egyedi eredmények optimalizálásával fokozása mellett a lassan futó feladat követőt, tiltólistára ismétlődő feladatokat.

Hosszan futó MapReduce-feladatok bemeneti nagy mennyiségű spekulatív végrehajtási nem szabad engedélyezni.

1. Ahhoz, hogy a spekulatív végrehajtási, keresse meg a Hive **Configs** lapot, és utána állítsa be a `hive.mapred.reduce.tasks.speculative.execution` paraméter igaz értékű. Az alapértelmezett értéke hamis.

    ![Hive mapred csökkentése spekulatív feladatok végrehajtása](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Dinamikus partíciók hangolása

Hive lehetővé teszi, hogy a dinamikus partíciók létrehozása, amikor egy tábla minden egyes partíció számítógépnevekről nélkül rekordok beszúrása. Ez a tulajdonság hatékony, bár számos partíciókat és a fájlok minden partíció esetében számos létrehozását eredményezhet.

1. A Hive dinamikus partíciók, ehhez a `hive.exec.dynamic.partition` kell, hogy a paraméter értéke igaz (alapértelmezett).

2. Módosítsa a dinamikus partíció módot *szigorú*. Legalább egy partíciót szigorú módban nem lehet statikus. Ez megakadályozza, hogy a partíció szűrő a WHERE záradék nélkül lekérdezések Ez azt jelenti, hogy *szigorú* megakadályozza, hogy a lekérdezések, amelyek az összes partíció vizsgálata. Keresse meg a Hive **Configs** lapot, és utána állítsa be `hive.exec.dynamic.partition.mode` való **szigorú**. Az alapértelmezett érték **nonstrict**.
 
3. A létrehozandó dinamikus partíciók számának korlátozása érdekében módosítsa a "hive.exec.max.dynamic.partitions' paraméter. Az alapértelmezett érték 5 000.
 
4. Az egyes csomópontok dinamikus partíciók száma korlátozza, módosítsa `hive.exec.max.dynamic.partitions.pernode`. Az alapértelmezett érték 2000.

### <a name="enable-local-mode"></a>Helyi üzemmód engedélyezése

Helyi módú lehetővé teszi, hogy a Hive feladat feladatokat végez egy gépen, vagy néha egyetlen folyamat. Ez növeli a lekérdezési teljesítményt, ha a bemeneti adatok kicsi, és növeli a lekérdezések feladatok elindítása használ fel a teljes lekérdezés végrehajtása jelentős részéért.

Helyi módú engedélyezéséhez vegye fel a `hive.exec.mode.local.auto` paraméter egyéni hive-hely panelre, 3. lépésében leírtak szerint a [köztes tömörítésének engedélyezéséhez](#enable-intermediate-compression) szakasz.

![Exec mód helyi automatikus struktúra](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Set egyetlen MapReduce MultiGROUP által

Ha ez a tulajdonság értéke true, közös csoportosítási kulccsal rendelkező MultiGROUP által lekérdezés egyetlen MapReduce feladatot hoz létre.  

Ez a viselkedés engedélyezéséhez vegye fel a `hive.multigroupby.singlereducer` paraméter egyéni hive-hely ablaktáblára, 3. lépésében leírtak szerint a [köztes tömörítésének engedélyezéséhez](#enable-intermediate-compression) szakasz.

![Hive állítja be egyetlen MapReduce MultiGROUP](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>További Hive optimalizálást

A következő részekben további Hive kapcsolatos optimalizálást állíthatja be.

#### <a name="join-optimizations"></a>Csatlakozás az optimalizálás.

A Hive az alapértelmezett illesztési típus szerepel a *sorrendű illesztési*. A Hive különleges mappers olvassa a bemeneti, és egy illesztési kulcs/érték pár köztes fájlba hozható létre. Hadoop rendezi, és egyesíti a párok egy véletlen szakaszában. Ebben a szakaszban sorrendű is drága. Az adatok alapján a megfelelő csatlakozási kiválasztásával jelentősen fejleszti a teljesítményt.

| Csatlakozás típusa | Időpont | Útmutató | Hive-beállítások | Megjegyzések |
| -- | -- | -- | -- | -- |
| Csatlakoztatás sorrendű | <ul><li>Alapértelmezett választás</li><li>Mindig működik.</li></ul> | <ul><li>A tábla részei olvassa be</li><li>Gyűjtők és a rendezés illesztési kulcs</li><li>Egy gyűjtő küld minden csökkentése</li><li>Csatlakozás a Reduce oldalon történik</li></ul> | Nincs jelentős Hive szükséges beállítása | Minden alkalommal működik |
| Térkép illesztés | <ul><li>Egy tábla elfér a memóriában</li></ul> | <ul><li>Kis tábla beolvassa a kivonattábla memória</li><li>A nagy méretű fájl keresztül adatfolyamok</li><li>A kivonattábla rekordokban illesztése</li><li>A kizárólag hozzárendelő vannak illesztések</li></ul> | `hive.auto.confvert.join=true` | Nagyon gyorsan, de korlátozott |
| Rendezés egyesítési gyűjtő | Ha mindkét táblákat: <ul><li>Azonos rendezve</li><li>Azonos bucketed</li><li>Csatlakozás a rendezett/összegyűjtött oszlopra.</li></ul> | Minden folyamat: <ul><li>A gyűjtő olvassa be az egyes táblanevek</li><li>Feldolgozza a legkisebb értékű sorok</li></ul> | `hive.auto.convert.sortmerge.join=true` | Rendkívül hatékony |

#### <a name="execution-engine-optimizations"></a>Végrehajtási motor optimalizálás.

A Hive-végrehajtó motor optimalizálási további javaslatokat:

| Beállítás | Ajánlott | A HDInsight alapértelmezett |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | Igaz értéket = biztonságosabb, lassabb; FALSE = gyorsabban | false |
| `tez.am.resource.memory.mb` | 4 GB-os felső határa a legtöbb | Automatikusan beállított |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="pig-optimization"></a>A Pig optimalizálása

Az Ambari webes felhasználói felület finomhangolhatják a Pig-lekérdezéseket a módosíthatja a Pig tulajdonságait. A Pig tulajdonságok közvetlenül módosítsák a Pig tulajdonságok az Ambari módosítja a `/etc/pig/2.4.2.0-258.0/pig.properties` fájlt.

1. A Pig tulajdonságainak módosításához nyissa meg a Pig **Configs** lapra, és végül a **speciális pig-tulajdonságok** ablaktáblán.

2. Található, és állítsa vissza a módosítani kívánt tulajdonság értékének módosítása.

3. Válassza ki **mentése** mentheti az új értéket az ablak jobb felső meg. Néhány tulajdonság a szolgáltatás újraindítására lehet szükség.

    ![A speciális pig-tulajdonságok](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]
> A munkamenet szintű beállítások felülírják a tulajdonságértékek a `pig.properties` fájlt.

### <a name="tune-execution-engine"></a>Végrehajtó motorja hangolása

Két végrehajtási motorok érhetők el a Pig-parancsfájlok végrehajtása: MapReduce és Tez. Tez egy optimalizált motor, és sokkal gyorsabb, mint a MapReduce.

1. A végrehajtó motor a módosítandó a **speciális pig-tulajdonságok** ablaktáblában található a tulajdonság `exectype`.

2. Az alapértelmezett érték **MapReduce**. Módosítsa úgy, hogy **Tez**.


### <a name="enable-local-mode"></a>Helyi üzemmód engedélyezése

Hasonló a Hive, helyi módú viszonylag kisebb mennyiségű adatot feladatok szolgál.

1. A helyi mód engedélyezéséhez állítsa `pig.auto.local.enabled` való **igaz**. Az alapértelmezett értéke hamis.

2. Feladatok egy bemeneti adatok mérete kisebb, mint a `pig.auto.local.input.maxbytes` tulajdonság értéke kisebb feladatokat kell tekinteni. Az alapértelmezett értéke 1 GB.


### <a name="copy-user-jar-cache"></a>Másolja a felhasználói jar gyorsítótár

Felhasználó által megadott függvények számára szükséges egy elosztott gyorsítótár elérhetővé feladat csomópontok JAR fájlok másolása a Pig. A JAR-fájlok kivételével nem módosulnak. Ha engedélyezve van, a `pig.user.cache.enabled` beállítás lehetővé teszi, hogy a JAR-fájlok kivételével használja fel őket a feladatok futnak, amelyeket az adott felhasználó a gyorsítótárban lévő kell elhelyezni. Ez a feladat teljesítmény kisebb növekedése eredményez.

1. Engedélyezéséhez állítsa `pig.user.cache.enabled` igaz értékre. Az alapértelmezett értéke false.

2. A gyorsítótárazott JAR-fájlok kivételével alap elérési útjának megadásához beállítása `pig.user.cache.location` az alap elérési útra. Az alapértelmezett érték `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>A memóriabeállításokat teljesítményének optimalizálása

A következő memóriabeállításokat segíthet a Pig-parancsprogram teljesítményének optimalizálásához.

* `pig.cachedbag.memusage`: A tulajdonságcsomag számára lefoglalt memória mennyisége. A tulajdonságcsomag rekordokat gyűjteménye. Egy rekord mezők egy rendezett sorozata, mező pedig az adatok. Ha az adatok egy tasakban túl a lefoglalt memória, akkor kiömlött lemezre. Az alapértelmezett érték: 0,2, amely a rendelkezésre álló memória 20 százalékát jelenti. Ez a memória az alkalmazás által megosztott minden zsákokban.

* `pig.spill.size.threshold`: Zsákokban nagyobb, mint a spill adatbázisméret küszöbértéke (bájtban) vannak kiömlött lemezre. Az alapértelmezett érték 5 MB.


### <a name="compress-temporary-files"></a>Ideiglenes fájlok tömörítése

Pig ideiglenes fájlokat generál a feladat végrehajtása során. A teljesítmény növelését olvasása vagy írása a lemezre közben eredményezi, hogy az ideiglenes fájlok tömörítése. A következő beállítások segítségével ideiglenes fájlok tömörítése.

* `pig.tmpfilecompression`: Ha értéke igaz, lehetővé teszi az ideiglenes fájl tömörítése. Az alapértelmezett értéke hamis.

* `pig.tmpfilecompression.codec`: Az ideiglenes fájlok tömörítése használandó tömörítési kodek. Az ajánlott tömörítési kodekeket LZO és Snappy alacsonyabb CPU-kihasználtság.

### <a name="enable-split-combining"></a>Vegyes egyidejű engedélyezése

Ha engedélyezve van, a rendszer kombinálja a kisméretű fájlok kevesebb térkép feladatokhoz. Ez növeli a sok kisméretű fájlok feladatok hatékonyságát. Engedélyezéséhez állítsa `pig.noSplitCombination` igaz értékre. Az alapértelmezett értéke hamis.


### <a name="tune-mappers"></a>Mappers hangolása

Mappers számát szabályozza a tulajdonságának módosításával `pig.maxCombinedSplitSize`. Azt határozza meg az egyetlen feladat által feldolgozandó adatok méretét. Az alapértelmezett érték a fájlrendszer alapértelmezett blokkméret. Ez a leképező feladatok számának csökkenést értéket eredményez növelését.


### <a name="tune-reducers"></a>Szűkítő hangolása

Szűkítő száma kiszámítja a paraméter `pig.exec.reducers.bytes.per.reducer`. A paraméter egy nyomáscsökkentő feldolgozott bájtok száma, 1 GB-os alapértelmezés szerint. Korlátozza az szűkítő maximális számát, állítsa be a `pig.exec.reducers.max` tulajdonság 999 alapértelmezés szerint.


## <a name="hbase-optimization-with-the-ambari-web-ui"></a>Az Ambari webes felhasználói felület HBase optimalizálása

A HBase-konfiguráció módosítása a **HBase Configs** fülre. A következő szakaszok ismertetik a HBase teljesítményt befolyásoló fontos konfigurációs beállításokat.

### <a name="set-hbaseheapsize"></a>HBASE_HEAPSIZE beállítása

A HBase halommemória mérete meghatározza a maximális halommemória mérete (MB) által használandó *régió* és *fő* kiszolgálók. Az alapértelmezett érték 1000 MB. Ez a fürt terheléshez kell beállítani.

1. Módosításához nyissa meg a **speciális HBase-env** a HBase ablaktábláján **Configs** lapot, és keresse meg a `HBASE_HEAPSIZE` beállítást.

2. Módosítsa az alapértelmezett érték 5 000 MB.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Olvasási műveleteket munkaterhelések optimalizálása

A következő konfigurációk fontosak olvasás-gyakori feladatok teljesítményének javítása érdekében.

#### <a name="block-cache-size"></a>Gyorsítótár blokkméret

A blokk-gyorsítótárához az olvasási gyorsítótár. Annak méretét szabályozza a `hfile.block.cache.size` paraméter. Az alapértelmezett értéke 0,4, vagyis a teljes tartomány kiszolgálómemória 40 %-kal. Minél nagyobb a gyorsítótár blokkméret, minél gyorsabb véletlenszerű olvasási lesz.

1. Módosíthatják ezt a paramétert, lépjen a **beállítások** a HBase lapján **Configs** lapra, és keresse meg **RegionServer lefoglalt pufferek olvasási %**.

    ![A HBase blokk-gyorsítótár mérete](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
2. Az érték módosításához jelölje ki a **szerkesztése** ikonra.


#### <a name="memstore-size"></a>Kapott mérete

Összes szerkesztést tárolódnak a memóriapuffer nevű egy *kapott*. Ez növeli a teljes adatmennyiség egyetlen művelettel lemezre lehet írni, és azt később elérhetők sebessége a legutóbbi módosítások számára. A kapott mérete a következő két paramétert határozzák meg:

* `hbase.regionserver.global.memstore.UpperLimit`: A régió kiszolgáló által kapott együtt használható maximális százalékát határozza meg.

* `hbase.regionserver.global.memstore.LowerLimit`: A régió kiszolgáló kombinált kapott használó minimális aránya határozza meg.

Véletlenszerű olvasási optimalizálásához a kérést kapott felső és alsó határértéke csökkentése érdekében.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>A lemezről vizsgálatára lehívott sorok száma

A `hbase.client.scanner.caching` beállítás határozza meg, olvassa el a sorok száma lemezre a `next` metódus lehívásra kerül a beolvasáshoz.  Az alapértelmezett érték 100. Minél nagyobb a szám, a kisebb értékre a távoli hívások az ügyféltől a régió kiszolgáló, ami azt eredményezi, hogy a vizsgálatok gyorsabban. Azonban ez növeli az ügyfél Memóriaterhelést.

![A HBase lehívott sorok száma](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]
> Az érték nincs beállítva, például úgy, hogy a beolvasáshoz a következő metódus meghívása között eltelő idő nagyobb, mint a képolvasó időtúllépése. Határozza meg a képolvasó időkorlát tartama az `hbase.regionserver.lease.period` tulajdonság.


### <a name="optimize-write-heavy-workloads"></a>Írási műveleteket munkaterhelések optimalizálása

A következő konfigurációk fontos írási műveleteket feladatok teljesítményének javítása érdekében.


#### <a name="maximum-region-file-size"></a>Fájl maximális méretét

A HBase tárolja az adatokat egy belső fájlformátum, úgynevezett *HFile*. A tulajdonság `hbase.hregion.max.filesize` egy régió egyetlen HFile méretét határozza meg.  Egy régiót két régió oszlik, ha az egy régióban található összes HFiles összege nagyobb, mint ez a beállítás.
 
![A HBase HRegion maximális fájl mérete](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Minél nagyobb a terület méretét, minél kisebb a megszakítások számát. A fájlméret, hogy a maximális eredményez írási teljesítményt értékének meghatározásához növelhető.


#### <a name="avoid-update-blocking"></a>Frissítés elkerülni

* A tulajdonság `hbase.hregion.memstore.flush.size` határozza meg, ahol ki van ürítve kérést kapott méretének lemezre. Az alapértelmezett mérete 128 MB.

* Határozza meg a Hbase régió letiltása szorzójaként `hbase.hregion.memstore.block.multiplier`. Az alapértelmezett érték a 4. A maximális szám a 8.

* HBase blokkolja a frissítéseket, ha a kapott (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bájt.

    Az alapértelmezett értékekkel kiürítési mérete és letiltása szorzójaként frissítések blokkolják a kérést kapott 128 * 4 = 512 MB-nál. A frissítést blokkolja számának csökkentése érdekében értékének növelése `hbase.hregion.memstore.block.multiplier`.

![A HBase régió letiltása szorzójaként](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Adja meg a kérést kapott mérete

Kapott méretének meghatározása szerint a `hbase.regionserver.global.memstore.UpperLimit` és `hbase.regionserver.global.memstore.LowerLimit` paraméterek. A beállítás ezeket az értékeket az egyes más csökkenti a szüneteltetése során egyenlőségi (is, amely gyakoribb kiürítése) ír, és nagyobb írási teljesítményt eredményez.


### <a name="set-memstore-local-allocation-buffer"></a>Set kérést kapott helyi foglalási puffer

A tulajdonság határozza meg kérést kapott helyi foglalási puffer használati `hbase.hregion.memstore.mslab.enabled`. Ha engedélyezve van (igaz), így a halommemória töredezettségének nagy írási művelet során. Az alapértelmezett érték: igaz.
 
![hbase.hregion.memstore.mslab.Enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="see-also"></a>Lásd még

* [Az Ambari webes felhasználói felület a HDInsight-fürtök kezelése](hdinsight-hadoop-manage-ambari.md)
* [Ambari REST API-n](hdinsight-hadoop-manage-ambari-rest-api.md)
