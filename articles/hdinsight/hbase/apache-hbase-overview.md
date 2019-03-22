---
title: Mi az a HBase az Azure HDInsightban?
description: Az Apache HBase HDInsightban való használatának bevezetése, amely egy, a Hadoop programra épített NoSQL-adatbázis. Megismerheti a használati eseteket, és összehasonlíthatja a HBase programot más Hadoop-fürtökkel.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: d109a1a233aaaf1f3922561106caa15781e31e01
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337346"
---
# <a name="what-is-apache-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-apache-hadoop"></a>Mi az Apache HBase a HDInsight: NoSQL-adatbázis, amely BigTable-szerű képességeket biztosít az Apache Hadoop
[Az Apache HBase](https://hbase.apache.org/) egy nyílt forráskódú nosql-alapú adatbázis-alapú [Apache Hadoop](https://hadoop.apache.org/) és modellezett után [Google BigTable](https://cloud.google.com/bigtable/). A HBase véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok számára egy séma nélküli adatbázisban oszlopcsaládok szerint rendezve.

Felhasználói szempontból a HBase hasonlít egy adatbázishoz. A sorok és a egy tábla oszlopait tárolt adatokat, és a sorokon belüli adatok oszlopcsalád szerint vannak csoportosítva. A HBase egy séma nélküli adatbázis abban az értelemben, hogy az oszlopokat és a bennük tárolt adattípusokat sem kell meghatározni a használatuk előtt. A nyílt forráskód lineáris módon méreteződik át a több ezer csomópontnyi adat petabájtjainak kezelése érdekében. Az adatredundanciára, a kötegelt feldolgozásra és más olyan szolgáltatásokra támaszkodhat, amelyeket elosztott alkalmazások nyújtanak a Hadoop rendszerben.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Hogyan megvalósítása az Azure HDInsight az Apache HBase?

A HDInsight HBase az Azure környezetbe integrált felügyelt fürtként érhető el. A fürtök úgy vannak konfigurálva, közvetlenül a adatok tárolásához [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md) biztosító alacsony késést és nagyobb rugalmasságot, a teljesítménnyel és költségekkel kapcsolatos választások során. Ez lehetővé teszi, hogy az ügyfelek olyan interaktív webhelyeket építsenek, amelyek nagy adatkészletekkel működnek, hogy a több millió végpontból származó érzékelői és telemetriai adatokat tároló szolgáltatásokat építhessenek, és hogy ezeket az adatokat Hadoop-feladatokkal elemezzék. A HBase és a Hadoop jó kezdőpont a big data-projektekhez az Azure rendszerben; különösképpen azt teszik lehetővé, hogy a valós idejű alkalmazások nagy adatkészletekkel dolgozhassanak.

A HDInsight-implementáció kihasználja a HBase méretezhető architektúráját, hogy a táblák automatikus árnyalását, az írások és olvasások erős következetességét és automatikus feladatátvételt nyújtson. A teljesítményt a memóriába való gyorsítótárazás növeli az olvasáshoz, és a nagy streaming-kapacitás az írásokhoz. A HBase-fürt a virtuális hálózaton belül hozható létre. További információ: [HDInsight-fürtök létrehozása az Azure Virtual Networkön](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hogyan történik az adatok kezelése a HDInsight HBase-ben?
Az adatok a HBase-ben a(z) `create`, `get`, `put` és `scan` parancsokkal kezelhetők a HBase rendszerhéjból. Az adatok a(z) `put` paranccsal írhatók, és a(z) `get` paranccsal olvashatók az adatbázisban. A(z) `scan` paranccsal szerezhetők be adatok a táblák több sorából. Az adatok a HBase C# API-val is kezelhetők, amely egy ügyfélkönyvtárat biztosít a HBase REST API-n felül. A HBase adatbázisok használatával is lekérdezhetők [Apache Hive](https://hive.apache.org/). Ezen programozási modellek bemutatását lásd: [Apache hadooppal a HDInsight Apache HBase használatának első lépései](./apache-hbase-tutorial-get-started-linux.md). Társprocesszorok is elérhetők, amelyek lehetővé teszik az adatfeldolgozást az adatbázist szolgáltató csomópontokban.

> [!NOTE]  
> A HBase nem támogatja a Thriftet a HDInsightban.

## <a name="scenarios-use-cases-for-apache-hbase"></a>Alkalmazási helyzetek: Az Apache HBase használati esetei
A kanonikus használati eset, amely BigTable (és továbbgyűrűzéseként HBase) létrejött, a webes keresés. A keresőmotorok indexeket építenek, amelyek kifejezéseket képeznek le az azokat tartalmazó weblapokra. A HBase azonban sok más használati esethez megfelelő – amelyek közül több ebben a szakaszban van felsorolva.

* Kulcs-érték tároló
  
    A HBase használható kulcs-érték tárolóként, és megfelelő az üzenetkezelési rendszerek kezeléséhez. A Facebook a HBase eszközt használja az üzenetkezelési rendszeréhez, amely ideális az internetes kommunikációk tárolásához és kezeléséhez. A WebTable a HBase eszközt használja a weblapokról kinyert táblázatok keresésére és kezelésére.
* Érzékelői adatok
  
    A HBase hasznos a növekményesen, különböző forrásokból gyűjtött adatok rögzítéséhez. Ebbe tartozik a közösségi hálók adatainak elemzése, az idősorozat, az interaktív irányítópultok naprakészen tartása trendekkel és számlálókkal, valamint a naplórendszerek kezelése. Példák lehetnek erre a Bloomberg Trader terminál és az Open Time Series Database (OpenTSDB), amelyek a kiszolgálórendszerek állapotával kapcsolatos mérőszámokat tárolnak és tesznek elérhetővé.
* Valós idejű lekérdezés
  
    [Az Apache Phoenix](https://phoenix.apache.org/) Apache hbase SQL lekérdezési motorja van. JDBC-illesztőként érhető el, és lehetővé teszi a HBase táblák SQL eszközzel végzett lekérdezését és kezelését.
* A HBase platformként
  
    Az alkalmazások a HBase felett futhatnak adattárolóként. Ilyenek például a Phoenix, [OpenTSDB](http://opentsdb.net/), Kiji és a Titan. Az alkalmazások integrálhatók is a HBase eszközzel. Ilyenek például [Apache Hive](https://hive.apache.org/), [Apache Pig](https://pig.apache.org/), [Solr](https://lucene.apache.org/solr/), [Apache Storm](https://storm.apache.org/), [Apache Flume](https://flume.apache.org/), [ Apache Impala](https://impala.apache.org/), [Apache Spark](https://spark.apache.org/) , [Ganglia](http://ganglia.info/), és [Apache Drill](https://drill.apache.org/).

## <a name="next-steps"></a>Következő lépések
* [Az Apache hadooppal a HDInsight Apache HBase használatának első lépései](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight-fürtök létrehozása az Azure Virtual Networkön](./apache-hbase-provision-vnet.md)
* [A HDInsight Apache HBase-replikálás konfigurálása](apache-hbase-replication.md)
* [Az Apache Maven használata Java-alkalmazások, amelyek használják az Apache HBase a HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md)

## <a name="see-also"></a>Lásd még:
* [Apache HBase](https://hbase.apache.org/)
* [Az Apache HBase referencia-útmutató](https://hbase.apache.org/book.html)
* [Bigtable: Elosztott tárolórendszer strukturált adatokhoz](https://research.google.com/archive/bigtable.html)
