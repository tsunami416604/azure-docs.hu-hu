---
title: A HDFS CLI használata az Azure Data Lake Storage Gen2
description: Bevezetés a Data Lake Storage Gen2 a HDFS-CLI
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d1c9eff08a7b9cc50ccdca4ce798ac4d0f3d35f2
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678020"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>A HDFS parancssori felület használatával a Data Lake Storage Gen2

Elérheti és az adatok kezelése a storage-fiókban a parancssori felület használatával, ugyanúgy, mint az egy [Hadoop elosztott fájlrendszer (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Ez a cikk néhány példa, amelyekkel a kezdéshez.

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer elérheti a rendszerhéj, amely közvetlenül kommunikál a HDFS-és a más fájlrendszereket, amely támogatja a Hadoop használatával.

A parancssori felület HDFS további információkért lásd: a [dokumentációs](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) és a [HDFS-engedélyek útmutatója](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Az Azure Databricks HDInsight helyett használja, és felhasználhatja meglévő adatait a parancssori felület használatával szeretne, ha a Databricks parancssori felület segítségével kezelheti a Databricks fájlrendszerrel. Lásd: [a Databricks parancssori felület](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>A HDFS CLI használata egy HDInsight Hadoop-fürt Linux rendszeren

Első lépésként létesíteni [távoli szolgáltatásokhoz való hozzáférés](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Ha kiválasztjuk az [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) ehhez módon néz ki: a minta PowerShell-kóddal:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
A kapcsolati karakterlánc található a "SSH + fürtbe bejelentkezési" szakaszában a HDInsight-fürt panelén, az Azure Portalon. Az SSH hitelesítő adatok a fürt létrehozása idején voltak megadva.

>[!IMPORTANT]
>HDInsight-fürt számlázása elindul, amikor egy fürt jön létre, és leállítja a fürt törlésekor. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. Ismerje meg, hogyan törölheti a fürtöt, tekintse meg a [foglalkozó témakör](../../hdinsight/hdinsight-delete-cluster.md). Azonban a Data Lake Storage Gen2 engedélyezve van a storage-fiókban tárolt adatok fenntartása, még akkor is egy HDInsight-fürt törlése után.

## <a name="create-a-file-system"></a>Hozzon létre egy fájlrendszer

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* Cserélje le a `<file-system-name>` kíván adni a fájlrendszer neve helyőrzőt.

* Cserélje le a `<storage-account-name>` helyőrzőt a tárfiók nevére.

## <a name="get-a-list-of-files-or-directories"></a>Fájlok vagy könyvtárak listájának lekérése

    hdfs dfs -ls <path>

Cserélje le a `<path>` URI-ját a fájlrendszerből vagy fájlrendszermappán helyőrzőt.

Például:`hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Könyvtár létrehozása

    hdfs dfs -mkdir [-p] <path>

Cserélje le a `<path>` helyőrzőt a legfelső szintű fájlrendszer neve vagy a fájlrendszer mappában.

Például:`hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Egy fájl vagy könyvtár törlése

    hdfs dfs -rm <path>

Cserélje le a `<path>` helyőrzőt az URI-ját a fájl vagy mappa, amelyet törölni szeretne.

Például:`hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>A hozzáférés-vezérlési listák (ACL) található fájlok és könyvtárak megjelenítése

    hdfs dfs -getfacl [-R] <path>

Példa:

`hdfs dfs -getfacl -R /dir`

Lásd: [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>A fájlok és mappák ACL-ek beállítása

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Példa:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Lásd: [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Fájlok tulajdonosának megváltoztatása

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Lásd: [tulajdonost](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Csoport-társítás módosítása a fájlok

    hdfs dfs -chgrp [-R] <group> <URI>

Lásd: [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>A fájlok engedélyeinek módosítása

    hdfs dfs -chmod [-R] <mode> <URI>

Lásd: [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

A parancsok teljes listáját megtekintheti a [Apache Hadoop 2.4.1 File System rendszerhéj útmutató](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) webhelyén.

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Storage Gen2 képes a fiókot használják az Azure Databricksben](./data-lake-storage-quickstart-create-databricks-account.md)

* [További tudnivalók a hozzáférés-vezérlési listák a fájlok és könyvtárak](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
