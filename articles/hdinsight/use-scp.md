---
title: SZOLGÁLTATÁSKAPCSOLÓDÁSI pont használata az Azure HDInsight Apache Hadoop
description: Ebből a dokumentumból megtudhatja, hogyan csatlakozhat a HDInsight az SSH és SCP parancsok használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: bb90db8a15a872fc28b3d09183228a67edf290e3
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103229"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>SZOLGÁLTATÁSKAPCSOLÓDÁSI pont használata az Azure HDInsight Apache Hadoop

Ez a cikk a HDInsight-fürtön található fájlok biztonságos átviteléről nyújt információkat.

## <a name="copy-files"></a>Fájlok másolása

Az `scp` segédprogrammal fájlokat másolhat a fürt egyes csomópontjairól más csomópontokra. A következő paranccsal például a `test.txt` könyvtárat a helyi rendszerről az elsődleges átjárócsomópontra másolhatja:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Mivel nincs megadva elérési út a `:` után, a fájl az `sshuser` kezdőkönyvtárba kerül.

A következő parancs a `test.txt` fájlt az elsődleges átjárócsomóponton található `sshuser` kezdőkönyvtárból a helyi rendszerre másolja:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

Az `scp` csak a fürt egyes csomópontjainak fájlrendszeréhez képes hozzáférni. Nem használható a fürt HDFS-kompatibilis tárolójában tárolt adatelérésre.

Ha egy SSH-munkamenetből kíván feltölteni egy használni kívánt erőforrást, használja az `scp` segédprogramot. Például töltsön fel egy Python-szkriptet, majd futtassa a szkriptet egy SSH-munkamenetből.

Adatok közvetlenül a HDFS-kompatibilis tárolóba való betöltésével kapcsolatos információkat a következő dokumentumokban talál:

* [HDInsight az Azure Storage használatával](hdinsight-hadoop-use-blob-storage.md).
* [HDInsight Azure Data Lake Storage használatával](hdinsight-hadoop-use-data-lake-store.md).

## <a name="next-steps"></a>További lépések

* [SSH használata a HDInsighttal](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [Élcsomópontok használata a HDInsightban](hdinsight-apps-use-edge-node.md#access-an-edge-node)
