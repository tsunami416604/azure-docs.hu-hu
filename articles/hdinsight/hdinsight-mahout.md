---
title: PowerShell – Azure Mahout HDInsight használatával javaslatok létrehozása
description: Ismerje meg, hogyan filmajánlók a HDInsight (Hadoop) az Apache Mahout machine learning-kódtár használatával egy PowerShell-parancsfájlt az ügyfélen futó a.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 06181eaf4a44a00ddeeedcd9c40edeae9157abd9
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438548"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-apache-hadoop-in-hdinsight-powershell"></a>Filmajánlók Apache Mahout az Apache hadooppal a HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Ismerje meg, hogyan használható a [Apache Mahout](http://mahout.apache.org) gépi tanulási kódtár, az Azure HDInsight a filmajánlók. A példában a jelen dokumentum létrehozása a Mahout feladatok futtatása az Azure Powershellt.

## <a name="prerequisites"></a>Előfeltételek

* Egy Linux-alapú HDInsight-fürt. Az egyik létrehozásával kapcsolatos információkért lásd: [HDInsight Linux-alapú Hadoop használatának első lépései][getstarted].

    > [!IMPORTANT]  
    > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Javaslatok létrehozása az Azure PowerShell-lel

> [!WARNING]  
> A feladat ebben a szakaszban az Azure PowerShell használatával működik. A mahouttal megadott osztályok többsége jelenleg nem működik az Azure PowerShell-lel. Nem fog tudni az Azure PowerShell-lel osztályok listáját lásd: a [hibaelhárítás](#troubleshooting) szakaszban.
>
> Egy példa, SSH használatával csatlakozhat a HDInsight és a futtatási Mahout példák közvetlenül a fürtön: [Filmajánlók Apache Mahout és a HDInsight-(SSH) használatával](hadoop/apache-hadoop-mahout-linux-mac.md).

A Mahout által biztosított függvények egyikét egy ajánlattételi modul beépítését. Ez a motor az formátumban adatokat fogad `userID`, `itemId`, és `prefValue` (a felhasználók szabályozó az elem). Mahout hasonló-cikk beállítások, amely használható ajánlásokat rendelkező felhasználók meghatározására használja az adatok.

Az alábbi példában egy egyszerűsített segédlet a javaslat folyamat működésének:

* **közös előfordulás**: János Alice és az összes tetszett Bob *Star Wars*, *vissza is a Empire*, és *visszaküldése az ereje*. Mahout határozza meg, hogy felhasználók, akik ezek filmek közül bármelyik is, mint például a másik kettőt.

* **közös előfordulás**: Bob és Alice is tetszett *a látszólagos támadása*, *támadások, a klónok*, és *, a Sith megtorlás*. Mahout határozza meg, hogy az előző három filmek is kedvelő felhasználók például ezek filmek.

* **Hasonlósági javaslat**: János az első három filmek tetszett, mert Mahout megvizsgálja filmek, hogy mások hasonló beállítások tetszett, de János rendelkezik nem figyelt (tetszett/névleges). Ebben az esetben a Mahout javasolja *a látszólagos támadása*, *támadások, a klónok*, és *, a Sith megtorlás*.

### <a name="understanding-the-data"></a>Az adatok megismerése

[GroupLens kutatási] [ movielens] adja meg, amely kompatibilis a mahout használatával formátumú filmek minősítés adatait. Ezek az adatok érhető el az alapértelmezett tároló, a fürt `/HdiSamples/HdiSamples/MahoutMovieData`.

Két fájl `moviedb.txt` (a filmek információt) és `user-ratings.txt`. A `user-ratings.txt` fájl elemzése során használatos. A `moviedb.txt` fájllal felhasználóbarát szöveget adja meg, az elemzés eredményeinek pénznemformátumban való megjelenítéséhez.

A felhasználó-ratings.txt szereplő adatok struktúrája `userID`, `movieID`, `userRating`, és `timestamp`, amely arról tájékoztatja, hogy minden egyes felhasználói hogyan magas számoljuk el egy filmet. Íme egy példa az adatokat:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>A feladat futtatása

A következő Windows PowerShell-parancsfájl használatával, amely a Mahout ajánlásokat készítő motor használja a film adatait az feladat futtatása:

> [!NOTE]  
> Ez a fájl információkat kér, amellyel csatlakozhat a HDInsight-fürt és a feladatok futtatásához. A feladatok elvégzéséhez, töltse le a kimenet.txt több percig is eltarthat.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]  
> Feladatok létrehozása a mahout ne távolítsa el a feladat feldolgozása során létrehozott ideiglenes adatok. A `--tempDir` paraméter van megadva a példa feladat elkülöníteni az ideiglenes fájlok egy adott könyvtárba.

A Mahout feladat nem áll vissza a kimeneti STDOUT. Ehelyett azt tárolja, mint a megadott kimeneti könyvtár **. rész – az r-00000**. A szkript letölti a fájlt **kimenet.txt** munkaállomáson az aktuális könyvtárban található.

A következő szöveg Ez a fájl tartalmát egy példát:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

Az első oszlop a `userID`. Szereplő értékeket a(z) "[" és "]" vannak `movieId`:`recommendationScore`.

A parancsfájl is letölti a `moviedb.txt` és `user-ratings.txt` fájlokat, amelyek szükségesek a érdekében a kimenet formázása.

### <a name="view-the-output"></a>A kimenet megtekintéséhez

Bár a generált kimeneti OK lehet az alkalmazások használatra, akkor sem felhasználóbarát. A `moviedb.txt` a kiszolgálóról is használható feloldani a `movieId` egy filmet névre. A következő PowerShell-parancsfájl használatával movie nevekkel javaslatok megjelenítése:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

A következő paranccsal egy felhasználóbarát formában jeleníti meg a javaslatok: 

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

### <a name="cannot-overwrite-files"></a>Fájl nem írható felül.

Mahout feladatok akkor ne törölje feldolgozás során létrehozott ideiglenes fájlokat. Emellett a feladatok nem felülírja a meglévő kimeneti fájlt.

Mahout feladatok futtatásakor a hibák elkerülése törli az ideiglenes és a kimeneti fájlok futtatásai között. A jelen dokumentum korábbi parancsfájlok által létrehozott fájlok eltávolításához használja a következő PowerShell-parancsfájlt:

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzureRmAccount
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

### <a name="nopowershell"></a>Osztályokat, amelyek nem működnek az Azure PowerShell használatával

A következő osztályokat használó feladatok létrehozása a mahout különböző hibaüzeneteket, a Windows PowerShell használatakor adja vissza:

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

Ezeket az osztályokat használó feladatok futtatásához, a HDInsight-fürthöz SSH használatával csatlakozhat, és a feladatok futtatása a parancssorból. Az SSH-val Mahout feladatok futtatásához egy példa: [Filmajánlók Apache Mahout és a HDInsight-(SSH) használatával](hadoop/apache-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>További lépések

Most, hogy, hogyan használható az Apache Mahout hogyan, Fedezze fel az adatok használata a HDInsight egyéb módjait:

* [Az Apache Hive a HDInsight](hadoop/hdinsight-use-hive.md)
* [A HDInsight Apache Pig](hadoop/hdinsight-use-pig.md)
* [A MapReduce és a HDInsight](hadoop/hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: https://en.wikipedia.org/wiki/Machine_learning
[forest]: https://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
