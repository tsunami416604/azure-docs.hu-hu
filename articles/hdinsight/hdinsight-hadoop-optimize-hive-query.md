---
title: Az Azure HDInsight Hive-lekérdezések optimalizálása
description: Ez a cikk azt ismerteti, hogyan optimalizálható az Apache Hive-lekérdezések a hadoop együttes használata a HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 1c863bc3b0ded0558e052ee3d98586ad815a0cfd
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202741"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Az Azure HDInsight az Apache Hive-lekérdezések optimalizálása

Az Azure HDInsight, a számos több fürttípusokat és technológiákat, amelyek Apache Hive-lekérdezéseket is futtathat. A HDInsight-fürt létrehozásakor válassza ki a megfelelő fürt segítségével optimalizálják a teljesítményt a munkaterhelés igényei szerint. 

Például választhatja, **interaktív lekérdezés** fürt típusát az alkalmi, interaktív lekérdezésekhez optimalizálja. Válassza ki az Apache **Hadoop** fürt típusa a kötegelt műveletként használható a Hive-lekérdezések optimalizálása érdekében. **A Spark** és **HBase** típusok fürtöt is futtathat Hive-lekérdezéseket. További információ a Hive-lekérdezések futtatása HDInsight-fürt különböző: [Mi az Apache Hive és az Azure HDInsight HiveQL?](hadoop/hdinsight-use-hive.md).

HDInsight-fürtökkel a Hadoop-fürt típusa nem optimalizált teljesítményt alapértelmezés szerint. Ez a cikk ismerteti az egyes a leggyakoribb struktúra módszereit, hogy a lekérdezések alkalmazhat.

## <a name="scale-out-worker-nodes"></a>Munkavégző csomópontok horizontális felskálázása

Egy HDInsight-fürtön a munkavégző csomópontok számának növelése lehetővé teszi, hogy több leképező és csökkentő futhasson párhuzamosan munka. Horizontális felskálázás a HDInsight növeléséhez két módja van:

* Amikor a fürt létrehozásakor a munkavégző csomópontok az Azure portal, az Azure PowerShell-lel vagy a parancssori felület használatával is megadhat.  További információ: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md). Az alábbi képernyőfelvételen a munkavégző csomópont-konfiguráció az Azure Portalon:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
    
* A létrehozás után szerkesztheti is az időformátum, egy további fürt horizontális munkavégző csomópontok számát:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

HDInsight méretezésével kapcsolatos további információkért lásd: [méretezési HDInsight-fürtök](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Az Apache Tez használhatja a Mapreduce helyett

[Az Apache Tez](https://hortonworks.com/hadoop/tez/) van egy alternatív végrehajtó motorja a MapReduce-motor. Linux-alapú HDInsight-fürtök Tez alapértelmezés szerint engedélyezve van.

![tez_1][image-hdi-optimize-hive-tez_1]

Tez gyorsabban azért, mert:

* **A MapReduce-motor önállóan irányított aciklikus Graph (DAG) végrehajtása**. A DAG igényel minden egyes leképező csökkentő egy készletét kell követnie. Ez azt eredményezi, hogy több MapReduce-feladatok minden egyes Hive-lekérdezés percenként. Tez nem tartalmaz ilyen korlátozást, és összetett DAG tud feldolgozni egy feladatként, így minimalizálja a feladat indítási terhelést.
* **Ezzel elkerülheti a felesleges írások**. Több feladat ugyanazt a Hive-lekérdezést a MapReduce-motor feldolgozásához használhatók. Minden egyes MapReduce-feladat kimenetét HDFS íródik a köztes adatokat. Tez minimálisra csökkenti a feladatok az egyes Hive-lekérdezések száma, mivel a elkerülhető a felesleges írást.
* **Minimalizálja a indítási késések**. Tez jobban is képes elindításához szükséges leképező számának csökkentése és optimalizálás során is javítása indítási késleltetés minimalizálása érdekében.
* **Tárolók újrahasználja**. Minden alkalommal, amikor lehetséges Tez is képes újra felhasználhatja győződjön meg arról, hogy csökkenti a késés miatt folyamatban van a tárolók, a tárolókban.
* **Folyamatos optimalizálási technikákat**. Hagyományosan optimalizálási végzett fordítási fázis során. Azonban további információt a bemeneti adatok érhető el, amelyek engedélyezik a hatékonyabb optimalizálás futásidőben. Tez folyamatos optimalizálási technikákat, amelyek lehetővé teszik, hogy a csomag további, a futásidejű fázis optimalizálása használ.

További információ ezekről a fogalmakról: [Apache TEZ](https://hortonworks.com/hadoop/tez/).

Választhat, hogy bármely engedélyezve van a lekérdezés a következő paranccsal set illesztésével Tez Hive-lekérdezést:

   ```hive
   set hive.execution.engine=tez;
   ```

## <a name="hive-partitioning"></a>Hive-particionálás

I/o-műveletekre a jelentős teljesítménybeli szűk keresztmetszetet a Hive-lekérdezések futtatásához. A teljesítmény javítható, ha csökkenteni lehet olvasható adatok mennyiségét. Alapértelmezés szerint a Hive-lekérdezések teljes Hive-táblák beolvasása. Azonban csak egy kis mennyiségű adatot (például a szűrés lekérdezések) vizsgálata igénylő lekérdezések esetén ezt a viselkedést hoz létre a felesleges többletterhelést okoz. Hive a particionálás révén a Hive-lekérdezések csak a szükséges mennyiségű Hive táblák adatainak eléréséhez.

Hive-particionálást átrendezi a nyers adatokat az új címtárakat van megvalósítva. Mindegyik partíció rendelkezik a saját fájl könyvtára. Felhasználó által definiált a particionálást. A következő ábra szemlélteti a Hive-tábla particionáló oszlop *év*. Minden évben egy új könyvtár jön létre.

![Hive-particionálás][image-hdi-optimize-hive-partitioning_1]

Particionálási szempontokat:

* **Nem a partíció szerint** -particionálás csak néhány értékekkel rendelkező oszlopokon néhány partíciók okozhat. Például a nemek particionálás csak hoz létre (Férfi és női) hozható létre, így csak a késés csökkentése fele legfeljebb két partíció.
* **Hajtsa végre a partíció nem keresztül** – vagyis a több partíciót hoz létre egy egyedi értéket (például a felhasználói azonosító) tartalmazó oszlopon okoz. Partíció keresztül hatására nagy terhelés a fürt namenode, mert a sok címtárat kezelni.
* **Kerülje az adatok torzulása** – a particionálókulcs megérik válassza az, hogy az összes partíció még akkor is, méretét. Például a particionálás *állapot* oszlop előfordulhat, hogy döntés az adatok eloszlása. Mivel érdekelnek a populáció elemeit szinte 30 x, hogy a Vermont, a partíció méretét a potenciálisan torzítja és a teljesítmény változhat visszaesés.

A partíciós tábla létrehozásához használja a *particionált által* záradékot:

   ```hive
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
   
A particionált tábla létrehozása után létrehozhat particionálás statikus vagy dinamikus particionálás.

* **Statikus particionálás** azt jelenti, hogy már horizontálisan skálázott adatok a megfelelő címtárakban. Statikus partícióval rendelkező adja hozzá manuálisan a könyvtár helye alapján Hive-partíciókat. Az alábbi kódrészletben látható egy példa.
  
   ```hive
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
   SELECT * FROM lineitem 
   WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
   
   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
   LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```
   
* **A dinamikus particionálás** azt jelenti, hogy szeretné-e az Ön számára automatikusan létrehozza a partíciók Hive. Mivel létrehozta a particionálási tábla az átmeneti tárolási tábla már, ehhez szüksége a particionált tábla adatok beszúrása:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE, 
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```
   
További információkért lásd: [particionált táblák](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Használja az ORCFile formátumot
Hive különböző fájlformátumokban támogatja. Példa:

* **Szöveg**: az alapértelmezett fájlformátum, és a legtöbb esetben működik.
* **Az Avro**: együttműködési forgatókönyvek esetében is működik.
* **ORC/Parquet**: a teljesítmény a legmegfelelőbb.

(Optimalizált sor Oszlopalapú) ORC formátum nagyon hatékony módja a Hive-adatok tárolására. ORC rendelkezik más formátumú képest, a következő előnyökkel jár:

* komplexní typy DateTime és összetett és részben strukturált típusok támogatása.
* akár 70 %-os tömörítést.
* indexek minden 10 000 sort, amelyek lehetővé teszik a rendszer kihagyja a sorokat.
* futásidejű végrehajtási jelentős csökkenését.

ORC formátum engedélyezéséhez először hozzon létre egy táblát a záradékkal *ORC tárolva*:

   ```hive
   CREATE TABLE lineitem_orc_part
       (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
        L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
        L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
        L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
        L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
   PARTITIONED BY(L_SHIPDATE STRING)
   STORED AS ORC;
   ```
   
Ezután, adatok beszúrása az ORC-táblára, az előkészítési táblában. Példa:

   ```hive
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
   
Tudjon meg többet az ORC formátumnak a [Apache Hive-nyelv manuális](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vektorizációt

Vektorizációt lehetővé teszi, hogy a Hive 1024 sorok sorolhatók, így egyszerre egy sor feldolgozása kötegelt feldolgozásához. Ez azt jelenti, hogy egyszerű műveleteket végzett gyorsabb, mert kevesebb belső kód kell futtatni.

Engedélyezze a vektorizációt a Hive-lekérdezést az alábbi beállítás előtagja:

   ```hive
    set hive.vectorized.execution.enabled = true;
   ```

További információkért lásd: [lekérdezés-végrehajtás Vectorized](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Más módszereit
Nincsenek további optimalizálást módszereket is fontolóra veheti, például:

* **Hive-bucketing:** olyan módszer, amely lehetővé teszi, hogy a fürt vagy a szegmens nagy méretű adatkészletek lekérdezési teljesítmény optimalizálása érdekében.
* **Csatlakozzon az optimalizálás:** optimalizálása illesztések a hatékonyság növelése és csökkentése érdekében a felhasználó mutatók tervezése a Hive-lekérdezés végrehajtása. További információkért lásd: [optimalizálási csatlakozzon](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Növelje csökkentő**.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedett a számos gyakori Hive lekérdezés módszereit. További tudnivalókért tekintse meg a következő cikkeket:

* [Az Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Repülőjáratok késési adatainak elemzése a HDInsight az Apache Hive használatával](hdinsight-analyze-flight-delay-data-linux.md)
* [Az Apache Hive a HDInsight használatával Twitter-adatok elemzése](hdinsight-analyze-twitter-data-linux.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
