---
title: Adatfolyam-MapReduce-feladatok a HDInsight - Azure Python kidolgozása |} Microsoft Docs
description: Ismerje meg, hogyan használható a Python az adatfolyam-MapReduce-feladatok. Hadoop streamelési API biztosít a Java nyelven írt a MapReduce.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: b5e19f81c3e869347f21ab3c70a70016196b946d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400514"
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>A HDInsight MapReduce programok streaming Python fejlesztése

Megtudhatja, hogyan használja a Python MapReduce műveletek adatfolyam. Hadoop streamelési API biztosít, amely lehetővé teszi, hogy a térkép írása, és csökkentheti a funkciók nem Java nyelven MapReduce. A jelen dokumentumban leírt lépések végrehajtása a térkép, és csökkentse az összetevők a Python.

## <a name="prerequisites"></a>Előfeltételek

* A Linux-based Hadoop on HDInsight-fürt

  > [!IMPORTANT]
  > A jelen dokumentumban leírt lépések egy HDInsight-fürt által használt Linux igényelnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Egy szövegszerkesztőben

  > [!IMPORTANT]
  > A szövegszerkesztőben kell LF használják a sor befejezése. Egy sor vége CRLF használatával hatására a hibák, a MapReduce feladatot Linux-alapú HDInsight-fürtök futtatásakor.

* A `ssh` és `scp` parancsok, vagy [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Word száma

Ebben a példában az alapvető szószámot python megvalósítva, a hozzárendelést és nyomáscsökkentő. A leképező mondat bontja egyedi szót, és a nyomáscsökkentő összesíti a szavakat, és megjeleníti a kimenet előállításához.

Az alábbi folyamatábra bemutatja, mi történik a leképezés során, és csökkentheti a fázisok.

![a mapreduce folyamat ábrája](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>Adatfolyam-továbbítási MapReduce

Hadoop lehetővé teszi a térkép tartalmazó fájlt ad meg, és csökkenti a feladat által használt logikai. A térkép a konkrét követelmények, ami csökkenti a logika vannak:

* **Bemeneti**: A térkép és összetevők bemeneti adatokat STDIN kell olvasni.
* **Kimeneti**: A térkép és összetevők kimeneti adatokat STDOUT kell írni.
* **Az adatformátum**: felhasznált és előállított adatokat egy kulcs/érték pár, karakterrel elválasztó karakterláncként lapon kell lennie.

Python használatával könnyen képes kezelni ezeket a követelményeket a `sys` STDIN és használatával olvasni modul `print` STDOUT nyomtatás. A fennmaradó feladat egyszerűen formázza az adatokat a lap (`\t`) karaktert a kulcs és az érték között.

## <a name="create-the-mapper-and-reducer"></a>A hozzárendelést és nyomáscsökkentő létrehozása

1. Hozzon létre egy fájlt `mapper.py` , és használja a tartalom a következő kódot:

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. Hozzon létre egy fájlt **reducer.py** , és használja a tartalom a következő kódot:

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>Futtatás a PowerShell használatával

Győződjön meg arról, hogy a fájlok rendelkeznek-e a megfelelő sorvégződések, használja a következő PowerShell-parancsfájlt:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Töltse fel a fájlokat, a feladat futtatása és eredményének megtekintéséhez használja a következő PowerShell-parancsfájlt:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Az SSH-munkamenetet futtatása

1. A fejlesztési környezetet ugyanabban a könyvtárban a `mapper.py` és `reducer.py` fájlok, a következő paranccsal:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Cserélje le `username` rendelkező a fürthöz az SSH-felhasználónév és `clustername` a fürt nevét.

    Ez a parancs másolja át a fájlokat a helyi rendszer az átjárócsomóponthoz.

    > [!NOTE]
    > Ha a jelszó SSH fiókja biztonsága érdekében, a rendszer kéri a jelszót. Ha SSH-kulcsot használt, előfordulhat, hogy használatára a `-i` paraméter és a titkos kulcs elérési útját. Például: `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Csatlakozzon a fürthöz SSH segítségével:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    További információkért lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Győződjön meg arról a mapper.py, illetve reducer.py rendelkezik a megfelelő sorvégeket, az alábbi parancsokat használja:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Az alábbi parancs segítségével indítsa el a MapReduce feladatot.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    Ez a parancs a következő részekből áll:

   * **hadoop-streaming.jar**: használt adatfolyam-továbbítási MapReduce műveletek végrehajtása során. A külső MapReduce kódot megadnia a Hadoop az illesztők.

   * **-fájlok**: a megadott fájlokat ad hozzá a MapReduce feladatot.

   * **-leképező**: közli a Hadoop melyik fájlt kívánja használni, mint a leképező.

   * **-Nyomáscsökkentő**: közli a Hadoop kívánja használni, mint a nyomáscsökkentő fájlt.

   * **-bemeneti**: A bemeneti fájl, amely azt számolja a jelenti.

   * **-kimeneti**: az írt a kimeneti könyvtár.

    A MapReduce feladatot működik, mert a folyamat százalékként jelenik meg.

        15-02-05 19:01:04 információ mapreduce. Feladat: a térkép 0 % csökkentheti a 0 % 15-02-05 19:01:16 információ mapreduce. Feladat: a térkép 100 %-os csökkentése 0 % 15-02-05 19:01:27 információ mapreduce. Feladat: a térkép 100 %-os csökkentheti a 100 %-os


5. A kimenet megtekintéséhez használja a következő parancsot:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Ez a parancs megjeleníti szavak és hányszor a word történt.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a MapRedcue folyamatos átviteli feladat használata a hdinsight eszközzel rendelkezik, az alábbi hivatkozások segítségével más módjai Azure HDInsight használata.

* [A Hive használata a HDInsightban](hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hdinsight-use-pig.md)
* [MapReduce-feladatok használata a HDInsightban](hdinsight-use-mapreduce.md)
