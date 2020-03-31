---
title: Mi az Apache Hive és HiveQL - Azure HDInsight
description: Az Apache Hive az Apache Hadoop adattárrendszere. A Hive-ben tárolt adatok lekérdezése hiveQL használatával, amely hasonló a Transact-SQL használatával. Ebben a dokumentumban megtudhatja, hogyan használhatja a Hive-t és a HiveQL-t az Azure HDInsight-mal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: f7dc7b520cba2bbf2351d93795a1a26b3b5124be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471353"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Mi az Apache Hive és a HiveQL az Azure HDInsightban?

[Az Apache Hive](https://hive.apache.org/) az Apache Hadoop adattárrendszere. Hive lehetővé teszi az adatok összegzése, lekérdezése és elemzése az adatok. Hive-lekérdezések vannak írva HiveQL, amely az SQL-hez hasonló lekérdezési nyelv.

Hive lehetővé teszi, hogy a nagyrészt strukturálatlan adatok struktúráját. Miután definiálta a struktúrát, a HiveQL segítségével lekérdezheti az adatokat a Java vagy a MapReduce ismerete nélkül.

A HDInsight számos fürttípust biztosít, amelyek adott számítási feladatokhoz vannak hangolva. A hive-lekérdezésekhez leggyakrabban a következő fürttípusokat használják:

|Fürt típusa |Leírás|
|---|---|
|Interaktív lekérdezés|Hadoop-fürt, amely [alacsony késleltetésű analitikai feldolgozási (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) funkciókat biztosít az interaktív lekérdezések válaszidejének javítása érdekében. További információt a [Start interaktív lekérdezéssel a HDInsight-dokumentumban című dokumentumban talál.](../interactive-query/apache-interactive-query-get-started.md)|
|Hadoop|A Batch-feldolgozási számítási feladatokra hangolt Hadoop-fürt. További információ: [Start with Apache Hadoop a HDInsight-dokumentumban.](../hadoop/apache-hadoop-linux-tutorial-get-started.md)|
|Spark|Az Apache Spark beépített funkciókkal rendelkezik a Hive-val való munkához. További információ: [Start with Apache Spark on HDInsight](../spark/apache-spark-jupyter-spark-sql.md) document.|
|HBase|A HiveQL az Apache HBase-ben tárolt adatok lekérdezésére használható. További információ: [Start with Apache HBase on HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) document.|

## <a name="how-to-use-hive"></a>A Hive használata

Az alábbi táblázat segítségével megismerheti a Hive HDInsight-mal való használatának különböző módjait:

| **Használja ezt a módszert,** ha azt szeretné... | ... **interaktív** lekérdezések | ... **kötegelt** feldolgozás | ... ebből az **ügyféloperációs rendszerből** |
|:--- |:---:|:---:|:--- |:--- |
| [HDInsight-eszközök a Visual Studio-kódhoz](../hdinsight-for-vscode.md) |✔ |✔ | Linux, Unix, Mac OS X vagy Windows |
| [HDInsight-eszközök a Visual Studio számára](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Hive-nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Bármely (böngésző alapú) |
| [Beeline ügyfél](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, Unix, Mac OS X vagy Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, Unix, Mac OS X vagy Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>HiveQL nyelvi útmutató

A HiveQL nyelvi referencia a [nyelvi kézikönyvben](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)érhető el.

## <a name="hive-and-data-structure"></a>Kaptár és adatstruktúra

Hive tisztában van a strukturált és félig strukturált adatok kal. Például olyan szövegfájlok, amelyekben a mezőket meghatározott karakterek jelölik. A következő HiveQL utasítás létrehoz egy táblázatot a térben tagolt adatok felett:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Hive is támogatja az egyéni **szerializáló/deszerializálók (SerDe)** összetett vagy szabálytalanul strukturált adatok. További információt az [egyéni JSON-serde használata hdinsight-dokumentummal](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) című témakörben talál.

A Hive által támogatott fájlformátumokról a [nyelvihttps://cwiki.apache.org/confluence/display/Hive/LanguageManual) kézikönyvben (](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Hive belső táblák vs külső táblák

A Hive segítségével kétféle táblát hozhat létre:

* __Belső__: Az adatok a Hive adattárházban tárolódnak. Az adattárház `/hive/warehouse/` a fürt alapértelmezett tárolóján található.

    Belső táblákat akkor használjon, ha az alábbi feltételek valamelyike érvényes:

    * Az adatok ideiglenesek.
    * Azt szeretné, hogy a Hive kezelje a tábla és az adatok életciklusát.

* __Külső__: Az adatok tárolása az adatraktáron kívül történik. Az adatok a fürt által elérhető bármely tárolón tárolhatók.

    Külső táblákat akkor használjon, ha az alábbi feltételek valamelyike érvényes:

    * Az adatok is használják a Hive kívül. Az adatfájlokat például egy másik folyamat frissíti (amely nem zárolja a fájlokat.)
    * Az adatoknak az alapul szolgáló helyen kell maradniuk, még a tábla eldobása után is.
    * Egyéni helyre van szüksége, például egy nem alapértelmezett tárfiókra.
    * A struktúratől eltérő program kezeli az adatformátumot, a helyet és így tovább.

További információ: [A Hive belső és külső táblák intro](https://blogs.msdn.microsoft.com/cindygross/2013/02/05/hdinsight-hive-internal-and-external-tables-intro/) blogbejegyzést.

## <a name="user-defined-functions-udf"></a>Felhasználó által definiált függvények (UDF)

A Hive a **felhasználó által definiált függvények (UDF)** használatával is bővíthető. Az UDF lehetővé teszi olyan funkciók vagy logika megvalósítását, amelyek nem könnyen modellezhetők a HiveQL-ben. Az UDF-ek Hive-val való használatával például a következő dokumentumokban található:

* [Java-felhasználó által definiált függvény használata az Apache Hive-val](../hadoop/apache-hadoop-hive-java-udf.md)

* [Python-felhasználó által definiált függvény használata az Apache Hive-val](../hadoop/python-udf-hdinsight.md)

* [C# felhasználó által definiált függvény használata az Apache Hive-val](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Egyéni Apache Hive-felhasználó által definiált függvény hozzáadása a HDInsighthoz](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Egy példa az Apache Hive felhasználó által definiált függvényre a dátum-/időformátumok Hive időbélyeggé történő konvertálásához](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="example-data"></a>Példaadatok

A HIVE a HDInsight-on előre `hivesampletable`be van töltve egy belső táblával, amelynek neve . A HDInsight a Hive-val használható példaadatkészleteket is biztosít. Ezeket az adatkészleteket `/example/data` a `/HdiSamples` könyvtárak és a könyvtárak tárolják. Ezek a könyvtárak a fürt alapértelmezett tárolójában léteznek.

## <a name="example-hive-query"></a>Példa Hive-lekérdezésre

A következő HiveQL utasítások projektoszlopokat vetítanek a `/example/data/sample.log` fájlra:

```hiveql
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
    GROUP BY t4;
```

Az előző példában a HiveQL utasítások a következő műveleteket hajtják végre:

|Nyilatkozat |Leírás |
|---|---|
|TÁBLÁZAT LEDOBÁSA|Ha a tábla már létezik, törölje.|
|KÜLSŐ TÁBLA LÉTREHOZÁSA|Új **külső** táblát hoz létre a Hive-ban. A külső táblák csak a tábladefiníciót tárolják hive.External tables only store the table definition in Hive. Az adatok az eredeti helyen és az eredeti formátumban maradnak.|
|SOR FORMÁTUMA|Megmutatja a Hive-nak, hogyan vannak formázva az adatok. Ebben az esetben az egyes naplók mezőit szóköz választja el egymástól.|
|TÁROLT SZÖVEGFÁJL HELYE|Megmondja a Hive-nak, `example/data` hogy hol tárolja az adatokat (a könyvtárat), és hogy szövegként tárolja. Az adatok lehetnek egy fájlban, vagy több fájl között elosztva a könyvtárban.|
|SELECT|Kijelöli az összes olyan sor számát, ahol a **t4** oszlop a **[HIBA]** értéket tartalmazza. Ez az utasítás **3** értéket ad vissza, mert három sor van, amely ezt az értéket tartalmazza.|
|INPUT__FILE__NAME'%.log' kedvelése|Hive megpróbálja alkalmazni a sémát a könyvtár összes fájljára. Ebben az esetben a könyvtár olyan fájlokat tartalmaz, amelyek nem egyeznek meg a sémával. Az eredményekben lévő szemétadatok elkerülése érdekében ez a nyilatkozat azt jelzi a Hive-nak, hogy csak a .log végződésbe végződő fájlokból kell adatokat visszaadnunk.|

> [!NOTE]  
> Külső táblákat kell használni, amikor azt várja, hogy az alapul szolgáló adatokat egy külső forrás frissíti. Például egy automatikus adatfeltöltési folyamat, vagy MapReduce művelet.
>
> A külső tábla eldobása **nem** törli az adatokat, csak a tábladefiníciót törli.

Ha külső helyett **belső** táblát szeretne létrehozni, használja a következő HiveQL-t:

```hiveql
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

Ezek az utasítások a következő műveleteket hajtják végre:

|Nyilatkozat |Leírás |
|---|---|
|TÁBLA LÉTREHOZÁSA, HA NEM LÉTEZIK|Ha a tábla nem létezik, hozza létre. Mivel a **KÜLSŐ** kulcsszó nincs használatban, ez az utasítás belső táblát hoz létre. A tábla a Hive adattárházban van tárolva, és teljes mértékben a Hive kezeli.|
|TÁROLT ORC|Az adatokat optimalizált soroszlopos (ORC) formátumban tárolja. Az ORC egy rendkívül optimalizált és hatékony formátum a Hive-adatok tárolására.|
|FELÜLÍRÁS BESZÚRÁSA ... Válassza ki|Kijelöli a sorokat a **log4jLogs** táblából, amely **[HIBA]** elemet tartalmaz, majd beszúrja az adatokat az **errorLogs** táblába.|

> [!NOTE]  
> A külső táblákkal ellentétben a belső táblák eldobása az alapul szolgáló adatokat is törli.

## <a name="improve-hive-query-performance"></a>A Hive-lekérdezési teljesítmény javítása

### <a name="apache-tez"></a>Apache Tez

[Az Apache Tez](https://tez.apache.org) egy olyan keretrendszer, amely lehetővé teszi az adatigényes alkalmazások, például a Hive futtatását sokkal hatékonyabban nagy léptékben. A Tez alapértelmezés szerint engedélyezve van.  Az [Apache Hive a Tez tervezési dokumentumok](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) részletesen ismerteti a megvalósítási lehetőségek et és a konfigurációk finomhangolását.

### <a name="low-latency-analytical-processing-llap"></a>Alacsony késleltetésű analitikai feldolgozás (LLAP)

[Az LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (más néven Élő hosszú és folyamat) a Hive 2.0 új szolgáltatása, amely lehetővé teszi a lekérdezések memórián belüli gyorsítótárazását. Az LLAP bizonyos esetekben sokkal gyorsabbá teszi a Hive-lekérdezéseket, akár [26-szor gyorsabbá, mint a Hive 1.x.](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)

A HDInsight az Interaktív lekérdezés fürttípusában biztosítja az LLAP-ot. További információt a [Start with Interactive Query](../interactive-query/apache-interactive-query-get-started.md) dokumentumban talál.

## <a name="scheduling-hive-queries"></a>Hive-lekérdezések ütemezése

Számos olyan szolgáltatás létezik, amely egy ütemezett vagy igény szerinti munkafolyamat részeként hive-lekérdezések futtatására használható.

### <a name="azure-data-factory"></a>Azure Data Factory

Az Azure Data Factory lehetővé teszi a HDInsight használatát a Data Factory folyamat részeként. A Hive folyamatból való használatával kapcsolatos további információkért tekintse meg az [adatok átalakítása hive-tevékenység használatával az Azure Data Factory dokumentumban.](../../data-factory/transform-data-using-hadoop-hive.md)

### <a name="hive-jobs-and-sql-server-integration-services"></a>Hive-feladatok és SQL Server-integrációs szolgáltatások

Az SQL Server Integration Services (SSIS) segítségével futtathat egy Hive-feladatot. Az Azure Feature Pack for SSIS a következő összetevőket biztosítja, amelyek a HDInsight Hive-feladataival működnek.

* [Azure HDInsight Hive-feladat](https://docs.microsoft.com/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Azure előfizetéses csatlakozáskezelő](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-subscription-connection-manager)

További információt az [Azure Feature Pack dokumentációjában talál.](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis)

### <a name="apache-oozie"></a>Apacs Oozie

Az Apache Oozie egy munkafolyamat- és koordinációs rendszer, amely a Hadoop-feladatokat kezeli. Az Oozie hive használatával kapcsolatos további információkért tekintse meg az [Apache Oozie használata munkafolyamat-dokumentum definiálása és futtatása](../hdinsight-use-oozie-linux-mac.md) című témakört.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, mi az a Hive, és hogyan használhatja azt a Hadoop segítségével a HDInsightban, az alábbi hivatkozások segítségével további módszereket is megtudthat az Azure HDInsight használatával.

* [Adatok feltöltése a HDInsightba](../hdinsight-upload-data.md)
* [Python-felhasználó által definiált függvények (UDF) használata Apache Hive-val és Apache Pig-rel a HDInsightban](./python-udf-hdinsight.md)
* [MapReduce-feladatok használata a HDInsightban](hdinsight-use-mapreduce.md)
