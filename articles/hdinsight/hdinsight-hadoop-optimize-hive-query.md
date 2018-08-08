---
title: Az Azure HDInsight Hive-lekérdezések optimalizálása
description: Ismerje meg, hogy a HDInsight hadoop Hive-lekérdezések optimalizálása.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: 5142f2d2c3828a5311a67ac4a7e5abd3cc434801
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594675"
---
# <a name="optimize-hive-queries-in-azure-hdinsight"></a>Az Azure HDInsight Hive-lekérdezések optimalizálása

Hadoop-fürtök alapértelmezés szerint nincs optimalizálva a teljesítmény. Ez a cikk ismerteti az egyes leggyakoribb struktúra módszereit, hogy a lekérdezések alkalmazhat.

## <a name="scale-out-worker-nodes"></a>Munkavégző csomópontok horizontális felskálázása

A fürtben található munkavégző csomópontok számának növelése használhatnak több leképező és csökkentő futhasson párhuzamosan. Horizontális felskálázás a HDInsight növeléséhez két módja van:

* A kiépítés időpontban a munkavégző csomópontok az Azure portal, az Azure PowerShell-lel vagy a többplatformos parancssori felület használatával is megadhat.  További információ: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md). Az alábbi képernyőfelvételen a munkavégző csomópont-konfiguráció az Azure Portalon:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
* A futási időben is horizontálisan a fürt egyik időformátum:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

A HDInsight által támogatott különböző virtuális gépek kapcsolatos további információkért lásd: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="enable-tez"></a>Engedélyezze a Tez

[Az Apache Tez](http://hortonworks.com/hadoop/tez/) van egy alternatív végrehajtó motorja a MapReduce-motor:

![tez_1][image-hdi-optimize-hive-tez_1]

Tez gyorsabban azért, mert:

* **A MapReduce-motor önállóan irányított aciklikus Graph (DAG) végrehajtása**. A DAG igényel minden egyes leképező csökkentő egy készletét kell követnie. Ez azt eredményezi, hogy több MapReduce-feladatok minden egyes Hive-lekérdezés percenként. Tez nem tartalmaz ilyen korlátozást, és összetett DAG tud feldolgozni egy feladatként, így minimalizálja a feladat indítási terhelést.
* **Ezzel elkerülheti a felesleges írások**. Több feladat esetében ugyanazt a Hive-lekérdezést a MapReduce-motor szűnnek, mert minden egyes feladat kimenetének íródik HDFS a köztes adatokat. Mivel a Tez minimálisra csökkenti az egyes Hive-lekérdezés képes kerülje a szükségtelen írási feladatok számát.
* **Minimalizálja a indítási késések**. Tez jobban is képes elindításához szükséges leképező számának csökkentése és optimalizálás során is javítása indítási késleltetés minimalizálása érdekében.
* **Tárolók újrahasználja**. Minden alkalommal, amikor lehetséges Tez is képes újra felhasználhatja győződjön meg arról, hogy csökkenti a késés miatt folyamatban van a tárolók, a tárolókban.
* **Folyamatos optimalizálási technikákat**. Hagyományosan optimalizálási végzett fordítási fázis során. Azonban további információt a bemeneti adatok érhető el, amelyek engedélyezik a hatékonyabb optimalizálás futásidőben. Tez folyamatos optimalizálási technikákat, amelyek lehetővé teszik, hogy a csomag további, a futásidejű fázis optimalizálása használ.

További információ ezekről a fogalmakról: [Apache TEZ](http://hortonworks.com/hadoop/tez/).

Választhat, hogy bármely engedélyezve van a lekérdezés az alábbi beállítást illesztésével Tez Hive-lekérdezést:

    set hive.execution.engine=tez;

Linux-alapú HDInsight-fürtök Tez alapértelmezés szerint engedélyezve van.


## <a name="hive-partitioning"></a>Hive-particionálás

I/o-művelet a jelentős teljesítménybeli szűk keresztmetszetet a Hive-lekérdezések futtatása. A teljesítmény javítható, ha csökkenteni lehet olvasható adatok mennyiségét. Alapértelmezés szerint a Hive-lekérdezések teljes Hive-táblák beolvasása. Ez a nagyszerű, mint szekvenciális rekordolvasással lekérdezések. Azonban csak egy kis mennyiségű adatot (például a szűrés lekérdezések) vizsgálata igénylő lekérdezések esetén ezt a viselkedést hoz létre a felesleges többletterhelést okoz. Hive a particionálás révén a Hive-lekérdezések csak a szükséges mennyiségű Hive táblák adatainak eléréséhez.

Hive-particionálást átrendezi a nyers adatokat az új címtárakat az egyes partíciók kellene a saját címtár -, felhasználó által definiált-e fel a partíció van megvalósítva. A következő ábra szemlélteti a Hive-tábla particionáló oszlop *év*. Minden évben egy új könyvtár jön létre.

![A particionálás][image-hdi-optimize-hive-partitioning_1]

Particionálási szempontokat:

* **Nem a partíció szerint** -particionálás csak néhány értékekkel rendelkező oszlopokon néhány partíciók okozhat. Például a nemek particionálás csak hoz létre (Férfi és női) hozható létre, így csak a késés csökkentése fele legfeljebb két partíció.
* **Hajtsa végre a partíció nem keresztül** – vagyis a több partíciót hoz létre egy egyedi értéket (például a felhasználói azonosító) tartalmazó oszlopon okoz. Partíció keresztül hatására nagy terhelés a fürt namenode, mert a sok címtárat kezelni.
* **Kerülje az adatok torzulása** – a particionálókulcs megérik válassza az, hogy az összes partíció még akkor is, méretét. Egy példa a particionáló *állapot* okozhat a kaliforniai közel 30 rekordok száma miatt a sokaságban szereplő Vermont az x.

A partíciós tábla létrehozásához használja a *particionált által* záradékot:

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE            STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

A particionált tábla létrehozása után létrehozhat particionálás statikus vagy dinamikus particionálás.

* **Statikus particionálás** azt jelenti, hogy már horizontálisan skálázott adatok a megfelelő könyvtárban van, és megkérheti a Hive-partíciók manuálisan a könyvtár helye alapján. Az alábbi kódrészletben látható egy példa.
  
        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
  
        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
* **A dinamikus particionálás** azt jelenti, hogy szeretné-e az Ön számára automatikusan létrehozza a partíciók Hive. Az átmeneti tárolási tábla azt már létrehozta a particionálási táblában, mert kell tennünk, hogy csak a particionált tábla adatok beszúrása:
  
        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
              L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as           L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as           L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as      L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

További információkért lásd: [particionált táblák](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Használja az ORCFile formátumot
Hive különböző fájlformátumokban támogatja. Példa:

* **Szöveg**: Ez az alapértelmezett fájlformátum és a legtöbb esetben működik
* **Az Avro**: jól használható az együttműködési forgatókönyvek
* **ORC/Parquet**: teljesítmény számára a leginkább megfelelő

(Optimalizált sor Oszlopalapú) ORC formátum nagyon hatékony módja a Hive-adatok tárolására. ORC rendelkezik más formátumú képest, a következő előnyökkel jár:

* komplexní typy DateTime és összetett és részben strukturált típusok támogatása
* akár 70 %-os tömörítés
* minden 10 000 sort, amelyek lehetővé teszik a rendszer kihagyja a sorokat indexel
* jelentősen csökkentek a futásidejű végrehajtási

ORC formátum engedélyezéséhez először hozzon létre egy táblát a záradékkal *ORC tárolva*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Ezután, adatok beszúrása az ORC-táblára, az előkészítési táblában. Példa:

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

Tudjon meg többet az ORC-formátumú [Itt](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vektorizációt

Vektorizációt lehetővé teszi, hogy a Hive 1024 sorok sorolhatók, így egyszerre egy sor feldolgozása kötegelt feldolgozásához. Ez azt jelenti, hogy egyszerű műveleteket végzett gyorsabb, mert kevesebb belső kód kell futtatni.

Engedélyezze a vektorizációt a Hive-lekérdezést az alábbi beállítás előtagja:

    set hive.vectorized.execution.enabled = true;

További információkért lásd: [lekérdezés-végrehajtás Vectorized](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Más módszereit
Nincsenek további optimalizálást módszereket is fontolóra veheti, például:

* **Hive-bucketing:** olyan módszer, amely lehetővé teszi, hogy a fürt vagy a szegmens nagy méretű adatkészletek lekérdezési teljesítmény optimalizálása érdekében.
* **Csatlakozzon az optimalizálás:** optimalizálása illesztések a hatékonyság növelése és csökkentése érdekében a felhasználó mutatók tervezése a Hive-lekérdezés végrehajtása. További információkért lásd: [optimalizálási csatlakozzon](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Növelje csökkentő**.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedett a számos gyakori Hive lekérdezés módszereit. További tudnivalókért tekintse meg a következő cikkeket:

* [Az Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Repülőjáratok késési adatainak elemzése a Hive a HDInsight használatával](hdinsight-analyze-flight-delay-data.md)
* [Hive HDInsight használatával egy Twitter-adatok elemzése](hdinsight-analyze-twitter-data.md)
* [Érzékelőadatok elemzése a Hive-lekérdezés konzol segítségével a HDInsight a Hadoop](hadoop/apache-hive-analyze-sensor-data.md)
* [A Hive használata a HDInsight, a webhelyek naplóinak elemzése](hadoop/apache-hive-analyze-website-log.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
