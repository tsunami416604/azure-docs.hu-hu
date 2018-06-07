---
title: Mi az Apache Hive és a HiveQL - Azure HDInsight |} Microsoft Docs
description: A Hadoop adatraktárrendszer Apache Hive. A Hive használata a HiveQL, adataihoz lekérheti amely Transact-SQL hasonló. Ebből a dokumentumból megtudhatja, hogyan Azure HDInsight Hive és a HiveQL használandó.
keywords: hiveql, mi az hive, hadoop hiveql a hive használata kapcsolatos további tudnivalók a hive, mi az hive
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: e418411cc6b681e304cc1ba66f0c815ad0d4db64
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34069709"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Mi az Apache Hive és a Azure HDInsight HiveQL?

[Apache Hive](http://hive.apache.org/) egy adatraktárrendszer van a Hadoop. Hive lehetővé teszi, hogy adatainak összefoglalója lekérdezése és az adatok elemzését. Hive-lekérdezések HiveQL, amely hasonló SQL lekérdezésnyelvet nyelven íródtak.

Hive lehetővé teszi a nagy mértékben strukturálatlan adatok szerkezetének. A struktúra meghatározása után HiveQL használatával Java vagy MapReduce ismerete nélkül a lekérdezést.

A HDInsight fürt számos különböző, amelyek adott munkaterhelés konkrét hangolt biztosít. A következő fürttípusok leggyakrabban használt a Hive-lekérdezéseket:

* __Interaktív lekérdezés__: A Hadoop-fürt biztosító [alacsony késleltetésű analitikus feldolgozási (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) funkciót a javíthatja interaktív lekérdezések válaszidejét. További információkért lásd: a [hdinsight interaktív lekérdezés kezdődnie](../interactive-query/apache-interactive-query-get-started.md) dokumentum.

* __Hadoop__: A Hadoop-fürt, amely a kötegelt feldolgozáson munkaterhelések van beállítva. További információkért lásd: a [indítsa el a HDInsight Hadoop](../hadoop/apache-hadoop-linux-tutorial-get-started.md) dokumentum.

* __Spark__: Apache Spark rendelkezik beépített funkcióval Hive használata. További információkért lásd: a [indítsa el a Spark on HDInsight](../spark/apache-spark-jupyter-spark-sql.md) dokumentum.

* __A HBase__: HiveQL a HBase lekérdezés adataihoz is használható. További információkért lásd: a [indítsa el a HDInsight HBase](../hbase/apache-hbase-tutorial-get-started-linux.md) dokumentum.

## <a name="how-to-use-hive"></a>Hive használata

A következő táblázat segítségével Hive használata a Hdinsightban különböző módjainak felderítéséhez:

| **Ezzel a módszerrel** Ha azt szeretné... | ... **interaktív** lekérdezések | ...**kötegelt** feldolgozása | és mivel ez **fürt operációs rendszer** | ...from ez **ügyfél operációs rendszer** |
|:--- |:---:|:---:|:--- |:--- |
| [A HDInsight tools for Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ |Linux | Linux, Unix, Mac OS X vagy Windows |
| [A HDInsight tools for Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Linux vagy a Windows |Windows |
| [Hive nézete](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Bármely (böngészőalapú) |
| [Beeline ügyfél](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X vagy Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux vagy a Windows |Linux, Unix, Mac OS X vagy Windows |
| [A Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux vagy a Windows |Windows |

> [!IMPORTANT]
> \* Linux az egyetlen operációs rendszer használt a HDInsight 3.4 vagy újabb verziója. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="hiveql-language-reference"></a>HiveQL nyelvi referencia

HiveQL nyelvi dokumentáció áll rendelkezésre a [nyelvi manuális (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive és az adatok szerkezete

Hive együttműködik a strukturált és félig strukturált adatok használata. Például szövegfájlok ahol a mezők határolja különleges karaktereket. A következő HiveQL-utasítás táblázatot hoz létre szóközökkel elválasztott kötetnevek adatok:

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

Hive is támogatja az egyéni **szerializáló/deserializers (SerDe)** túl összetett vagy szabálytalan strukturált adatok számára. További információkért lásd: a [egyéni JSON-SerDe használata a HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx) dokumentum.

A Hive támogatott fájlformátumok további információkért lásd: a [nyelvi manuális ()https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Belső tábla és a külső táblákra struktúra

Olyan táblázatot, amely a Hive hozhat létre két típusa van:

* __Belső__: adatokat a Hive-adatraktárban tárolja. Az adatraktár itt található: `/hive/warehouse/` az alapértelmezett tároló, a fürt számára.

    Belső táblázatot használnak, ha a következő feltételek valamelyike esetén:

    * Adatok csak átmenetileg létezik.
    * Azt szeretné, hogy a struktúra a táblázat és az adatok az életciklus kezeléséhez.

* __Külső__: kívül az adatraktár tárolja. Az adatok a fürt által elérhető minden tárterület tárolható.

    Külső táblák használja, amikor alkalmazza a következő feltételek valamelyike esetén:

    * Az adatok Hive kívül is használható. Például az adatfájlok frissítése egy másik folyamat (vagyis nem zárolja a fájlokat.)
    * Adatoknak kell alapul szolgáló helyét, a tábla eldobása után is megmaradnak.
    * Egyéni helyen, például egy nem alapértelmezett tárfiók van szüksége.
    * Nem a hive kezeli az adatformátum, hely, stb.

További információkért lásd: a [Hive belső és külső táblák bevezetés] [ cindygross-hive-tables] blogbejegyzést.

## <a name="user-defined-functions-udf"></a>Felhasználói függvény (UDF)

Hive is kiterjeszthető keresztül **felhasználói függvény (UDF)**. Egy UDF funkció vagy logika, amely nem egyszerű modellezve megvalósítását a HiveQL teszi lehetővé. Például egy felhasználó által megadott függvények használata a Hive lásd a következő dokumentumokat:

* [A Java-felhasználó által definiált függvény használata struktúra](../hadoop/apache-hadoop-hive-java-udf.md)

* [A Python felhasználó által definiált függvény használata struktúra](../hadoop/python-udf-hdinsight.md)

* [A C# felhasználó által definiált függvény használata struktúra](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [HDInsight egyéni Hive felhasználó által definiált függvény hozzáadása](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Példa Hive felhasználó által definiált függvény Dátum-/ időformátumok átalakítása Hive időbélyeg](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Példa adatok

A HDInsight Hive előre betöltött tartalmaz egy belső tábla nevű `hivesampletable`. HDInsight Hive használható például adatkészleteket is biztosít. Ezek az adathalmazok tárolódnak a `/example/data` és `/HdiSamples` könyvtárak. Ezeket a könyvtárakat az alapértelmezett tároló, a fürt szerepel.

## <a id="job"></a>Példa Hive-lekérdezések

A következő hiveql-projekt oszlopok alakzatot a `/example/data/sample.log` fájlt:

```hiveql
set hive.execution.engine=tez;
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

Az előző példában a HiveQL utasításokat a következő műveleteket:

* `set hive.execution.engine=tez;`: A-végrehajtó motor Tez használatára állítja be. Tez használatával biztosít a lekérdezési teljesítmény növelését. Tez további információkért tekintse meg a [Apache Tez használja a jobb teljesítmény](#usetez) szakasz.

    > [!NOTE]
    > Az utasítás csak egy Windows-alapú HDInsight-fürt használata esetén szükséges. Tez a Linux-alapú hdinsight alapértelmezett-végrehajtó motor.

* `DROP TABLE`: Ha a tábla már létezik, törölje azt.

* `CREATE EXTERNAL TABLE`: Létrehoz egy új **külső** Hive táblát. Külső táblák csak tárolja a tábladefiníció struktúra. Az adatok marad az eredeti helyen és az eredeti formátumban.

* `ROW FORMAT`: Az adatok formázását Hive jelzi. Ebben az esetben a mezőket az egyes naplókon szóközzel elválasztva.

* `STORED AS TEXTFILE LOCATION`: Az adatok tárolására Hive jelzi (a `example/data` könyvtár) és szövegként tárolt. Az adatok egyetlen fájlban vagy több fájl a könyvtárban lévő elosztva.

* `SELECT`: Választja ki az összes sor száma ha az oszlop **t4** értéke **[hiba]**. A jelen nyilatkozat értéket ad vissza, **3** mert három ezt az értéket tartalmazó sorok.

* `INPUT__FILE__NAME LIKE '%.log'` -Hive megkísérli a séma alkalmazása a könyvtárban található összes fájl. Ebben az esetben a directory nem egyeznek meg a séma fájlokat tartalmazza. Szemétgyűjtési adatok a eredmények elkerülése érdekében a jelen nyilatkozat közli struktúra, hogy azt kell csak vissza adatokat fájlok végződése. napló.

> [!NOTE]
> Külső táblák kell használni, amikor külső forrásból frissítenie kell az alapul szolgáló adatokat várt. Például egy automatizált adatok feltöltési folyamat vagy MapReduce művelethez.
>
> A külső tábla eldobása does **nem** törli az adatokat, csak a tábla definíciójában törli.

Létrehozásához egy **belső** helyett külső tábla, használja a következő HiveQL:

```hiveql
set hive.execution.engine=tez;
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

Ezekre az utasításokra hajtsa végre a következő műveleteket:

* `CREATE TABLE IF NOT EXISTS`: Ha a tábla nem létezik, hozza létre. Mivel a **külső** kulcsszó nem használható, a jelen nyilatkozat egy belső táblát hoz létre. A tábla a Hive-adatraktárban tárolja, és Hive teljesen kezeli.

* `STORED AS ORC`: Tárolja az adatokat optimalizált sor oszlopos (ORC) formátumban. ORC formátuma egy magas optimalizált és hatékony Hive adatainak tárolásához.

* `INSERT OVERWRITE ... SELECT`: Azon sorait kiválasztja a **log4jLogs** tartalmazó tábla **[hiba]**, majd beilleszti az adatokat a **errorLogs** tábla.

> [!NOTE]
> Külső táblák eltérően eldobását egy belső tábla is törli az alapul szolgáló adatokat.

## <a name="improve-hive-query-performance"></a>Hive-lekérdezések teljesítményének növelése

### <a id="usetez"></a>Apache Tez

[Apache Tez](http://tez.apache.org) egy keretrendszer, amely lehetővé teszi az adatok alkalmazások, például a Hive, a méretekben sokkal hatékonyabban futtatható. A Linux-alapú HDInsight-fürtökön alapértelmezés szerint engedélyezve van a Tez.

> [!NOTE]
> Tez jelenleg ki alapértelmezés szerint a Windows-alapú HDInsight-fürtök és engedélyezni kell. Tez kihasználását, a következő értéket kell beállítani a Hive-lekérdezések:
>
> `set hive.execution.engine=tez;`
>
> Tez a Linux-alapú HDInsight-fürtök alapértelmezett motor.

A [Hive Tez tervezési dokumentumok](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) megvalósítási és hangolási konfigurációkkal kapcsolatos részleteket tartalmazza.

A feladatok hibakeresés futtatta a Tez használatával, a következő web UI, amelyek lehetővé teszik a Tez feladatok részletes adatainak megtekintéséhez a HDInsight lehetővé:

* [Az Ambari Tez nézetben a Linux-alapú HDInsight-on](../hdinsight-debug-ambari-tez-view.md)

* [A Tez felhasználói felület használata a Windows-alapú HDInsight-on](../hdinsight-debug-tez-ui.md)

### <a name="low-latency-analytical-processing-llap"></a>Kis késleltetésű analitikus feldolgozási (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (más néven hosszú Live és a folyamat), amely lehetővé teszi, hogy a memóriában történő gyorsítótárazás lekérdezések Hive 2.0 új szolgáltatása. LLAP lehetővé teszi a Hive-lekérdezések sokkal gyorsabb, legfeljebb [26 x gyorsabb, mint a Hive 1.x bizonyos esetekben](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight LLAP nyújt az interaktív lekérdezés fürt típusa. További információkért lásd: a [interaktív lekérdezés kezdődnie](../interactive-query/apache-interactive-query-get-started.md) dokumentum.

## <a name="scheduling-hive-queries"></a>Hive-lekérdezések ütemezése

Nincsenek több szolgáltatás segítségével Hive-lekérdezések futtatása az ütemezett vagy igény szerinti munkafolyamat részeként.

### <a name="azure-data-factory"></a>Azure Data Factory

Az Azure Data Factory lehetővé teszi a HDInsight a Data Factory-folyamat részeként használja. A Hive eszközzel egy láncból további információkért lásd: a [Hive tevékenység használata az Azure Data Factory adatok átalakítása](/data-factory/transform-data-using-hadoop-hive.md) dokumentum.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Hive-feladatok és az SQL Server Integration Services

SQL Server Integration Services (SSIS) segítségével egy Hive-feladat futtatása. Az Azure funkciócsomag SSIS biztosít a következő összetevők hdinsight Hive-feladatok együtt használható.

* [Az Azure HDInsight Hive feladat][hivetask]

* [Az Azure előfizetés Csatlakozáskezelő][connectionmanager]

További információkért lásd: a [Azure funkciócsomag] [ ssispack] dokumentációját.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie egy munkafolyamat és koordinációs rendszer, amely a Hadoop-feladatokat kezeli. A Hive használata a Oozie további információkért lásd: a [használata Oozie határozza meg, és egy munkafolyamat futtatása](../hdinsight-use-oozie-linux-mac.md) dokumentum.

## <a id="nextsteps"></a>Következő lépések

Most, hogy megismerte az Hive van, és a hadooppal a Hdinsightban használatával, az alábbi hivatkozások segítségével más módjai Azure HDInsight használata.

* [Adatok feltöltése a HDInsightba][hdinsight-upload-data]
* [A Pig használata a HDInsightban][hdinsight-use-pig]
* [MapReduce-feladatok használata a hdinsight eszközzel][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
