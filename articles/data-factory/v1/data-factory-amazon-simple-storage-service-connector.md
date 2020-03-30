---
title: Adatok áthelyezése az Amazon Simple Storage Szolgáltatásból a Data Factory használatával
description: Ismerje meg, hogyan helyezhetát át az adatokat az Amazon Simple Storage Service (S3) szolgáltatásból az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b23be9901df7ca435f412d9f49e1a7ad88382ade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281651"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Adatok áthelyezése az Amazon Simple Storage Szolgáltatásból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-amazon-simple-storage-service-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [Amazon S3 csatlakozót a V2-ben.](../connector-amazon-simple-storage-service.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenységet az Azure Data Factory ban az Amazon Simple Storage Service (S3) adatainak áthelyezéséhez. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatok mozgásáról a másolási tevékenységgel.

Az Amazon S3-ról bármilyen támogatott fogadó adattárba másolhat adatokat. A másolási tevékenység által fogadóként támogatott adattárak listáját a [Támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblában láthatja. A Data Factory jelenleg csak az Amazon S3-ról más adattárolókba való átkerülést támogatja, de nem helyezi át az adatokat más adattárakból az Amazon S3-ba.

## <a name="required-permissions"></a>Szükséges engedélyek
Ha adatokat szeretne másolni az Amazon S3-ból, győződjön meg arról, hogy megkapta a következő engedélyeket:

* `s3:GetObject`és `s3:GetObjectVersion` az Amazon S3 Object Operations.
* `s3:ListBucket`Az Amazon S3 Bucket Operations. Ha a Data Factory Copy `s3:ListAllMyBuckets` wizardt használja, akkor is szükség van.

Az Amazon S3 engedélyek teljes listájáról az [Engedélyek megadása a házirendben](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)című témakörben talál további részleteket.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök vagy API-k használatával áthelyezi az adatokat egy Amazon S3-forrásból.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. A rövid útmutatót az [Oktatóanyag: Folyamat létrehozása a Másolás varázslóval című témakörben ismerteti.](data-factory-copy-data-wizard-tutorial.md)

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** A másolási tevékenységgel rendelkező folyamat létrehozásának lépésenkénti útmutatója a [Tevékenység másolása oktatóanyagban található.](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Akár eszközöket, akár API-kat használ, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására.
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök vagy API-k használataesetén (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja. Az Amazon S3-adattárból adatok másolására használt Data Factory-entitások JSON-definícióival rendelkező minta a [JSON-példa: Adatok másolása az Amazon S3-ból](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) az Azure Blob szakaszba ebben a cikkben.

> [!NOTE]
> A másolási tevékenység támogatott fájl- és tömörítési formátumairól az [Azure Data Factory fájl- és tömörítési formátumok című témakörében](data-factory-supported-file-and-compression-formats.md)talál további információt.

A következő szakaszok az Amazon S3-ra jellemző Data Factory-entitások meghatározására használt JSON-tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
A csatolt szolgáltatás adattaránttárolót kapcsol össze egy adat-előállítóval. Hozzon létre egy **awsAccessKey** típusú kapcsolt szolgáltatást, amely összeköti az Amazon S3 adattárolót az adatgyárával. Az alábbi táblázat az Amazon S3 (AwsAccessKey) kapcsolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| accessKeyID azonosító |A titkos hozzáférési kulcs azonosítója. |sztring |Igen |
| secretAccessKey |Maga a titkos hozzáférési kulcs. |Titkosított titkos karakterlánc |Igen |

>[!NOTE]
>Ehhez az összekötőhöz hozzáférési kulcsok szükségesek az IAM-fiókhoz az Amazon S3-ból történő adatok másolásához. [Az ideiglenes biztonsági hitelesítő adat](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nem támogatott.
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
Ha az Azure Blob storage-ban bemeneti adatokat jelölő adatkészletet szeretne megadni, állítsa az adatkészlet típustulajdonságát **az AmazonS3-ra.** Állítsa az adatkészlet **linkedServiceName** tulajdonságát az Amazon S3 csatolt szolgáltatás nevére. Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek létrehozása című témakörben található.](data-factory-create-datasets.md) 

A szakaszok, például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlet-típushoz (például az SQL-adatbázishoz, az Azure blobhoz és az Azure-táblához). A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. Az **AmazonS3** típusú adatkészlet **typeProperties** szakasza (amely az Amazon S3 adatkészletet is tartalmazza) a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| bucketName (vödörneve) |Az S3 gyűjtő neve. |Sztring |Igen |
| kulcs |Az S3 objektumkulcs. |Sztring |Nem |
| Előtag |Az S3 objektumkulcs előtagja. Azok az objektumok, amelyek billentyűi ezzel az előtaggal kezdődnek, ki vannak jelölve. Csak akkor érvényes, ha a kulcs üres. |Sztring |Nem |
| version |Az S3 objektum verziója, ha az S3 verziószámozás engedélyezve van. |Sztring |Nem |
| Formátum | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információ: Text format , JSON format , Avro format , Orc format és [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) [(Szöveg formátum,](data-factory-supported-file-and-compression-formats.md#text-format) [JSON formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc format](data-factory-supported-file-and-compression-formats.md#orc-format)) című szakasz. <br><br> Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné másolni, hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is. | |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**. A támogatott szintek a következők: **Optimális** és **leggyorsabb.** További információt a [Fájl- és tömörítési formátumok az Azure Data Factoryban című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) | |Nem |


> [!NOTE]
> **bucketName + kulcs** az S3 objektum helyét adja meg, ahol a gyűjtő az S3 objektumok gyökértárolója, a kulcs pedig az S3 objektum teljes elérési útja.

### <a name="sample-dataset-with-prefix"></a>Mintaadatkészlet előtaggal

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
### <a name="sample-dataset-with-version"></a>Mintaadatkészlet (verzióval)

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

### <a name="dynamic-paths-for-s3"></a>Dinamikus görbék az S3-hoz
Az előző minta rögzített értékeket használ az Amazon S3 adatkészlet **kulcs-** és **bucketName** tulajdonságaihoz.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Beállíthatja, hogy a Data Factory futásidőben dinamikusan számítsa ki ezeket a tulajdonságokat olyan rendszerváltozók használatával, mint például a SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Ugyanezt teheti meg az Amazon S3 adatkészlet **előtag-tulajdonságával** is. A támogatott függvények és változók listáját a [Data Factory függvények és rendszerváltozók című témakörben található.](data-factory-functions-variables.md)

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok létrehozása című témakörben található.](data-factory-create-pipelines.md) Az olyan tulajdonságok, mint a név, a leírás, a bemeneti és kimeneti táblák és a házirendek minden típusú tevékenységhez elérhetők. A tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. A másolási tevékenység tulajdonságai a források és a fogadók típusától függően változnak. Ha a másolási tevékenység egy forrása **FileSystemSource** típusú (amely magában foglalja az Amazon S3-ot is), a következő tulajdonság érhető el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| Rekurzív |Itt adható meg, hogy rekurzív módon felsoroljon-e S3 objektumokat a könyvtár alatt. |igaz/hamis |Nem |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON-példa: Adatok másolása az Amazon S3-ról az Azure Blob storage-ba
Ez a minta bemutatja, hogyan másolhat adatokat az Amazon S3-ból egy Azure Blob-tárba. Azonban az adatok közvetlenül másolhatók [a fogadók, amelyek](data-factory-data-movement-activities.md#supported-data-stores-and-formats) által támogatott másolási tevékenység data factory használatával.

A minta JSON-definíciókat biztosít a következő Data Factory entitásokhoz. Ezekkel a definíciókkal létrehozhat egy folyamatot az Amazon S3-ról a Blob storage-ba a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával.   

* [AwsAccessKey](#linked-service-properties)típusú csatolt szolgáltatás.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
* [AmazonS3](#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
* [A FileSystemSource](#copy-activity-properties) és [a BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)programot használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta óránként másolja az adatokat az Amazon S3-ból egy Azure-blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

### <a name="amazon-s3-linked-service"></a>Amazon S3 kapcsolt szolgáltatás

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

A **"külső" beállítás: true** tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található. Állítsa ezt a tulajdonságot igaz értékre egy olyan bemeneti adatkészleten, amelyet nem a folyamatban lévő tevékenység hoz létre.

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

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Tevékenység másolása amazon S3-forrással és blobfogadóval rendelkező folyamatban

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A json-definícióban a **forrástípus** **Fájlrendszerforrás,** **a fogadó** típusa pedig **BlobSink**lesz.

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
> Ha oszlopokat szeretne leképezni egy forrásadatkészletből egy fogadó adatkészlet oszlopaihoz, olvassa [el az Adatkészlet-oszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

* Ha többet szeretne megtudni az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről és optimalizálási lehetőségekről, olvassa el a [Tevékenység teljesítményének másolása és hangolása útmutatót.](data-factory-copy-activity-performance.md)

* A másolási tevékenységgel rendelkező folyamat létrehozásáról a [Másolási tevékenység oktatóanyagcímű témakörben](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)talál részletes útmutatást.
