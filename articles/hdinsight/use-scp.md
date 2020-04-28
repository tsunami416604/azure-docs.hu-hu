---
title: SZOLGÁLTATÁSKAPCSOLÓDÁSI pont használata az Azure HDInsight Apache Hadoop
description: Ebből a dokumentumból megtudhatja, hogyan csatlakozhat a HDInsight az SSH és SCP parancsok használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 559746a817442602c76ba91f12c195be1d7f3cc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188429"
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
