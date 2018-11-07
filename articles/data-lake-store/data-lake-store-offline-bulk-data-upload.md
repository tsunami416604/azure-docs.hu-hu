---
title: Nagy mennyiségű adat feltöltése az Azure Data Lake Storage Gen1 offline módszerrel |} A Microsoft Docs
description: Adatok másolása az Azure Storage-blobokat az Azure Data Lake Storage Gen1 az AdlCopy eszközzel
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: fc70089517bbc1aa90f95f1e0231f2c67f930090
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242194"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-azure-data-lake-storage-gen1"></a>Az Azure Data Lake Storage Gen1 adatok offline másolásához az Azure Import/Export szolgáltatás használata
Ebből a cikkből elsajátíthatja a hatalmas adatkészletek másolása (> 200 GB-os) az Azure Data Lake Storage Gen1 módszerrel offline másolásához, például a [Azure Import/Export szolgáltatás](../storage/common/storage-import-export-service.md). Pontosabban a fájl, használjuk példaként ebben a cikkben nem 339,420,860,416 bájt vagy a lemezen körülbelül 319 GB. Adjuk a fájl 319GB.tsv.

Az Azure Import/Export szolgáltatás segít a nagy mennyiségű adat átvitele biztonságosabb az Azure Blob storage-merevlemez-meghajtók egy Azure-adatközpontban való szállításával.

## <a name="prerequisites"></a>Előfeltételek
Elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure storage-fiók**.
* **Az Azure Data Lake Storage Gen1 fiók**. Létrehozásával kapcsolatos utasításokért lásd: [Ismerkedés az Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Az adatok előkészítése

Az Import/Export szolgáltatást használja, mielőtt felosztása át lehet adni az adatfájl **be, amelyek a 200 GB-nál kevesebb példányt** mérete. Az importálási eszköz nem működik a 200 GB-nál nagyobb fájlokkal. Ebben az oktatóanyagban a fájl adattömbökbe egyenként 100 GB-os osztottuk. Ezt megteheti használatával is [Cygwin](https://cygwin.com/install.html). Cygwin támogatja a Linux-parancsok. Ebben az esetben használja a következő parancsot:

    split -b 100m 319GB.tsv

A felosztási művelet fájlokat hoz létre a következő nevekkel.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Felkészülés a lemezek adatokkal
Kövesse a [az Azure Import/Export szolgáltatás használatával](../storage/common/storage-import-export-service.md) (alatt a **meghajtó előkészítése** szakaszt) a merevlemezek előkészítéséhez. Itt látható a teljes feladatütemezés:

1. Be kell szereznie egy merevlemezt, amely megfelel a követelmény az Azure Import/Export szolgáltatás használható.
2. Azonosítsa az Azure storage-fiók, amelyben az adatok másolandó után el az Azure-adatközpontba.
3. Használja a [Azure Import/Export eszköz](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), parancssori segédprogramot. Íme egy példa kódrészletet, amely bemutatja, hogyan használhatja az eszközt.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Lásd: [az Azure Import/Export szolgáltatás használatával](../storage/common/storage-import-export-service.md) további példa kódtöredékek esetében.
4. Az előző parancs létrehoz egy journal-fájlt a megadott helyen. Ez a naplófájl használatával az importálási feladat létrehozása a [az Azure portal](https://portal.azure.com).

## <a name="create-an-import-job"></a>Importálási feladat létrehozása
Mostantól létrehozhat az importálási feladat szakaszban foglaltak szerint [az Azure Import/Export szolgáltatás használatával](../storage/common/storage-import-export-service.md) (alatt a **az importálási feladat létrehozása** szakaszt). Az importálási feladatot más adatokkal is adja meg a napló-fájlt a merevlemez-meghajtók előkészítése során létrehozott.

## <a name="physically-ship-the-disks"></a>Fizikailag szállítsa
Most már fizikailag küldhet el a lemezeket egy Azure-adatközpontban. Itt az adatok át van másolva az Azure Storage-blobokkal, az importálási feladat létrehozásakor megadott. Is a feladat létrehozásakor, ha úgy döntött, hogy később, a nyomon követési információkat biztosít, ezután lépjen vissza az importálási feladat és a nyomkövetési azonosító szám frissítése.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Adatok másolása az Azure Storage-blobokat az Azure Data Lake Storage Gen1
Után az importálási feladat állapotát jeleníti meg, hogy elkészült, ellenőrizheti, hogy volt a megadott Azure Storage-blobokban rendelkezésre áll-e az adatok. A blobok az adatok áthelyezése az Azure Data Lake Storage Gen1 felhasználhatja többféle módszerrel. Az elérhető lehetőségeket tölthet fel adatokat, lásd: [tölt be adatot a Data Lake Storage Gen1 be](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

Ebben a szakaszban olyan biztosítunk, amelyek segítségével hozzon létre egy Azure Data Factory-folyamatot az adatok másolása JSON-definíciói. Ezek a JSON-definíciók is használhatja a [az Azure portal](../data-factory/tutorial-copy-data-portal.md) vagy [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md).

### <a name="source-linked-service-azure-storage-blob"></a>Társított adatforrás-szolgáltatása (Azure Storage blob)
````
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
````

### <a name="target-linked-service-azure-data-lake-storage-gen1"></a>Cél társított szolgáltatás (Azure Data Lake Storage Gen1)
````
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
````
### <a name="input-data-set"></a>A bemeneti adatkészlet
````
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
````
### <a name="output-data-set"></a>Kimeneti adatkészlet
````
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
````
### <a name="pipeline-copy-activity"></a>Folyamat (a másolási tevékenység)
````
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
````
További információkért lásd: [adatok áthelyezése az Azure Storage-blobból az Azure Data Lake Storage Gen1 Azure Data Factory használatával](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-storage-gen1"></a>Hozza az adatfájlokat az Azure Data Lake Storage Gen1
Hogy lépései egy fájlt, amely 319 GB volt, és úgy, hogy sikerült átvinni az Azure Import/Export szolgáltatás használatával érvénytelenítése azt le azokat a kisebb méretű fájlokat. Most, hogy az adatokat az Azure Data Lake Storage Gen1, hogy rekonstruálhassák a betűméretét az eredeti fájlt. Ehhez a következő Azure PowerShell-parancsmagok segítségével.

```
# Login to our account
Connect-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext -SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
````

## <a name="next-steps"></a>További lépések
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
