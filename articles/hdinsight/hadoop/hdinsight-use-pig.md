---
title: A HDInsight Hadoop a Pig használata
description: Ismerje meg, hogyan lehet a Pig használata a Hadooppal a HDInsight.
services: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.openlocfilehash: 7f469efb536f8c2dfc95cfe1770544b93c06b29f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597259"
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>A Pig használata a HDInsight Hadoop-keretrendszerrel

Ismerje meg, hogyan használható [Apache Pig](http://pig.apache.org/) a HDInsight.

A Pig az programok hoz létre Hadoop néven eljárási nyelv használatával platform *Pig Latin*. A Pig alternatívája a Java létrehozásához *MapReduce* megoldásokat, és az Azure HDInsight része. Az alábbi táblázat segítségével felderítése a különféle módokon, amely a Pig-alapú HDInsight használható:

| **Ezzel** Ha azt szeretné... | ...an **interaktív** rendszerhéj | ...**kötegelt** feldolgozása | .. során ez **fürt operációs rendszerének** | ...from ez **ügyfél operációs rendszer** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X vagy Windows |
| [REST API](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux vagy Windows |Linux, Unix, Mac OS X vagy Windows |
| [.NET SDK a Hadoophoz](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux vagy Windows |Windows (egyelőre) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux vagy Windows |Windows |

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="why"></a>Miért érdemes a Pig használata

A feldolgozó logika csak egy térképet, és csökkentse a függvény használatával megvalósítása a legnagyobb kihívás az adatok feldolgozási MapReduce a Hadoop használatával. Összetett feldolgozást, gyakran kell a feldolgozása felosztása több MapReduce művelet kapcsolt együtt, hogy a kívánt eredmények eléréséhez.

A Pig lehetővé teszi az adatok előállításához a kívánt kimeneti áthaladó adatátvitelen átalakítások sorozataként feldolgozási.

A Pig Latin nyelv lehetővé teszi a nyers adatok bevitele, egy vagy több átalakítások, a kívánt kimeneti előállításához keresztül érkező adatfolyam ismertetik. A Pig Latin programok az általános mintát követik:

* **Betöltés**: kezelhetők a fájlrendszerből adatokat olvasni.

* **Átalakítás**: az adatok kezelése

* **Memóriakép és nem tárolnak**: kimeneti adatok a képernyőre, vagy a feldolgozáshoz tárolja

### <a name="user-defined-functions"></a>Felhasználó által definiált függvények

A Pig Latin is támogatja felhasználó által definiált függvények (UDF), amely lehetővé teszi, hogy a külső összetevők, amelyek logikákat implementálhat, amely a Pig Latin modell nehéz meghívása.

A Pig Latin kapcsolatos további információkért lásd: [Pig Latin referencia manuális 1](http://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) és [Pig Latin referencia manuális 2](http://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

UDF-EK használatával és a Pig együttes példát a következő dokumentumokban talál:

* [A datafu és a Pig, a HDInsight együttes](apache-hadoop-use-pig-datafu-udf.md) -DataFu hasznos UDF-EK Apache által karbantartott gyűjteménye
* [A Pig and Hive a HDInsight a Python használata](python-udf-hdinsight.md)
* [A Hive és a Piggel a HDInsight használata a C#](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Példa adatok

HDInsight biztosít különböző példa adatkészletek, amelyek tárolása a `/example/data` és `/HdiSamples` könyvtárak. Ezek a könyvtárak a a fürt alapértelmezett tárolója találhatók. Az ebben a dokumentumban a Pig példa a *log4j* fájlt `/example/data/sample.log`.

Minden napló a fájl tartalmaz egy sort, amely tartalmazza a mezők egy `[LOG LEVEL]` mezők típusát és súlyosságát, például megjelenítéséhez:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Az előző példában a naplózási szint: Hiba történt.

> [!NOTE]
> A log4j fájl használatával is létrehozhat a [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) naplózása eszközt, és majd töltse fel ezt a fájlt a blobba. Lásd: [töltse fel az adatokat HDInsight](../hdinsight-upload-data.md) útmutatást. Az Azure storage-blobok használata a HDInsight kapcsolatos további információkért lásd: [az Azure Blob Storage a HDInsight](../hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Példa feladat

A következő Pig Latin-feladat tölti be a `sample.log` fájl az alapértelmezett Storage a HDInsight-fürt számára. Majd elvégez egy sorozatát átalakítások használhatók, amelyek az egyes naplózási szint a bemeneti adatokat a következő hány alkalommal-számot eredményez. Az eredmények STDOUT kerüljenek.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

Az alábbi képen az egyes átalakítási célja az adatok összegzését jeleníti meg.

![Az átalakítások grafikus ábrázolása][image-hdi-pig-data-transformation]

## <a id="run"></a>A Pig Latin-feladat futtatása

HDInsight Pig Latin-feladatok futtatásához számos módszer használatával. A következő táblázat segítségével döntse el, melyik módszer a legmegfelelőbb Önnek, majd kövesse a hivatkozást bemutató.

| **Ezzel** Ha azt szeretné... | ...an **interaktív** rendszerhéj | ...**kötegelt** feldolgozása | .. során ez **fürt operációs rendszerének** | ...from ez **ügyfél** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X vagy Windows |
| [A curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux vagy Windows |Linux, Unix, Mac OS X vagy Windows |
| [.NET SDK a Hadoophoz](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux vagy Windows |Windows (egyelőre) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux vagy Windows |Windows |

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="pig-and-sql-server-integration-services"></a>A Pig és az SQL Server Integration Services

Az SQL Server Integration Services (SSIS) használatával egy Pig feladatot futtatni. Az Azure Feature Pack for SSIS biztosít a következő összetevőket, HDInsight Pig-feladatok dolgozhat.

* [Az Azure HDInsight Pig-tevékenység][pigtask]

* [Az Azure előfizetés Csatlakozáskezelő][connectionmanager]

További információ az Azure Feature Pack for SSIS [Itt][ssispack].

## <a id="nextsteps"></a>Következő lépések
Most, hogy megtanulhatta, hogyan lehet a Pig használata a HDInsight, egyéb módon az Azure HDInsight használata az alábbi hivatkozások segítségével.

* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md)
* [A Hive használata a HDInsightban][hdinsight-use-hive]
* [A Sqoop használata a HDInsight](hdinsight-use-sqoop.md)
* [Az Oozie használata a HDInsight](../hdinsight-use-oozie.md)
* [HDInsight MapReduce-feladatok használata][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
