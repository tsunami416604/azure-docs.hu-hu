---
title: "Nagy mennyiségű adatok feltöltése a Data Lake Store kapcsolat nélküli módszerrel |} Microsoft Docs"
description: "Adatok másolása az Azure Storage blobs Data Lake store a AdlCopy eszközzel"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 1309b44ea99af6d20a4d0f730dd68969f3c3082b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-store"></a>Data Lake Store-adatok offline példányát az Azure Import/Export szolgáltatás használata
Ebből a cikkből megtudhatja, hogyan hatalmas adatok másolása (> 200 GB-os) azokat az Azure Data Lake Store módszerrel offline másolat, például a [Azure Import/Export szolgáltatás](../storage/common/storage-import-export-service.md). Pontosabban ebben a cikkben példa fájl 339,420,860,416 bájt vagy körülbelül 319 GB-TAL a lemezen. Most hívja meg a fájl 319GB.tsv.

Az Azure Import/Export szolgáltatás segítségével átvitele nagy mennyiségű adatok biztonsága érdekében az Azure Blob storage szállítási merevlemez-meghajtók számára egy Azure-adatközpontban.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt hozzákezd, rendelkeznie kell a következő:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Azure-tárfiók**.
* **Egy Azure Data Lake Store-fiók**. Hogyan hozhat létre ilyet, lásd: [Ismerkedés az Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Az adatok előkészítése
A Import/Export szolgáltatás használata előtt át kell helyezni az adatfájl törés **azokat, amelyek 200 GB-nál kisebb másolatok** mérete. 200 GB-nál nagyobb fájlokat nem működik az import eszközt. Az oktatóanyag azt ossza fel a fájl minden 100 GB méretű adattömböket írnak. Ehhez a [Cygwin](https://cygwin.com/install.html). Cygwin támogatja a Linux-parancsok. Ebben az esetben használja a következő parancsot:

    split -b 100m 319GB.tsv

A felosztott művelet a következő nevű fájlokat hozza létre.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Felkészülés a lemezek adatokkal
Kövesse az utasításokat a [az Azure Import/Export szolgáltatás használatával](../storage/common/storage-import-export-service.md) (alatt a **készítse elő a meghajtók** szakasz) a merevlemez-meghajtók előkészítéséhez. Az általános feladatütemezési itt található:

1. Be kell szereznie egy merevlemezt, amely megfelel a követelmény az Azure Import/Export szolgáltatás használt.
2. Az adatok másolatának tárolására után a szállított az Azure-adatközpontban az Azure storage-fiók azonosítása.
3. Használja a [Azure Import/Export eszköz](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), parancssori segédprogramot. Íme egy minta kódrészletet, amely bemutatja, hogyan használhatja az eszközt.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Lásd: [az Azure Import/Export szolgáltatás használatával](../storage/common/storage-import-export-service.md) a minta további részletek.
4. Az előző parancs létrehoz egy napló fájlt a megadott helyen. Ez a napló fájl segítségével az importálási feladat létrehozása a [a klasszikus Azure portálon](https://manage.windowsazure.com).

## <a name="create-an-import-job"></a>Importálási feladat létrehozása
Mostantól létrehozhat egy importálási feladat található utasítások segítségével [az Azure Import/Export szolgáltatás használatával](../storage/common/storage-import-export-service.md) (alatt a **az importálási feladat létrehozása** szakaszban). Az importálási feladathoz más adatokkal is adja meg a napló-fájlját a merevlemez-meghajtók előkészítése során létrehozott.

## <a name="physically-ship-the-disks"></a>Fizikailag küldje el a lemezek
Fizikailag most szállítása a lemezt egy Azure-adatközpontban. Hiba, a adatokat másolja át az Azure Storage blobs szolgáltatásban, az importálási feladat létrehozásakor megadott. Is a feladat létrehozásakor, ha később, a nyomon követési információk választotta mostantól térjen vissza az importálás és frissítés a nyomon követési száma.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Adatok másolása az Azure Storage blobs szolgáltatásban az Azure Data Lake Store
Után az importálási feladat állapotát jeleníti meg, hogy végzett, ellenőrizheti, hogy az adatok érhető el az Azure Storage blobs volt megadva. Számos módszer a blobok adatok áthelyezése az Azure Data Lake Store használhatja. Az összes elérhető beállítások adatfeltöltési, lásd: [adatok bevitele a Data Lake Store](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

Ez a szakasz azt adja meg a JSON-definíciók, amelyek segítségével hozzon létre egy Azure Data Factory-folyamat az adatok másolása. A JSON-definíciókban is használhatja a [Azure-portálon](../data-factory/v1/data-factory-copy-activity-tutorial-using-azure-portal.md), vagy [Visual Studio](../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [Azure PowerShell](../data-factory/v1/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Forrás társított szolgáltatás (Azure Storage-blobba)
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

### <a name="target-linked-service-azure-data-lake-store"></a>Cél társított szolgáltatás (az Azure Data Lake Store)
````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Bemeneti adatkészlet
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
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
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
### <a name="pipeline-copy-activity"></a>Feldolgozási sor (másolási tevékenység)
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
További információkért lásd: [helyezze át az adatokat az Azure Storage-blobból az Azure Data Lake Store Azure Data Factory használatával](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Hozza létre újból az Azure Data Lake Store-adatfájlok
A fájl, 319 GB, és úgy, hogy az Azure Import/Export szolgáltatás használatával sikerült továbbítani túllépte azt le a kisebb méretű fájlok használatába azt. Most, hogy az adatok Azure Data Lake Store-ban, hogy a fájl eredeti méretének is helyreállítására. A következő Azure PowerShell cmldts ehhez használhatja.

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>Következő lépések
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)
