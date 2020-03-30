---
title: Javaslatok létrehozása az Apache Mahout használatával az Azure HDInsightban
description: Ismerje meg, hogyan használhatja az Apache Mahout gépi tanulási könyvtárat a HDInsight (Hadoop) segítségével a filmjavaslatok létrehozásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/03/2020
ms.openlocfilehash: 33110e9f1d45fcd11e5f4cad1b589ab929a9472d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767636"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>Filmjavaslatok készítése az Apache Mahout használatával az Apache Hadoop segítségével a HDInsightban (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Ismerje meg, hogyan használhatja az [Apache Mahout](https://mahout.apache.org) gépi tanulási könyvtárát az Azure HDInsight-mal filmjavaslatok létrehozásához.

A Mahout az Apache Hadoop [gépi tanulási](https://en.wikipedia.org/wiki/Machine_learning) könyvtára. A Mahout algoritmusokat tartalmaz az adatok feldolgozásához, például a szűréshez, a besoroláshoz és a fürtözéshez. Ebben a cikkben egy ajánlási motor segítségével generálhat olyan filmjavaslatokat, amelyek az ismerőseid által látott filmeken alapulnak.

## <a name="prerequisites"></a>Előfeltételek

Apache Hadoop-fürt a HDInsighton. Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](./apache-hadoop-linux-tutorial-get-started.md)

## <a name="apache-mahout-versioning"></a>Apache Mahout verziószámozás

A Mahout HDInsight-verziójáról a [HDInsight-verziók és az Apache Hadoop-összetevők](../hdinsight-component-versioning.md)című témakörben talál további információt.

## <a name="understanding-recommendations"></a>Az ajánlások ismertetése

A Mahout által biztosított egyik funkció egy ajánlási motor. Ez a motor a , `userID` `itemId`és `prefValue` (az elem előnyben részesítése) formátumú adatokat fogad el. A Mahout ezután közös előfordulási elemzést végezhet annak meghatározására, hogy azok *a felhasználók, akik előnyben részesítik az elemet, szintén előnyben részesítik ezeket az elemeket.* A Mahout ezután meghatározza a hasonló elem beállításaival rendelkező felhasználókat, amelyek javaslatok at tehetnek.

A következő munkafolyamat egy egyszerűsített példa, amely filmadatokat használ:

* **Együtt előfordulása:** Joe, Alice, és Bob minden tetszett *Star Wars,* *A Birodalom visszavág*, és a Return of *the Jedi*. Mahout határozza meg, hogy a felhasználók, akik szeretik az egyik ilyen filmek is, mint a másik kettő.

* **Együtt előfordulása**: Bob és Alice is tetszett *a Phantom Menace*, Attack of the *Clones*, és *a Revenge of the Sith*. Mahout határozza meg, hogy a felhasználók, akik szerették az előző három film is, mint ez a három film.

* **Hasonlóság ajánlás:** Mivel Joe tetszett az első három film, Mahout néz filmeket, hogy mások hasonló preferenciák tetszett, de Joe nem nézte meg (tetszett / értékelt). Ebben az esetben Mahout ajánlja: *The Phantom Menace*, Attack of the *Clones*és *Revenge of the Sith*.

### <a name="understanding-the-data"></a>Az adatok ismertetése

A [GroupLens Research](https://grouplens.org/datasets/movielens/) kényelmesen biztosít minősítési adatokat a filmekhez a Mahout-tal kompatibilis formátumban. Ezek az adatok a fürt alapértelmezett `/HdiSamples/HdiSamples/MahoutMovieData`tárolóján érhetők el a.-on.

Két fájl `moviedb.txt` van, `user-ratings.txt`és . A `user-ratings.txt` fájl az elemzés során használatos. `moviedb.txt` Az segítségével felhasználóbarát szöveges információkat adhat meg az eredmények megtekintésekor.

`user-ratings.txt` A benne lévő adatok `userID`szerkezete `userRating`, `timestamp`, `movieID`és , amely azt jelzi, hogy az egyes felhasználók milyen magasra értékelték a mozgóképet. Íme egy példa az adatokra:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Az elemzés futtatása

1. Az [ssh paranccsal](../hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A javaslati feladat futtatásához használja a következő parancsot:

    ```bash
    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
    ```

> [!NOTE]  
> A feladat több percet is igénybe vehet, és több MapReduce feladatot is futtathat.

## <a name="view-the-output"></a>A kimenet megtekintése

1. A feladat befejezése után a következő paranccsal tekintse meg a létrehozott kimenetet:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    A kimenet a következőképpen jelenik meg:

    ```output
    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
    ```

    Az első oszlop `userID`a . A "[" és "]" `movieId`értékek`recommendationScore`a következők: .

2. Használhatja a kimenetet, valamint a moviedb.txt, hogy további információt a javaslatokat. Először másolja a fájlokat helyileg a következő parancsokkal:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Ez a parancs a kimeneti adatokat egy **recommendations.txt** nevű fájlba másolja az aktuális könyvtárba, a mozgókép-adatfájlokkal együtt.

3. A következő paranccsal hozzon létre egy Python-parancsfájlt, amely megkeresi a javaslatok kimenetében szereplő adatok filmneveit:

    ```bash
    nano show_recommendations.py
    ```

    Amikor a szerkesztő megnyílik, a fájl tartalmaként a következő szöveget használja:

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

    Az adatok mentéséhez nyomja le a **Ctrl-X**, **Y**billentyűkombinációt, és végül az **Enter billentyűt.**

4. Futtassa a Python-szkriptet. A következő parancs feltételezi, hogy abban a könyvtárban van, ahol az összes fájlt letöltik:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Ez a parancs a 4-es felhasználói azonosítóhoz létrehozott javaslatokat vizsgálja.

   * A **user-ratings.txt** fájl a minősített filmek lekéréséhez használatos.

   * A **moviedb.txt** fájl segítségével letölteni a nevét a filmeket.

   * A **recommendations.txt** fájl a felhasználó filmjavaslatainak lekéréséhez használatos.

     A parancs kimenete hasonló a következő szöveghez:

        ```output
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        ```

## <a name="delete-temporary-data"></a>Ideiglenes adatok törlése

A Mahout-feladatok nem távolítják el a feladat feldolgozása során létrehozott ideiglenes adatokat. A `--tempDir` paraméter a példafeladatban van megadva, hogy az ideiglenes fájlokat egy adott elérési útba különítse el a könnyebb törlés érdekében. Az ideiglenes fájlok eltávolításához használja a következő parancsot:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Ha újra szeretné futtatni a parancsot, törölnie kell a kimeneti könyvtárat is. A könyvtár törléséhez használja az alábbiakat:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta a Mahout használatát, fedezze fel a HDInsight-adatokkal való munka más módjait:

* [Apache Hive HDInsightdal](hdinsight-use-hive.md)
* [MapReduce a HDInsight segítségével](hdinsight-use-mapreduce.md)
