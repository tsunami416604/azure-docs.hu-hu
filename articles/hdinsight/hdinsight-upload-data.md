---
title: Adatok feltöltése apache hadoop feladatok HDInsight
description: Ismerje meg, hogyan tölthet fel és érhet el adatokat az Apache Hadoop-feladatokhoz a HDInsightban az Azure klasszikus CLI, az Azure Storage Explorer, az Azure PowerShell, a Hadoop parancssor vagy a Sqoop használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdiseo17may2017
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 7eb1f7e1ce02a30f84cb520438f60fcbcfa3a965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100132"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Adatok feltöltése apache hadoop feladatok HDInsight

Az Azure HDInsight teljes funkcionalitású Hadoop elosztott fájlrendszert (HDFS) biztosít az Azure Storage és az Azure Data Lake Storage (Gen1 és Gen2) szolgáltatáson keresztül. Az Azure Storage és a Data Lake Storage Gen1 és gen2 HDFS-bővítményekként lett tervezve, hogy zökkenőmentes élményt nyújtson az ügyfeleknek. Lehetővé teszik, hogy a Hadoop ökoszisztéma összetevőinek teljes készlete közvetlenül az általa kezelt adatokon működjön. Az Azure Storage, a Data Lake Storage Gen1 és a Gen2 különálló fájlrendszerek, amelyek az adatok tárolására és az adatok számítására vannak optimalizálva. Az Azure Storage használatának előnyeiről az [Azure Storage használata a HDInsightval](hdinsight-hadoop-use-blob-storage.md), a Data Lake Storage [Gen1 használata a HDInsight segítségével](hdinsight-hadoop-use-data-lake-store.md)és a Data Lake Storage [Gen2 használata a HDInsight szolgáltatással](hdinsight-hadoop-use-data-lake-storage-gen2.md)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

A kezdés előtt vegye figyelembe az alábbi követelményeket:

* Egy Azure-beli HDInsight-fürt. További információt az [Azure HDInsight – vagy](hadoop/apache-hadoop-linux-tutorial-get-started.md) a [HDInsight-fürtök létrehozása című](hdinsight-hadoop-provision-linux-clusters.md)témakörben talál.
* A következő cikkek ismerete:
    * [Az Azure Storage használata a HDInsight segítségével](hdinsight-hadoop-use-blob-storage.md)
    * [A Data Lake Storage Gen1 használata a HDInsight segítségével](hdinsight-hadoop-use-data-lake-store.md)
    * [A Data Lake Storage Gen2 használata a HDInsight segítségével](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Adatok feltöltése az Azure Storage szolgáltatásba

## <a name="utilities"></a>Segédprogramok

A Microsoft a következő segédprogramokat biztosítja az Azure Storage szolgáltatáshoz:

| Eszköz | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-portál](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop parancs](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> A Hadoop parancs csak a HDInsight-fürtön érhető el. A parancs csak lehetővé teszi az adatok betöltését a helyi fájlrendszerből az Azure Storage-ba.  

## <a name="hadoop-command-line"></a><a id="commandline"></a>Hadoop parancssor

A Hadoop parancssor csak akkor hasznos az adatok azure storage blobban való tárolásához, ha az adatok már jelen vannak a fürtfőcsomóponton.

A Hadoop parancs használatához először [ssh vagy PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md)használatával kell csatlakoznia a fejnódához.

A csatlakozás után a következő szintaxissal tölthet fel egy fájlt a tárolóba.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Például: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Mivel a HDInsight alapértelmezett fájlrendszere az Azure Storage-ban található, a /example/data/data.txt valójában az Azure Storage-ban található. A fájlra a következőképpen is hivatkozhat:

    wasbs:///example/data/data.txt

vagy

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

A fájlokkal dolgozó többi Hadoop-parancs listáját a[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Az Apache HBase-fürtökön az adatok írásakor használt alapértelmezett blokkméret 256 KB. Bár ez jól működik hbase API-k vagy `hadoop` REST `hdfs dfs` API-k használataesetén, a vagy parancsok használatával a ~12 GB-nál nagyobb adatok írása hiba történik. További információkért tekintse meg a [tárolási kivétel a blob szakasz ebben](#storageexception) a cikkben.

## <a name="graphical-clients"></a>Grafikus ügyfelek

Számos olyan alkalmazás is létezik, amelyek grafikus felületet biztosítanak az Azure Storage-szal való munkához. Az alábbi táblázat néhány alkalmazást tartalmaz:

| Ügyfél | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio eszközök a HDInsighthoz](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea között](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [Felhő-Xplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer a Microsoft Azure-hoz](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Az Azure Storage csatlakoztatása helyi meghajtóként

Lásd: [Mount Azure Storage as Local Drive](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Feltöltés szolgáltatások használatával

### <a name="azure-data-factory"></a>Azure Data Factory

Az Azure Data Factory szolgáltatás egy teljes körűen felügyelt szolgáltatás az adattárolás, adatfeldolgozás és adatmozgatási szolgáltatások egyszerűsített, méretezhető és megbízható adatértékesítési folyamatokba való összeállításához.

|Tárolási típus|Dokumentáció|
|----|----|
|Azure Blob Storage|[Adatok másolása az Azure Blob storage-ba vagy onnan az Azure Data Factory használatával](../data-factory/connector-azure-blob-storage.md)|
|1. generációs Azure Data Lake Storage|[Adatok másolása az Azure Data Lake Storage Gen1 szolgáltatásba vagy onnan az Azure Data Factory használatával](../data-factory/connector-azure-data-lake-store.md)|
|2. generációs Azure Data Lake Storage |[Adatok betöltése az Azure Data Lake Storage Gen2 szolgáltatásba az Azure Data Factory segítségével](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a><a id="sqoop"></a>Apache Sqoop

A Sqoop egy olyan eszköz, amely a Hadoop és a relációs adatbázisok közötti adatátvitelre szolgál. Segítségével importálhat adatokat egy relációs adatbázis-kezelő rendszerből (RDBMS), például az SQL Server, a MySQL vagy az Oracle a Hadoop elosztott fájlrendszerbe (HDFS), átalakíthatja az adatokat a Hadoopban a MapReduce vagy a Hive segítségével, majd exportálhatja az adatokat RDBMS-be.

További információ: [Sqoop használata a HDInsight segítségével.](hadoop/hdinsight-use-sqoop.md)

### <a name="development-sdks"></a>Fejlesztési SDK-k

Az Azure Storage a következő programozási nyelvekből származó Azure SDK használatával is elérhető:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Az Azure SDK-k telepítéséről az [Azure letöltések](https://azure.microsoft.com/downloads/) című témakörben talál további információt.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="storage-exception-for-write-on-blob"></a><a id="storageexception"></a>Tárolási kivétel a blobíráshoz

**Jelenségek**: `hadoop` Ha `hdfs dfs` a vagy parancsokat ~12 GB vagy annál nagyobb fájlok írására használja egy HBase-fürtön, a következő hiba jelenhet meg:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Ok:** A HDInsight-fürtök hbázisa alapértelmezés szerint 256 KB blokkméretű, amikor az Azure storage-ba ír. Bár hbase API-k vagy REST API-k esetében működik, `hadoop` `hdfs dfs` hibát okoz a vagy a parancssori segédprogramok használatakor.

**Megoldás**: `fs.azure.write.request.size` Nagyobb blokkméret megadására használható. Ezt a `-D` paraméter használatával megteheti használatonként. A következő parancs egy példa ezzel `hadoop` a paraméterrel a paranccsal:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Az Apache Ambari használatával `fs.azure.write.request.size` globálisan is növelheti az értékét. Az Ambari webfelhasználói felület értékének módosítására a következő lépések módosíthatók:

1. A böngészőben nyissa meg a fürt Ambari Web felhasználói felületét. Ez `https://CLUSTERNAME.azurehdinsight.net`a `CLUSTERNAME` , hol van a fürt neve.

    Amikor a rendszer kéri, adja meg a fürt rendszergazdai nevét és jelszavát.
2. A képernyő bal oldalán válassza a **HDFS**lehetőséget, majd a **Configs (Konfigurációs fájl)** lapot.
3. A **Szűrő...** mezőbe `fs.azure.write.request.size`írja be a mezőt. Ez a mezőt és az aktuális értéket jeleníti meg az oldal közepén.
4. Módosítsa az értéket 262144-ről (256 KB) az új értékre. Például 4194304 (4 MB).

    ![Az érték módosítása az Ambari Web felhasználói felületén](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Az Ambari használatáról további információt [a HDInsight-fürtök kezelése az Apache Ambari Web felhasználói felületén című témakörben talál.](hdinsight-hadoop-manage-ambari.md)

## <a name="next-steps"></a>További lépések

Most, hogy már tisztában van azzal, hogyan juthat be az adatoka HDInsightba, olvassa el az alábbi cikkeket az elemzés elvégzéséről:

* [Ismerkedés az Azure HDInsight-mal](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop-feladatok beküldése programozott módon](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Az Apache Hive használata a HDInsight segítségével](hadoop/hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight segítségével](hadoop/hdinsight-use-pig.md)
