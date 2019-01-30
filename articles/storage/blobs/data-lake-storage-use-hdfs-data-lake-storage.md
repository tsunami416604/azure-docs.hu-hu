---
title: A HDFS-CLI-vel rendelkező Azure Data Lake Storage Gen2 előzetes verzió
description: Bevezetés a HDFS CLI a Data Lake Storage Gen2 előzetes verzió
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 2691346433b3cc29b22446152ea797cefb02a380
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241270"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>A HDFS parancssori felület használatával a Data Lake Storage Gen2

Az Azure Data Lake Storage Gen2 előzetes verzió lehetővé teszi, hogy a kezelése és az adatok eléréséhez, ugyanúgy, mint az egy [Hadoop elosztott fájlrendszer (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). E van csatlakoztatva, vagy egy Azure Databricks használatával egy Azure Storage-fiókban tárolt adatokon végrehajtandó analytics Apache Spark-feladatok futtatása egy HDInsight-fürt, parancssori felület (CLI) segítségével lekérése és módosítására a betöltött adatokra.

## <a name="hdfs-cli-with-hdinsight"></a>HDFS CLI-t a HDInsight

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer elérhető lesz a rendszerhéj, amely közvetlenül kommunikál a HDFS-és más fájlrendszereket, amely támogatja a Hadoop használatával. Az alábbiakban a gyakran használt parancsok és a hasznos forrásokra mutató hivatkozásokat.

>[!IMPORTANT]
>HDInsight-fürt számlázása elindul, amikor egy fürt jön létre, és leállítja a fürt törlésekor. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. Ismerje meg, hogyan törölheti a fürtöt, tekintse meg a [foglalkozó témakör](../../hdinsight/hdinsight-delete-cluster.md). Azonban a Data Lake Storage Gen2 engedélyezve van a storage-fiókban tárolt adatok fenntartása, még akkor is egy HDInsight-fürt törlése után.

### <a name="get-a-list-of-files-or-directories"></a>Fájlok vagy könyvtárak listájának lekérése

    hdfs dfs -ls <args>

### <a name="create-a-directory"></a>Könyvtár létrehozása

    hdfs dfs -mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>Egy fájl vagy könyvtár törlése

    hdfs dfs -rm [-skipTrash] URI [URI ...]

### <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>A HDFS CLI használata egy HDInsight Hadoop-fürt Linux rendszeren

Első lépésként létesíteni [távoli szolgáltatásokhoz való hozzáférés](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Ha kiválasztjuk az [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) ehhez módon néz ki: a minta PowerShell-kóddal:

```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
A kapcsolati karakterlánc található a "SSH + fürtbe bejelentkezési" szakaszában a HDInsight-fürt panelén, az Azure Portalon. Az SSH hitelesítő adatok a fürt létrehozása idején voltak megadva.

A parancssori felület HDFS további információkért lásd: a [dokumentációs](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) és a [HDFS-engedélyek útmutatója](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html). A hozzáférés-vezérlési listák a Databricksben kapcsolatos további információkért tekintse meg a [titkos kulcsok parancssori felület](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#secrets-cli). 

## <a name="hdfs-cli-with-azure-databricks"></a>HDFS CLI-t az Azure Databricks

A Databricks a Databricks REST API-ra épülő, egy könnyen használható parancssori felület biztosít. A nyílt forráskódú projekt lévő üzemeltetett [GitHub](https://github.com/databricks/databricks-cli). Az alábbiakban a gyakran használt parancsok.

### <a name="get-a-list-of-files-or-directories"></a>Fájlok vagy könyvtárak listájának lekérése

    dbfs ls [-l]

### <a name="create-a-directory"></a>Könyvtár létrehozása

    dbfs mkdirs

### <a name="delete-a-file"></a>Fájl törlése

    dbfs rm [-r]

Egy másik módszer a Databricks implementálására notebookok. Míg a jegyzetfüzet elsődleges nyelv, a nyelvi varázsparancs % nyelv egy cella elején megadásával kombinálhatja nyelveket. Pontosabban % sh lehetővé teszi rendszerhéj kód végrehajtása a jegyzetfüzet lényegében ugyanúgy a HDInsight-példában a cikk korábbi részében.

### <a name="get-a-list-of-files-or-directories"></a>Fájlok vagy könyvtárak listájának lekérése

    %sh ls <args>

### <a name="create-a-directory"></a>Könyvtár létrehozása

    %sh mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>Egy fájl vagy könyvtár törlése

    %sh rm [-skipTrash] URI [URI ...]

Az Azure Databricks a Spark-fürt indítás után az új notebook fog létrehozni. A notebook példaszkript a következőképpen fog kinézni:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder
    #Get the ACL of the newly created directory.
    hdfs dfs -getfacl /samplefolder

A Databricks parancssori felület további információkért lásd: a [dokumentációs](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Notebookok további információkért lásd: a [notebookok](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) a dokumentáció része.

## <a name="set-file-and-directory-level-permissions"></a>Fájl- és oszlopszintű engedélyek beállítása

Állítsa be, és hozzáférési engedélyek beolvasása a fájl és könyvtár szintjén. Íme néhány parancsot a kezdéshez. 

Fájl- és az Azure Data Lake Gen2 FileSystem szintű engedélyekkel kapcsolatos további tudnivalókért lásd: [hozzáférés-vezérlés az Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>A hozzáférés-vezérlési listák (ACL) található fájlok és könyvtárak megjelenítése

    hdfs dfs -getfacl [-R] <path>

Példa:

`hdfs dfs -getfacl -R /dir`

Lásd: [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

### <a name="set-acls-of-files-and-directories"></a>A fájlok és mappák ACL-ek beállítása

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Példa:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Lásd: [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

### <a name="change-the-owner-of-files"></a>Fájlok tulajdonosának megváltoztatása

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Lásd: [tulajdonost](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

### <a name="change-group-association-of-files"></a>Csoport-társítás módosítása a fájlok

    hdfs dfs -chgrp [-R] <group> <URI>

Lásd: [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

### <a name="change-the-permissions-of-files"></a>A fájlok engedélyeinek módosítása

    hdfs dfs -chmod [-R] <mode> <URI>

Lásd: [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

A parancsok teljes listáját megtekintheti a [Apache Hadoop 2.4.1 File System rendszerhéj útmutató](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) webhelyén.

## <a name="next-steps"></a>További lépések

[Az Azure Data Lake Storage Gen2 képes a fiókot használják az Azure Databricksben](./data-lake-storage-quickstart-create-databricks-account.md) 
