---
title: Mi az Apache HBase az Azure HDInsight?
description: Az Apache HBase HDInsightban való használatának bevezetése, amely egy, a Hadoop programra épített NoSQL-adatbázis. Megismerheti a használati eseteket, és összehasonlíthatja a HBase programot más Hadoop-fürtökkel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/03/2020
ms.openlocfilehash: 97814f4d22629fd74f395887a7361a3aabe55012
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271834"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Mi az Apache HBase az Azure HDInsight

Az [Apache HBase](https://hbase.apache.org/) egy nyílt forráskódú, NoSQL-adatbázis, amely a [Google BigTable](https://cloud.google.com/bigtable/)szolgáltatáson alapuló, [Apache Hadoopra](https://hadoop.apache.org/) épül. A HBase véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok számára egy séma nélküli adatbázisban oszlopcsaládok szerint rendezve.

A felhasználói szemszögből a HBase hasonló egy adatbázishoz. Az adattárolás egy tábla soraiban és oszlopaiban történik, és egy sorban lévő, oszlopos család szerint csoportosított adatmennyiség. A HBase egy séma nélküli adatbázis abban az értelemben, hogy az oszlopokat és a bennük tárolt adattípusokat sem kell meghatározni a használatuk előtt. A nyílt forráskód lineáris módon méreteződik át a több ezer csomópontnyi adat petabájtjainak kezelése érdekében. Az adatredundanciára, a kötegelt feldolgozásra és más olyan szolgáltatásokra támaszkodhat, amelyeket elosztott alkalmazások nyújtanak a Hadoop rendszerben.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Hogyan valósítható meg az Apache HBase az Azure HDInsight?

A HDInsight HBase az Azure környezetbe integrált felügyelt fürtként érhető el. A fürtök úgy vannak konfigurálva, hogy közvetlenül az [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md)-ban tárolják az adataikat, ami alacsony késést és nagyobb rugalmasságot biztosít a teljesítmény és a költséghatékonyság terén. Ez lehetővé teszi, hogy az ügyfelek olyan interaktív webhelyeket építsenek, amelyek nagy adatkészletekkel működnek, hogy a több millió végpontból származó érzékelői és telemetriai adatokat tároló szolgáltatásokat építhessenek, és hogy ezeket az adatokat Hadoop-feladatokkal elemezzék. A HBase és a Hadoop jó kezdőpont a big data-projektekhez az Azure rendszerben; különösképpen azt teszik lehetővé, hogy a valós idejű alkalmazások nagy adatkészletekkel dolgozhassanak.

A HDInsight-implementáció kihasználja a HBase méretezhető architektúráját, hogy a táblák automatikus árnyalását, az írások és olvasások erős következetességét és automatikus feladatátvételt nyújtson. A teljesítményt a memóriába való gyorsítótárazás növeli az olvasáshoz, és a nagy streaming-kapacitás az írásokhoz. A HBase-fürt a virtuális hálózaton belül hozható létre. További információ: [HDInsight-fürtök létrehozása az Azure Virtual Networkön](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hogyan történik az adatok kezelése a HDInsight HBase-ben?

Az adatok a HBase-ben a(z) `create`, `get`, `put` és `scan` parancsokkal kezelhetők a HBase rendszerhéjból. Az adatok a(z) `put` paranccsal írhatók, és a(z) `get` paranccsal olvashatók az adatbázisban. A(z) `scan` paranccsal szerezhetők be adatok a táblák több sorából. Az adatok a HBase C# API-val is kezelhetők, amely egy ügyfélkönyvtárat biztosít a HBase REST API-n felül. A HBase-adatbázis a [Apache Hive](https://hive.apache.org/)használatával is lekérdezhető. A programozási modellek bevezetését itt tekintheti meg: az [Apache HBase és Apache Hadoop a HDInsight használatának első lépései](./apache-hbase-tutorial-get-started-linux.md). A rendszer szintén elérhetővé teszi a munkafolyamatokat, amelyek lehetővé teszik az adatfeldolgozást az adatbázist futtató csomópontokon.

> [!NOTE]  
> A HBase nem támogatja a Thriftet a HDInsightban.

## <a name="use-cases-for-apache-hbase"></a>Az Apache HBase használati esetei

A kanonikus használati eset, amelyre a BigTable (és az HBase-t) a webes keresésből hozták létre. A keresőmotorok indexeket építenek, amelyek kifejezéseket képeznek le az azokat tartalmazó weblapokra. A HBase azonban sok más használati esethez megfelelő – amelyek közül több ebben a szakaszban van felsorolva.

|Forgatókönyv |Leírás |
|---|---|
|Kulcs-érték tároló|A HBase használható kulcs-érték tárolóként, és alkalmas az üzenetek rendszerének kezelésére. A Facebook HBase használ az üzenetkezelési rendszerhez, és ideális megoldás az internetes kommunikáció tárolására és kezelésére. A WebTable a HBase eszközt használja a weblapokról kinyert táblázatok keresésére és kezelésére.|
|Érzékelői adatok|A HBase hasznos a növekményesen, különböző forrásokból gyűjtött adatok rögzítéséhez. Ide tartozik a közösségi elemzés, az idősorozat, az interaktív irányítópultok naprakészen tartása trendekkel és számlálókkal, valamint a naplózási rendszerek kezelése. Példák lehetnek erre a Bloomberg Trader terminál és az Open Time Series Database (OpenTSDB), amelyek a kiszolgálórendszerek állapotával kapcsolatos mérőszámokat tárolnak és tesznek elérhetővé.|
|Valós idejű lekérdezés|A [Apache Phoenix](https://phoenix.apache.org/) az Apache HBase SQL-lekérdezési motorja. A szolgáltatás JDBC-illesztőprogramként érhető el, és lehetővé teszi a HBase-táblák lekérdezését és kezelését az SQL használatával.|
|A HBase platformként|Az alkalmazások a HBase felett futhatnak adattárolóként. Ilyenek például a Phoenix, a [OpenTSDB](http://opentsdb.net/), a Kiji és a Titan. Az alkalmazások integrálhatók is a HBase eszközzel. Ilyenek például a következők: [Apache Hive](https://hive.apache.org/), [Apache Pig](https://pig.apache.org/), [Solr](https://lucene.apache.org/solr/), [Apache Storm](https://storm.apache.org/), [Apache Flume](https://flume.apache.org/), [Apache Impala](https://impala.apache.org/), [Apache Spark](https://spark.apache.org/) , [ganglionok](http://ganglia.info/)és [Apache fúrógép](https://drill.apache.org/).|

## <a name="next-steps"></a>Következő lépések

* [Az Apache HBase használatának első lépései a HDInsight Apache Hadoop](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight-fürtök létrehozása az Azure Virtual Networkön](./apache-hbase-provision-vnet.md)
* [Apache HBase-replikálás konfigurálása a HDInsight-ben](apache-hbase-replication.md)
