---
title: Adatok feltöltése Apache Hadoop feladatok számára a HDInsight-ben
description: Megtudhatja, hogyan tölthet fel és férhet hozzá Apache Hadoop feladatok adataihoz a HDInsight-ben a klasszikus Azure CLI, a Azure Storage Explorer, a Azure PowerShell, a Hadoop parancssori felület vagy a Sqoop használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdiseo17may2017
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 7eb1f7e1ce02a30f84cb520438f60fcbcfa3a965
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100132"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Adatok feltöltése Apache Hadoop feladatok számára a HDInsight-ben

Az Azure HDInsight egy teljes funkcionalitású Hadoop elosztott fájlrendszert (HDFS) biztosít az Azure Storage és a Azure Data Lake Storage (Gen1 és Gen2) szolgáltatáson keresztül. Az Azure Storage és a Data Lake Storage Gen1 és a Gen2 HDFS-bővítményként lett kialakítva, így zökkenőmentes élményt biztosítanak az ügyfeleknek. Lehetővé teszik, hogy a Hadoop-ökoszisztémában található összetevők teljes készlete közvetlenül az általa kezelt adaton működjön. Az Azure Storage, a Data Lake Storage Gen1 és a Gen2 különálló fájlrendszerek, amelyek az adatok tárolására és számítására vannak optimalizálva. További információ az Azure Storage használatának előnyeiről: az [Azure Storage használata a HDInsight](hdinsight-hadoop-use-blob-storage.md), a [Data Lake Storage Gen1 használata a HDInsight](hdinsight-hadoop-use-data-lake-store.md)és a [Data Lake Storage Gen2 használata a HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt vegye figyelembe a következő követelményeket:

* Egy Azure-beli HDInsight-fürt. Útmutatásért lásd: Ismerkedés [Az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) vagy a [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).
* A következő cikkek ismerete:
    * [Az Azure Storage és a HDInsight használata](hdinsight-hadoop-use-blob-storage.md)
    * [Data Lake Storage Gen1 használata a HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Data Lake Storage Gen2 használata a HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Adatok feltöltése az Azure Storage-ba

## <a name="utilities"></a>Közművek

A Microsoft az alábbi segédprogramokat biztosítja az Azure Storage-hoz való együttműködéshez:

| Eszköz | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portalra](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop parancs](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> A Hadoop parancs csak a HDInsight-fürtön érhető el. A parancs csak a helyi fájlrendszerből származó adatok Azure Storage szolgáltatásba való betöltését teszi lehetővé.  

## <a id="commandline"></a>Hadoop parancssor

A Hadoop parancssor csak akkor hasznos az Azure Storage-blobba történő adattároláshoz, ha az adat már szerepel a fürt fő csomópontján.

A Hadoop parancs használatához először [SSH vagy Putty](hdinsight-hadoop-linux-use-ssh-unix.md)használatával kell csatlakoznia a átjárócsomóponthoz.

A csatlakozás után a következő szintaxissal tölthet fel egy fájlt a tárolóba.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Például: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Mivel a HDInsight alapértelmezett fájlrendszere Az Azure Storage-ban található, a/example/Data/Data.txt valójában az Azure Storage-ban érhető el. A fájlt a következő néven is megtekintheti:

    wasbs:///example/data/data.txt

vagy

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

A fájlokkal használható egyéb Hadoop-parancsok listáját itt tekintheti meg: [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Az Apache HBase-fürtökön az adatíráskor használt alapértelmezett blokk mérete 256 KB. Habár ez jól működik a HBase API-k vagy a REST API-k használatakor, a `hadoop` vagy a `hdfs dfs` parancs használatával a ~ 12 GB-nál nagyobb adatírások hibát eredményeznek. További információ: [Storage-kivétel a Blobok számára](#storageexception) című rész ebben a cikkben.

## <a name="graphical-clients"></a>Grafikus ügyfelek

Több alkalmazás is rendelkezésre áll, amelyek grafikus felületet biztosítanak az Azure Storage-ban való használathoz. Az alábbi táblázat néhány ilyen alkalmazást felsorol:

| Ügyfél | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [HDInsight készült Microsoft Visual Studio-eszközök](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [A Microsoft Azure törpemálna Explorer](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Az Azure Storage csatlakoztatása helyi meghajtóként

Lásd: [Az Azure Storage csatlakoztatása helyi meghajtóként](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Feltöltés a szolgáltatásokkal

### <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory szolgáltatás egy teljes körűen felügyelt szolgáltatás, amely az adattárolási, adatfeldolgozási és adatátviteli szolgáltatásokat az egyszerűsített, méretezhető és megbízható adatéles folyamatokra alakítja.

|Tárhely típusa|Dokumentáció|
|----|----|
|Azure Blob Storage|[Adatok másolása az Azure Blob Storage-ba vagy onnan az Azure Data Factory használatával](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Adatok másolása Azure Data Lake Storage Gen1ba vagy onnan a Azure Data Factory használatával](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Betöltés az Azure Data Lake Storage Gen2ba Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop

A Sqoop olyan eszköz, amely a Hadoop és a kapcsolati adatbázisok közötti adatátvitelre szolgál. Felhasználhatja az adatok importálását egy, a Hadoop elosztott fájlrendszerbe (HDFS) tartozó, a RDBMS-ből, például a SQL Server, a MySQL-ből vagy az Oracle-ből, majd a Hadoop-ben lévő adatok MapReduce vagy struktúrával való exportálásával, majd az adatok RDBMS való exportálásával.

További információ: [Sqoop használata a HDInsight](hadoop/hdinsight-use-sqoop.md)-mel.

### <a name="development-sdks"></a>Fejlesztői SDK-k

Az Azure Storage egy Azure SDK-val is elérhető a következő programozási nyelvekből:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Az Azure SDK-k telepítésével kapcsolatos további információkért lásd: [Azure-letöltések](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Hibakeresés

### <a id="storageexception"></a>Tárolási kivétel a blobon való íráshoz

**Tünetek**: Ha a HBase-fürtön legalább 12 GB-nyi vagy nagyobb méretű fájlokat szeretne írni `hadoop` vagy `hdfs dfs` paranccsal, a következő hibaüzenet jelenhet meg:

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

**OK**: az Azure Storage-ba való íráskor a HDInsight-fürtök HBase alapértelmezett értéke 256 kb. Habár HBase API-k vagy REST API-k esetében is működik, hibaüzenetet eredményez a `hadoop` vagy `hdfs dfs` parancssori segédprogramok használatakor.

**Megoldás**: a `fs.azure.write.request.size` használata nagyobb blokk méretének megadásához. Ezt igény szerint használhatja a `-D` paraméter használatával. A következő parancs egy példa erre a paraméterre a `hadoop` parancs használatával:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Az Apache Ambari használatával az `fs.azure.write.request.size` globális értékét is növelheti. Az alábbi lépéseket követve módosíthatja a Ambari webes felhasználói felületének értékét:

1. A böngészőben nyissa meg a Ambari webes felhasználói felületét a fürthöz. Ez `https://CLUSTERNAME.azurehdinsight.net`, ahol a `CLUSTERNAME` a fürt neve.

    Ha a rendszer kéri, adja meg a fürthöz tartozó rendszergazdai nevet és jelszót.
2. A képernyő bal oldalán válassza a **HDFS**lehetőséget, majd válassza a **konfigurációk** fület.
3. A **szűrő...** mezőbe írja be a `fs.azure.write.request.size`értéket. Ekkor megjelenik a mező és az aktuális érték az oldal közepén.
4. Módosítsa a 262144 (256 KB) értéket az új értékre. Például 4194304 (4 MB).

    ![Az érték Ambari webes felhasználói felületén keresztüli módosításának képe](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

A Ambari használatával kapcsolatos további információkért lásd: [HDInsight-fürtök kezelése az Apache Ambari webes felhasználói felületén](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Következő lépések

Most, hogy megértette, hogyan szerezhet be információkat a HDInsight-ba, olvassa el a következő cikkeket az elemzés végrehajtásához:

* [Ismerkedés az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop feladatok programozott beküldése](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Az Apache Pig és a HDInsight használata](hadoop/hdinsight-use-pig.md)
