---
title: "Mahout és-(SSH) HDInsight - Azure javaslatok generálása |} Microsoft Docs"
description: "Megtudhatja, hogyan használja az Apache Mahout machine learning-könyvtárral movie javaslatok és a HDInsight (Hadoop) együttes létrehozásához."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c78ec37c-9a8c-4bb6-9e38-0bdb9e89fbd7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: larryfr
ms.openlocfilehash: 28450d72f19a5467d88bc787d11f6c37c5afbf9a
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight-ssh"></a>Film javaslatok generálása Apache Mahout Linux-alapú hadooppal a HDInsight-(SSH) használatával

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Ismerje meg, hogyan használható a [Apache Mahout](http://mahout.apache.org) machine learning függvénytár, amely Azure HDInsight movie javaslatok létrehozásához.

Mahout van egy [gépi tanulás] [ ml] könyvtára Apache Hadoop. Mahout adatok, például a szűrést, besorolást, és a fürtszolgáltatás feldolgozására algoritmusok tartalmazza. Ebben a cikkben egy javaslat motor movie javaslatok az ismerősök láthatta filmek alapuló létrehozásához használt.

## <a name="prerequisites"></a>Előfeltételek

* A Linux-alapú HDInsight-fürtöt. Egy létrehozásával kapcsolatos további információkért lásd: [hdinsight Linux-alapú Hadoop használatának megkezdésében][getstarted].

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Egy SSH-ügyfél. További információ: [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="mahout-versioning"></a>Mahout versioning

A hdinsight Mahout verziójával kapcsolatos további információkért lásd: [HDInsight verziója és a Hadoop-összetevők](hdinsight-component-versioning.md).

## <a name="recommendations"></a>Understanding javaslatok

A funkciók Mahout által biztosított egyike egy javaslat motor. Ez a motor elfogadja a kell adatokat `userID`, `itemId`, és `prefValue` (a beállítások a cikkhez). Mahout végezhet el, közös elemként elemzés meghatározásához: *egy elem előnyben rendelkező felhasználók is hozzáférhetnek ezeket a beállításokat szabályozó egyéb elemek*. Mahout majd határozza meg a felhasználók hasonló-cikk beállítások, amely ajánlásokat is használható.

Az alábbi munkafolyamat egy egyszerűsített példa movie adatait használó:

* **Közös elemként**: Joe, Ágnes és minden tetszését Bob *csillag ütközések*, *vissza sztrájkok a Empire*, és *a Jedi visszaküldése*. Mahout határozza meg, hogy a felhasználók számára is, például a filmek egyikét sem például a másik kettőt.

* **Közös elemként**: Bálint és Alice is tetszését *a látszólagos támadása*, *támadás a klónja*, és *a Sith a megtorlás*. Mahout határozza meg, hogy felhasználók, akik az előző három filmek is tetszését hasonlóan ezen három filmek.

* **Hasonlóság ajánlás**: mivel Joe tetszését az első három filmek, Mahout ellenőrzi, hogy az filmek, hogy más, hasonló beállítások tetszett, de Joe rendelkezik nem figyelt (tetszését/névleges). Ebben az esetben Mahout javasolja *a látszólagos támadása*, *támadás a klónja*, és *a Sith a megtorlás*.

### <a name="understanding-the-data"></a>Az adatok ismertetése

Kényelmesen [GroupLens kutatási] [ movielens] minősítés adatokat biztosít a filmek formátuma nem kompatibilis a Mahout. Ezek az adatok érhető el a fürt alapértelmezett tárolás `/HdiSamples/HdiSamples/MahoutMovieData`.

Két fájlt `moviedb.txt` és `user-ratings.txt`. A felhasználó-ratings.txt fájllal elemzés, míg az moviedb.txt felhasználóbarát szöveg információk megadására, amikor a vizsgálat eredményeit jeleníti meg.

A felhasználó-ratings.txt szereplő adatok struktúrája `userID`, `movieID`, `userRating`, és `timestamp`, amely közli a gép magas hogyan minden felhasználó besorolású film. Íme egy példa:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Az elemzés futtatása

Az SSH-kapcsolatot a fürt alkalmazás a javaslat feladat futtatásához a következő parancsot:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]
> A feladat is igénybe vehet néhány percet, és előfordulhat, hogy több MapReduce-feladatok futtatásához.

## <a name="view-the-output"></a>A kimeneti megtekintése

1. Ha a feladat befejeződik, a következő paranccsal létrehozott eredményének megtekintéséhez:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    A kimenet a következőképpen jelenik meg:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    Az első oszlop a `userID`. Szereplő értékeket ' ["és"] "vannak `movieId`:`recommendationScore`.

2. A kimeneti együtt a moviedb.txt segítségével nyújtanak további információt a ajánlása. Először igazolnia kell átmásolni a fájlokat helyileg az alábbi parancsokkal:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Ez a parancs nevű fájlt másolja a kimeneti adatokat **recommendations.txt** az aktuális könyvtárban található, a movie adatfájlok együtt.

3. Az alábbi parancs segítségével hozzon létre egy Python-parancsfájl, amely megkeresi a javaslatok kimenet movie neve:

    ```bash
    nano show_recommendations.py
    ```

    A szerkesztő megnyitása után használata a fájl tartalmát a következő szöveget:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Nyomja le az **Ctrl-X**, **Y**, és végül **Enter** az adatok mentése.

4. A Python-parancsfájl futtatása. Az alábbi parancs feltételezi, hogy a könyvtárban, ahol az összes fájl letöltése:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Ez a parancs ellenőrzi, hogy az a felhasználói azonosító 4 előállított javaslatok.

    * A **felhasználói-ratings.txt** fájl értékelése filmek beolvasásához használt.

    * A **moviedb.txt** fájl segítségével olvassa be a filmek.

    * A **recommendations.txt** a felhasználó movie ajánlások beolvasásához használt.

     A parancs kimenete az alábbihoz hasonló:

        Tibet (1997), a hét éven pontozása = 5.0 pontozása Indiana János és az utolsó Crusade (1989), = 5.0 Jaws (1975) pontozása = 5.0 logika és Sensibility (1995) pontszám = 5.0 függetlenség napja (ID4) (1996) pontszám = 5.0 a legjobb barátjának Esküvői (1997), pontozása = 5.0 Jerry Maguire (1996) pontszám = 5.0 Scream 2 (1997) pontszám = 5.0 pontozása Kill, az idő A (1996), = 5.0

## <a name="delete-temporary-data"></a>Ideiglenes adatok törlése

Mahout feladatok ne távolítsa el a feldolgozás során létrehozott ideiglenes adatok. A `--tempDir` paraméter van megadva a példa feladat különítheti el az ideiglenes fájlokat egyszerűen a törlésre megadott elérési útra. Az ideiglenes fájlok eltávolításához használja a következő parancsot:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]
> Ha azt szeretné, hogy futtassa újra a parancsot, a kimeneti könyvtár is törölnie kell. A címtár törléséhez használja a következőket:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Következő lépések

Most, hogy rendelkezik megismerte Mahout használata, felderítése egyéb módjait a HDInsight-adatokkal végzett munka:

* [A HDInsight Hive](hdinsight-use-hive.md)
* [Pig with HDInsight](hdinsight-use-pig.md)
* [MapReduce a hdinsight eszközzel](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
