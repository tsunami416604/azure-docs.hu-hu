---
title: A HDFS CLI használata az Azure Data Lake Storage Gen2 szolgáltatással
description: Bevezetés a HDFS CLI for Data Lake Storage Gen2 szolgáltatásba
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 1d5313f3f0fff128dd09f9c9857b7dd9921ea4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992222"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>A HDFS CLI használata a Data Lake Storage Gen2-vel

A tárfiókban lévő adatokat ugyanúgy elérheti és kezelheti a tárfiókban, mint a [Hadoop Distributed File System (HDFS) rendszert.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) Ez a cikk néhány példát mutat be, amelyek segítenek az első lépésekben.

A HDInsight hozzáférést biztosít az elosztott tárolóhoz, amely helyileg csatlakozik a számítási csomópontokhoz. Ezt a tárolót a Hadoop által támogatott más fájlrendszerekkel közvetlenül kommunikáló rendszerhéj használatával érheti el.

A HDFS CLI-ről a [hivatalos dokumentációban](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) és a [HDFS-engedélyek útmutatójában olvashat bővebben.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Ha a HDInsight helyett Azure Databricks-t használ, és egy parancssori felület használatával szeretne együttműködni az adatokkal, a Databricks CLI segítségével kommunikálhat a Databricks fájlrendszerrel. Lásd [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>A HDFS CLI használata HDInsight Hadoop-fürttel Linuxon

Először hozzon létre [távoli hozzáférést a szolgáltatásokhoz.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services) Ha [az SSH-t](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) választja, a minta PowerShell-kód a következőképpen fog kinézni:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
A kapcsolati karakterlánc az Azure Portal HDInsight fürtpaneljének "SSH + fürtbejelentkezés" szakaszában található. Az SSH hitelesítő adatok a fürt létrehozásakor vannak megadva.

>[!IMPORTANT]
>A HDInsight-fürt számlázása a fürt létrehozása után indul el, és a fürt törlésekor leáll. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. Ha meg szeretné tudni, hogyan törölhet idát egy fürtről, olvassa el [a témáról szóló cikkünket.](../../hdinsight/hdinsight-delete-cluster.md) Azonban a Data Lake Storage Gen2 engedélyezve lévő tárfiókban tárolt adatok a HDInsight-fürt törlése után is megmaradnak.

## <a name="create-a-container"></a>Tároló létrehozása

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/

* Cserélje `<container-name>` le a helyőrzőt a tárolónak adni kívánt névre.

* Cserélje `<storage-account-name>` le a helyőrzőt a tárfiók nevére.

## <a name="get-a-list-of-files-or-directories"></a>Fájlok és könyvtárak listájának beszereznie

    hdfs dfs -ls <path>

Cserélje `<path>` le a helyőrzőt a tároló vagy tárolómappa URI-jára.

Például:`hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Könyvtár létrehozása

    hdfs dfs -mkdir [-p] <path>

Cserélje `<path>` le a helyőrzőt a gyökértároló nevére vagy a tárolón belüli mappára.

Például:`hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Fájl vagy könyvtár törlése

    hdfs dfs -rm <path>

Cserélje `<path>` le a helyőrzőt a törölni kívánt fájl vagy mappa URI-jára.

Például:`hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Fájlok és könyvtárak hozzáférés-vezérlési listáinak (Hozzáférés-vezérlési listái) megjelenítése

    hdfs dfs -getfacl [-R] <path>

Példa:

`hdfs dfs -getfacl -R /dir`

Lásd [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Fájlok és könyvtárak ACL-jainak beállítása

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Példa:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Lásd [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>A fájlok tulajdonosának módosítása

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Lásd [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Fájlok csoporttársításának módosítása

    hdfs dfs -chgrp [-R] <group> <URI>

Lásd: [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Fájlok engedélyeinek módosítása

    hdfs dfs -chmod [-R] <mode> <URI>

Lásd: [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

A parancsok teljes listáját megtekintheti az [Apache Hadoop 2.4.1 Fájlrendszer Shell Guide](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) webhelyén.

## <a name="next-steps"></a>További lépések

* [Azure Data Lake Storage Gen2 képes fiók használata az Azure Databricks-ben](./data-lake-storage-quickstart-create-databricks-account.md)

* [Tudnivalók a fájlok és könyvtárak hozzáférés-vezérlési listáiról](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
