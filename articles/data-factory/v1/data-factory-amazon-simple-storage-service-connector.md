---
title: Az Amazon Simple Storage szolgáltatásból származó adatok áthelyezése Data Factory használatával | Microsoft Docs
description: Ismerje meg, hogy miként helyezhetők át adatok az Amazon Simple Storage Service (S3) szolgáltatásból Azure Data Factory használatával.
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
ms.openlocfilehash: ec44ae7956669ee4e16d2c6ca00794c566272037
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69892010"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Az Amazon Simple Storage szolgáltatásból származó adatok áthelyezése Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-amazon-simple-storage-service-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az Amazon S3-összekötőt a v2-ben](../connector-amazon-simple-storage-service.md).

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factory az Amazon Simple Storage Service (S3) adatainak áthelyezéséhez. Az adattovábbítási [tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

Az Amazon S3-ból bármilyen támogatott fogadó adattárba másolhat adatok. A másolási tevékenység által mosogatóként támogatott adattárak listáját a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblázatban tekintheti meg. Data Factory jelenleg csak az Amazon S3-ból származó adatok áthelyezését támogatja más adattárakba, de más adattárakból az Amazon S3-ba nem helyezi át az adatok áthelyezését.

## <a name="required-permissions"></a>Szükséges engedélyek
Adatok másolása az Amazon S3, győződjön meg arról, kapott a következő engedélyeket:

* `s3:GetObject`és `s3:GetObjectVersion` az Amazon S3 Object műveletekhez.
* `s3:ListBucket`Amazon S3-gyűjtő műveletekhez. Ha a Data Factory másolás varázslót használja, `s3:ListAllMyBuckets` szintén szükséges.

További információk az Amazon S3-engedélyek teljes listája: [engedélyek megadása egy házirendben](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök vagy API-k használatával helyez át egy Amazon S3-forrásból származó adatokkal.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. A gyors útmutatóért lásd [: oktatóanyag: Folyamat létrehozása a másolás varázsló](data-factory-copy-data-wizard-tutorial.md)használatával.

A folyamat létrehozásához a következő eszközöket is használhatja: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és **REST API**. A másolási tevékenységgel rendelkező folyamat lépésről lépésre történő létrehozásával kapcsolatban lásd a [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Akár eszközöket, akár API-kat használ, hajtsa végre a következő lépéseket egy olyan folyamat létrehozásához, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre adatkészleteket a másolási művelet bemeneti és kimeneti adatok ábrázolásához.
3. Hozzon létre egy másolási tevékenységgel rendelkező folyamatot, amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket vagy API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. A JSON-definíciókkal rendelkező Data Factory entitások Amazon S3-adattárból történő másolásához használt minta esetében tekintse meg a [JSON-példát: Adatok másolása az Amazon S3-ból az](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) Azure blobba című rész a cikkből.

> [!NOTE]
> A másolási tevékenység támogatott fájl-és tömörítési formátumával kapcsolatos részletekért tekintse [meg a Azure Data Factory fájl-és tömörítési formátumait](data-factory-supported-file-and-compression-formats.md).

A következő szakaszokban részletesen ismertetjük az Amazon S3-ra jellemző Data Factory entitások definiálásához használt JSON-tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A társított szolgáltatás egy adattárhoz csatol egy adattárolót egy adatgyárhoz. Hozzon létre egy **awsaccesskey használnia** típusú társított szolgáltatást, amely összekapcsolja az Amazon S3-adattárat a saját adatgyárával. Az alábbi táblázat az Amazon S3 (Awsaccesskey használnia) társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| accessKeyID |A titkos hozzáférési kulcs azonosítója. |string |Igen |
| secretAccessKey |A titkos hívóbetűje magát. |Titkosított titkos karakterlánc |Igen |

>[!NOTE]
>Az összekötő használatához az adatok másolása az Amazon S3 IAM-fiók hozzáférési kulcsait. [Ideiglenes biztonsági hitelesítő adat](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nem támogatott.
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
Az Azure Blob Storage-ban bemeneti adatokat jelölő adatkészlet megadásához állítsa az adatkészlet Type (típus) tulajdonságát **AmazonS3**értékre. Állítsa az adatkészlet **linkedServiceName** tulajdonságát az Amazon S3 társított szolgáltatás nevére. Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek létrehozása](data-factory-create-datasets.md). 

A struktúra, a rendelkezésre állás és a házirend többek között az összes adatkészlet (például az SQL Database, az Azure Blob és az Azure Table) esetében hasonló. A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **AmazonS3** típusú adatkészlet (amely tartalmazza az Amazon S3-adatkészletet) **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| bucketName |Az S3 gyűjtő neve. |String |Igen |
| key |Az S3-objektum kulcsa. |String |Nem |
| prefix |Az S3-objektum kulcs előtag. Ezzel az előtaggal start amelynek kulcsok objektum van kijelölve. Csak akkor érvényes, ha a kulcs üres. |String |Nem |
| version |Az S3-objektum, ha engedélyezve van a S3 versioning verziója. |String |Nem |
| format | A következő formátumú típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: a [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [JSON formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum ](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha fájlokat szeretne másolni a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban. | |Nem |
| compression | Adja meg a típus és az adatok tömörítési szintje. A támogatott típusok a következők: **Gzip**,deflate, **BZip2**és **ZipDeflate**. A támogatott szintek a következők: **Optimális** és **leggyorsabb**. További információ: [fájl-és Tömörítési formátumok Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). | |Nem |


> [!NOTE]
> a **bucketName + Key** megadja az S3 objektum helyét, ahol a gyűjtő az S3-objektumok legfelső szintű tárolója, a kulcs pedig az S3 objektum teljes elérési útja.

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

### <a name="dynamic-paths-for-s3"></a>Dinamikus elérési utak az S3-hoz
Az előző minta rögzített értékeket használ az Amazon S3 adatkészlet **kulcs** -és **bucketName** tulajdonságaihoz.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Ezeket a tulajdonságokat a rendszerváltozók (például a SliceStart) használatával dinamikusan Data Factory kiszámíthatja futásidőben.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Ugyanezt megteheti az Amazon S3-adatkészlethez tartozó **előtag** tulajdonságnál is. A támogatott függvények és változók listáját itt tekintheti meg: [Data Factory functions és System változók](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok létrehozása](data-factory-create-pipelines.md). A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a házirendek minden típusú tevékenységhez elérhetők. A tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. A másolási tevékenység esetében a tulajdonságok a források típusától és a mosdótól függően változnak. Ha a másolási tevékenységben lévő forrás **FileSystemSource** típusú (amely tartalmazza az Amazon S3-t), az alábbi tulajdonság a **typeProperties** szakaszban érhető el:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| recursive |Megadja, hogy a címtárban az S3 objektumok rekurzív listázása megtörténjen-e. |Igaz/hamis |Nem |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON-példa: Adatok másolása az Amazon S3-ból az Azure Blob Storage-ba
Ez a minta bemutatja, hogyan másolhat adatok az Amazon S3-ból egy Azure Blob Storage-ba. Az Adatmásolás azonban közvetlenül a Data Factoryban található másolási tevékenység által [támogatott nyelők](data-factory-data-movement-activities.md#supported-data-stores-and-formats) számára is lehetséges.

A minta JSON-definíciókat biztosít a következő Data Factory entitásokhoz. Ezekkel a definíciókkal folyamatokat hozhat létre az Amazon S3-ból blob Storage-ba történő adatmásoláshoz a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával.   

* [Awsaccesskey használnia](#linked-service-properties)típusú társított szolgáltatás.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
* [AmazonS3](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
* [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta óránként másolja az Amazon S3-ból egy Azure-blobba az adatait. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

### <a name="amazon-s3-linked-service"></a>Amazon S3 társított szolgáltatás

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

### <a name="amazon-s3-input-dataset"></a>Amazon S3 bemeneti adatkészlet

A **"külső" beállítása: igaz** értékkel tájékoztatja a Data Factory szolgáltatást arról, hogy az adatkészlet kívül esik az adat-előállítón. Állítsa a tulajdonságot igaz értékre olyan bemeneti adatkészleten, amelyet nem a folyamat tevékenysége hozott létre.

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

Az új blobba (frekvencia: óra, időköz: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év, hónap, nap és óra részét használja.

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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Másolási tevékenység az Amazon S3-forrással és a blob-fogadóval rendelkező folyamatokban

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **FileSystemSource**értékre van állítva, a fogadó típusa pedig **BlobSink**.

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
> Ha egy forrás adatkészletből származó oszlopokat kíván leképezni egy fogadó adatkészletből származó oszlopokra, tekintse meg [az adatkészlet oszlopainak leképezése Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

* Az adatáthelyezés (másolási tevékenység) teljesítményét befolyásoló fő tényezőkről Data Factory, valamint az optimalizálásának különböző módjairól a [másolási tevékenység teljesítményének és hangolási útmutatója](data-factory-copy-activity-performance.md)című témakörben olvashat.

* A másolási tevékenységgel rendelkező folyamatok létrehozásával kapcsolatos részletes útmutatásért lásd a [másolási tevékenységről](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)szóló oktatóanyagot.
