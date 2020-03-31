---
title: Nagy adatkészlet feltöltése az Azure Data Lake Storage Gen1 szolgáltatásba – offline módszerek
description: Az Importálás/exportálás szolgáltatás használata adatok másolása az Azure Data Lake Storage Gen1 szolgáltatásba
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: aa3eb0bcd9ddd2a094563efe326f7af7e9e8708a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839297"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Az Azure import/export szolgáltatás használata a Data Lake Storage Gen1 adatok offline másolásához

Ebből a cikkből megtudhatja, hogyan másolhat hatalmas adatkészleteket (>200 GB) a Data Lake Storage Gen1-be offline másolási módszerekkel, például az [Azure importálási/exportálási szolgáltatásával.](../storage/common/storage-import-export-service.md) Pontosabban, a fájl példaként ebben a cikkben 339.420.860.416 bájt, vagy körülbelül 319 GB a lemezen. Nevezzük ezt a fájlt 319GB.tsv.

Az Azure importálási/exportálási szolgáltatás segítségével nagyobb mennyiségű adatot vihet át biztonságosabban az Azure Blob storage-ba merevlemez-meghajtók azure-adatközpontba szállításával.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, rendelkeznie kell a következőkkel:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure-tárfiók.**
* **Egy Azure Data Lake Storage Gen1 fiók.** Az Azure Data Lake Storage Gen1 – [Első lépések az Azure Data Lake Storage Gen1 című](data-lake-store-get-started-portal.md)témakörben található.

## <a name="prepare-the-data"></a>Az adatok előkészítése

Az Importálás/exportálás szolgáltatás használata előtt bontsa fel az átvinni kívánt adatfájlt **200 GB-nál kisebb méretű példányokra.** Az importálási eszköz nem működik 200 GB-nál nagyobb fájlokkal. Ebben a cikkben a fájlt egyenként 100 GB-os adattömbökre osztjuk. Ezt a [Cygwin](https://cygwin.com/install.html)segítségével teheti meg. A Cygwin támogatja a Linux parancsokat. Ebben az esetben használja a következő parancsot:

    split -b 100m 319GB.tsv

A felosztási művelet a következő nevű fájlokat hozza létre.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Lemezek felkészítése adatokkal

Kövesse az [Azure importálási/exportálási szolgáltatás használata](../storage/common/storage-import-export-service.md) című útmutató utasításait (a **meghajtók előkészítése** szakaszban) a merevlemezek előkészítéséhez. Itt a teljes sorrend:

1. Szerezzen be egy olyan merevlemezt, amely megfelel az Azure importálási/exportálási szolgáltatáshoz való használatra vonatkozó követelménynek.
2. Azonosítsa az Azure storage-fiók, ahol az adatok at kell másolni, miután az Azure-adatközpontba történő szállítása után.
3. Használja az [Azure import/export eszközt,](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)egy parancssori segédprogramot. Íme egy mintarészlet, amely bemutatja az eszköz használatát.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    További mintakódrészletek [az Azure importálási/exportálási szolgáltatás használata](../storage/common/storage-import-export-service.md) című témakörben.
4. Az előző parancs naplófájlt hoz létre a megadott helyen. Ezzel a naplófájlval importálási feladatot hozhat létre az [Azure Portalról.](https://portal.azure.com)

## <a name="create-an-import-job"></a>Importálási feladat létrehozása

Most már létrehozhat egy importálási feladatot [az Azure importálási/exportálási szolgáltatás használata](../storage/common/storage-import-export-service.md) című útmutató utasításai val **(az Importálási feladat létrehozása** szakaszban). Ehhez az importálási feladathoz más részletekkel együtt adja meg a lemezmeghajtók előkészítése során létrehozott naplófájlt is.

## <a name="physically-ship-the-disks"></a>A lemezek fizikai szállítása

Most már fizikailag szállíthatja a lemezeket egy Azure-adatközpontba. Ott az adatok átmásolva az Azure Storage-blobok megadott az importálási feladat létrehozása során. A feladat létrehozásakor, ha később úgy döntött, hogy megadja a nyomon követési adatokat, most visszatérhet az importálási feladathoz, és frissítheti a nyomon követési számot.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Adatok másolása blobokból a Data Lake Storage Gen1 szolgáltatásba

Miután az importálási feladat állapota azt mutatja, hogy befejeződött, ellenőrizheti, hogy az adatok elérhetők-e a megadott Azure Storage-blobokban. Ezután számos módszerrel áthelyezheti ezeket az adatokat a blobokból az Azure Data Lake Storage Gen1-be. Az adatok feltöltéséhez rendelkezésre álló összes lehetőséget [lásd: Adatok betöltése a Data Lake Storage Gen1 szolgáltatásba.](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1)

Ebben a szakaszban biztosítjuk a JSON-definíciókat, amelyek segítségével létrehozhat egy Azure Data Factory-folyamatot az adatok másolásához. Ezeket a JSON-definíciókat az [Azure Portalon](../data-factory/tutorial-copy-data-portal.md) vagy a [Visual Studio-ban használhatja.](../data-factory/tutorial-copy-data-dot-net.md)

### <a name="source-linked-service-azure-storage-blob"></a>Forráskapcsolt szolgáltatás (Azure Storage blob)

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-data-lake-storage-gen1"></a>Célhoz kötött szolgáltatás (Data Lake Storage Gen1)

```JSON
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```

### <a name="input-data-set"></a>Bemeneti adatkészlet

```JSON
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

### <a name="output-data-set"></a>Kimeneti adatkészlet

```JSON
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```

### <a name="pipeline-copy-activity"></a>Folyamat (másolási tevékenység)

```JSON
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

További információ: [Adatok áthelyezése az Azure Storage blobból az Azure Data Lake Storage Gen1-be az Azure Data Factory használatával](../data-factory/connector-azure-data-lake-store.md)című témakörben talál.

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Az adatfájlok rekonstruálása a Data Lake Storage Gen1 szolgáltatásban

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Egy 319 GB-os fájllal kezdtük, és kisebb méretű fájlokra bontottuk, hogy az Azure Import/Export szolgáltatás használatával átlehessen vinni. Most, hogy az adatok az Azure Data Lake Storage Gen1-ben vannak, rekonstruálhatjuk a fájlt az eredeti méretére. Ehhez használhatja a következő Azure PowerShell-parancsmagokat.

```PowerShell
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>További lépések

* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1 szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1 szolgáltatással](data-lake-store-hdinsight-hadoop-use-portal.md)
