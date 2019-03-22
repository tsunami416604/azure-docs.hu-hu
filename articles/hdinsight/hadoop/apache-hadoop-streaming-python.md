---
title: Python-streamelési HDInsight – Azure MapReduce feladatok fejlesztése
description: Megtudhatja, hogyan használható a Python MapReduce-feladatok továbbítását. Az Apache Hadoop MapReduce a egy olyan streamelési API biztosít más nyelveken írt.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 4c1d7357dc78ca6c9387fc594f3d49f043008631
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120573"
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Python-streamelés HDInsight MapReduce-programok fejlesztése

Megtudhatja, hogyan használható a Python MapReduce műveletek továbbítását. Az Apache Hadoop MapReduce, amely lehetővé teszi, hogy térkép írása, és csökkentse a függvényeket más nyelveken biztosít egy olyan streamelési API. A jelen dokumentumban leírt lépések végrehajtása a térkép, és csökkentheti a Pythonban összetevők.

## <a name="prerequisites"></a>Előfeltételek

* Egy Linux-alapú Apache Hadoop HDInsight-fürtön

  > [!IMPORTANT]
  > A dokumentum lépéseinek elvégzéséhez egy Linux-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Egy szövegszerkesztőben

  > [!IMPORTANT]
  > Szövegszerkesztő LF Karakterrel kell használnia, mint a sor vége. Egy sor vége a CRLF rendelkezik, hibák a Linux-alapú HDInsight-fürtökön a MapReduce feladat futtatásakor.

* A `ssh` és `scp` parancsok, vagy [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Word száma

Ebben a példában egy alapszintű szószámlálási száma egy leképező és nyomáscsökkentő végrehajtott egy Python. A teljesítményleképező mondatokat bontja az egyes szavak, és a nyomáscsökkentő összesíti a szavakat, és a kimenet létrehozására száma.

A következő folyamatábra szemlélteti azt szemlélteti, mi történik a leképezés során, és csökkentheti a fázisait.

![a mapreduce folyamat ábrája](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>Streaming MapReduce

Hadoop lehetővé teszi, hogy adjon meg egy fájlt, amely tartalmazza a térképen, és csökkentse a feladat által használt logikát. A térkép a konkrét követelmények, és csökkentse a logikai vannak:

* **Bemeneti**: A térképen, és csökkentse összetevők bemeneti adatokat STDIN kell olvasni.
* **Kimeneti**: A térképen, és csökkentse összetevők kimeneti adatokat STDOUT kell írni.
* **Adatformátum**: A felhasznált és előállított adatok egy kulcs/érték pár, tabulátor karakterrel elválasztva kell lennie.

Python használatával könnyen képes kezelni ezeket a követelményeket a `sys` STDIN és használatával olvasni modul `print` STDOUT nyomtatni. A többi feladat egyszerűen formázza az adatokat a lap (`\t`) karaktert a kulcs-érték között.

## <a name="create-the-mapper-and-reducer"></a>Hozza létre a teljesítményleképező és nyomáscsökkentő

1. Hozzon létre egy fájlt `mapper.py` és a tartalom a következő kód használatával:

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

2. Hozzon létre egy fájlt **reducer.py** és a tartalom a következő kód használatával:

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

Győződjön meg arról, hogy a fájlok rendelkezik-e a megfelelő sorvégződések, használja a következő PowerShell-parancsfájlt:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

A következő PowerShell-parancsfájl használatával töltse fel a fájlokat, a feladat futtatásához és a kimenet megtekintéséhez:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>SSH-munkamenetből történő futtatása

1. A fejlesztői környezetből ugyanabban a címtárban `mapper.py` és `reducer.py` fájlok, használja a következő parancsot:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Cserélje le `username` a fürt SSH-felhasználónév- és `clustername` a fürt nevére.

    Ez a parancs másolja a fájlokat a helyi rendszerről a fő csomópontot.

    > [!NOTE]  
    > Ha az SSH-fiókhoz jelszót használt, a rendszer kéri a jelszót. Ha SSH-kulcsot használt, előfordulhat, hogy akkor alkalmazza a `-i` paraméter és a titkos kulcs elérési útját. Például: `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Csatlakozzon a fürthöz SSH használatával:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    További információkért lásd: [az SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Annak biztosítása érdekében a mapper.py és reducer.py rendelkezik a megfelelő sorvégeket, használja a következő parancsokat:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. A következő paranccsal indítsa el a MapReduce-feladatot.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    Ez a parancs a következő részekből áll:

   * **hadoop-streaming.jar**: Streamelési MapReduce műveletek végrehajtásakor használni. A külső MapReduce-kódot, adja meg a Hadoop, felületeihez.

   * **-fájlok**: A MapReduce-feladatot ad hozzá a megadott fájlokat.

   * **-mapper**: Arra utasítja a Hadoop leképezőjét használandó fájlt.

   * **-reducer**: Arra utasítja a Hadoop a nyomáscsökkentő használandó fájlt.

   * **– a bemeneti**: A bemeneti fájl, amely a szavakat azt kell száma.

   * **-kimenet**: A kimenet írt könyvtár.

     A MapReduce-feladatot működik, mivel a folyamat százalékként jelenik meg.

        05-15-02 19:01:04 INFO mapreduce. Feladat: a térkép 0 %-os csökkentheti a 0 %-os 05-15-02 19:01:16 INFO mapreduce. Feladat: a térkép 100 %-os csökkentheti a 0 %-os 05-15-02 19:01:27 INFO mapreduce. Feladat: a térkép 100 %-os csökkentheti a 100 %-os


5. A kimenet megtekintéséhez használja a következő parancsot:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Ez a parancs megjeleníti szavakat, és hány alkalommal a word történt.

## <a name="next-steps"></a>További lépések

Most, hogy, hogyan MapRedcue folyamatos átviteli feladatok használata a HDInsight, egyéb módon az Azure HDInsight használata az alábbi hivatkozások segítségével.

* [Az Apache Hive használata a HDInsight](hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight](hdinsight-use-pig.md)
* [MapReduce-feladatok használata a HDInsightban](hdinsight-use-mapreduce.md)
