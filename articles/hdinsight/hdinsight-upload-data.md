---
title: Hdinsight Hadoop-feladatokat az adatok feltöltése |} Microsoft Docs
description: Megtudhatja, hogyan töltse fel, és hozzáférési adatokat az Azure parancssori felület, a Azure Tártallózó, Azure PowerShell, a Hadoop parancssor vagy a Sqoop használatával hdinsight Hadoop-feladatokat.
keywords: etl hadoop adatok beérkezése hadoop, hadoop adatok betöltése
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 56b913ee-0f9a-4e9f-9eaf-c571f8603dd6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jgao
ms.openlocfilehash: ddb6291cdff7e2b65f54e89196c2b07dd6e4aaff
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Hadoop-feladatok adatainak feltöltése a HDInsightba

Az Azure HDInsight egy teljes körű Hadoop elosztott fájlrendszer (HDFS) keresztül Azure Storage és az Azure Data Lake Store biztosítja. Azure Storage és a Data lake Store tervezték HDFS bővítményként zökkenőmentes élményt nyújtsanak az ügyfeleinek. Ezek lehetővé teszik a Hadoop ökoszisztémájának közvetlenül az általa felügyelt adatok való működésre összetevők teljes készlete. Azure Storage és a Data Lake Store különböző fájlrendszerek, adatokat és a számítások az adatok tárolására vannak optimalizálva. A következő előnyöket Azure Storage használatával információ: [használata Azure Storage a hdinsight eszközzel] [ hdinsight-storage] és [használata Data Lake Store a hdinsight eszközzel](hdinsight-hadoop-use-data-lake-store.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, vegye figyelembe az alábbi követelményeknek:

* Egy Azure-beli HDInsight-fürt. Útmutatásért lásd: [Ismerkedés az Azure HDInsight] [ hdinsight-get-started] vagy [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).
* A következő két cikkek Tudásbázis:

    - [Az Azure Storage használata a hdinsight eszközzel][hdinsight-storage]
    - [Használjon Data Lake Store a hdinsight eszközzel](hdinsight-hadoop-use-data-lake-store.md)

## <a name="upload-data-to-azure-storage"></a>Adatok feltöltése az Azure Storage

### <a name="command-line-utilities"></a>Parancssori segédprogramok
Az alábbi segédprogramokat, ha az Azure Storage használatához a Microsoft biztosítja:

| Eszköz | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure parancssori felület][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] |✔ | |✔ |
| [Hadoop parancs](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> Az Azure CLI-t, az Azure PowerShell és az AzCopy összes használható kívül az Azure-ból, míg a Hadoop érhető el parancs csak a HDInsight-fürt. És a parancs csak a helyi fájlrendszer adatok betöltését Azure Storage.
>
>

#### <a id="xplatcli"></a>Azure CLI
Az Azure parancssori felület az Azure-szolgáltatások kezelése platformfüggetlen eszközzel. Az alábbi lépések segítségével adatokat feltölteni az Azure Storage:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Telepítse és konfigurálja az Azure parancssori felület Mac, Linux és Windows](../cli-install-nodejs.md).
2. Nyisson meg egy parancssort, bash vagy más rendszerhéj, és használja a következő hitelesítéséhez az Azure-előfizetéshez.

    ```cli
    azure login
    ```

    Amikor a rendszer kéri, adja meg a felhasználónevet és jelszót az előfizetéséhez.
3. Adja meg a következő paranccsal listát készíthet a storage-fiókok az előfizetéséhez:

    ```cli
    azure storage account list
    ```

4. Válassza ki a tárfiókot, amely tartalmazza a blob, amellyel dolgozni szeretne, majd beolvasni a fiók a kulcsot a következő paranccsal:

    ```cli
    azure storage account keys list <storage-account-name>
    ```

    Ez a parancs visszaadja a **elsődleges** és a **másodlagos** kulcsok. Másolás a **elsődleges** kulcs értékét, mert a következő lépésben lesz használható.
5. A következő paranccsal kérje le a tárfiókon belül blob tárolók listáját:

    ```cli
    azure storage container list -a <storage-account-name> -k <primary-key>
    ```

6. Fájlok feltöltését és letöltését a blobra mutató használja a következő parancsokat:

   * A fájl feltöltéséhez:

        ```cli
        azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
        ```

   * A fájl letöltésére:

        ```cli
        azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>
        ```
    
> [!NOTE]
> Ha mindig ugyanazt a tárfiókot, állítsa be az alábbi környezeti változókat a fiók megadása helyett, és minden parancs kulcs:
>
> * **AZURE\_tárolási\_fiók**: A tárfiók neve
> * **AZURE\_tárolási\_hozzáférés\_kulcs**: A tárfiók kulcsa
>
>

#### <a id="powershell"></a>Azure PowerShell
Az Azure PowerShell egy parancsfájl-kezelési környezet, melyekkel automatizálhatja a központi telepítési és felügyeleti a munkaterhelések az Azure-ban, és szabályozhatja. A munkaállomás Azure PowerShell futtatásához konfigurálásával kapcsolatos további információkért lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Azure Storage egy helyi fájl feltöltése**

1. Nyissa meg az Azure PowerShell-konzolt, útmutatását [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview).
2. Az első öt változók értékeinek beállítása a következő parancsfájlban:

    ```powershell
    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $fileName ="<LocalFileName>"
    $blobName = "<BlobName>"

    # Get the storage account key
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    # Create the storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    # Copy the file from local workstation to the Blob container
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
    ```

3. Illessze be a parancsfájl futtatásához, a fájl másolása az Azure PowerShell-konzolon.

Például a PowerShell-parancsfájlok működéséhez a hdinsight eszközzel, lásd: [a HDInsight tools](https://github.com/blackmist/hdinsight-tools).

#### <a id="azcopy"></a>AzCopy
AzCopy parancssori eszköz, amely arra tervezték, hogy egyszerűbbé tehető adatátvitel esetében bejövő és kimenő egy Azure Storage-fiók. Önálló eszközként használható, vagy az eszköz már meglévő alkalmazás tartalmaznia. [Töltse le az AzCopy][azure-azcopy-download].

Az AzCopy szintaxisa:

```command
AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]
```

További információkért lásd: [AzCopy - feltöltése/letöltése fájlok Azure Blobokra vonatkozó][azure-azcopy].

A Linux Preview-ban Azcopy érhető el.  Lásd: [AzCopy lévő Linux előzetes bejelentése](https://blogs.msdn.microsoft.com/windowsazurestorage/2017/05/16/announcing-azcopy-on-linux-preview/).

#### <a id="commandline"></a>Hadoop parancssor
A Hadoop parancssor csak az adatok tárolására az Azure storage-blob, amikor az adatok már megtalálható az átjárócsomóponthoz hasznos.

A Hadoop parancs használatához először csatlakoznia kell a headnode, az alábbi módszerek egyikének használatával:

* **Windows-alapú HDInsight**: [csatlakozzon a távoli asztali kapcsolattal](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **Linux-alapú HDInsight**: protokoll használatával kapcsolódó levelezőprogramokkal [SSH vagy PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

A csatlakozás után a következő szintaxissal fájl feltöltése tárolás.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Például: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Mivel az alapértelmezett fájlrendszer a HDInsight az Azure Storage, /example/data.txt valójában az Azure Storage. A fájlt is vonatkozik:

    wasb:///example/data/data.txt

vagy

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Egyéb Hadoop parancsok, amelyek a fájlok listája: [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]
> A HBase fürtök esetén az alapértelmezett blokkméret használható, ha a adatainak írása 256 KB. Amíg ez a HBase API-k és a REST API-k használata remekül működik, használja a `hadoop` vagy `hdfs dfs` parancsok írása ~ 12 GB-nál nagyobb adatok hibát eredményez. További információkért lásd: a [írás a blob storage-hibát](#storageexception) című részben.
>
>

### <a name="graphical-clients"></a>Grafikus ügyfelek
Van még több alkalmazás, amely a grafikus felületet biztosít az Azure Storage. Az alábbi táblázat néhány ezeknek az alkalmazásoknak a listáját:

| Ügyfél | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [A Microsoft Visual Studio Tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](http://storageexplorer.com/) |✔ |✔ |✔ |
| [Felhőalapú tárolás Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

#### <a name="visual-studio-tools-for-hdinsight"></a>Visual Studio Tools for HDInsight
További információkért lásd: [Navigálás a kapcsolt erőforrásokban](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources).

#### <a id="storageexplorer"></a>Azure Storage Explorer
*Az Azure Tártallózó* hasznos eszköz a Kérem, és módosítsa az adatokat a BLOB. Ingyenes, nyissa meg a forrás eszköz, amely letölthető [ http://storageexplorer.com/ ](http://storageexplorer.com/). A forráskódot, valamint a hivatkozás érhető el.

Az eszköz használata előtt ismernie kell az Azure storage-fiók nevét és a fiók kulcs. Az alábbi információk eljussanak vonatkozó utasításokért lásd: a "hogyan: nézet, a másolás és a hívóbetűk újragenerálása tárolási" szakasza [létrehozása, kezelése vagy törlése a tárfiók][azure-create-storage-account].

1. Az Azure Storage-kezelő futtatása. Ha első alkalommal a Tártallózó futtatása, akkor kéri a **tá_rolási fióknév** és **Tárfiók kulcsa**. Futtatásakor az előtt, használja a **Hozzáadás** gombra kattintva adja hozzá egy új tárfiók neve és kulcsa.

    Adja meg a nevét, és a tárfiók a HDInsight-fürt által használt kulcs, és válassza ki **MENTÉS és MEGNYITÁS**.

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. A bal oldali interfész tárolók listájáról kattintson a tárolót a HDInsight-fürthöz társított nevét. Alapértelmezés szerint azt a HDInsight-fürt neve, de eltérő, ha a fürt létrehozásakor adja meg az adott név lehet.
3. Az eszköztáron válassza ki a Feltöltés ikonra.

    ![A feltöltés ikonja kiemelve eszköztár](./media/hdinsight-upload-data/toolbar.png)
4. Adjon meg egy fájlt feltölteni, majd **nyitott**. Amikor a rendszer kéri, válassza ki a **feltöltése** feltölteni a fájlt a tároló gyökeréhez. Ha szeretne feltölteni a fájlt egy adott helyre, adja meg az elérési utat a **cél** mezőbe, majd válassza ki **feltöltése**.

    ![Fájl feltöltése párbeszédpanel](./media/hdinsight-upload-data/fileupload.png)

    Ha a fájl befejezte a feltöltést, használhatja a HDInsight-fürt feladatokból.

### <a name="mount-azure-storage-as-local-drive"></a>Helyi meghajtó, az Azure Storage csatlakoztatása
Lásd: [csatlakoztatási Azure Storage helyi meghajtóként](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

### <a name="upload-using-services"></a>Töltse fel a szolgáltatások használata
#### <a name="azure-data-factory"></a>Azure Data Factory
Az Azure Data Factory szolgáltatásnak egy teljes körűen felügyelt szolgáltatás zökkenőmentes, méretezhető és megbízható éles adatcsatornák a tárolás, az adatfeldolgozás és adatok adatátviteli szolgáltatások létrehozására.

Az Azure Data Factory használható az Azure Storage áthelyezni az adatokat, vagy hozzon létre adatok folyamatok, amelyek közvetlenül a HDInsight-szolgáltatások, mint a Hive és a Pig használata.

További információkért lásd: a [Azure Data Factory dokumentáció](https://azure.microsoft.com/documentation/services/data-factory/).

#### <a id="sqoop"></a>Apache Sqoop
Sqoop az eszköz a Hadoop és a relációs adatbázisok közötti adattovábbításra. Adatok importálása egy relációs adatbázis-kezelő rendszerének (RDBMS), például az SQL Server, MySQL, vagy a Hadoop elosztott fájlrendszer (HDFS), az Oracle átalakíthatja az adatokat a Hadoop MapReduce vagy a Hive, majd az adatok exportálása vissza egy RDBMS használhatja.

További információkért lásd: [és a HDInsight együttes használata Sqoop][hdinsight-use-sqoop].

### <a name="development-sdks"></a>Fejlesztési SDK-k
Az Azure Storage használata az Azure SDK-t a következő programozási nyelven is elérhető:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Az Azure SDK-k telepítésével kapcsolatos további információkért lásd: [Azure tölti le.](https://azure.microsoft.com/downloads/)

### <a name="troubleshooting"></a>Hibaelhárítás
#### <a id="storageexception"></a>Írás a blob Storage-hibát
**A jelenség**: használata esetén a `hadoop` vagy `hdfs dfs` parancsok fájloknak, amelyek ~ 12 GB vagy nagyobb a HBase-fürtöt, felmerülhet a következő hiba:

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

**OK**: a HBase a HDInsight-fürtök alapértelmezett egy 256 KB-os blokkméretet az Azure storage írása közben. A HBase API-k vagy a REST API-k működik, amíg azt hibát eredményez használata esetén a `hadoop` vagy `hdfs dfs` parancssori segédprogramok.

**Megoldási**: használata `fs.azure.write.request.size` adhatja meg a nagyobb blokkméret. Ehhez a használati alapon használatával a `-D` paraméter. A következő paranccsal Íme egy használja ezt a paramétert a `hadoop` parancs:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Növelje a értékének `fs.azure.write.request.size` globálisan Ambari használatával. Az alábbi lépések segítségével módosítsa az értéket a Ambari webes felhasználói felületén:

1. A böngészőben nyissa meg az Ambari webes felhasználói felületén, a fürt számára. Ez az https://CLUSTERNAME.azurehdinsight.net, ahol **CLUSTERNAME** a fürt neve.

    Amikor a rendszer kéri, adja meg a rendszergazda nevét és jelszavát a fürt.
2. Válassza ki a képernyő bal oldalán, **HDFS**, majd válassza ki a **Configs** fülre.
3. Az a **szűrő...**  mezőbe írja be `fs.azure.write.request.size`. Ez a mező és a lap közepén aktuális értékét jeleníti meg.
4. Módosítsa az értéket a 262144 (256KB) az új érték. Például 4194304 (4MB).

![Az Ambari webes felhasználói felületén keresztül érték módosítása képe](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

További információ az Ambari használatával, lásd: [kezelése HDInsight-fürtök az Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>További lépések
Most, hogy megismerkedett az adatok lekérése a HDInsight, olvassa el az elemzést további információt a következő cikkeket:

* [Azure HDInsight – első lépések][hdinsight-get-started]
* [Hadoop-feladatokat programozott módon küldhet][hdinsight-submit-jobs]
* [A Hive használata a HDInsightban][hdinsight-use-hive]
* [A Pig használata a HDInsightban][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]:../storage/common/storage-create-storage-account.md
[azure-azcopy-download]:../storage/common/storage-use-azcopy.md
[azure-azcopy]:../storage/common/storage-use-azcopy.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
