---
title: Adatok áthelyezése az Amazon Simple Storage Service Data Factory használatával |} A Microsoft Docs
description: Ismerje meg az Amazon Simple Storage Service (S3) adatok áthelyezése az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fda80455cf3504bf992fabc3018be2d5c05612ae
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019146"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Adatok áthelyezése az Amazon Simple Storage Service az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-amazon-simple-storage-service-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [Amazon S3-összekötő a v2-ben](../connector-amazon-simple-storage-service.md).

Ez a cikk bemutatja, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok áthelyezése az Amazon Simple Storage Service (S3 esetén). Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

Az Amazon S3-ból adatokat másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. A Data Factory jelenleg csak helyez át adatokat az Amazon S3-ból pedig más adattárakban támogatja, de nem az adatok áthelyezését egyéb adatok tárolja az Amazon S3.

## <a name="required-permissions"></a>Szükséges engedélyek
Adatok másolása az Amazon S3, győződjön meg arról, kapott a következő engedélyeket:

* `s3:GetObject` és `s3:GetObjectVersion` Amazon S3 objektum műveletekhez.
* `s3:ListBucket` az Amazon S3 gyűjtő műveletekhez. Ha a Data Factory Copy varázslót használja `s3:ListAllMyBuckets` is szükség.

További információk az Amazon S3-engedélyek teljes listája: [engedélyek megadása egy házirendben](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez az Amazon S3 forrásból a különböző eszközök vagy API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. A gyors útmutatóban talál [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md).

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Egy másolási tevékenységgel ellátott adatcsatorna létrehozása a lépésenkénti útmutatójáért lásd: a [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Az eszközök vagy az API-kat, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli.
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Amikor az eszközök vagy az API-k (kivéve a .NET API), meghatározhatja a Data Factory-entitások a JSON formátumban. A minta az adatok másolása az Amazon S3 adatokat az adattárból használt Data Factory-entitások JSON-definíciói: a [JSON-példa: Adatok másolása az Amazon S3-ból az Azure-Blobba](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) című szakaszát.

> [!NOTE]
> További információ a másolási tevékenység támogatott fájl- és tömörítési formátumok: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md).

A következő szakaszok az Amazon S3 adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részleteket.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A társított szolgáltatás egy adattárba hivatkozik, adat-előállító. Létrehoz egy társított szolgáltatást típusú **AwsAccessKey** az Amazon S3-adattárhoz összekapcsolása a data factoryhoz. A következő táblázat tartalmazza a megadott JSON-elemek leírását az Amazon S3 (AwsAccessKey) társított szolgáltatást.

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| accessKeyID |A titkos hozzáférési kulcs azonosítója. |sztring |Igen |
| secretAccessKey |A titkos hívóbetűje magát. |Titkosított titkos karakterlánc |Igen |

>[!NOTE]
>Az összekötő használatához az adatok másolása az Amazon S3 IAM-fiók hozzáférési kulcsait. [Ideiglenes biztonsági hitelesítő adat](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nem támogatott.
>

Például:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Adja meg az Azure Blob storage-ban a bemeneti adatokat képviselő adatkészlet, állítsa be a type tulajdonság, az adatkészlet **AmazonS3**. Állítsa be a **linkedServiceName** tulajdonságot az adatkészlet neve lesz az Amazon S3-beli társított szolgáltatást. Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: [adatkészletek létrehozása](data-factory-create-datasets.md). 

Például a szerkezetet, rendelkezésre állás és a házirend szakaszok hasonlóak az összes adatkészlet esetében (például az SQL database, az Azure blob és az Azure-tábla). A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. A **typeProperties** szakasz egy adatkészlet típusú **AmazonS3** (amely tartalmazza az Amazon S3-adatkészletek) a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| bucketName |Az S3 gyűjtő neve. |Karakterlánc |Igen |
| kulcs |Az S3-objektum kulcsa. |Karakterlánc |Nem |
| előtag |Az S3-objektum kulcs előtag. Ezzel az előtaggal start amelynek kulcsok objektum van kijelölve. Érvényes, csak ha kulcsa üres. |Karakterlánc |Nem |
| version |Az S3-objektum, ha engedélyezve van a S3 versioning verziója. |Karakterlánc |Nem |
| Formátum | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: a [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [JSON formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum ](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha szeretné, a fájlok másolása a-rendszer közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem | |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. A támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. A támogatott szintek a következők: **Optimális** és **leggyorsabb**. További információkért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem | |


> [!NOTE]
> **bucketName + kulcs** az S3-objektum, ahol gyűjtőbe az S3 szintű objektumok a legfelső szintű tárolója és a kulcs teljes elérési útját az S3-objektum helyét adja meg.

### <a name="sample-dataset-with-prefix"></a>Minta adatkészlet előtaggal

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>Minta adatkészlet (verzióval)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Az S3 dinamikus útvonalak
A fenti példa rögzített értékeit a **kulcs** és **bucketName** az Amazon S3-adatkészlet tulajdonságai.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Ezeket a tulajdonságokat dinamikusan, futásidőben, például a SliceStart rendszerváltozók használatával számítják ki a Data Factory rendelkezhet.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Is tegye meg ugyanezt a a **előtag** az Amazon S3-adatkészletek tulajdonságát. Támogatott funkciók és a változók listáját lásd: [Data Factory-függvények és rendszerváltozók](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: [folyamatok létrehozása](data-factory-create-pipelines.md). Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és szabályzatok minden típusú tevékenységek érhetők el. A rendelkezésre álló tulajdonságok a **typeProperties** a tevékenység szakaszban tevékenységek minden típusának számától függ. A másolási tevékenység tulajdonságai a forrásként és fogadóként típusú változhat. Ha a másolási tevékenység a forrás típusa nem **FileSystemSource** (amely tartalmazza az Amazon S3), a következő tulajdonság megtalálható **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a rekurzív |Itt adhatja meg, hogy rekurzív listában S3 objektumok a könyvtárban. |Igaz/hamis |Nem |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON-példa: Adatok másolása az Amazon S3-ból az Azure Blob storage
Ez a példa bemutatja, hogyan másolhat adatokat az Amazon S3-ból egy Azure Blob Storage-tárolóba. Azonban adatokat közvetlenül a átmásolható [bármelyik a fogadóként támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység használatával a Data Factoryban.

A minta az alábbi Data Factory-entitások JSON-definíciói biztosít. Használhatja ezeket a definíciókat hozhat létre egy folyamatot az adatok másolása az Amazon S3-ból a Blob storage használatával a [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* A társított szolgáltatás típusa [AwsAccessKey](#linked-service-properties).
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AmazonS3](#dataset-properties).
* Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol az Amazon S3-ból egy Azure-blobból óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

### <a name="amazon-s3-linked-service"></a>Amazon S3-beli társított szolgáltatás

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="amazon-s3-input-dataset"></a>Az Amazon S3 bemeneti adatkészlet

Beállítás **"external": true** a Data Factory szolgáltatás értesíti arról, hogy a data factory a külső-e az adatkészlethez. Ez a tulajdonság TRUE meg, amely nem hozzák a folyamat egyik tevékenységének bemeneti adatkészlet.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Azure Blob kimeneti adatkészlet

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1.). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, az év, hónap, nap és óra részei a kezdési időpontot.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Az Amazon S3-forrás és a egy blob fogadó a folyamat másolási tevékenysége

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és az óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **FileSystemSource**, és **fogadó** típusa **BlobSink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Fogadó-adatkészlet az oszlopok a forrásadatkészlet oszlopok leképezésére, lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

* Az adatáthelyezés (másolási tevékenységgel) a Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre legfontosabb tényezők kapcsolatos további információkért lásd: a [másolási tevékenységek teljesítményéhez és finomhangolási útmutató](data-factory-copy-activity-performance.md).

* Folyamat létrehozása másolási tevékenységgel rendelkező részletes ismertetését lásd: a [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
