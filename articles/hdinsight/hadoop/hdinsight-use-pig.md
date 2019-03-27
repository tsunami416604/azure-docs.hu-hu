---
title: Apache Pig - Azure HDInsight használata
description: Ismerje meg, hogyan lehet a Pig használata a HDInsight az Apache Hadoop.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.openlocfilehash: 2b0f3a4394267a808b6e6e0a73abfaf1e698ff1c
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445025"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel

Ismerje meg, hogyan használható [Apache Pig](https://pig.apache.org/) a HDInsight.

Az Apache Pig az Apache Hadoop néven eljárási nyelv használatával programok hoz platform *Pig Latin*. A Pig alternatívája a Java létrehozásához *MapReduce* megoldásokat, és az Azure HDInsight része. Az alábbi táblázat segítségével felderítése a különféle módokon, amely a Pig-alapú HDInsight használható:

| **Ezzel** Ha azt szeretné... | ...an **interaktív** rendszerhéj | ...**kötegelt** feldolgozása | .. során ez **fürt operációs rendszerének** | ...from ez **ügyfél operációs rendszer** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X vagy Windows |
| [REST API](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux vagy Windows |Linux, Unix, Mac OS X vagy Windows |
| [.NET SDK a Hadoophoz](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux vagy Windows |Windows (egyelőre) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux vagy Windows |Windows |

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="why"></a>Miért érdemes használni az Apache Pig

A feldolgozó logika csak egy térképet, és csökkentse a függvény használatával megvalósítása a legnagyobb kihívás az adatok feldolgozási MapReduce a Hadoop használatával. Összetett feldolgozást, gyakran kell a feldolgozása felosztása több MapReduce művelet kapcsolt együtt, hogy a kívánt eredmények eléréséhez.

A Pig lehetővé teszi az adatok előállításához a kívánt kimeneti áthaladó adatátvitelen átalakítások sorozataként feldolgozási.

A Pig Latin nyelv lehetővé teszi a nyers adatok bevitele, egy vagy több átalakítások, a kívánt kimeneti előállításához keresztül érkező adatfolyam ismertetik. A Pig Latin programok az általános mintát követik:

* **Betöltés**: Olvassa el az adatok kezelhetők a fájlrendszerből.

* **Átalakítás**: Módosíthatja az adatokat.

* **Memóriakép és nem tárolnak**: A kimeneti adatoknak a képernyőre, vagy a feldolgozáshoz tárolja.

### <a name="user-defined-functions"></a>Felhasználó által definiált függvények

A Pig Latin is támogatja felhasználó által definiált függvények (UDF), amely lehetővé teszi, hogy a külső összetevők, amelyek logikákat implementálhat, amely a Pig Latin modell nehéz meghívása.

A Pig Latin kapcsolatos további információkért lásd: [Pig Latin referencia manuális 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) és [Pig Latin referencia manuális 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

UDF-EK használatával és a Pig együttes példát a következő dokumentumokban talál:

* [Az Apache datafu és a HDInsight az Apache Pig együttes](apache-hadoop-use-pig-datafu-udf.md) -DataFu hasznos UDF-EK Apache által karbantartott gyűjteménye
* [Az Apache Pig- és Apache Hive a HDInsight a Python használata](python-udf-hdinsight.md)
* [Használat C# az Apache Hive és a HDInsight Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Példa adatok

HDInsight biztosít különböző példa adatkészletek, amelyek tárolása a `/example/data` és `/HdiSamples` könyvtárak. Ezek a könyvtárak a a fürt alapértelmezett tárolója találhatók. Az ebben a dokumentumban a Pig példa a *log4j* fájlt `/example/data/sample.log`.

Minden napló a fájl tartalmaz egy sort, amely tartalmazza a mezők egy `[LOG LEVEL]` mezők típusát és súlyosságát, például megjelenítéséhez:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Az előző példában a naplózási szint: Hiba történt.

> [!NOTE]  
> A log4j fájl használatával is létrehozhat a [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) naplózása eszközt, és majd töltse fel ezt a fájlt a blobba. Lásd: [töltse fel az adatokat HDInsight](../hdinsight-upload-data.md) útmutatást. Az Azure storage-blobok használata a HDInsight kapcsolatos további információkért lásd: [az Azure Blob Storage a HDInsight](../hdinsight-hadoop-use-blob-storage.md).

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

* [Azure Subscription Connection Manager][connectionmanager]

További információ az Azure Feature Pack for SSIS [Itt][ssispack].

## <a id="nextsteps"></a>Következő lépések
Most, hogy megtanulhatta, hogyan lehet a Pig használata a HDInsight, egyéb módon az Azure HDInsight használata az alábbi hivatkozások segítségével.

* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md)
* [Az Apache Hive használata a HDInsight][hdinsight-use-hive]
* [A HDInsight Apache Sqoop használata](hdinsight-use-sqoop.md)
* [Az Apache Oozie használata a HDInsight](../hdinsight-use-oozie-linux-mac.md)
* [HDInsight MapReduce-feladatok használata][hdinsight-use-mapreduce]

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: https://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
