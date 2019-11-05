---
title: Mi az Apache Hive és a HiveQL – Azure HDInsight
description: A Apache Hive egy adattárház-rendszer Apache Hadoop számára. A kaptárban tárolt adatlekérdezéseket a Transact-SQL-hez hasonló HiveQL használatával kérdezheti le. Ebből a dokumentumból megtudhatja, hogyan használható a kaptár és a HiveQL az Azure HDInsight.
keywords: hiveql, mi az a kaptár, a Hadoop hiveql, a kaptár használata, a struktúra megismerése, mi az a kaptár
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: e07939bd5f0264df637fda439d96be213a8d28d1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499209"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Mi a Apache Hive és a HiveQL az Azure HDInsight?

A [Apache Hive](https://hive.apache.org/) egy adattárház-rendszer Apache Hadoop számára. A struktúra lehetővé teszi az adatösszesítést, az adatlekérdezést és az adatelemzést. A kaptár-lekérdezések a HiveQL-ben íródnak, amely az SQL-hez hasonló lekérdezési nyelv.

A struktúra lehetővé teszi a nagy mértékben strukturált adatmennyiségek struktúrájának kivetítését. A struktúra meghatározása után a HiveQL segítségével lekérdezheti az adatait Java vagy MapReduce ismerete nélkül.

A HDInsight többféle típusú fürtöt biztosít, amelyek meghatározott számítási feladatokhoz vannak hangolva. A következő típusú fürtök leggyakrabban a kaptár-lekérdezésekhez használatosak:

* __Interaktív lekérdezés__: olyan Hadoop-fürt, amely [kis késleltetésű analitikai feldolgozási (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) funkciókat biztosít az interaktív lekérdezések válaszideje érdekében. További információ: az [interaktív lekérdezés elindítása a HDInsight](../interactive-query/apache-interactive-query-get-started.md) dokumentumban.

* __Hadoop__: Hadoop-fürt, amely a kötegelt feldolgozási feladatokhoz van beállítva. További információ: [Start with apache Hadoop in HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) Document.

* __Spark__: a Apache Spark beépített funkcionalitással rendelkezik a struktúra használatáról. További információ: [Start with apache Spark on HDInsight](../spark/apache-spark-jupyter-spark-sql.md) Document.

* __HBase__: a HiveQL használható az Apache HBase tárolt adatlekérdezéshez. További információ: az [Apache HBase használata a HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) -dokumentumban.

## <a name="how-to-use-hive"></a>A kaptár használata

A következő táblázat segítségével megismerheti a Kaptárnak a HDInsight való használatának különböző módszereit:

| **Használja ezt a módszert** , ha szeretné... | ... **interaktív** lekérdezések | ... **kötegelt** feldolgozás | ... Ebből az **ügyféloldali operációs rendszerből** |
|:--- |:---:|:---:|:--- |:--- |
| [A Visual Studio Code-hoz készült HDInsight Tools](../hdinsight-for-vscode.md) |✔ |✔ | Linux, UNIX, Mac OS X vagy Windows |
| [A Visual studióhoz készült HDInsight-eszközök](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Struktúra nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Bármely (böngésző alapú) |
| [Ügyfél Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, UNIX, Mac OS X vagy Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, UNIX, Mac OS X vagy Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>HiveQL nyelvi referenciája

A HiveQL nyelvi referenciája a [nyelvi kézikönyvben](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)érhető el.

## <a name="hive-and-data-structure"></a>Struktúra és adatstruktúra

A struktúra tisztában van a strukturált és részben strukturált adatmennyiséggel. Például olyan szövegfájlok, amelyekben a mezők meghatározott karakterek szerint vannak tagolva. A következő HiveQL-utasítás egy táblát hoz létre szóközzel tagolt adatokkal:

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

A kaptár az egyéni **szerializáló/deszerializáló (SerDe)** használatát is támogatja összetett vagy szabálytalanul strukturált adatként. További információ: [Egyéni JSON-SerDe használata HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) -dokumentummal.

A kaptár által támogatott fájlformátumokkal kapcsolatos további információkért tekintse meg a [nyelvi kézikönyvet (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Belső táblák és külső táblák struktúrája

A struktúra két típusú táblát hozhat létre:

* __Belső__: a rendszer a struktúra adattárházában tárolja az adattárolási adatraktárat. Az adatraktár a fürt alapértelmezett tárolóján `/hive/warehouse/` helyen található.

    Belső táblák használata, ha a következő feltételek valamelyike teljesül:

    * Az adatmennyiség ideiglenes.
    * Azt szeretné, hogy a struktúra kezelje a tábla és az adatmennyiség életciklusát.

* __Külső__: a rendszer az adatraktáron kívül tárolja az adattárolást. Az adattárolás a fürt által elérhető bármely tárolóban lehetséges.

    Külső táblák használata, ha a következő feltételek valamelyike teljesül:

    * Az adatszerkezet a kaptáron kívül is használatos. Az adatfájlokat például egy másik folyamat frissíti (amely nem zárolja a fájlokat)
    * Az adatokat a mögöttes helyen kell tartani, még a tábla eldobása után is.
    * Egyéni helyre van szüksége, például egy nem alapértelmezett Storage-fiókra.
    * A kaptártól eltérő program nem kezeli az adatformátumot, a helyet stb.

További információért lásd a [belső és külső táblák](https://blogs.msdn.microsoft.com/cindygross/2013/02/05/hdinsight-hive-internal-and-external-tables-intro/) összeadását ismertető blogbejegyzést.

## <a name="user-defined-functions-udf"></a>Felhasználó által definiált függvények (UDF)

A struktúra a **felhasználó által definiált függvények (UDF)** használatával is bővíthető. Az UDF lehetővé teszi olyan funkciók vagy logika megvalósítását, amelyek nem könnyen modellezhető a HiveQL-ben. A UDF és a kaptár használatával kapcsolatos példát a következő dokumentumokban talál:

* [Java-felhasználó által definiált függvény használata Apache Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Python felhasználó által definiált függvény használata Apache Hive](../hadoop/python-udf-hdinsight.md)

* [C# Felhasználó által definiált függvény használata Apache Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Egyéni Apache Hive felhasználó által definiált függvény hozzáadása a HDInsight-hez](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Példa Apache Hive felhasználó által definiált függvényt a dátum-és időformátumok átalakításához a kaptár időbélyegére](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Példa az adatértékekre

A kaptár on HDInsight előre be van töltve egy `hivesampletable` nevű belső táblával. A HDInsight olyan adatkészleteket is biztosít, amelyek a kaptár használatával használhatók. Ezeket az adatkészleteket a `/example/data` és a `/HdiSamples` könyvtár tárolja. Ezek a könyvtárak a fürt alapértelmezett tárolójában találhatók.

## <a id="job"></a>Példa a kaptár lekérdezésére

A következő HiveQL utasítások a `/example/data/sample.log` fájlba kerülnek:

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

* `DROP TABLE`: Ha a tábla már létezik, törölje.

* `CREATE EXTERNAL TABLE`: új **külső** táblát hoz létre a kaptárban. A külső táblák csak a struktúra tábla definícióját tárolják. Az adatmező az eredeti helyen és az eredeti formátumban marad.

* `ROW FORMAT`: azt jelzi, hogy az adat hogyan formázott. Ebben az esetben az egyes naplók mezői szóközzel vannak elválasztva.

* `STORED AS TEXTFILE LOCATION`: azt jelzi, hogy a struktúra hol tárolja az adattárolást (a `example/data` könyvtárat), valamint azt, hogy az a szövegként van tárolva. Az adatfájlok egy fájlban lehetnek, vagy a címtárban található több fájl között is elterjedhetnek.

* `SELECT`: kiválasztja az összes olyan sor számát, amelyben a **T4** oszlop tartalmazza a **[Error]** értéket. Ez az utasítás **3** értéket ad vissza, mert három sor tartalmazza ezt az értéket.

* `INPUT__FILE__NAME LIKE '%.log'` – a struktúra megpróbálja alkalmazni a sémát a címtárban található összes fájlra. Ebben az esetben a könyvtár olyan fájlokat tartalmaz, amelyek nem egyeznek a sémával. Ha meg szeretné akadályozni, hogy az eredmények ne kerüljenek az adatokba, ez az utasítás azt ismerteti, hogy a kaptár csak a. log fájlban végződő fájlokból tér vissza.

> [!NOTE]  
> Külső táblákat kell használni, ha várható, hogy a mögöttes adatokat külső forrás frissíti. Például egy automatizált adatfeltöltési folyamat vagy MapReduce művelet.
>
> Egy külső tábla eldobása **nem** törli az adatforrást, csak a tábla definícióját törli.

**Belső** tábla külső helyett történő létrehozásához használja a következő HiveQL:

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

* `CREATE TABLE IF NOT EXISTS`: Ha a tábla nem létezik, hozza létre. Mivel a **külső** kulcsszó nincs használatban, az utasítás belső táblát hoz létre. A rendszer a struktúra adattárházában tárolja a táblázatot, és a struktúra teljes mértékben kezeli.

* `STORED AS ORC`: az adatok az optimalizált sorok oszlopos (ORK) formátumban vannak tárolva. Az ork kiválóan optimalizált és hatékony formátum a kaptárak adatok tárolására.

* `INSERT OVERWRITE ... SELECT`: kijelöli a **log4jLogs** tábla azon sorait, amelyek **[Error]** elemet tartalmaznak, majd beszúrja az adatait a **alkalmazásnaplókat** táblába.

> [!NOTE]  
> A külső tábláktól eltérően a belső táblák eldobása is törli az alapul szolgáló adatokat.

## <a name="improve-hive-query-performance"></a>A kaptár-lekérdezés teljesítményének javítása

### <a id="usetez"></a>Apache TEZ

Az [Apache TEZ](https://tez.apache.org) egy olyan keretrendszer, amely lehetővé teszi az adatigényes alkalmazások, például a kaptárok számára, hogy sokkal hatékonyabban futtassák a méretezést. A TEZ alapértelmezés szerint engedélyezve van.  A [TEZ-tervezési dokumentumok Apache Hive](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) a megvalósítási döntések részleteit és hangolási konfigurációkat tartalmaz.

### <a name="low-latency-analytical-processing-llap"></a>Kis késleltetésű analitikus feldolgozás (LLAP)

A [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (más néven élő hosszú és folyamat) a kaptár 2,0 új funkciója, amely lehetővé teszi a lekérdezések memórián belüli gyorsítótárazását. A LLAP sokkal gyorsabban teszi a kaptár-lekérdezéseket, és akár [26x gyorsabban, mint a kaptár 1. x bizonyos esetekben](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

A HDInsight az interaktív lekérdezési fürt típusa LLAP biztosít. További információ: az [interaktív lekérdezési dokumentum elindítása](../interactive-query/apache-interactive-query-get-started.md) .

## <a name="scheduling-hive-queries"></a>Struktúra-lekérdezések ütemezése

Több szolgáltatás is használható a kaptár-lekérdezések ütemezett vagy igény szerinti munkafolyamatok részeként történő futtatására.

### <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory lehetővé teszi a HDInsight használatát egy Data Factory folyamat részeként. A struktúra folyamatból való használatával kapcsolatos további információkért lásd az [adatok átalakítása struktúra használatával Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md) dokumentumban.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Struktúra-feladatok és SQL Server Integration Services

A kaptár-feladatok futtatásához SQL Server Integration Services (SSIS) használható. A SSIS készült Azure Feature Pack a következő összetevőket nyújtja, amelyek a HDInsight-on található kaptár-feladatokkal működnek.

* [Azure HDInsight-struktúra feladat](https://docs.microsoft.com/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Azure-előfizetési kapcsolatkezelő](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-subscription-connection-manager)

További információt az [Azure Feature Pack](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis) dokumentációjában talál.

### <a name="apache-oozie"></a>Apache Oozie

Az Apache Oozie egy munkafolyamat-és koordinációs rendszer, amely a Hadoop-feladatokat kezeli. A Oozie és a kaptár használatával kapcsolatos további információkért tekintse meg az [Apache Oozie használata munkafolyamat-dokumentum definiálásához és futtatásához](../hdinsight-use-oozie-linux-mac.md) című témakört.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogy mi a kaptár, és hogyan használható a Hadoop-ben a HDInsight-ben, az alábbi hivatkozásokat követve megismerheti az Azure HDInsight szolgáltatással való munkavégzés más módszereit.

* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md)
* [Python-felhasználó által definiált függvények (UDF) használata Apache Hive és Apache Pig használatával a HDInsight-ben](./python-udf-hdinsight.md)
* [MapReduce-feladatok használata a HDInsightban](hdinsight-use-mapreduce.md)
