---
title: A HDInsight Apache Hadoop-feladatok adatok feltöltése
description: Ismerje meg, hogyan tölthet fel és Apache Hadoop-feladatok a HDInsight a klasszikus Azure CLI, Azure Storage Explorer, az Azure PowerShell, a Hadoop parancssor vagy Sqoop használatával érheti el adatait.
keywords: etl hadoop, hadoop, hadoop-adatok betöltése az adatok beolvasása
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/08/2019
ms.openlocfilehash: 820bb22fee75e5c1159ba90f23b478339f6345b5
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201925"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>A HDInsight Apache Hadoop-feladatok adatok feltöltése

Az Azure HDInsight egy teljes körű Hadoop elosztott fájlrendszer (HDFS) Azure Storage és az Azure Data Lake Storage (Gen1 és Gen2) biztosít. Az Azure Storage és Data Lake Storage Gen1 és Gen2 célja HDFS bővítményként zökkenőmentes felhasználói élményt nyújtson az ügyfeleknek. A Hadoop-ökoszisztéma közvetlenül gazdagépvezérlő által kezelt adatok összetevők teljes készlete lehetővé teszik. Az Azure Storage, Data Lake Storage Gen1 és Gen2 olyan különálló fájlrendszereket, amely az adatok és a számítások az adatok tárolására vannak optimalizálva. Az előnyök, az Azure Storage információ: [az Azure Storage a HDInsight][hdinsight-storage], [használata Data Lake Storage Gen1 HDInsight](hdinsight-hadoop-use-data-lake-store.md), és [ Data Lake Storage Gen2 használata a HDInsight](../storage/blobs/data-lake-storage-use-hdi-cluster.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, vegye figyelembe az alábbi követelményeknek:

* Egy Azure-beli HDInsight-fürt. Útmutatásért lásd: [Azure HDInsight – első lépések] [ hdinsight-get-started] vagy [létre HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md).
* Az alábbi cikkekben Tudásbázis:

    - [Az Azure Storage használata a HDInsight][hdinsight-storage]
    - [HDInsight Data Lake Storage Gen1 használata](hdinsight-hadoop-use-data-lake-store.md)
    - [HDInsight Data Lake Storage Gen2 használata](../storage/blobs/data-lake-storage-use-hdi-cluster.md)  

## <a name="upload-data-to-azure-storage"></a>Az Azure Storage-adatok feltöltése

## <a name="utilities"></a>Közművek
A Microsoft az Azure Storage használatához az alábbi segédprogramokat biztosít:

| Eszköz | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop-parancs](#commandline) |✔ |✔ |✔ |


> [!NOTE]  
> A Hadoop-parancs csak a HDInsight-fürtön érhető el. A parancs csak lehetővé teszi, hogy a helyi fájlrendszerből adatok betöltése az Azure Storage-bA.  


## <a id="commandline"></a>Hadoop parancssor
A Hadoop parancssor csak hasznos adatok tárolása az Azure storage-blobba, amikor az adatok már megtalálható a fürt fő csomópontjának.

Annak érdekében, hogy a Hadoop parancsot használja, először csatlakoznia kell az átjárócsomópont használatával [SSH- vagy putty-kapcsolaton keresztül](hdinsight-hadoop-linux-use-ssh-unix.md).

A csatlakozás után az alábbi szintaxissal fájl feltöltése storage.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Például: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Mivel az alapértelmezett fájlrendszer, a HDInsight az Azure Storage-ban, /example/data.txt valójában az Azure Storage-ban. A fájlt is hivatkoznak:

    wasb:///example/data/data.txt

vagy

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Más Hadoop-parancsokat, amelyek együttműködnek a fájlok listáját lásd: [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Az Apache HBase fürtök esetén az alapértelmezett blokkméret használható, ha adatok 256 KB. Ez a jól működik a HBase API-k vagy REST API-k használatakor, miközben használata a `hadoop` vagy `hdfs dfs` parancsokat írhat adatokat ~ 12 GB-nál nagyobb hibát eredményez. További információkért lásd: a [írási a blob storage kivételt](#storageexception) szakasz ebben a cikkben.

## <a name="graphical-clients"></a>Grafikus ügyfelek
Is találhatók, amely a grafikus felületet biztosít az Azure Storage használatát több alkalmazás is. Az alábbi táblázat az néhány ezeknek az alkalmazásoknak a listája:

| Ügyfél | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [A Microsoft Visual Studio Tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer, a Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |


## <a name="mount-azure-storage-as-local-drive"></a>Az Azure Storage csatlakoztatása helyi meghajtóként
Lásd: [az Azure Storage csatlakoztatása helyi meghajtóként](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Töltse fel a szolgáltatások használata
### <a name="azure-data-factory"></a>Azure Data Factory
Az Azure Data Factory szolgáltatás nem egy teljes körűen felügyelt szolgáltatás, adatfeldolgozási adatok tárolási és adatátviteli szolgáltatások létrehozása egyszerű, méretezhető és megbízható éles adatfolyamatokat állíthat be.

|Tárolási típus|Dokumentáció|
|----|----|
|Azure Blob Storage|[Adatok másolása, vagy az Azure Blob storage-ból az Azure Data Factory használatával](../data-factory/connector-azure-blob-storage.md)|
|1. generációs Azure Data Lake Storage|[Adatok másolása, vagy az Azure Data Lake Storage Gen1 Azure Data Factory használatával](../data-factory/connector-azure-data-lake-store.md)|
|2. generációs Azure Data Lake Storage |[Adatok betöltése az Azure Data Lake Storage Gen2 az Azure Data Factoryvel](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop
A Sqoop az eszköz a Hadoop és relációs adatbázisok közötti adatátvitelhez. Adatokat importálhat egy relációsadatbázis-kezelő rendszerének (RDBMS), például SQL Server, MySQL és a Hadoop elosztott fájlrendszer (HDFS), az Oracle, az adatok átalakítása a Hadoop MapReduce- vagy Hive-, és majd exportálja az adatokat egy RDBMS be újra, használhatja azt.

További információkért lásd: [Sqoop használata a HDInsight-][hdinsight-use-sqoop].

### <a name="development-sdks"></a>Fejlesztés SDK-k
Az Azure Storage a következő programozási nyelvek a az Azure SDK-val is elérhető:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Az Azure SDK-k telepítésével kapcsolatos további információkért lásd: [Azure letöltések](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Hibaelhárítás
### <a id="storageexception"></a>Írás a blob Storage kivétel
**A jelenség**: Használatakor a `hadoop` vagy `hdfs dfs` parancsokat írhat fájlokat, amelyek ~ 12 GB vagy nagyobb, a HBase-fürtöt, felmerülhet a következő hibával:

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

**Ok**: A HDInsight-alapú HBase-fürtök alapértelmezett egy 256 KB-os blokkméret az Azure storage való írás esetén. A HBase API-k vagy REST API-k esetében működik, amíg ez hibát eredményez használatakor a `hadoop` vagy `hdfs dfs` parancssori segédeszközöket.

**Megoldás**: Használat `fs.azure.write.request.size` a nagyobb blokkméret megadásához. Ezt megteheti is használati alapon használatával a `-D` paraméter. A következő parancs használatával ezt a paramétert a `hadoop` parancsot:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Az értékét növelje `fs.azure.write.request.size` globálisan az Apache Ambari használatával. Módosítsa az Ambari webes Kezelőfelületen használható a következő lépéseket:

1. A böngészőben nyissa meg az Ambari webes felhasználói Felületet, a fürt számára. Ez a https://CLUSTERNAME.azurehdinsight.net, ahol **CLUSTERNAME** a fürt neve.

    Amikor a rendszer kéri, adja meg a rendszergazdai felhasználónevét és jelszavát a fürt számára.
2. Válassza ki a képernyő bal oldalán, **HDFS**, majd válassza ki a **Configs** fülre.
3. Az a **szűrése...**  írja be a következőt `fs.azure.write.request.size`. Ez a mező és az oldal közepén aktuális értékét jeleníti meg.
4. Új értékét módosítsa az értéket a 262144 (256 KB). Ha például 4194304 (4 MB).

![Az Ambari webes felhasználói felületen keresztül értékét képe](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Az Ambari használatával kapcsolatos további információkért lásd: [kezelése a HDInsight-fürtök az Apache Ambari webes kezelőfelületen](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>További lépések
Most, hogy megismerte, hogyan olvashatja be őket a HDInsight, olvassa el a további elemzéseket végezhet a következő cikkeket:

* [Azure HDInsight – első lépések][hdinsight-get-started]
* [Programozott módon az Apache Hadoop-feladatok elküldése][hdinsight-submit-jobs]
* [Az Apache Hive használata a HDInsight][hdinsight-use-hive]
* [Az Apache Pig használata a HDInsight][hdinsight-use-pig]

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
