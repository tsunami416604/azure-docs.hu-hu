---
title: Hadoop-feladatok adatainak feltöltése a HDInsightba
description: Ismerje meg, hogyan töltse fel, és a hozzáférési adatokat a klasszikus Azure CLI, Azure Storage Explorer, az Azure PowerShell, a Hadoop parancssor vagy Sqoop használatával HDInsight Hadoop-feladatok.
keywords: etl hadoop, hadoop, hadoop-adatok betöltése az adatok beolvasása
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.author: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 44aaccee436011bd7d27bec87515fde0e898732e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985979"
---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Hadoop-feladatok adatainak feltöltése a HDInsightba

Az Azure HDInsight egy teljes körű Hadoop elosztott fájlrendszer (HDFS) keresztül az Azure Storage és az Azure Data Lake Store biztosítja. Az Azure Storage és Data lake Store célja HDFS bővítményeként zökkenőmentes felhasználói élményt nyújtson az ügyfeleknek. A Hadoop-ökoszisztéma közvetlenül gazdagépvezérlő által kezelt adatok összetevők teljes készlete lehetővé teszik. Az Azure Storage és Data Lake Store olyan különálló fájlrendszereket, amely az adatok és a számítások az adatok tárolására vannak optimalizálva. Az előnyök, az Azure Storage információ: [az Azure Storage a HDInsight] [ hdinsight-storage] és [használata Data Lake Store a HDInsight](hdinsight-hadoop-use-data-lake-store.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, vegye figyelembe az alábbi követelményeknek:

* Egy Azure-beli HDInsight-fürt. Útmutatásért lásd: [Azure HDInsight – első lépések] [ hdinsight-get-started] vagy [létre HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md).
* A következő két cikkek Tudásbázis:

    - [Az Azure Storage használata a HDInsight][hdinsight-storage]
    - [Használata Data Lake Store a HDInsight](hdinsight-hadoop-use-data-lake-store.md)

## <a name="upload-data-to-azure-storage"></a>Az Azure Storage-adatok feltöltése

### <a name="command-line-utilities"></a>Parancssori segédprogramok
A Microsoft az Azure Storage használatához az alábbi segédprogramokat biztosít:

| Eszköz | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Az Azure klasszikus parancssori felület][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell-lel][azure-powershell] | | |✔ |
| [Az AzCopy][azure-azcopy] |✔ | |✔ |
| [Hadoop-parancs](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> Míg a klasszikus Azure CLI, az Azure PowerShell-lel és az AzCopy az összes használható Azure-on kívülről, a Hadoop-parancs érhető el csak a HDInsight-fürtön. Így a parancs csak a helyi fájlrendszerből adatok betöltése az Azure Storage-bA.
>
>

#### <a id="xplatcli"></a>Az Azure klasszikus parancssori felület
A klasszikus Azure-CLI egy platformfüggetlen eszköz, amellyel az Azure-szolgáltatások kezelését. Használja az alábbi lépéseket az Azure Storage-ba való adatfeltöltést:

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. [Telepítse és konfigurálja az Azure klasszikus parancssori felület Mac, Linux és Windows](../cli-install-nodejs.md).
2. Nyisson meg egy parancssort, bash vagy más rendszerhéj, és használja a következő hitelesítéséhez az Azure-előfizetéshez.

    ```cli
    azure login
    ```

    Amikor a rendszer kéri, adja meg a felhasználónevet és jelszót az előfizetéshez.
3. Adja meg a storage-fiókot az előfizetés listáját a következő parancsot:

    ```cli
    azure storage account list
    ```

4. Válassza ki a tárfiókot, amely tartalmazza a blob dolgozni szeretne, majd ehhez a fiókhoz a kulcs lekérése a következő paranccsal:

    ```cli
    azure storage account keys list <storage-account-name>
    ```

    Ez a parancs visszaadja a **elsődleges** és a **másodlagos** kulcsok. Másolás a **elsődleges** kulcs értékét, mert a következő lépésben lesz használható.
5. A következő parancs használatával a tárfiókban található blob-tárolók listájának lekéréséhez:

    ```cli
    azure storage container list -a <storage-account-name> -k <primary-key>
    ```

6. Fájlok feltöltését és letöltését a blobra mutató a következő parancsok használatával:

   * A fájl feltöltéséhez:

        ```cli
        azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
        ```

   * Fájlok letöltéséhez:

        ```cli
        azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>
        ```
    
> [!NOTE]
> Ha mindig ugyanazt a tárfiókot, a következő környezeti változókat a fiók megadása helyett, és minden parancshoz kulcs:
>
> * **AZURE\_tárolási\_fiók**: A tárfiók neve
> * **AZURE\_tárolási\_hozzáférés\_kulcs**: A tárfiók kulcsa
>
>

#### <a id="powershell"></a>Azure PowerShell
Az Azure PowerShell-parancsfájl-kezelési környezet, amellyel szabályozhatja és automatizálhatja az üzembe helyezés és a számítási feladatok Azure-beli felügyeleti. A munkaállomáson futtassa az Azure PowerShell-lel való konfigurálásával kapcsolatos további információkért lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Az Azure Storage egy helyi fájl feltöltése**

1. Nyissa meg az Azure PowerShell-konzolt, a utasításai [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview).
2. Az első öt változók értékeit, állítsa be a következő parancsfájlt:

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

Például tekintse meg a PowerShell-parancsfájlok használata a HDInsight, a létrehozott [HDInsight eszközök](https://github.com/blackmist/hdinsight-tools).

#### <a id="azcopy"></a>Az AzCopy
Az AzCopy parancssori eszköz, amely egyszerűsíti az adatok áthelyezése az Azure Storage-fiók-a feladat. Önálló eszközként használható, vagy az eszköz már meglévő alkalmazás építhet be. [Töltse le az AzCopy][azure-azcopy-download].

Az AzCopy szintaxisa:

```command
AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]
```

További információkért lásd: [AzCopy - feltöltése/letöltése fájlokat az Azure-Blobok][azure-azcopy].

Az Azcopy a Linux előzetes verziójában érhető el.  Lásd: [AzCopy a Linuxos előzetes bejelentése](https://blogs.msdn.microsoft.com/windowsazurestorage/2017/05/16/announcing-azcopy-on-linux-preview/).

#### <a id="commandline"></a>Hadoop parancssor
A Hadoop parancssor csak hasznos adatok tárolása az Azure storage-blobba, amikor az adatok már megtalálható a fürt fő csomópontjának.

Annak érdekében, hogy a Hadoop parancsot használja, először csatlakoznia kell az átjárócsomóponthoz, a következő módszerek egyikével:

* **Windows-alapú HDInsight**: [csatlakoztatása a távoli asztal használatával](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **Linux-alapú HDInsight**: csatlakozás használatával [SSH- vagy putty-kapcsolaton keresztül](hdinsight-hadoop-linux-use-ssh-unix.md).

A csatlakozás után az alábbi szintaxissal fájl feltöltése storage.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Például: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Mivel az alapértelmezett fájlrendszer, a HDInsight az Azure Storage-ban, /example/data.txt valójában az Azure Storage-ban. A fájlt is hivatkoznak:

    wasb:///example/data/data.txt

vagy

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Más Hadoop-parancsokat, amelyek együttműködnek a fájlok listáját lásd: [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]
> A HBase fürtök esetén az alapértelmezett blokkméret használható, ha adatok 256 KB. Ez a jól működik a HBase API-k vagy REST API-k használatakor, miközben használata a `hadoop` vagy `hdfs dfs` parancsokat írhat adatokat ~ 12 GB-nál nagyobb hibát eredményez. További információkért lásd: a [írási a blob storage kivételt](#storageexception) szakasz ebben a cikkben.
>
>

### <a name="graphical-clients"></a>Grafikus ügyfelek
Is találhatók, amely a grafikus felületet biztosít az Azure Storage használatát több alkalmazás is. Az alábbi táblázat az néhány ezeknek az alkalmazásoknak a listája:

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
*Az Azure Storage Explorer* vizsgálatához és módosítása a nyerhető a blobok fontos eszköze. Egy ingyenes, nyílt forráskódú eszköz, amely letölthető a [ http://storageexplorer.com/ ](http://storageexplorer.com/). A forráskódja elérhető a hivatkozásból.

Az eszköz használata előtt ismernie kell az Azure storage-fiók tárfióknév és fiókkulcs. Az alábbi információk kapcsolatos útmutatásért lásd: a "How to: megtekintése, másolása és újragenerálása storage hozzáférési kulcsok" szakaszában [létrehozása, kezelése vagy törlése a storage-fiók][azure-create-storage-account].

1. Futtassa az Azure Storage Explorerben. Ha első alkalommal futtatnia kell a Storage Explorert, kéri a **tá_rolási fióknév** és **tárfiókkulcs**. Ha kell, mielőtt futtatja, a **Hozzáadás** gombra kattintva adhat hozzá egy új tárfiók neve és kulcsa.

    Adja meg a nevét, és a HDInsight-fürt által használt a tárfiók kulcsát, majd **nyílt & mentése**.

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. A felület balra tárolók listájában kattintson a tároló a HDInsight-fürthöz társított neve. Alapértelmezés szerint azt a HDInsight-fürt neve, de eltérő, ha a fürt létrehozásakor megadott adott névvel lehet.
3. Az eszköztáron válassza a Feltöltés ikonra.

    ![Az eszköztáron a kiemelt ikon feltöltése](./media/hdinsight-upload-data/toolbar.png)
4. Adjon meg egy fájlt, és kattintson a töltse fel **nyílt**. Amikor a rendszer kéri, válassza ki a **feltöltése** feltölteni a fájlt a tároló gyökérkönyvtárába. Ha szeretne feltölteni a fájlt egy adott helyre, adja meg az elérési utat a **cél** mezőre, majd válassza **feltöltése**.

    ![Fájl feltöltése párbeszédpanel](./media/hdinsight-upload-data/fileupload.png)

    Után a fájl befejezte a feltöltést, használhatja a feladatok a HDInsight-fürtön.

### <a name="mount-azure-storage-as-local-drive"></a>Az Azure Storage csatlakoztatása helyi meghajtóként
Lásd: [az Azure Storage csatlakoztatása helyi meghajtóként](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

### <a name="upload-using-services"></a>Töltse fel a szolgáltatások használata
#### <a name="azure-data-factory"></a>Azure Data Factory
Az Azure Data Factory szolgáltatás nem egy teljes körűen felügyelt szolgáltatás, adatfeldolgozási adatok tárolási és adatátviteli szolgáltatások létrehozása egyszerű, méretezhető és megbízható éles adatfolyamatokat állíthat be.

Az Azure Data Factory adatok áthelyezése az Azure Storage-ba, vagy közvetlenül használni a HDInsight-szolgáltatások, például a Hive és Pig adatfolyamatok létrehozására is használhatók.

További információkért lásd: a [Azure Data Factory-dokumentáció](https://azure.microsoft.com/documentation/services/data-factory/).

#### <a id="sqoop"></a>Az Apache sqoop használatával
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

### <a name="troubleshooting"></a>Hibaelhárítás
#### <a id="storageexception"></a>Írás a blob Storage kivétel
**A jelenség**: használatakor a `hadoop` vagy `hdfs dfs` parancsokat írhat fájlokat, amelyek ~ 12 GB-os vagy nagyobb, a HBase-fürtöt, felmerülhet a következő hiba:

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

**OK**: a HDInsight-alapú HBase-fürtök alapértelmezett egy 256 KB-os blokkméret az Azure storage való írás esetén. A HBase API-k vagy REST API-k esetében működik, amíg ez hibát eredményez használatakor a `hadoop` vagy `hdfs dfs` parancssori segédeszközöket.

**Feloldási**: használata `fs.azure.write.request.size` a nagyobb blokkméret megadásához. Ezt megteheti is használati alapon használatával a `-D` paraméter. A következő parancs használatával ezt a paramétert a `hadoop` parancsot:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Az értékét növelje `fs.azure.write.request.size` globálisan az Ambari használatával. Módosítsa az Ambari webes Kezelőfelületen használható a következő lépéseket:

1. A böngészőben nyissa meg az Ambari webes felhasználói Felületet, a fürt számára. Ez a https://CLUSTERNAME.azurehdinsight.net, ahol **CLUSTERNAME** a fürt neve.

    Amikor a rendszer kéri, adja meg a rendszergazdai felhasználónevét és jelszavát a fürt számára.
2. Válassza ki a képernyő bal oldalán, **HDFS**, majd válassza ki a **Configs** fülre.
3. Az a **szűrése...**  írja be a következőt `fs.azure.write.request.size`. Ez a mező és az oldal közepén aktuális értékét jeleníti meg.
4. Új értékét módosítsa az értéket a 262144 (256KB). Ha például 4194304 (4MB).

![Az Ambari webes felhasználói felületen keresztül értékét képe](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Az Ambari használatával kapcsolatos további információkért lásd: [kezelése a HDInsight-fürtök az Ambari webes kezelőfelületen](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>További lépések
Most, hogy megismerte, hogyan olvashatja be őket a HDInsight, olvassa el a további elemzéseket végezhet a következő cikkeket:

* [Azure HDInsight – első lépések][hdinsight-get-started]
* [Programozott módon a Hadoop-feladatok elküldése][hdinsight-submit-jobs]
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
