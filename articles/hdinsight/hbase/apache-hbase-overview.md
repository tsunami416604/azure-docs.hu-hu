---
title: Mi az Apache HBase az Azure HDInsightban?
description: Az Apache HBase HDInsightban való használatának bevezetése, amely egy, a Hadoop programra épített NoSQL-adatbázis. Megismerheti a használati eseteket, és összehasonlíthatja a HBase programot más Hadoop-fürtökkel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/20/2020
ms.openlocfilehash: e977d4d68a330f57c4b53da4270e4515d4e69ee0
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729810"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Mi az Apache HBase az Azure HDInsightban?

[Apache HBase](https://hbase.apache.org/) egy nyílt forráskódú, NoSQL adatbázis épül Apache Hadoop és modellezve a [Google BigTable](https://cloud.google.com/bigtable/). A HBase véletlenszerű hozzáférést és erős konzisztenciát biztosít a séma nélküli adatbázis nagy mennyiségű adatához. Az adatbázis oszlopcsaládok szerint van rendezve.

Felhasználói szempontból a HBase hasonló az adatbázishoz. Az adatok egy tábla soraiban és oszlopaiban tárolódnak, a sorokon belüli adatok pedig oszlopcsalád szerint vannak csoportosítva. A HBase egy séma nélküli adatbázis. Az oszlopok és adattípusok használat előtt meghatározatlanok lehetnek. A nyílt forráskód lineáris módon méreteződik át a több ezer csomópontnyi adat petabájtjainak kezelése érdekében. Támaszkodhat az adatredundancia, a kötegelt feldolgozás és egyéb funkciók, amelyek által nyújtott elosztott alkalmazások a Hadoop környezetben.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Hogyan valósítja meg az Apache HBase implementált szolgáltatását az Azure HDInsightban?

A HDInsight HBase az Azure környezetbe integrált felügyelt fürtként érhető el. A fürtök úgy vannak konfigurálva, hogy az adatokközvetlenül [az Azure Storage-ban](./../hdinsight-hadoop-use-blob-storage.md)tárolják, ami alacsony késést és nagyobb rugalmasságot biztosít a teljesítmény és a költségválaszték ban. Ez a tulajdonság lehetővé teszi az ügyfelek számára, hogy olyan interaktív webhelyeket hozzanak létre, amelyek nagy adatkészletekkel működnek. Olyan szolgáltatások létrehozásához, amelyek több millió végpontérzékelő- és telemetriai adatait tárolják. És elemezni ezeket az adatokat A Hadoop feladatokat. A HBase és a Hadoop jó kiindulópontaz Azure-beli big data-projektekhez. A szolgáltatások lehetővé teszik a valós idejű alkalmazások nagy adatkészletek.

A HDInsight-implementáció a HBase kibővített architektúráját használja a táblák automatikus horizontális skálázásának biztosításához. Az olvasások és írások erős konzisztenciája és az automatikus feladatátvétel. A teljesítményt a memóriába való gyorsítótárazás növeli az olvasáshoz, és a nagy streaming-kapacitás az írásokhoz. A HBase-fürt a virtuális hálózaton belül hozható létre. További információ: [HDInsight-fürtök létrehozása az Azure Virtual Networkön](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hogyan történik az adatok kezelése a HDInsight HBase-ben?

Az adatok a HBase-ben a(z) `create`, `get`, `put` és `scan` parancsokkal kezelhetők a HBase rendszerhéjból. Az adatok a(z) `put` paranccsal írhatók, és a(z) `get` paranccsal olvashatók az adatbázisban. A(z) `scan` paranccsal szerezhetők be adatok a táblák több sorából. Az adatok a HBase C# API-val is kezelhetők, amely egy ügyfélkönyvtárat biztosít a HBase REST API-n felül. A HBase-adatbázisok apache [hive](https://hive.apache.org/)használatával is lekérdezhetők. Ezeknek a programozási modelleknek a bemutatása Az [Apache HBase használatának első lépései az Apache Hadoop segítségével a HDInsight ban című témakörben található.](./apache-hbase-tutorial-get-started-linux.md) Társfeldolgozók is rendelkezésre állnak, amelyek lehetővé teszik az adatfeldolgozást az adatbázist üzemeltető csomópontokban.

> [!NOTE]  
> A HBase nem támogatja a Thriftet a HDInsightban.

## <a name="use-cases-for-apache-hbase"></a>Használati esetek az Apache HBase-hez

A gyűjtőhasználati eset, amelyre a BigTable (és tágabb értelemben a HBase) a webes keresésből készült. A keresőmotorok indexeket építenek, amelyek kifejezéseket képeznek le az azokat tartalmazó weblapokra. A HBase azonban sok más használati esethez megfelelő – amelyek közül több ebben a szakaszban van felsorolva.

|Forgatókönyv |Leírás |
|---|---|
|Kulcs-érték tároló|A HBase kulcsérték-tárolóként használható, és alkalmas az üzenetrendszerek kezelésére. A Facebook a HBase-t használja az üzenetküldő rendszeréhez, és ideális az internetes kommunikáció tárolására és kezelésére. A WebTable a HBase eszközt használja a weblapokról kinyert táblázatok keresésére és kezelésére.|
|Érzékelői adatok|A HBase hasznos a növekményesen, különböző forrásokból gyűjtött adatok rögzítéséhez. Ezek közé tartoznak a közösségi elemzések és az idősorok. Az interaktív irányítópultok naprakészen tartása a trendekkel és számlálókkal, valamint a naplórendszerek kezelése. Ilyen például a Bloomberg kereskedői terminál és az Open Time Series Database (OpenTSDB). Az OpenTSDB tárolja és hozzáférést biztosít a kiszolgálórendszerek állapotáról gyűjtött mutatókhoz.|
|Valós idejű lekérdezés|[Az Apache Phoenix](https://phoenix.apache.org/) az Apache HBase SQL-lekérdezési motorja. JDBC-illesztőprogramként érhető el, és lehetővé teszi a HBase-táblák lekérdezését és kezelését az SQL használatával.|
|A HBase platformként|Az alkalmazások a HBase felett futhatnak adattárolóként. Ilyen például a Phoenix, `Kiji`az OpenTSDB és a Titan. Az alkalmazások integrálhatók is a HBase eszközzel. Ilyenek például: [Apache Hive](https://hive.apache.org/), Apache Pig, [Solr](https://lucene.apache.org/solr/), Apache Storm, `Ganglia`Apache Flume, Apache [Impala](https://impala.apache.org/), Apache Spark , és Apache Drill.|

## <a name="next-steps"></a>További lépések

* [Az Apache HBase és az Apache Hadoop használatának első lépései a HDInsightban](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight-fürtök létrehozása az Azure Virtual Networkön](./apache-hbase-provision-vnet.md)
* [Az Apache HBase replikáció konfigurálása a HDInsightban](apache-hbase-replication.md)
