---
title: "SSH egy HDInsight-fürt - Azure Hadoop a Pig használata |} Microsoft Docs"
description: "Megtudhatja, hogyan kapcsolódnak a Linux-alapú Hadoop-fürtök SSH, majd használja a Pig parancs interaktív Pig Latin utasítás futása vagy kötegelt feladatként."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b646a93b-4c51-4ba4-84da-3275d9124ebe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 8f80b8c3b067496b4387ca31161d2e24f1c19da6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>A Pig parancs (SSH) rendelkező Linux-alapú fürtön Pig feladatok futtatása

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Megtudhatja, hogyan interaktív módon futtassa Pig-feladatokhoz az SSH-kapcsolat a HDInsight-fürthöz. A Pig Latin programozási nyelv lehetővé teszi a bemeneti adatok a kívánt kimeneti létrehozásához alkalmazott átalakítások ismertetik.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy Linux-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="ssh"></a>Csatlakozzon SSH

Az SSH használata a HDInsight-fürthöz való csatlakozáshoz. Az alábbi példa nevű fürthöz csatlakozó **myhdinsight** nevű fiók **sshuser**:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="pig"></a>A Pig paranccsal

1. A csatlakozás után a következő parancs használatával indítsa el a Pig parancssori felület (CLI):

    ```bash
    pig
    ```

    Néhány percet, miután a kérdés vált`grunt>`.

2. Adja meg a következő utasítást:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    ```

    Ez a parancs a sample.log fájl tartalmát betölti a NAPLÓKAT. A fájl tartalmát a következő utasítás használatával tekintheti meg:

    ```piglatin
    DUMP LOGS;
    ```

3. A következő átalakíthatja az adatokat úgy, hogy alkalmazza minden rekordot a következő utasítás használatával csak a naplózási szint kibontani reguláris kifejezést:

    ```piglatin
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    ```

    Használhat **DUMP** az átalakítás után az adatok megtekintéséhez. Ebben az esetben használjon `DUMP LEVELS;`.

4. További, átalakítások alkalmazása a következő táblázat a kimutatások segítségével:

    | A Pig Latin utasítás | Az utasítás funkciója |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | A naplózási szint megadásához null értéket tartalmazó sorok eltávolítása és az eredményt a `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | A sorok naplózási szint szerint csoportosítja, és az eredményt a `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Hoz létre, amely tartalmazza az egyes egyedi napló adatok értéke szinten, és hány alkalommal következik be. Az adatkészlet tárolja azokat `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | A naplózási szintek rendelések (csökkenő) száma szerint, és tárolja a `RESULT`. |

    > [!TIP]
    > Használjon `DUMP` az átalakítás lépések után az eredmények megjelenítéséhez.

5. Az átalakítás eredménye használatával is mentheti a `STORE` utasítást. Például a következő utasítás menti a `RESULT` számára a `/example/data/pigout` a fürt az alapértelmezett tároló könyvtár:

    ```piglatin
    STORE RESULT into '/example/data/pigout';
    ```

   > [!NOTE]
   > Az adatok tárolási fájlban található a megadott könyvtár `part-nnnnn`. Ha a könyvtár már létezik, akkor egy hibaüzenet.

6. Lépjen ki a grunt parancssorába, írja be a következő utasítást:

    ```piglatin
    QUIT;
    ```

### <a name="pig-latin-batch-files"></a>A Pig Latin parancsfájlokat

A Pig-parancs segítségével futtassa a Pig Latin fájlban található.

1. A grunt parancssorába kilépéskor a következő paranccsal nevű fájl létrehozása `pigbatch.pig`:

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

    Ha elkészült, használja __Ctrl__ + __X__, __Y__, majd __Enter__ fájl mentéséhez.

3. A következő paranccsal futtassa a `pigbatch.pig` fájl a Pig-parancs használatával.

    ```bash
    pig ~/pigbatch.pig
    ```

    Miután befejeződött a kötegelt, a következő eredmény jelenik meg:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>Következő lépések

A hdinsight Pig általános információkért lásd: a következő dokumentumban:

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)

Egyéb módjai használata a HDInsight Hadoop további információkért tekintse meg a következő dokumentumokat:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)
