---
title: A HDFS CLI használata az Azure Data Lake tárolási Gen2 előzetes verzió
description: Bevezetés a Data Lake tárolási Gen2 Preview HDFS CLI
services: storage
documentationcenter: ''
author: artemuwka
manager: jahogg
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 75fb07120c78c45d422ee5017eac0afcf0e80859
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060818"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>A Data Lake tárolási Gen2 a HDFS parancssori felület használatával

Az Azure Data Lake tárolási Gen2 előzetes lehetővé teszi, hogy ugyanúgy, mint az adatok elérése és kezelésére egy [Hadoop elosztott fájlrendszerrel (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). E van csatlakoztatva, vagy egy Azure Databricks használata az Azure Data Lake tárolási Gen2 tárolt adatok elemzés végrehajtásához az Apache Spark on feladat futtatása HDInsight-fürtöt parancssori felület (CLI) segítségével lekérése, valamint a betöltött adatokról kezelése. A cikk többi ismerteti a beállítások rendelkezik során [Azure Storage csapat dolgozik a Azure Tártallózó és az Azure-portálon támogatását](https://azure.microsoft.com/roadmap/) -játszva!

## <a name="hdfs-cli-with-hdinsight"></a>HDFS CLI a hdinsight eszközzel

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer, amely közvetlenül együttműködik a rendszerhéj a HDFS és egyéb, amely támogatja a Hadoop használatával elérhetők. Az alábbiakban a gyakran használt parancsok és hasznos forrásokra mutató hivatkozásokat.

>[!IMPORTANT]
>A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. Számlázási az arányosított / perc, így mindig törölnie kell a fürthöz, amikor már nincs használatban (megtudhatja, hogyan [törölhet olyan fürtöket](../../hdinsight/hdinsight-delete-cluster.md)). Azonban az Azure Data Lake tárolási Gen2 tárolt adatokat is fennáll, egy HDInsight-fürtök törlése után is.

Fájlok vagy könyvtárak listájának beolvasása:

    hdfs dfs -ls <args>
Könyvtár létrehozása:

    hdfs dfs -mkdir [-p] <paths>
Egy fájl vagy könyvtár törlése:

    hdfs dfs -rm [-skipTrash] URI [URI ...]


Most vessünk HDInsight Hadoop-fürt Linux rendszeren példaként. A HDFS CLI használatához először kell létesíteni [-szolgáltatásokhoz való távoli hozzáférés](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Ha a választott [SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) PowerShell példakód következőképpen nézne ki:
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

A kapcsolati karakterláncban található az "SSH + a fürt bejelentkezési" szakasza a HDInsight fürt panel az Azure-portálon. SSH hitelesítő adatok a fürt létrehozása idején megadva.

A HDFS CLI további információkért lásd: a [dokumentációs](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) és a [HDFS engedélyek útmutató](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="hdfs-cli-with-azure-databricks"></a>Az Azure Databricks HDFS CLI

A Databricks biztosít egy könnyen használható parancssori felület Databricks REST API platformra épül. A nyílt forráskódú projekt üzemelteti [GitHub](https://github.com/databricks/databricks-cli). Az alábbiakban a gyakran használt parancsok.

Fájlok vagy könyvtárak listájának beolvasása:

    dbfs ls [-l]
Könyvtár létrehozása:

    dbfs mkdirs
Fájlok törléséhez:

    dbfs rm [-r]

Egy másik módszer a Databricks való interakció notebookok. Míg a notebook elsődleges nyelvét, kombinálhatja a nyelvek egy cella elején nyelvi magic parancs % nyelvi megadásával. Pontosabban % megosztása lehetővé teszi a notebook rendszerhéj kód végrehajtása hasonlóan a HDInsight-példa az ebben a cikkben.

Fájlok vagy könyvtárak listájának beolvasása:

    %sh ls <args>
Könyvtár létrehozása:

    %sh mkdir [-p] <paths>
Egy fájl vagy könyvtár törlése:

    %sh rm [-skipTrash] URI [URI ...]

Azure Databricks a Spark-fürt megkezdése után létre fog hozni egy új notebookot. A notebook parancsfájlpéldát a következőképpen fog kinézni:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

A parancssori felület Databricks további információkért lásd: a [dokumentációs](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Notebookok további információkért tekintse meg a [notebookok](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) című részben.

## <a name="next-steps"></a>További lépések

- [HDInsight-fürtök létrehozása az Azure Data Lake tárolási Gen2](./quickstart-create-connect-hdi-cluster.md)
- [Egy Azure Data Lake tárolási Gen2 képes a fiókot használja az Azure Databricks](./quickstart-create-databricks-account.md) 