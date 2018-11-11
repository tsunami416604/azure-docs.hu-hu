---
title: A HDFS-CLI-vel rendelkező Azure Data Lake Storage Gen2 előzetes verzió
description: Bevezetés a HDFS CLI a Data Lake Storage Gen2 előzetes verzió
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: c5f11cbb12b727f5f308d7a71c51706fa8ec373f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277086"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>A HDFS parancssori felület használatával a Data Lake Storage Gen2

Az Azure Data Lake Storage Gen2 előzetes verzió lehetővé teszi, hogy a kezelése és az adatok eléréséhez, ugyanúgy, mint az egy [Hadoop elosztott fájlrendszer (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). E van csatlakoztatva, vagy egy Azure Databricks használatával egy Azure Storage-fiókban tárolt adatokon végrehajtandó analytics Apache Spark-feladatok futtatása egy HDInsight-fürt, parancssori felület (CLI) segítségével lekérése és módosítására a betöltött adatokra.

## <a name="hdfs-cli-with-hdinsight"></a>HDFS CLI-t a HDInsight

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer elérhető lesz a rendszerhéj, amely közvetlenül kommunikál a HDFS-és más fájlrendszereket, amely támogatja a Hadoop használatával. Az alábbiakban a gyakran használt parancsok és a hasznos forrásokra mutató hivatkozásokat.

>[!IMPORTANT]
>HDInsight-fürt számlázása elindul, amikor egy fürt jön létre, és leállítja a fürt törlésekor. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. Ismerje meg, hogyan törölheti a fürtöt, tekintse meg a [foglalkozó témakör](../../hdinsight/hdinsight-delete-cluster.md). Azonban a Data Lake Storage Gen2 engedélyezve van a storage-fiókban tárolt adatok fenntartása, még akkor is egy HDInsight-fürt törlése után.

Fájlok vagy könyvtárak listájának lekérése:

    hdfs dfs -ls <args>
Könyvtár létrehozása:

    hdfs dfs -mkdir [-p] <paths>
Egy fájl vagy könyvtár törlése:

    hdfs dfs -rm [-skipTrash] URI [URI ...]


Most tekintsük HDInsight Hadoop-fürt Linux rendszeren példaként. A HDFS-parancssori használatához először kell létesíteni [távoli szolgáltatásokhoz való hozzáférés](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Ha kiválasztjuk az [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) ehhez módon néz ki: a minta PowerShell-kóddal:
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

A kapcsolati karakterlánc található a "SSH + fürtbe bejelentkezési" szakaszában a HDInsight-fürt panelén, az Azure Portalon. Az SSH hitelesítő adatok a fürt létrehozása idején voltak megadva.

A parancssori felület HDFS további információkért lásd: a [dokumentációs](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) és a [HDFS-engedélyek útmutatója](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="hdfs-cli-with-azure-databricks"></a>HDFS CLI-t az Azure Databricks

A Databricks a Databricks REST API-ra épülő, egy könnyen használható parancssori felület biztosít. A nyílt forráskódú projekt lévő üzemeltetett [GitHub](https://github.com/databricks/databricks-cli). Az alábbiakban a gyakran használt parancsok.

Fájlok vagy könyvtárak listájának lekérése:

    dbfs ls [-l]
Könyvtár létrehozása:

    dbfs mkdirs
Fájl törléséhez:

    dbfs rm [-r]

Egy másik módszer a Databricks implementálására notebookok. Míg a jegyzetfüzet elsődleges nyelv, a nyelvi varázsparancs % nyelv egy cella elején megadásával kombinálhatja nyelveket. Pontosabban % sh lehetővé teszi rendszerhéj kód végrehajtása a jegyzetfüzet lényegében ugyanúgy a HDInsight-példában a cikk korábbi részében.

Fájlok vagy könyvtárak listájának lekérése:

    %sh ls <args>
Könyvtár létrehozása:

    %sh mkdir [-p] <paths>
Egy fájl vagy könyvtár törlése:

    %sh rm [-skipTrash] URI [URI ...]

Az Azure Databricks a Spark-fürt indítás után az új notebook fog létrehozni. A notebook példaszkript a következőképpen fog kinézni:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

A Databricks parancssori felület további információkért lásd: a [dokumentációs](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Notebookok további információkért lásd: a [notebookok](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) a dokumentáció része.

## <a name="next-steps"></a>További lépések

- [Hozzon létre egy HDInsight-fürtön az Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
- [Az Azure Data Lake Storage Gen2 képes a fiókot használják az Azure Databricksben](./quickstart-create-databricks-account.md) 