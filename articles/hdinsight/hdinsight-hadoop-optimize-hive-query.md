---
title: Hive-lekérdezések optimalizálása az Azure HDInsightban
description: Ez a cikk ismerteti, hogyan optimalizálhatja az Apache Hive-lekérdezések Hadoop a HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 144d51d08a61526ec0f183a63e1fdf5658136293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272330"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Apache Hive-lekérdezések optimalizálása az Azure HDInsightban

Az Azure HDInsightban számos fürttípus és -technológia futtatható Apache Hive-lekérdezések. A HDInsight-fürt létrehozásakor válassza ki a megfelelő fürttípust a számítási feladatok igényeinek megfelelő teljesítmény optimalizálásához.

Válassza például **az Interaktív lekérdezés** fürttípusát az ad hoc, interaktív lekérdezésekre való optimalizáláshoz. Válassza az Apache **Hadoop** fürttípusát a kötegelt folyamatként használt Hive-lekérdezésekhez való optimalizáláshoz. **A Spark** és **a HBase** fürttípusok is futtathatnak Hive-lekérdezéseket. A Hive-lekérdezések különböző HDInsight-fürttípusokon való futtatásáról a [Mi az Apache Hive és a HiveQL az Azure HDInsight-on című témakörben.](hadoop/hdinsight-use-hive.md)

A Hadoop-fürttípusú HDInsight-fürtök alapértelmezés szerint nincsenek teljesítményre optimalizálva. Ez a cikk a Hive teljesítményoptimalizálási módszereit ismerteti, amelyeket a lekérdezésekre alkalmazhat.

## <a name="scale-out-worker-nodes"></a>Munkavégző csomópontok horizontális felskálázása

A HDInsight-fürt munkavégző csomópontjaiszámának növelése lehetővé teszi, hogy a munka több leképezőt és szűkítőt használhat párhuzamosan. A HDInsight ban kétféleképpen növelheti a horizontális felskálázást:

* Fürt létrehozásakor megadhatja a feldolgozócsomópontok számát az Azure Portalon, az Azure PowerShellben vagy a parancssori felületen.  További információ: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md). Az alábbi képernyőképen látható a munkavégző csomópont konfigurációja az Azure Portalon:
  
    ![Az Azure Portal fürtméret-csomópontjai](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png "scaleout_1")

* A létrehozás után módosíthatja a feldolgozócsomópontok számát is, hogy tovább skálázhassa a fürtöt anélkül, hogy újra létrehozna egyet:

    ![Az Azure Portal méretezése fürtméret](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png "scaleout_2")

A HDInsight méretezéséről a [HDInsight-fürtök méretezése című](hdinsight-scaling-best-practices.md) témakörben talál további információt.

## <a name="use-apache-tez-instead-of-map-reduce"></a>Az Apache Tez használata a Térképcsökkentés helyett

[Az Apache Tez](https://tez.apache.org/) a MapReduce motor alternatív végrehajtási motorja. Linux-alapú HDInsight-fürtök tez alapértelmezés szerint engedélyezve van.

![HDInsight Apache Tez – áttekintés diagram](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png)

Tez gyorsabb, mert:

* **Irányított aciklikus gráf (DAG) végrehajtása egyetlen feladatként a MapReduce motorban.** A DAG megköveteli, hogy minden egyes leképezőkészletet egy szűkítőkészlet kövessen. Ez azt eredményezi, hogy minden Hive-lekérdezéshez több MapReduce-feladat kerül kiforgatva. Tez nem rendelkezik ilyen korlátozás, és képes feldolgozni összetett DAG, mint egy feladat, így minimalizálva feladat indítási terhelést.
* **Elkerüli a felesleges írásokat**. Több feladat ugyanazt a Hive-lekérdezést a MapReduce motorban dolgozza fel. Az egyes MapReduce-feladat kimenete a hdfs-be íródik a köztes adatokhoz. Mivel a Tez minimálisra csökkenti az egyes Hive-lekérdezések feladatainak számát, képes elkerülni a szükségtelen írásokat.
* **Minimálisra csökkenti az indítási késleltetést.** A Tez jobban tudja minimalizálni az indítási késleltetést azáltal, hogy csökkenti a kezdéshez szükséges leképezők számát, és javítja az optimalizálást.
* **Újrafelhasználása konténerek**. Amikor csak lehetséges, a Tez újra fel tudja használni a tárolókat annak érdekében, hogy a tárolók indítása miatti késés csökkenjen.
* **Folyamatos optimalizálási technikák**. Hagyományosan optimalizálás történt fordítási fázisban. Azonban további információ áll rendelkezésre a bemenetek, amelyek lehetővé teszik a jobb optimalizálás futásidőben. A Tez folyamatos optimalizálási technikákat használ, amelyek lehetővé teszik a terv további optimalizálását a futásidejű fázisban.

Ezekről a fogalmakról további információt az [Apache TEZ .](https://tez.apache.org/)

Bármely Hive-lekérdezést engedélyezhet, ha a lekérdezést a következő set paranccsal előzi meg:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Hive particionálás

I/O-műveletek a fő teljesítmény szűk keresztmetszetet a Hive-lekérdezések futtatásához. A teljesítmény javítható, ha az olvasandó adatok mennyisége csökkenthető. Alapértelmezés szerint a Hive lekérdezések teljes Hive-táblák at késnek. Azonban a lekérdezések, amelyek csak kis mennyiségű adatot kell bevizsgálni (például a lekérdezések szűréssel), ez a viselkedés szükségtelen többletterhelést okoz. Hive particionálás lehetővé teszi, hogy a Hive-lekérdezések csak a szükséges mennyiségű adat hoz való hozzáférés a Hive-táblák.

Hive particionálás valósítja meg a nyers adatok átrendezése az új könyvtárakat. Minden partíció saját fájlkönyvtárral rendelkezik. A particionálást a felhasználó határozza meg. Az alábbi ábra egy Hive-tábla év oszlop on-át történő particionálását mutatja *be.* Minden évben új könyvtár jön létre.

![HDInsight Apache Hive particionálás](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png)

Néhány particionálási szempont:

* **Ne partíció alatt** - Particionálás oszlopok csak néhány értéket okozhat néhány partíciót. Például a nemek szerinti particionálás csak két létrehozandó partíciót hoz létre (férfi és női), így csak a késés legfeljebb a felére csökken.
* **Ne a partíció n –** A másik véglet, egy partíció létrehozása egy oszlopban egy egyedi érték (például userid) okoz több partíciót. Partíción keresztül okoz sok stresszt a fürt namenode, mivel kezelnie kell a nagy számú könyvtárak.
* **Elkerülése adatdöntés** - Válassza ki a particionálási kulcsot bölcsen, hogy az összes partíció egyenletes méretű legyen. Például az *Állapot* oszlop particionálása torzíthatja az adatok eloszlását. Mivel Kalifornia állam lakossága majdnem 30-szor, hogy a Vermont, a partíció mérete potenciálisan ferde és a teljesítmény változhat rettenetesen.

Partíciós tábla létrehozásához használja a *Partitioned By záradékot:*

```sql
CREATE TABLE lineitem_part
      (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
PARTITIONED BY(L_SHIPDATE STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE;
```

A particionált tábla létrehozása után statikus particionálást vagy dinamikus particionálást hozhat létre.

* **Statikus particionálás** azt jelenti, hogy már szilánkos adatokat a megfelelő könyvtárakban. Statikus partíciók hozzáadása hive partíciók manuálisan a címtár helye alapján. A következő kódrészlet egy példa.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **A dinamikus particionálás** azt jelenti, hogy azt szeretné, hogy a Hive automatikusan hozzon létre partíciókat. Mivel már létrehozta a particionálási táblát az átmeneti táblából, mindössze annyit kell tennie, hogy adatokat szúr be a particionált táblába:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

További információt a [Particionált táblák című témakörben talál.](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables)

## <a name="use-the-orcfile-format"></a>Az ORCFile formátum használata

A Hive különböző fájlformátumokat támogat. Példa:

* **Szöveg**: az alapértelmezett fájlformátum, és a legtöbb forgatókönyvvel működik.
* **Avro**: jól működik az együttműködési forgatókönyvek.
* **ORC/Parketta:** a legjobban alkalmas a teljesítményre.

Orc (Optimalizált sor oszlopos) formátum egy rendkívül hatékony módja a Hive-adatok tárolására. Más formátumokkal összehasonlítva az ORC-nek a következő előnyei vannak:

* komplex típusok támogatása, beleértve a DateTime-ot és az összetett és félig strukturált típusokat.
* akár 70%-os tömörítésig.
* 10 000 soronként indexel, ami lehetővé teszi a sorok kihagyását.
* a futásidejű végrehajtás jelentős csökkenése.

Az ORC formátum engedélyezéséhez először hozzon létre egy táblázatot az *ORC-ként tárolt*záradékkal:

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

Ezután az előkészítési táblából szúrhat be adatokat az ORC táblába. Példa:

```sql
INSERT INTO TABLE lineitem_orc
SELECT L_ORDERKEY as L_ORDERKEY,
         L_PARTKEY as L_PARTKEY ,
         L_SUPPKEY as L_SUPPKEY,
         L_LINENUMBER as L_LINENUMBER,
         L_QUANTITY as L_QUANTITY,
         L_EXTENDEDPRICE as L_EXTENDEDPRICE,
         L_DISCOUNT as L_DISCOUNT,
         L_TAX as L_TAX,
         L_RETURNFLAG as L_RETURNFLAG,
         L_LINESTATUS as L_LINESTATUS,
         L_SHIPDATE as L_SHIPDATE,
         L_COMMITDATE as L_COMMITDATE,
         L_RECEIPTDATE as L_RECEIPTDATE,
         L_SHIPINSTRUCT as L_SHIPINSTRUCT,
         L_SHIPMODE as L_SHIPMODE,
         L_COMMENT as L_COMMENT
FROM lineitem;
```

Az ORC formátumról az [Apache Hive Language kézikönyvben olvashat bővebben.](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC)

## <a name="vectorization"></a>Vektorizálás

Vektorizálás lehetővé teszi, hogy a Hive feldolgozni egy köteg 1024 sorok együtt feldolgozása helyett egy sorban egy időben. Ez azt jelenti, hogy az egyszerű műveletek gyorsabban elvégezhetők, mert kevesebb belső kódot kell futtatni.

A Hive-lekérdezés vektorizálási előtagának engedélyezése a következő beállítással:

```hive
set hive.vectorized.execution.enabled = true;
```

További információt a [Vektorizált lekérdezések végrehajtása című témakörben talál.](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution)

## <a name="other-optimization-methods"></a>Egyéb optimalizálási módszerek

Több optimalizálási módszert is figyelembe vehet, például:

* **Hive-gyűjtő:** olyan technika, amely lehetővé teszi a fürt vagy szegmentálás a lekérdezési teljesítmény optimalizálása nagy adathalmazok.
* **Csatlakozás optimalizálása:** optimalizálása Hive lekérdezés-végrehajtási tervezés hatékonyságának növelése az illesztések és csökkenti a felhasználói tippeket. További információ: [Csatlakozás optimalizálás](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Növelje a szűkítőket**.

## <a name="next-steps"></a>További lépések

Ebben a cikkben számos gyakori Hive-lekérdezésoptimalizálási módszert ismertettél meg. További információ: a következő cikkek:

* [Az Apache Hive használata a HDInsightban](hadoop/hdinsight-use-hive.md)
* [A járatkésési adatok elemzése a HDInsight interaktív lekérdezése segítségével](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
* [Twitter-adatok elemzése az Apache Hive használatával a HDInsightban](hdinsight-analyze-twitter-data-linux.md)
