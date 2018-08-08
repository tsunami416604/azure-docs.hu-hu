---
title: Az SSH egy HDInsight-fürtön – Azure Hadoop a Pig használata
description: Ismerje meg, hogyan csatlakozhat egy Linux-alapú Hadoop-fürtöt, az SSH és a Pig parancs futása Pig Latin utasításokkal majd használja, vagy egy batch-feladatot.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: jasonh
ms.openlocfilehash: c521f5781c1fb8bae1e036649ee31744d0742796
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590296"
---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>A Pig-parancsot (SSH) rendelkező Linux-alapú fürt Pig-feladatok futtatása

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Ismerje meg, interaktív módon futtatása a HDInsight-fürthöz az SSH-kapcsolatot a Pig-feladatokhoz. A Pig Latin programozási nyelv lehetővé teszi átalakítások használhatók, amelyek érvényesek a bemeneti adatokat és létrehozza a kívánt kimeneti ismertetik.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések a Linux-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="ssh"></a>Kapcsolódás az ssh-val

Az SSH használata a HDInsight-fürthöz való csatlakozáshoz. Az alábbi példa nevű fürthöz csatlakozó **myhdinsight** nevű fiókként **sshuser**:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="pig"></a>A Pig-parancs használata

1. Ha csatlakoztatva van, indítsa el a Pig parancssori felület (CLI) használatával a következő parancsot:

    ```bash
    pig
    ```

    Pár pillanat után a parancssor megváltozik, és`grunt>`.

2. Adja meg a következő utasítást:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    ```

    Ez a parancs a sample.log fájlt tartalmát betölti a NAPLÓKAT. A fájl tartalmát a következő utasítás használatával tekintheti meg:

    ```piglatin
    DUMP LOGS;
    ```

3. Ezután az adatok átalakítása alkalmazása csak a naplózási szint kinyerésére minden rekord használatával a következő utasítást egy reguláris kifejezést:

    ```piglatin
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    ```

    Használhat **DUMP** az átalakítás után az adatok megtekintéséhez. Ebben az esetben használjon `DUMP LEVELS;`.

4. Továbbra is alkalmazása átalakításokat az alábbi táblázatban a-utasítások használatával:

    | A Pig Latin utasítás | Az utasítás leírása |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | A naplózási szint megadásához null értéket tartalmazó sorok eltávolítja, és tárolja az eredményeket az `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | A sorok naplózási szint szerint csoportosítja, és tárolja az eredményeket az `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Létrehoz egy csoportot, amely tartalmazza az egyes egyedi naplózási adatok szintű hatékonyságot, és hány alkalommal történik. Az adatkészlet tárolt be `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | A naplózási szintek orders (csökkenő) száma szerint, és tárolja `RESULT`. |

    > [!TIP]
    > Használat `DUMP` az átalakítás minden lépése után az eredmény megtekintéséhez.

5. Az eredményeket az átalakítás használatával is mentheti a `STORE` utasítást. Ha például a következő utasítás menti a `RESULT` , a `/example/data/pigout` a a fürt alapértelmezett tárolója könyvtárába:

    ```piglatin
    STORE RESULT into '/example/data/pigout';
    ```

   > [!NOTE]
   > Az adatok fájlban található a megadott könyvtárban tárolt `part-nnnnn`. Ha a könyvtár már létezik, hibaüzenetet kap.

6. A kilépéshez a grunt használatával adja meg a következő utasítást:

    ```piglatin
    QUIT;
    ```

### <a name="pig-latin-batch-files"></a>A Pig Latin kötegfájlok

A Pig-parancs segítségével is futtathat a Pig Latin a fájlban található.

1. Után kilép a grunt használatával, a következő parancs használatával hozzon létre fájlt `pigbatch.pig`:

    ```bash
    nano ~/pigbatch.pig
    ```

2. Írja be vagy illessze be a következő sorokat:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

    Amikor végzett, __Ctrl__ + __X__, __Y__, majd __Enter__ szeretné menteni a fájlt.

3. Futtassa a következő parancs használatával a `pigbatch.pig` fájlt a Pig-parancs segítségével.

    ```bash
    pig ~/pigbatch.pig
    ```

    A batch-feladat befejezése után a következő kimenet jelenik meg:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>Következő lépések

A HDInsight Pig általános információkért lásd a következő dokumentumban:

* [A Pig használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-pig.md)

További információ az egyéb módon történő használata a HDInsight Hadoop a következő dokumentumokban talál:

* [A Hive használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight Hadoop](hdinsight-use-mapreduce.md)
