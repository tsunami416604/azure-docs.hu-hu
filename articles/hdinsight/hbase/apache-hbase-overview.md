---
title: Mi az Apache HBase az Azure HDInsightban?
description: Az Apache HBase HDInsightban való használatának bevezetése, amely egy, a Hadoop programra épített NoSQL-adatbázis. Megismerheti a használati eseteket, és összehasonlíthatja a HBase programot más Hadoop-fürtökkel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/03/2020
ms.openlocfilehash: 97814f4d22629fd74f395887a7361a3aabe55012
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78271834"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Mi az Apache HBase az Azure HDInsightban?

[Apache HBase](https://hbase.apache.org/) egy nyílt forráskódú, NoSQL adatbázis épül [Apache Hadoop](https://hadoop.apache.org/) és modellezve a [Google BigTable](https://cloud.google.com/bigtable/). A HBase véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok számára egy séma nélküli adatbázisban oszlopcsaládok szerint rendezve.

Felhasználói szempontból a HBase hasonló az adatbázishoz. Az adatok egy tábla soraiban és oszlopaiban tárolódnak, a sorokon belüli adatok pedig oszlopcsalád szerint vannak csoportosítva. A HBase egy séma nélküli adatbázis abban az értelemben, hogy az oszlopokat és a bennük tárolt adattípusokat sem kell meghatározni a használatuk előtt. A nyílt forráskód lineáris módon méreteződik át a több ezer csomópontnyi adat petabájtjainak kezelése érdekében. Az adatredundanciára, a kötegelt feldolgozásra és más olyan szolgáltatásokra támaszkodhat, amelyeket elosztott alkalmazások nyújtanak a Hadoop rendszerben.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Hogyan valósítja meg az Apache HBase implementált szolgáltatását az Azure HDInsightban?

A HDInsight HBase az Azure környezetbe integrált felügyelt fürtként érhető el. A fürtök úgy vannak konfigurálva, hogy az adatokközvetlenül [az Azure Storage-ban](./../hdinsight-hadoop-use-blob-storage.md)tárolják, ami alacsony késést és nagyobb rugalmasságot biztosít a teljesítmény és a költségválaszték ban. Ez lehetővé teszi, hogy az ügyfelek olyan interaktív webhelyeket építsenek, amelyek nagy adatkészletekkel működnek, hogy a több millió végpontból származó érzékelői és telemetriai adatokat tároló szolgáltatásokat építhessenek, és hogy ezeket az adatokat Hadoop-feladatokkal elemezzék. A HBase és a Hadoop jó kezdőpont a big data-projektekhez az Azure rendszerben; különösképpen azt teszik lehetővé, hogy a valós idejű alkalmazások nagy adatkészletekkel dolgozhassanak.

A HDInsight-implementáció kihasználja a HBase méretezhető architektúráját, hogy a táblák automatikus árnyalását, az írások és olvasások erős következetességét és automatikus feladatátvételt nyújtson. A teljesítményt a memóriába való gyorsítótárazás növeli az olvasáshoz, és a nagy streaming-kapacitás az írásokhoz. A HBase-fürt a virtuális hálózaton belül hozható létre. További információ: [HDInsight-fürtök létrehozása az Azure Virtual Networkön](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hogyan történik az adatok kezelése a HDInsight HBase-ben?

Az adatok a HBase-ben a(z) `create`, `get`, `put` és `scan` parancsokkal kezelhetők a HBase rendszerhéjból. Az adatok a(z) `put` paranccsal írhatók, és a(z) `get` paranccsal olvashatók az adatbázisban. A(z) `scan` paranccsal szerezhetők be adatok a táblák több sorából. Az adatok a HBase C# API-val is kezelhetők, amely egy ügyfélkönyvtárat biztosít a HBase REST API-n felül. A HBase-adatbázisok apache [hive](https://hive.apache.org/)használatával is lekérdezhetők. Ezeknek a programozási modelleknek a bemutatása Az [Apache HBase használatának első lépései az Apache Hadoop segítségével a HDInsight ban című témakörben található.](./apache-hbase-tutorial-get-started-linux.md) Társfeldolgozók is rendelkezésre állnak, amelyek lehetővé teszik az adatfeldolgozást az adatbázist üzemeltető csomópontokban.

> [!NOTE]  
> A HBase nem támogatja a Thriftet a HDInsightban.

## <a name="use-cases-for-apache-hbase"></a>Használati esetek az Apache HBase-hez

A gyűjtőhasználati eset, amelyre a BigTable (és tágabb értelemben a HBase) a webes keresésből készült. A keresőmotorok indexeket építenek, amelyek kifejezéseket képeznek le az azokat tartalmazó weblapokra. A HBase azonban sok más használati esethez megfelelő – amelyek közül több ebben a szakaszban van felsorolva.

|Forgatókönyv |Leírás |
|---|---|
|Kulcs-érték tároló|A HBase kulcsérték-tárolóként használható, és alkalmas az üzenetrendszerek kezelésére. A Facebook a HBase-t használja az üzenetküldő rendszeréhez, és ideális az internetes kommunikáció tárolására és kezelésére. A WebTable a HBase eszközt használja a weblapokról kinyert táblázatok keresésére és kezelésére.|
|Érzékelői adatok|A HBase hasznos a növekményesen, különböző forrásokból gyűjtött adatok rögzítéséhez. Ez magában foglalja a közösségi elemzést, az idősorozatokat, az interaktív irányítópultok naprakészen tartását a trendekkel és számlálókkal, valamint a naplórendszerek kezelését. Példák lehetnek erre a Bloomberg Trader terminál és az Open Time Series Database (OpenTSDB), amelyek a kiszolgálórendszerek állapotával kapcsolatos mérőszámokat tárolnak és tesznek elérhetővé.|
|Valós idejű lekérdezés|[Az Apache Phoenix](https://phoenix.apache.org/) az Apache HBase SQL-lekérdezési motorja. JDBC-illesztőprogramként érhető el, és lehetővé teszi a HBase-táblák lekérdezését és kezelését az SQL használatával.|
|A HBase platformként|Az alkalmazások a HBase felett futhatnak adattárolóként. Ilyen például a Phoenix, [az OpenTSDB](http://opentsdb.net/), a Kiji és a Titan. Az alkalmazások integrálhatók is a HBase eszközzel. Ilyen például [az Apache Hive,](https://hive.apache.org/) [az Apache Pig, a](https://pig.apache.org/) [Solr,](https://lucene.apache.org/solr/)az [Apache Storm,](https://storm.apache.org/) [az Apache Flume, az](https://flume.apache.org/)Apache [Impala,](https://impala.apache.org/)az [Apache Spark,](https://spark.apache.org/) [a Ganglionok](http://ganglia.info/)és az [Apache Drill.](https://drill.apache.org/)|

## <a name="next-steps"></a>További lépések

* [Az Apache HBase és az Apache Hadoop használatának első lépései a HDInsightban](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight-fürtök létrehozása az Azure Virtual Networkön](./apache-hbase-provision-vnet.md)
* [Az Apache HBase replikáció konfigurálása a HDInsightban](apache-hbase-replication.md)
