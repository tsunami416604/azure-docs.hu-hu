---
title: "Az Azure HDInsight Hive-lekérdezések optimalizálása |} Microsoft Docs"
description: "Ismerje meg, hogyan optimalizálható a Hive-lekérdezéseket a hdinsight Hadoop."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d6174c08-06aa-42ac-8e9b-8b8718d9978e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/26/2016
ms.author: jgao
ms.openlocfilehash: edbf797e6277a65b5311e4939f5ab72776b11557
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-hive-queries-in-azure-hdinsight"></a>Az Azure HDInsight Hive-lekérdezések optimalizálása

Hadoop-fürtök alapértelmezés szerint nincs optimalizálva a teljesítmény. Ez a cikk ismertet néhány leggyakoribb Hive teljesítmény optimalizálása módszereket alkalmazhat a lekérdezéseket.

## <a name="scale-out-worker-nodes"></a>Horizontális felskálázás munkavégző csomópontokhoz

További mappers és párhuzamosan futtatható szűkítő kihasználhatják a adhatja meg a fürt feldolgozó csomópontjainak számát. Növelheti a Hdinsightban bővítő két módja van:

* A kiépítés során az Azure-portálon, az Azure PowerShell vagy a platformfüggetlen parancssori felület használatával feldolgozó csomópontok száma is megadhat.  További információ: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md). Az alábbi képernyőfelvételen a munkavégző csomópont-konfigurációját mutatja be az Azure-portálon:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
* A futási időben is méretezheti ki a fürt egyik létrehozása:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

A HDInsight által támogatott különböző virtuális gépek kapcsolatos további információkért lásd: [HDInsight árképzési](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="enable-tez"></a>Tez engedélyezése

[Apache Tez](http://hortonworks.com/hadoop/tez/) egy alternatív végrehajtó motorja a MapReduce motor van:

![tez_1][image-hdi-optimize-hive-tez_1]

Tez használata gyorsabb, mert:

* **A MapReduce-vezérlő önállóan irányított aciklikus diagramhoz (DAG) végrehajtása**. A DAG igényel minden egyes mappers szűkítő egy készletét kell követnie. Ez azt eredményezi, hogy több MapReduce-feladatok minden egyes Hive lekérdezés percenként. Tez nem tartalmaz ilyen korlátozást, és így minimalizálja a feladat indítási többletterhelés egy feladatként összetett DAG tud feldolgozni.
* **Ezzel elkerülheti a szükségtelen írások**. Több feladat éppen hoz azonos Hive lekérdezés esetén a MapReduce-vezérlő, mert minden egyes feladat eredményének beíródik HDFS köztes adatok. Mivel a Tez minimálisra csökkenti a tud-e a szükségtelen írási elkerülése érdekében minden egyes Hive-lekérdezések feladatok számát.
* **Minimalizálja a kezdeti késések**. Tez jobban az indítási késleltetés minimálisra elindításához szükséges mappers számának csökkentése és is az egész optimalizálási javítása.
* **A rendszer újból felhasználja tárolók**. Amikor lehetséges Tez tovább használhassa a tárolók győződjön meg arról, hogy lecsökkent van-e a késés miatt tárolók indítása.
* **Folyamatos optimalizálási technikákat**. Hagyományosan optimalizálási fordítási fázis során végezhető el. Azonban további információt a bemenetek érhető el, amelyek engedélyezik a hatékonyabb optimalizálás futásidőben. Tez használ, amely lehetővé teszi, hogy optimalizálja a terv további futásidejű fázisba történő folyamatos optimalizálási technikákat.

Ezekről a fogalmakról további részletekért lásd: [Apache TEZ](http://hortonworks.com/hadoop/tez/).

A Hive-lekérdezések engedélyezve van a lekérdezés az alábbi beállításokkal illesztésével Tez végezheti el:

    set hive.execution.engine=tez;

Linux-alapú HDInsight-fürtök Tez alapértelmezés szerint engedélyezve van.


## <a name="hive-partitioning"></a>Particionálás struktúra

I/o-művelet a jelentős teljesítménybeli szűk keresztmetszetek a Hive-lekérdezések futtatásához. A teljesítmény javítható, ha olvasható adatok mennyisége csökkenthető. Alapértelmezés szerint a Hive-lekérdezések teljes Hive táblák beolvasása. Ez a táblázatbeolvasás például lekérdezések nagy. Azonban csak egy kis mennyiségű adatokat (például a szűrési lekérdezések) szolgáltatást igénylő lekérdezések esetén ez a viselkedés hoz létre szükségtelen terhelés. Hive particionálás lehetővé teszi, hogy a Hive-lekérdezések csak a szükséges mennyiségű Hive táblák adatainak eléréséhez.

A nyers adatok átrendezése be új könyvtárak mindegyik partíció rendelkezik saját könyvtár - Ha a partíció a felhasználó által definiált-e a Hive particionálás megvalósítja. A következő ábra szemlélteti a Hive tábla particionáló oszlop *év*. Minden évben új könyvtár jön létre.

![Particionálás][image-hdi-optimize-hive-partitioning_1]

A particionáló szempontokat:

* **Nem a partíció szerint** -particionálási oszlop csak néhány értékekkel néhány partíciók okozhat. Például a nemét particionálás csak hoz létre (hímivarú és nőivarú) hozható létre, így csak a késés csökkentésére fele legfeljebb két partíció.
* **Nem keresztül partíció** – vagyis a több partíciót hoz létre egy egyedi értéket (például a felhasználói azonosítóját) tartalmazó oszlopon okoz. Partíció keresztül aktiválja az mekkora terhelés a a fürt namenode, mert a könyvtárak nagy számú kezelésére.
* **Adatok eltérésére elkerülése** -válassza ki a particionálási kulcs georeplikációt, hogy az összes partíció található, még akkor is, méret. Példa a particionáló *állapot* okozhat a kaliforniai szinte 30 kell rekordok száma miatt a feltöltési Vermont az x.

A partíciós tábla létrehozásához használja a *particionálva által* záradékban:

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE            STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

A particionált tábla létrehozása után létrehozhat particionálás statikus vagy dinamikus particionálást.

* **Statikus particionálás** azt jelenti, hogy már horizontálisan skálázott adatok a megfelelő könyvtárban van, és kérje meg kézzel a könyvtár helye alapján Hive partíciókat. A következő kódrészletet csak példaként szolgál.
  
        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
  
        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
* **Dinamikus particionálást** azt jelenti, hogy szeretné-e meg automatikusan partíciókat struktúra. Már az átmeneti tárolási tábla létrehoztunk a particionálási tábla, mert az csak azt kell tennie a particionált tábla adatok beszúrása:
  
        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
              L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as           L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as           L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as      L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

További részletekért lásd: [particionált táblák](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>A következő ORCFile formátumot használja
Hive különböző fájlformátumok támogatja. Példa:

* **Szöveg**: Ez az alapértelmezett fájlformátuma, és együttműködik a legtöbb esetben
* **Az Avro**: együttműködési forgatókönyvek esetén működik
* **ORC/Parquet**: teljesítmény a legalkalmasabb

ORC (optimalizált sor oszlopos) formátumban rendkívül hatékony módja a Hive-adatok tárolására. Összehasonlítás más formátumokkal, ORC rendelkezik a következő előnyökkel jár:

* összetett típusok, beleértve a DateTime és félig strukturált és a komplex típusok támogatása
* akár 70 % tömörítés
* minden 10 000 sorok, amelyek lehetővé teszik a rendszer kihagyja a sort indexeli
* futásidejű végrehajtási jelentős csökkenését

Ahhoz, hogy az ORC formátum, akkor először hozzon létre egy táblát a záradék *ORC tárolt*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

A következő szúr be adatokat az ORC táblázathoz az átmeneti tárolási tábla. Példa:

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

További ORC formátumnak [Itt](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vectorization

Vectorization lehetővé teszi, hogy a struktúra egy sorköteg 1024 együtt helyett egy sor feldolgozása egyszerre feldolgozandó. Azt jelenti, hogy egyszerű műveletek végzett gyorsabb, mert kevesebb belső kódot kell futtatni.

Ahhoz, hogy vectorization előtag a Hive-lekérdezést a következő beállításokkal:

    set hive.vectorized.execution.enabled = true;

További információkért lásd: [lekérdezés-végrehajtás Vectorized](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Más optimalizálási módszerek
Nincsenek további optimalizálás módszereket is fontolóra veheti, például:

* **Hive bucketing:** olyan módszer, amely lehetővé teszi, hogy a fürt vagy szegmentálni nagy adatkészletek lekérdezési teljesítmény optimalizálása érdekében.
* **Csatlakozás optimalizálási:** struktúrájának lekérdezés-végrehajtás javítsa a táblákra és felhasználói mutatók szükségességének tervezése az optimális. További információkért lásd: [optimalizálási csatlakozás](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Növelje a szűkítő**.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta rendelkezik Hive lekérdezés több közös optimalizálási módszert. További tudnivalókért tekintse meg a következő cikkeket:

* [Apache Hive hdinsight használata](hdinsight-use-hive.md)
* [Repülési késleltetés adatok elemzése a Hive HDInsight használatával](hdinsight-analyze-flight-delay-data.md)
* [Hdinsight Hive eszközzel Twitter-adatok elemzése](hdinsight-analyze-twitter-data.md)
* [HDInsight hadoop Hive lekérdezés konzol használata érzékelőadatok elemzése](hdinsight-hive-analyze-sensor-data.md)
* [A Hive használata a hdinsight eszközzel webhelyek naplóinak elemzése](hdinsight-hive-analyze-website-log.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
