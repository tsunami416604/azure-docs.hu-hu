---
title: Az Apache Pig használata
titleSuffix: Azure HDInsight
description: Ismerje meg, hogyan használhatja a Pig-t az Apache Hadoop-mal a HDInsight-on.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: ea960a92aee1c9447bb12d27cffdc42de9fd907a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672123"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Az Apache Pig használata az Apache Hadoop segítségével a HDInsighton

További információ az [Apache Pig](https://pig.apache.org/) hdinsight-val való használatáról.

Az Apache Pig egy platform az Apache Hadoop programjainak létrehozására a *Pig Latin*néven ismert eljárási nyelv használatával. A Pig a Java alternatívája a *MapReduce* megoldások létrehozásához, és az Azure HDInsight része. Az alábbi táblázat segítségével megismerheti, hogy a Pig milyen különböző módokon használható a HDInsight segítségével:

## <a name="why-use-apache-pig"></a><a id="why"></a>Miért érdemes használni az Apache Pig-t?

Az adatok feldolgozásának egyik kihívása a MapReduce használatával a Hadoop-ban a feldolgozási logika megvalósítása csak egy térkép és egy csökkentési funkció használatával. Összetett feldolgozáshoz gyakran kell a feldolgozást több MapReduce műveletre bontani, amelyek össze vannak láncolva a kívánt eredmény elérése érdekében.

A Pig lehetővé teszi a feldolgozásdefiniálást olyan átalakítások sorozataként, amelyeken az adatok a kívánt kimenet létrehozásához folynak.

A Pig Latin nyelv lehetővé teszi, hogy leírja az adatfolyamot a nyers bemenettől egy vagy több átalakításon keresztül, hogy a kívánt kimenetet hozza létre. Pig Latin programok követik ezt az általános mintát:

* **Betöltés**: A fájlrendszerből manipulálandó adatok olvasása.

* **Átalakítás**: Az adatok kezelése.

* **Dump vagy store**: Kimeneti adatokat a képernyőn, vagy tárolja feldolgozásra.

### <a name="user-defined-functions"></a>Felhasználó által meghatározott függvények

A Pig Latin támogatja a felhasználó által definiált függvényeket (UDF), amelyek lehetővé teszik olyan külső összetevők meghívását, amelyek pig latin nyelven nehezen modellező logikát valósítanak meg.

A Pig Latinról további információt a [Pig Latin Reference Manual 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) és a Pig Latin Reference Manual 2 [című](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html)kézikönyvben talál.

## <a name="example-data"></a><a id="data"></a>Példaadatok

A HDInsight különböző példaadatkészleteket biztosít, `/example/data` amelyek `/HdiSamples` a és a könyvtárakban tárolódnak. Ezek a könyvtárak a fürt alapértelmezett tárolójában vannak. A dokumentumban szereplő Pig példa a `/example/data/sample.log` *log4j* fájlt használja a programból.

A fájlon belüli minden napló egy mezősorból áll, amely egy mezőt `[LOG LEVEL]` tartalmaz a típus és a súlyosság megjelenítéséhez, például:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Az előző példában a napló szintje HIBA.

> [!NOTE]  
> A log4j fájlt az Apache [Log4j](https://en.wikipedia.org/wiki/Log4j) naplózási eszközzel is létrehozhatja, majd feltöltheti a fájlt a blobba. Az utasításokat az [Adatok feltöltése a HDInsightba témakörben](hdinsight-upload-data.md) találja. Ha többet szeretne tudni arról, hogy az Azure Storage-ban lévő blobok hogyan használhatók a HDInsight segítségével, olvassa [el az Azure Blob Storage használata a HDInsight-mal című témakört.](hdinsight-hadoop-use-blob-storage.md)

## <a name="example-job"></a><a id="job"></a>Példa feladatra

A következő Pig Latin `sample.log` feladat betölti a fájlt a HDInsight-fürt alapértelmezett tárolójából. Ezután egy sor átalakítások, amelyek eredményeként a száma, hogy hányszor minden naplószint történt a bemeneti adatok. Az eredmények az STDOUT-ba vannak írva.

    ```
    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

Az alábbi képen az egyes átalakítások az adatokkal kapcsolatos összegzése látható.

![Az átalakítások grafikus ábrázolása][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>A Pig Latin feladat futtatása

A HDInsight számos módszerrel futtathatja a Pig Latin-feladatokat. Az alábbi táblázat segítségével eldöntheti, hogy melyik módszer felel meg Önnek, majd kövesse a forgatókönyvhivatkozást.

## <a name="pig-and-sql-server-integration-services"></a>Pig és SQL Server integrációs szolgáltatások

Az SQL Server Integration Services (SSIS) segítségével futtathat egy Pig-feladatot. Az Azure Feature Pack for SSIS a következő összetevőket biztosítja, amelyek a HDInsight Pig-feladataival működnek.

* [Azure HDInsight Pig feladat][pigtask]

* [Azure előfizetéses csatlakozáskezelő][connectionmanager]

Az SSIS-hez való Azure Feature Pack csomagról itt olvashat [bővebben.][ssispack]

## <a name="next-steps"></a><a id="nextsteps"></a>További lépések

Most, hogy megtanulta a Pig és a HDInsight használatát, az alábbi hivatkozások segítségével további módszereket fedezhet fel az Azure HDInsight használatával való együttműködésre.

* [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md)
* [Az Apache Hive használata a HDInsight segítségével](./hadoop/hdinsight-use-hive.md)
* [Az Apache Sqoop használata a HDInsight segítségével](hdinsight-use-sqoop.md)
* [MapReduce-feladatok használata a HDInsightban](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: https://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif
