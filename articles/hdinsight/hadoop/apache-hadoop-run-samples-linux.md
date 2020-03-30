---
title: Apache Hadoop MapReduce példák futtatása a HDInsighton – Azure
description: A MapReduce minták használatának első lépései a HDInsight ban található jar-fájlokban. Az SSH használatával csatlakozzon a fürthöz, majd a Hadoop paranccsal futtassa a mintafeladatokat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 58f7d99af638c8d03bbce46b7fcf8204aaca11d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435748"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>A MAPReduce példák futtatása a HDInsightban

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Ismerje meg, hogyan futtathat a MapReduce példák at Apache Hadoop a HDInsight.

## <a name="prerequisites"></a>Előfeltételek

* Apache Hadoop-fürt a HDInsighton. Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](./apache-hadoop-linux-tutorial-get-started.md)

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="the-mapreduce-examples"></a>A MapReduce példák

A minták a HDInsight-fürtön találhatók a helyen. `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar` Ezeknek a mintáknak a forráskódja `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`megtalálható a HDInsight-fürtön a.-on.

Az archívum a következő mintákat tartalmazza:

|Sample |Leírás |
|---|---|
|összesítésszószám|Megszámolja a beviteli fájlokban lévő szavakat.|
|aggregátumszós|Kiszámítja a bemeneti fájlokban lévő szavak hisztogramját.|
|bbp között|Bailey-Borwein-Plouffe segítségével számítja ki a Pi pontos számjegyeit.|
|dbcount (adatbázis-szám|Megszámolja az adatbázisban tárolt oldalnézeti naplókat.|
|distbbp között|A Pi pontos bitjének kiszámításához BBP típusú képletet használ.|
|Grep|Megszámolja a regex egyezéseit a bemenetben.|
|csatlakozás|Illesztést hajt végre rendezett, egyenlő particionált adatkészletek felett.|
|multifilewc|Több fájlból származó szavakat számol meg.|
|pentomino között|Csempe szóló program ot megoldást találni pentomino problémákat.|
|Pi|A Pi-t kvázi Monte Carlo módszerrel becsüli meg.|
|véletlen szövegíró|Csomópontonként 10 GB véletlenszerű szöveges adatot ír.|
|véletleníró|Csomópontonként 10 GB véletlenszerű adatot ír.|
|secondarysort|Másodlagos rendezést határoz meg a csökkentési fázishoz.|
|Rendezés|A véletlenszerű író által írt adatokat rendezi.|
|Sudoku|Egy sudoku megoldó.|
|teragen|Adatok létrehozása a terasort számára.|
|terasort között|Futtasd le a terasortot.|
|teravalid|Ellenőrzöm a terasort eredményeit.|
|szószám|Megszámolja a beviteli fájlokban lévő szavakat.|
|wordmean (szó)|Megszámolja a bemeneti fájlokban lévő szavak átlagos hosszát.|
|wordmedian (szómedián)|Megszámolja a bemeneti fájlokban lévő szavak medián hosszát.|
|szószórás|Megszámolja a bemeneti fájlokban lévő szavak hosszának szórását.|

## <a name="run-the-wordcount-example"></a>Példa a wordcount

1. Csatlakozzon a HDInsighthoz az SSH használatával. Cserélje `CLUSTER` le a fürt nevére, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Az SSH-munkamenetből a következő paranccsal sorolja fel a mintákat:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Ez a parancs a dokumentum előző szakaszából származó mintalistát hozza létre.

3. A következő paranccsal segítséget kaphat egy adott mintához. Ebben az esetben a **wordcount** minta:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    A következő üzenet jelenik meg:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Ez az üzenet azt jelzi, hogy a forrásdokumentumokhoz több bemeneti útvonal is megadható. A végső elérési út a kimenet (a forrásdokumentumokban lévő szavak száma) tárolása.

4. Az alábbi módon megszámlálhatja a Leonardo da Vinci notebookjaiban szereplő összes szót, amelyek mintaadatként vannak megadva a fürtben:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    A feladat bemenete `/example/data/gutenberg/davinci.txt`a-ból olvasható. A példában szereplő kimenet `/example/data/davinciwordcount`a ban van tárolva. Mindkét elérési út a fürt alapértelmezett tárolóján található, nem a helyi fájlrendszerben.

   > [!NOTE]  
   > Amint azt a szószámminta súgója is megjegyezte, több bemeneti fájlt is megadhat. Például `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` a davinci.txt és az ulysses.txt fájlban is megszámolná a szavakat.

5. A feladat befejezése után a következő paranccsal tekintse meg a kimenetet:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Ez a parancs összefűzi a feladat által létrehozott összes kimeneti fájlt. Megjeleníti a kimenetet a konzolra. A kimenet az alábbi szöveghez hasonló:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Minden sor egy szót jelöl, és azt, hogy hányszor fordult elő a bemeneti adatokban.

## <a name="the-sudoku-example"></a>A Sudoku példa

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) egy logikai puzzle álló kilenc 3x3 rácsok. A rács egyes celláinak számai vannak, míg mások üresek, és a cél az üres cellák megoldása. Az előző link több információt a puzzle, de a célja ennek a mintának, hogy megoldja az üres sejteket. Tehát a mi bemenet kell egy fájlt, amely a következő formátumban:

* Kilenc oszlop kilenc sora
* Minden oszlop tartalmazhat egy `?` számot vagy (amely üres cellát jelöl)
* A cellákat szóköz választja el egymástól

Van egy bizonyos módja annak, hogy építeni Sudoku rejtvények; oszlopban vagy sorban nem lehet számot megismételni. Van egy példa a HDInsight-fürtön, amely megfelelően van kialakítva. Ez található, `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` és tartalmazza a következő szöveget:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Ha a sudoku-példán keresztül szeretné futtatni ezt a példát, használja a következő parancsot:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Az eredmények a következő szöveghez hasonlóak:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Példa pi (π)

A pi minta statisztikai (kvázi-Monte Carlo) módszert használ a pi értékének becslésére. A pontok véletlenszerűen kerülnek elhelyezésre egy egység négyzetben. A négyzet is tartalmaz egy kört. Annak valószínűsége, hogy a pontok a körbe esnek, megegyezik a kör pi/4 területével. A pi értéke a 4R értékből becsülhető meg. R a körön belüli pontok számának és a négyzeten belüli pontok teljes számának az aránya. Minél nagyobb a felhasznált pontok mintája, annál jobb a becslés.

A minta futtatásához használja a következő parancsot. Ez a parancs 16 térképet használ 10 000 000 mintával a pi értékének becsléséhez:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

A parancs által visszaadott érték hasonló a **3,1415915000000000000000000000000000000000000000000000000000000000000000000000000000000000** A referenciaként a pi első 10 tizedesjegye a 3,1415926535.

## <a name="10-gb-graysort-example"></a>10 GB GraySort példa

A GraySort egy viszonyítási alap. A metrika a rendezési sebesség (TB/perc), amely nagy mennyiségű adat rendezése közben érhető el, általában legalább 100 TB.The metric is the sort rate (TB/minute) that is achieved while válogatás large amounts of data, usually a 100 TB minimum.

Ez a minta szerény 10 GB-os adatokat használ, hogy viszonylag gyorsan futtatható legyen. Ez használ a MapReduce alkalmazások által kifejlesztett Owen O'Malley és Arun Murthy. Ezek az alkalmazások 2009-ben nyerték meg az éves általános célú ("Daytona") terabájt rendezési referenciaértéket, 0,578 TB/perc (100 TB 173 perc alatt). Erről és más rendezési referenciaértékekről a [Rendezési teljesítménypont](https://sortbenchmark.org/) webhelyen talál további információt.

Ez a minta a MapReduce programok három csoportját használja:

* **TeraGen**: A MapReduce program, amely generál sorok at adatok rendezésére

* **TeraSort**: Mintát vesz a bemeneti adatokból, és a MapReduce segítségével rendezi az adatokat

    TeraSort egy szabványos MapReduce rendezés, kivéve egy egyéni partitioner. A particionáló az N-1 mintavételezett kulcsok rendezett listáját használja, amelyek meghatározzák az egyes csökkentéskulcs-tartományt. Különösen az összes olyan kulcsot küldi el a minta[i-1] <= a < minta[i] kulcsa az i csökkentése érdekében. Ez a particionáló garantálja, hogy a csökkentés i kimenetei mind kisebbek, mint az i+1 csökkentés kimenete.

* **TeraValidate**: A MapReduce program, amely ellenőrzi, hogy a kimenet globálisan rendezve

    Fájlonként egy leképezést hoz létre a kimeneti könyvtárban, és minden egyes leképezés biztosítja, hogy minden kulcs kisebb vagy egyenlő legyen az előzővel. A térképfüggvény az egyes fájlok első és utolsó kulcsainak rekordjait hozza létre. A reduce funkció biztosítja, hogy az első kulcs a fájl i nagyobb, mint az utolsó kulcs a fájl i-1. Minden problémát a csökkentési fázis kimeneteként jelentenek, a kulcsok nem sorrendben.

Az alábbi lépésekkel hozhat létre adatokat, rendezheti, majd érvényesítheti a kimenetet:

1. 10 GB adatot hozhat létre, amely a HDInsight-fürt `/example/data/10GB-sort-input`alapértelmezett tárolójában van tárolva:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    A `-Dmapred.map.tasks` megmondja a Hadoopnak, hogy hány térképes feladatot kell használnia ehhez a feladathoz. Az utolsó két paraméter arra utasítja a feladatot, hogy `/example/data/10GB-sort-input`hozzon létre 10 GB adatot, és tárolja a rendszeren.

2. Az adatok rendezéséhez használja a következő parancsot:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    A `-Dmapred.reduce.tasks` megmondja a Hadoopnak, hogy hány olyan tevékenységet kell csökkentenie a feladathoz, amelyet használni szeretne. Az utolsó két paraméter csak az adatok bemeneti és kimeneti helye.

3. A rendezés által létrehozott adatok érvényesítéséhez használja az alábbiakat:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtanulta, hogyan futtathatja a Linux-alapú HDInsight-fürtökben található mintákat. A Pig, a Hive és a MapReduce HDInsight használatával kapcsolatos oktatóanyagokról az alábbi témakörökben olvashat:

* [Az Apache Hive használata az Apache Hadoop segítségével a HDInsighton](hdinsight-use-hive.md)
* [A MapReduce használata az Apache Hadoop segítségével a HDInsighton](hdinsight-use-mapreduce.md)