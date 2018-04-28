---
title: Mi az a HBase az Azure HDInsightban? | Microsoft Docs
description: Az Apache HBase HDInsightban való használatának bevezetése, amely egy, a Hadoop programra épített NoSQL-adatbázis. Megismerheti a használati eseteket, és összehasonlíthatja a HBase programot más Hadoop-fürtökkel.
keywords: bigtable,nosql,mi az a hbase,apache hbase,hbase,habase áttekintés,
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: d2a76d53-133a-4849-a30c-88d9c794391c
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 36c6a16520d78d4fa2465a1fdea585a3b3c88cd7
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>Mi az a HBase a HDInsight eszközben: Egy NoSQL-adatbázis, amely BigTable-szerű képességeket nyújt a Hadoophoz
Az Apache HBase egy nyílt forráskódú NoSQL-adatbázis, amely a Hadoopra épült, és a Google BigTable után van modellezve. A HBase véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok számára egy séma nélküli adatbázisban oszlopcsaládok szerint rendezve.

Az adatok a táblasorokban vannak tárolva, és a sorokon belüli adatok oszlopcsalád szerint vannak csoportosítva. A HBase egy séma nélküli adatbázis abban az értelemben, hogy az oszlopokat és a bennük tárolt adattípusokat sem kell meghatározni a használatuk előtt. A nyílt forráskód lineáris módon méreteződik át a több ezer csomópontnyi adat petabájtjainak kezelése érdekében. Az adatredundanciára, a kötegelt feldolgozásra és más olyan szolgáltatásokra támaszkodhat, amelyeket elosztott alkalmazások nyújtanak a Hadoop rendszerben.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>Hogyan van megvalósítva a HBase az Azure HDInsight eszközben?
A HDInsight HBase az Azure környezetbe integrált felügyelt fürtként érhető el. A fürtök úgy vannak konfigurálva, hogy közvetlenül az [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md)-ban vagy az [Azure Data Lake Store](./../hdinsight-hadoop-use-data-lake-store.md)-ban tárolják az adatokat, ami kis késést és nagyobb rugalmasságot biztosít a teljesítménnyel és költségekkel kapcsolatos lehetőségek terén. Ez lehetővé teszi, hogy az ügyfelek olyan interaktív webhelyeket építsenek, amelyek nagy adatkészletekkel működnek, hogy a több millió végpontból származó érzékelői és telemetriai adatokat tároló szolgáltatásokat építhessenek, és hogy ezeket az adatokat Hadoop-feladatokkal elemezzék. A HBase és a Hadoop jó kezdőpont a big data-projektekhez az Azure rendszerben; különösképpen azt teszik lehetővé, hogy a valós idejű alkalmazások nagy adatkészletekkel dolgozhassanak.

A HDInsight-implementáció kihasználja a HBase méretezhető architektúráját, hogy a táblák automatikus árnyalását, az írások és olvasások erős következetességét és automatikus feladatátvételt nyújtson. A teljesítményt a memóriába való gyorsítótárazás növeli az olvasáshoz, és a nagy streaming-kapacitás az írásokhoz. A HBase-fürt a virtuális hálózaton belül hozható létre. További információ: [HDInsight-fürtök létrehozása az Azure Virtual Networkön](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hogyan történik az adatok kezelése a HDInsight HBase-ben?
Az adatok a HBase-ben a(z) `create`, `get`, `put` és `scan` parancsokkal kezelhetők a HBase rendszerhéjból. Az adatok a(z) `put` paranccsal írhatók, és a(z) `get` paranccsal olvashatók az adatbázisban. A(z) `scan` paranccsal szerezhetők be adatok a táblák több sorából. Az adatok a HBase C# API-val is kezelhetők, amely egy ügyfélkönyvtárat biztosít a HBase REST API-n felül. A HBase adatbázisok a Hive használatával is lekérdezhetők. Ezen programozási modellek bemutatását lásd: [A HBase és a Hadoop használatának első lépései a HDInsightban](./apache-hbase-tutorial-get-started-linux.md). Társprocesszorok is elérhetők, amelyek lehetővé teszik az adatfeldolgozást az adatbázist szolgáltató csomópontokban.

> [!NOTE]
> A HBase nem támogatja a Thriftet a HDInsightban.
>

## <a name="scenarios-use-cases-for-hbase"></a>Forgatókönyvek: A HBase használati esetei
A kanonikus használati eset, amelyhez a BigTable (és így a HBase) létrejött, a webes keresés. A keresőmotorok indexeket építenek, amelyek kifejezéseket képeznek le az azokat tartalmazó weblapokra. A HBase azonban sok más használati esethez megfelelő – amelyek közül több ebben a szakaszban van felsorolva.

* Kulcs-érték tároló
  
    A HBase használható kulcs-érték tárolóként, és megfelelő az üzenetkezelési rendszerek kezeléséhez. A Facebook a HBase eszközt használja az üzenetkezelési rendszeréhez, amely ideális az internetes kommunikációk tárolásához és kezeléséhez. A WebTable a HBase eszközt használja a weblapokról kinyert táblázatok keresésére és kezelésére.
* Érzékelői adatok
  
    A HBase hasznos a növekményesen, különböző forrásokból gyűjtött adatok rögzítéséhez. Ebbe tartozik a közösségi hálók adatainak elemzése, az idősorozat, az interaktív irányítópultok naprakészen tartása trendekkel és számlálókkal, valamint a naplórendszerek kezelése. Példák lehetnek erre a Bloomberg Trader terminál és az Open Time Series Database (OpenTSDB), amelyek a kiszolgálórendszerek állapotával kapcsolatos mérőszámokat tárolnak és tesznek elérhetővé.
* Valós idejű lekérdezés
  
    A [Phoenix](http://phoenix.apache.org/) az Apache HBase SQL lekérdezési motorja. JDBC-illesztőként érhető el, és lehetővé teszi a HBase táblák SQL eszközzel végzett lekérdezését és kezelését.
* A HBase platformként
  
    Az alkalmazások a HBase felett futhatnak adattárolóként. Erre példa a Phoenix, az OpenTSDB, a Kiji és a Titan. Az alkalmazások integrálhatók is a HBase eszközzel. Erre példa a Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia és Drill.

## <a name="next-steps"></a>Következő lépések
* [A HBase és a Hadoop használatának első lépései a HDInsightban](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight-fürtök létrehozása az Azure Virtual Networkön](./apache-hbase-provision-vnet.md)
* [HBase-replikálás konfigurálása a HDInsightban](apache-hbase-replication.md)
* [A Maven használata a HDInsight (Hadoop) eszközzel HBase-t használó Java-alkalmazások építéséhez](./apache-hbase-build-java-maven-linux.md)

## <a name="see-also"></a>Lásd még:
* [Apache HBase](https://hbase.apache.org/)
* [Bigtable: Elosztott tárolórendszer strukturált adatokhoz](http://research.google.com/archive/bigtable.html)




