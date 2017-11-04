---
title: "PowerShell - Azure Mahout HDInsight segítségével javaslatok generálása |} Microsoft Docs"
description: "Útmutató az Apache Mahout machine learning-könyvtárral segítségével és a HDInsight (Hadoop) együttes movie javaslatok generálása az ügyfélszámítógépen futó PowerShell parancsfájl."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: larryfr
ms.openlocfilehash: 8f40beb4e3eb8020a65eac6e43ffefe4fedfcee2
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>Film javaslatok generálása Apache Mahout a hadooppal a Hdinsightban (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Ismerje meg, hogyan használható a [Apache Mahout](http://mahout.apache.org) machine learning függvénytár, amely Azure HDInsight movie javaslatok létrehozásához. A példa az ebben a dokumentumban szereplő Azure PowerShell Mahout feladatok futtatásához.

## <a name="prerequisites"></a>Előfeltételek

* A Linux-alapú HDInsight-fürtöt. Egy létrehozásával kapcsolatos további információkért lásd: [hdinsight Linux-alapú Hadoop használatának megkezdésében][getstarted].

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Javaslatok generálása Azure PowerShell használatával

> [!WARNING]
> Ebben a szakaszban a feladat használható Azure PowerShell használatával. A Mahout megadott osztályok többsége nem jelenleg Azure PowerShell-lel dolgozni. Nem működnek az Azure PowerShell osztályok listáját lásd: a [hibaelhárítás](#troubleshooting) szakasz.
>
> Például az SSH használatával csatlakozhat a HDInsight és futtatási Mahout példák közvetlenül a fürtön, [Mahout és a HDInsight-(SSH) használatával movie javaslatok generálása](hadoop/apache-hadoop-mahout-linux-mac.md).

A funkciók Mahout által biztosított egyike egy javaslat motor. Ez a motor elfogadja a kell adatokat `userID`, `itemId`, és `prefValue` (a felhasználók beállítások elem). Mahout hasonló-cikk beállítások, amely ajánlásokat rendelkező felhasználók meghatározására használja az adatok.

A következő példa egy egyszerűsített segédlet az ajánlás folyamat működéséről:

* **közös előfordulási**: Joe, Ágnes és minden tetszését Bob *csillag ütközések*, *vissza sztrájkok a Empire*, és *a Jedi visszaküldése*. Mahout határozza meg, hogy a felhasználók számára is, például a filmek egyikét sem például a másik kettőt.

* **közös előfordulási**: Bálint és Alice is tetszését *a látszólagos támadása*, *támadás a klónja*, és *megtorlás a Sith a*. Mahout határozza meg, hogy felhasználók, akik az előző három filmek is tetszett hasonlóan ezek filmek.

* **Hasonlóság ajánlás**: mivel Joe tetszését az első három filmek, Mahout ellenőrzi, hogy az filmek, hogy más, hasonló beállítások tetszett, de Joe rendelkezik nem figyelt (tetszését/névleges). Ebben az esetben Mahout javasolja *a látszólagos támadása*, *támadás a klónja*, és *a Sith a megtorlás*.

### <a name="understanding-the-data"></a>Az adatok ismertetése

[GroupLens kutatási] [ movielens] minősítés adatokat biztosít a filmek formátuma nem kompatibilis a Mahout. Ezek az adatok érhető el az alapértelmezett tároló, a fürt `/HdiSamples/HdiSamples/MahoutMovieData`.

Két fájlt `moviedb.txt` (a filmek információ) és `user-ratings.txt`. A `user-ratings.txt` fájllal elemzése során. A `moviedb.txt` fájllal meg felhasználóbarát szöveges, amikor a vizsgálat eredményeit jeleníti meg.

A felhasználó-ratings.txt szereplő adatok struktúrája `userID`, `movieID`, `userRating`, és `timestamp`, amely azt ismerteti, hogyan magas minden felhasználó besorolású film. Íme egy példa:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>A feladat futtatása

A következő Windows PowerShell-parancsfájl használatával futtatni egy feladatot, amely a Mahout javaslat motort használja, a movie adatokkal:

> [!NOTE]
> Ez a fájl információkat kér, amellyel csatlakozhat a HDInsight-fürthöz, és feladatok futtatása. A feladat befejeződését, majd töltse le a kimenet.txt több percig is eltarthat.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]
> Mahout feladatok ne távolítsa el a feldolgozás során létrehozott ideiglenes adatok. A `--tempDir` paraméter van megadva a példa feladat különítheti el az ideiglenes fájlok egy adott könyvtárba.

A Mahout feladat nem áll vissza a kimeneti STDOUT. Ehelyett akkor is tartalmaz, mint a megadott kimeneti könyvtár **rész-r-00000**. A parancsfájl letölti a fájlt a **kimenet.txt** az aktuális könyvtárban található a munkaállomáson.

A következő szöveget a fájl tartalma példája:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

Az első oszlop a `userID`. Szereplő értékeket ' ["és"] "vannak `movieId`:`recommendationScore`.

A parancsfájl is letölti a `moviedb.txt` és `user-ratings.txt` fájlokat, amelyek szükségesek ahhoz, hogy a kimenet, hogy jobban olvasható formátumban.

### <a name="view-the-output"></a>A kimeneti megtekintése

A létrehozott kimeneti OK lehet az alkalmazásban használható, de nincs felhasználóbarát. A `moviedb.txt` a kiszolgálóról segítségével hárítsa el a `movieId` movie névre. A következő PowerShell-parancsfájl használatával megjelenítheti a movie nevű ajánlásokat:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

A következő paranccsal egy felhasználóbarát formátumban a javaslatok megjelenítéséhez: 

```powershell
.\show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt
```

A kimenet az alábbi szöveghez hasonló:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="cannot-overwrite-files"></a>Fájlok nem írható felül.

Mahout feladatok adatformátuma nem karbantartása feldolgozás során létrehozott ideiglenes fájlokat. Emellett a feladatok fájl felülírásának mellőzése meglévő kimeneti.

Futó feladatok Mahout hibák elkerülése törli az ideiglenes és a kimeneti fájlok közötti futtatása. A jelen dokumentum korábbi parancsfájlok által létrehozott fájlok eltávolításához használja a következő PowerShell-parancsfájlt:

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard,
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Olyan osztállyal, amelynek nem működnek az Azure PowerShell

A következő osztályokat használó mahout feladatok vissza különböző hibaüzenetek a Windows PowerShell használata esetén:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Ezeket az osztályokat használó feladatok futtatásához, a HDInsight-fürthöz SSH használatával csatlakozhat, és a feladatok futtatása a parancssorból. Az SSH használatával Mahout feladatok futtatásához példáért lásd: [Mahout és a HDInsight-(SSH) használatával movie javaslatok generálása](hadoop/apache-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Következő lépések

Most, hogy rendelkezik megismerte Mahout használata, felderítése egyéb módjait a HDInsight-adatokkal végzett munka:

* [A HDInsight Hive](hadoop/hdinsight-use-hive.md)
* [Pig with HDInsight](hadoop/hdinsight-use-pig.md)
* [MapReduce a hdinsight eszközzel](hadoop/hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
