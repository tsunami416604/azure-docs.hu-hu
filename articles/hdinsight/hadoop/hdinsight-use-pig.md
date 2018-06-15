---
title: Hadoop Pig használata a Hdinsightban |} Microsoft Docs
description: Útmutató a Pig használata a HDInsight Hadoop.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: acfeb52b-4b81-4a7d-af77-3e9908407404
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: d382293ce815f24ad635b50c920d7988b664d79c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32169608"
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>A Pig használata a HDInsight Hadoop

Ismerje meg, hogyan használható [Apache Pig](http://pig.apache.org/) a hdinsight eszközzel.

A Pig egy platform programok létrehozásához a Hadoop által ismert eljárási nyelv használatával *Pig Latin*. A Pig Java helyett létrehozásához *MapReduce* megoldások, és az Azure HDInsight része. Az alábbi táblázat segítségével felderítése a különböző módszereket, amelyek sertésfelmérés HDInsight használható:

| **Ezzel** Ha azt szeretné... | ...an **interaktív** rendszerhéj | ...**kötegelt** feldolgozása | és mivel ez **fürt operációs rendszer** | ...from ez **ügyfél operációs rendszer** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X vagy Windows |
| [REST API](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux- vagy Windows |Linux, Unix, Mac OS X vagy Windows |
| [.NET SDK a Hadoophoz](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux- vagy Windows |(Egyelőre) Windows |
| [A Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux- vagy Windows |Windows |

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="why"></a>Miért érdemes használni a Pig

A feldolgozó logika csak térkép és a reduce függvény használatával végrehajtó egyik kihívás az adatok feldolgozása a MapReduce a Hadoop használatával. Összetett feldolgozásra, gyakran kell feldolgozási felosztása több MapReduce művelet kapcsolt együtt a kívánt eredmény elérése érdekében.

A Pig megadhatja feldolgozási átalakítások, amely az adatok előállításához a kívánt kimeneti áthaladó sorozataként.

A Pig Latin nyelvi ismertetik az adatfolyam nyers bemeneti, egy vagy több átalakítások, a kívánt kimeneti létrehozásához keresztül teszi lehetővé. A Pig Latin programok kövesse az ebben a mintában általános:

* **Betöltési**: kezelhetők a fájlrendszerből adatokat olvasni.

* **Átalakítás**: az adatok kezelését

* **Memóriakép, vagy tárolja**: kimeneti adatai a képernyőre, vagy tárolja el azt a feldolgozás

### <a name="user-defined-functions"></a>Felhasználó által definiált függvények

Pig Latin is támogatja a felhasználói függvény (UDF), így külső összetevők, amelyek megvalósítják az logika, amely a Pig Latin modell nehéz meghívni.

A Pig Latin kapcsolatos további információkért lásd: [Pig Latin hivatkozás manuális 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) és [Pig Latin hivatkozás manuális 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Például egy felhasználó által megadott függvények használata a Pig lásd a következő dokumentumokat:

* [DataFu használata a Hdinsightban Pig](apache-hadoop-use-pig-datafu-udf.md) -DataFu hasznos felhasználó által megadott függvények Apache által fenntartott gyűjteménye
* [Python az Pig és a Hive HDInsight használata](python-udf-hdinsight.md)
* [C# az Hive és a hdinsight a Pig használata](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Példa adatok

A HDInsight lehetővé különböző példa adatkészletek, amelyek tárolása a `/example/data` és `/HdiSamples` könyvtárak. Ezeket a könyvtárakat az alapértelmezett tároló, a fürt szerepelnek. Az ebben a dokumentumban a Pig példa a *log4j* fájlként `/example/data/sample.log`.

Egyes naplókon belül a fájl egy mezőket tartalmazó sor tartalmaz egy `[LOG LEVEL]` mező típusát és súlyosságát, például:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Az előző példában a naplózási szint: Hiba történt.

> [!NOTE]
> A log4j fájl használatával is létrehozhat a [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) eszköz naplózás, és majd, hogy a fájl feltöltése a blob. Lásd: [adatok feltöltése a HDInsight](../hdinsight-upload-data.md) utasításokat. Az Azure storage blobs használata a hdinsight eszközzel kapcsolatos további információkért lásd: [az Azure Blob Storage a hdinsight eszközzel](../hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Példa feladat

A következő Pig Latin feladat betölti a `sample.log` fájl az alapértelmezett tárolóból a HDInsight-fürthöz. Ez hajtja végre, amelyek az egyes naplózási szint történt a bemeneti adatok hány alkalommal számát átalakítások sorozata. Az eredmények STDOUT kerülnek.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

Az alábbi képen minden átalakítása funkciója az adatok összegzését jeleníti meg.

![Az átalakítás grafikus ábrázolása][image-hdi-pig-data-transformation]

## <a id="run"></a>A Pig Latin feladat futtatása

A Pig Latin feladatok futtatásával a HDInsight többféle módszerrel. A következő táblázat segítségével döntse el, melyik módszert részesíti az Ön számára legmegfelelőbb, majd kövesse a hivatkozást útmutatást.

| **Ezzel** Ha azt szeretné... | ...an **interaktív** rendszerhéj | ...**kötegelt** feldolgozása | és mivel ez **fürt operációs rendszer** | ...from ez **ügyfél** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X vagy Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux- vagy Windows |Linux, Unix, Mac OS X vagy Windows |
| [.NET SDK a Hadoophoz](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux- vagy Windows |(Egyelőre) Windows |
| [A Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux- vagy Windows |Windows |

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="pig-and-sql-server-integration-services"></a>A Pig és az SQL Server Integration Services

SQL Server Integration Services (SSIS) segítségével futtassa a Pig feladatot. Az Azure funkciócsomag SSIS biztosít a következő összetevőket, amelyek használhatók a Pig-feladatokhoz a HDInsight-on.

* [Az Azure HDInsight a Pig feladatot][pigtask]

* [Az Azure előfizetés Csatlakozáskezelő][connectionmanager]

További információk az Azure funkciócsomag SSIS [Itt][ssispack].

## <a id="nextsteps"></a>Következő lépések
Most, hogy megismerte a Pig használata a hdinsight eszközzel rendelkezik, az alábbi hivatkozások segítségével más módjai Azure HDInsight használata.

* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md)
* [A Hive használata a HDInsightban][hdinsight-use-hive]
* [Use Sqoop with HDInsight](hdinsight-use-sqoop.md)
* [Oozie használata a hdinsight eszközzel](../hdinsight-use-oozie.md)
* [MapReduce-feladatok használata a hdinsight eszközzel][hdinsight-use-mapreduce]

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
