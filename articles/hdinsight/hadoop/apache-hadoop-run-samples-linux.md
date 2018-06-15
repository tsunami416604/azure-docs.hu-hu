---
title: Futtassa a Hadoop-MapReduce példák a HDInsight - Azure-on |} Microsoft Docs
description: MapReduce-minták a HDInsight szereplő jar-fájlok az első lépéseiben. Csatlakozzon a fürthöz az SSH segítségével, és a Hadoop paranccsal minta feladatok futtatásához.
keywords: hadoop példa jar, a hadoop példák jar, a hadoop-mapreduce példák, a mapreduce példák
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1d2a0b9-1659-4fab-921e-4a8990cbb30a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 14f860d64c482ac7ef74512aea4850821d30132c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202305"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Futtassa a HDInsight szereplő MapReduce példák

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Útmutató a mellékelt a HDInsight Hadoop MapReduce példák futtatásához.

## <a name="prerequisites"></a>Előfeltételek

* **HDInsight-fürtök**: lásd: [Hadoop használatának megkezdésében a Hive HDInsight Linux rendszeren](apache-hadoop-linux-tutorial-get-started.md)

    > [!IMPORTANT]
    > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Egy SSH-ügyfél**: további információkért lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>A MapReduce példák

**Hely**: A minták a HDInsight-fürthöz, a lévő `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Tartalom**: A következő minták ebből az archívumból tartalmazza:

* `aggregatewordcount`: Egy összesítés mapreduce program, amely a bemeneti fájlok szavakat száma alapján.
* `aggregatewordhist`: Egy összesítés mapreduce program, amely kiszámítja a szövegrész szerepel a bemeneti fájlok hisztogram alapján.
* `bbp`: Mapreduce használó program Bailey-Borwein-Plouffe pi számjegyeket kiszámításához.
* `dbcount`: Egy példa a feladat, amely megjeleníti az adatbázisban tárolt pageview naplókat.
* `distbbp`: Mapreduce program pi pontos bits számítási BBP típusú képlet használatával.
* `grep`: A mapreduce program, amely egy reguláris kifejezéssel, a bemeneti adatok egyezések száma.
* `join`: Egy feladatot, amely végrehajtja a való csatlakozást keresztül rendezve, adatkészletek egyaránt particionálva.
* `multifilewc`: Egy feladatot, amely a szavakat több fájlok száma.
* `pentomino`: A mapreduce csempe szóló program pentomino problémák megoldása.
* `pi`: A mapreduce program, amely segítségével látszólagos Monte Pi becslése Carlo metódust.
* `randomtextwriter`: A mapreduce program 10 GB-nyi véletlenszerű szöveges adatok csomópontonként írja.
* `randomwriter`: A mapreduce program 10 GB-nyi véletlenszerű adatokat csomópontonként írja.
* `secondarysort`: Példa egy másodlagos rendezési meghatározása a reduce szakaszhoz ugranak.
* `sort`: Mapreduce program a véletlenszerű író írt adatok rendezi.
* `sudoku`: A sudoku solver.
* `teragen`: A terasort adatainak létrehozása.
* `terasort`: A terasort futtatásához.
* `teravalidate`: Terasort eredményeinek ellenőrzése.
* `wordcount`: A mapreduce program, amely a szavakat a bemeneti fájlok száma.
* `wordmean`: A mapreduce program, amely a bemeneti fájlok szavakat átlagos hosszának száma.
* `wordmedian`: Mapreduce program a közepes hossza a szövegrész szerepel a bemeneti fájlok száma.
* `wordstandarddeviation`: A mapreduce program, amely a bemeneti fájlok szavakat hosszát szórását száma.

**Forráskód**: ezeket a mintákat forráskódja szerepel-e a HDInsight-fürt `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

## <a name="run-the-wordcount-example"></a>Futtassa a wordcount-példa

1. Csatlakozzon a HDInsight SSH használatával. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Az a `username@#######:~$` kérni, használja a következő parancsot a minták listázásához:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Ez a parancs létrehozza az összetevőlistát a minta a jelen dokumentum korábbi szakaszában.

3. A következő paranccsal egy adott minta segítség. Ebben az esetben a **wordcount** minta:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    A következő hibaüzenet jelenik meg:

        Usage: wordcount <in> [<in>...] <out>

    Ez az üzenet azt jelzi, hogy több bemeneti elérési utak biztosíthat a forrás-dokumentumok. A végső elérési út, a kimenet (a forrás-dokumentumokban szavak száma) tárolására.

4. A megszámlálandó a notebookok a Leonardo Da Vinci, amely vannak megadva, a mintaadatok a fürthöz az összes szó használja a következőket:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Ez a feladat olvasása az adatokat `/example/data/gutenberg/davinci.txt`. Az ebben a példában tárolódik kimeneti `/example/data/davinciwordcount`. Mindkét elérési utak a fürt, nem a helyi fájlrendszer alapértelmezett tároló található.

   > [!NOTE]
   > Amint a Súgó gombra a wordcount-példa, több bemeneti fájl is megadhatja. Például `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` számolja davinci.txt és ulysses.txt szavakat.

5. Ha a feladat befejeződik, a következő paranccsal eredményének megtekintéséhez:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Ez a parancs a feladat által létrehozott kimeneti fájlok fűzi össze. A kimenet megjeleníti a konzolhoz. A kimenet az alábbi szöveghez hasonló:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Soronként egy szót, és hány alkalommal azt történt a bemeneti adatok jelöli.

## <a name="the-sudoku-example"></a>A Sudoku – példa

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) egy logikai kirakós kilenc 3 x 3 rácsok áll. Néhány a rács celláinak számok, üres, és a cél az, hogy az üres cellák megoldásában. A korábbi hivatkozás a további információk a Kirakós rendelkezik, de ez a minta az a célja, hogy oldja meg az üres cellák. A bemenet, a következő formátumú fájlnak kell lennie:

* Kilenc oszlopok kilenc sorok
* Minden oszlop tartalmazhat vagy egy szám vagy `?` (amely azt jelzi, egy üres cella)
* Cellák egymástól elválasztva

Egy bizonyos módon összeállítani Sudoku rejtvények; egy szám oszlop vagy sor nem ismétlődhet. A HDInsight-fürt megfelelően összeállított például nincs. Az itt található: `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` és tartalmazza a következő szöveget:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Példa a probléma keresztül a Sudoku példa futtatásához a következő paranccsal:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Az eredmény az alábbihoz hasonló jelennek meg:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>A pi (π) – Példa

A pi mintát használ a statisztikai (látszólagos Monte Carlo) módszer a pi értékét. Pontok egység négyzet véletlenszerűen kerülnek. A négyzet kör is tartalmaz. Annak a valószínűsége, hogy a pontok esik-e a kör megegyeznek a terület a kör pi/4. A pi értékét megbecsülhető 4R értékét. R a pontok, amelyek a teljes számú négyzetét belüli pontra körben aránya. Minél nagyobb a mintában használt pontok, annál pontosabb becslést van.

A minta futtatásához használja a következő parancsot. Ez a parancs használ 16 maps 10,000,000 minták becsléséhez a pi értékét:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Ez a parancs által visszaadott érték hasonlít **3.14159155000000000000**. Hivatkozások az első 10 tizedesjegyek pi 3.1415926535 vonatkoznak.

## <a name="10-gb-greysort-example"></a>10 GB-os Greysort – példa

GraySort teljesítményteszt rendezés. A metrika az a rendezési gyakoriság (TB percenként), amelyek során nagy mennyiségű adat, általában egy 100 TB-os minimális rendezés érhető el.

Ez a minta egy mérsékelt 10 GB adatot használ, így viszonylag gyorsan futtatása. A MapReduce alkalmazások Owen O'Malley és Arun Murthy által fejlesztett használ. Ezeket az alkalmazásokat az éves általános célú ("daytona") terabájt rendezési teljesítményteszt nyert 2009, sebességet 0.578 TB/perc (173 percben 100 TB). Erről és más rendezési referenciaalapok további információkért lásd: a [Sortbenchmark](http://sortbenchmark.org/) hely.

A példa a MapReduce programok három különböző használja:

* **TeraGen**: A MapReduce program, amely hoz létre sorok az adatok rendezése

* **TeraSort**: a bemeneti adatok minták és MapReduce segítségével rendezze az adatokat egy teljes rendelés

    TeraSort szabványos MapReduce rendezési, kivéve egy egyéni particionáló. A particionáló mintát N-1 kulcsok minden egyes csökkentse kulcs tartományának megadása rendezett listáját használja. Ebben az esetben, minden kulcsok ilyen mintát [i-1] < kulcs = < minta [i] kerülnek i csökkentése érdekében. Ez a particionáló garantálja, hogy a kimenetének csökkentése i segédanyagokra-nál kisebb kimenete csökkentése i + 1.

* **TeraValidate**: A MapReduce program, amely ellenőrzi, hogy a kimeneti globálisan rendezett

    Fájlonként egy leképezést a kimeneti könyvtár hozna létre, és minden leképezés biztosítja, hogy minden kulcs kisebb vagy egyenlő, mint az előzőt. A térkép függvény állít elő, az első és utolsó kulcsok minden egyes fájl rögzíti. Csökkentse a függvény biztosítja, hogy az első kulcsát fájl i nagyobb, mint az utolsó fájl i-1 kulcsa. Problémák a kulcsokkal, amelyek nem megfelelő sorrendben csökkentse fázis kimenetként jelenti.

Az alábbi lépések végrehajtásával hozhat létre adatokat, rendezés, és a kimeneti ellenőrzéséhez:

1. 10 GB-os adatait, amely a HDInsight-fürt alapértelmezett tárhelyre, létre `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    A `-Dmapred.map.tasks` Hadoop közli az feladathoz használandó térkép feladatok. Az utolsó két paramétert kérje meg a feladat létrehozása a 10 GB adatot és tárolja a `/example/data/10GB-sort-input`.

2. Az alábbi parancs segítségével rendezze az adatokat:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    A `-Dmapred.reduce.tasks` közli a Hadoop számának csökkentése a feladathoz használandó feladatok. Az utolsó két paraméterei csak a bemeneti és kimeneti helyek az adatok.

3. A rendezés által létrehozott adatok érvényesítéséhez használja a következőket:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>További lépések

Az ebben a cikkben megtanulta, a Linux-alapú HDInsight-fürtökkel minták futtatása. Oktatóanyagok Pig, a Hive és a MapReduce használata a hdinsight eszközzel a következő témakörökben található:

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)
* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:apache-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

