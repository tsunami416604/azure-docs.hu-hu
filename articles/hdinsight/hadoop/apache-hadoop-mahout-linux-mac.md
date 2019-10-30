---
title: Javaslatok készítése az Apache Mahout használatával az Azure HDInsight
description: Megtudhatja, hogyan hozhatja ki a HDInsight (Hadoop) a filmkészítési javaslatokat az Apache Mahout Machine learning-kódtár használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 3923abd10fc3a64773d561b1f375f9e2f00a7e56
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044566"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>Filmkészítési javaslatok előállítása az Apache Mahout és az Apache Hadoop HDInsight (SSH) használatával

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Ismerje meg, hogyan hozhatja elő a filmkészítési javaslatokat az [Apache Mahout](https://mahout.apache.org) Machine learning-kódtár és az Azure HDInsight használatával.

A Mahout egy [gépi tanulási](https://en.wikipedia.org/wiki/Machine_learning) könyvtár a Apache Hadoop számára. A Mahout az adatfeldolgozáshoz szükséges algoritmusokat, például a szűrést, a besorolást és a fürtözést is tartalmazza. Ebben a cikkben egy ajánlási motort használ, amely a barátok által készített filmek alapján készít javaslatokat.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).

* Egy SSH-ügyfél. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Apache Mahout verziószámozás

A HDInsight-ben található Mahout verziójával kapcsolatos további információkért lásd: [HDInsight-verziók és Apache Hadoop-összetevők](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>A javaslatok ismertetése

A Mahout által biztosított függvények egyike egy ajánlási motor. Ez a motor `userID`, `itemId`és `prefValue` formátumban fogadja el az adatmennyiséget (az elemre vonatkozó beállítás). A Mahout Ezután elvégezheti a közös előfordulási elemzést annak megállapításához, hogy az adott *elemhez előnyben részesített felhasználók is rendelkeznek-e ezekkel a többi elemmel*. A Mahout ezután meghatározza a hasonló elemekkel rendelkező felhasználókat, amelyekkel ajánlásokat lehet tenni.

A következő munkafolyamat egy egyszerűsített példa, amely a Movie-adatbevitelt használja:

* **Közös előfordulás**: Joe, Alice és Bob minden tetszett *Star Wars*, *a birodalom*visszatér, és *visszaküldi a jedit*. A Mahout határozza meg, hogy a fenti filmek egyike, a másik kettőhöz hasonlóan a felhasználók is hasonlóak.

* **Együttes előfordulás**: Bob és Alice is tetszett *a Phantom árnyak*, *a klónok támadása*és *a Sith bosszúja*. A Mahout határozza meg, hogy az előző három filmhez hasonló felhasználók is szeretik a három filmet.

* **Hasonlósági javaslat**: mivel Joe tetszett az első három film, a Mahout megtekinti a hasonló beállításokkal rendelkező filmeket, de Joe nem néztem (tetszett/értékelt). Ebben az esetben a Mahout *a Phantom árnyak*, a *klónok támadását*és *a Sith bosszúját*javasolja.

### <a name="understanding-the-data"></a>Az adatgyűjtés ismertetése

A [GroupLens Research](https://grouplens.org/datasets/movielens/) kényelmesen, a Mahout-mel kompatibilis formátumban biztosítja a filmek minősítési információit. Ezek az adatkészletek a fürt alapértelmezett tárolójában érhetők el `/HdiSamples/HdiSamples/MahoutMovieData`.

Két fájl, `moviedb.txt` és `user-ratings.txt`. Az elemzés során a rendszer a `user-ratings.txt` fájlt használja. A `moviedb.txt` a felhasználóbarát szöveges információk megadására szolgál az eredmények megtekintésekor.

A User-Ratings. txt fájlban található adat `userID`, `movieID`, `userRating`és `timestamp`szerkezetét mutatja be, amely azt jelzi, hogy az egyes felhasználók milyen jól értékelték a filmet. Íme egy példa az adatmennyiségre:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Az elemzés futtatása

A fürthöz való SSH-kapcsolatban használja a következő parancsot a javaslati feladatoknak a futtatásához:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> A feladat végrehajtása több percet is igénybe vehet, és több MapReduce-feladatot is futtathat.

## <a name="view-the-output"></a>A kimenet megtekintése

1. Miután a feladatok befejeződik, a következő paranccsal tekintheti meg a generált kimenetet:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    A kimenet a következőképpen jelenik meg:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    Az első oszlop a `userID`. A (z) "[" és "]" fájlban található értékek `movieId`:`recommendationScore`.

2. A MovieDB. txt fájllal együtt a kimenetet is használhatja, hogy további információkat szolgáltasson a javaslatokról. Először másolja a fájlokat helyileg a következő parancsok használatával:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Ez a parancs a kimeneti adatokat egy **javaslatok. txt** nevű fájlba másolja az aktuális könyvtárban, valamint a film adatfájljaival együtt.

3. A következő parancs használatával hozzon létre egy Python-parancsfájlt, amely a javaslatok kimenetében szereplő adatokat keresi meg.

    ```bash
    nano show_recommendations.py
    ```

    A szerkesztő megnyitásakor használja a következő szöveget a fájl tartalmának megfelelően:

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

    Nyomja le a **CTRL-X**, az **Y**billentyűt, és végül **adja meg** az adatmentést.

4. Futtassa a Python-szkriptet. Az alábbi parancs feltételezi, hogy abban a könyvtárban van, ahol az összes fájl le lett töltve:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Ez a parancs a 4. felhasználói AZONOSÍTÓhoz generált ajánlásokat vizsgálja.

   * A **User-Ratings. txt** fájl a minősítés alatt álló filmek beolvasására szolgál.

   * A **MovieDB. txt** fájl a filmek nevének beolvasására szolgál.

   * A **javaslatok. txt** a felhasználóhoz tartozó filmkészítési javaslatok beolvasására szolgál.

     A parancs kimenete az alábbi szöveghez hasonló:

       Hét éve Tibetben (1997), pontszám = 5,0 Indiana Jones és az utolsó Crusade (1989), score = 5.0 JAWS (1975), pontszám = 5.0 értelem és érzelem (1995), score = 5.0 Independence Day (ID4) (1996), pontszám = 5.0 a legjobb barátja esküvője (1997), pontszám = 5,0 Jerry Maguire (1996), pontszám = 5.0 Scream 2 (1997), pontszám = 5,0 idő a kill, A (1996), pontszám = 5.0

## <a name="delete-temporary-data"></a>Ideiglenes adatmennyiség törlése

A Mahout feladatok nem távolítják el a feladat feldolgozása során létrehozott ideiglenes adatok körét. Az `--tempDir` paraméter meg van adva a példa feladatokban, hogy az ideiglenes fájlokat egy adott elérési útra különítse el az egyszerű törlés érdekében. Az ideiglenes fájlok eltávolításához használja a következő parancsot:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Ha újra futtatni szeretné a parancsot, törölnie kell a kimeneti könyvtárat is. A következő paranccsal törölheti a könyvtárat:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan használhatja a Mahout-t, Fedezze fel a HDInsight-on tárolt adatkezelés egyéb módszereit:

* [Apache Hive HDInsight](hdinsight-use-hive.md)
* [Apache Pig és HDInsight](hdinsight-use-pig.md)
* [MapReduce a HDInsight](hdinsight-use-mapreduce.md)