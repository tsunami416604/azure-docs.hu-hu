---
title: Adatok áthelyezése tárolószolgáltatásból Amazon egyszerű Data Factory használatával |} Microsoft Docs
description: További tudnivalók az adatok mozgatása az Amazon egyszerű tároló szolgáltatás (S3) Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d895dcdf9eefac01790aab6dc3f36a3feb0a8b12
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051150"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Adatok áthelyezése tárolószolgáltatásból Amazon egyszerű Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](data-factory-amazon-simple-storage-service-connector.md)
> * [(Az aktuális verzió) 2-es verzió](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [Amazon S3-összekötőt, a V2](../connector-amazon-simple-storage-service.md).

Ez a cikk ismerteti, hogyan a másolási tevékenység során az Azure Data Factoryben az adatok mozgatása Amazon egyszerű tároló szolgáltatás (S3). Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.

Amazon S3 adatok bármely támogatott fogadó adattárolóhoz másolhatja. A másolási tevékenység által támogatott mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. Adat-előállító jelenleg támogatja a mozgóátlag adatok kizárólag az Amazon S3 más adattárolókhoz, de az Amazon S3 adatok nem áthelyezését más adatokat tárolja.

## <a name="required-permissions"></a>Szükséges engedélyek
Adatok másolása az Amazon S3, győződjön meg arról, hogy rendelkezik a következő engedélyekkel:

* `s3:GetObject` és `s3:GetObjectVersion` Amazon S3 objektum műveletekhez.
* `s3:ListBucket` Amazon S3 gyűjtő műveletekhez. A Data Factory másolása varázsló használata `s3:ListAllMyBuckets` is szükség.

A teljes listát az Amazon S3 engedélyekkel kapcsolatos részletekért lásd: [megadása engedélyeket egy házirendben](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, amely Amazon S3 forrásból származó adatokat a különböző eszközök vagy API-k használatával helyezi át a feldolgozási sor hozhatja létre.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Gyors bemutatóért lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md).

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-t**. Hozzon létre egy folyamatot a másolási tevékenység lépésenkénti útmutatójáért tekintse meg a [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Akár eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban:

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok.
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel.

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök vagy API-k (kivéve a .NET API-t) használ, amikor az a JSON formátum használatával adja meg a Data Factory entitások. Adatok másolása az Amazon S3 adattár használt adat-előállító entitások JSON-definíciók minta, tekintse meg a [JSON-példa: adatok másolása az Amazon S3 az Azure Blob](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) című szakaszát.

> [!NOTE]
> A másolási tevékenység során támogatott fájl- és tömörítési formátumok kapcsolatos részletekért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

A következő szakaszok részletesen bemutatják az Amazon S3 megadása a Data Factory tartozó entitások használt JSON-tulajdonságok.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A társított szolgáltatás adattárat egy adat-előállító hivatkozásokat tartalmaz. Típusú társított szolgáltatás létrehozása **AwsAccessKey** az Amazon S3 adattároló összekapcsolása a data factory. A következő táblázat tartalmazza a megadott JSON-elemek leírása az Amazon S3 (AwsAccessKey) társított szolgáltatás.

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| accessKeyID |A titkos hívóbetű azonosítója. |sztring |Igen |
| secretAccessKey |A titkos hívóbetű magát. |Titkosított titkos karakterlánc |Igen |

>[!NOTE]
>Ennél az összekötőnél követelmény az adatok másolása az Amazon S3 IAM-fiók hozzáférési kulcsainak listázása. [Ideiglenes biztonsági hitelesítő adatok](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nem támogatott.
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
Adja meg a bemeneti adatok az Azure Blob storage dataset, állítsa be a type tulajdonságot az adathalmaz **AmazonS3**. Állítsa be a **linkedServiceName** tulajdonság nevére, az Amazon S3 DataSet társított szolgáltatás. Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: [adatkészletek létrehozása](data-factory-create-datasets.md). 

Például struktúra, a rendelkezésre állás és a házirend hasonlítanak minden adatkészlet esetében (például az SQL-adatbázis, az Azure blob és az Azure tábla). A **typeProperties** szakasz más adatkészlet egyes típusai, és az adatok az adattárban helyét ismerteti. A **typeProperties** szakasz egy adatkészlet típusú **AmazonS3** (amely magában foglalja az Amazon S3 dataset) tulajdonságai a következők:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| bucketName |S3 gyűjtő neve. |Sztring |Igen |
| kulcs |S3 objektum kulcsa. |Sztring |Nem |
| előtag |S3 objektum kulcshoz előtag. Kiválasztott objektumok, amelynek kulcsait a előtaggal kezdődik. Érvényes, csak ha kulcsa üres. |Sztring |Nem |
| verzió: |A S3 objektum, ha engedélyezve van a S3 versioning verziója. |Sztring |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: a [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [JSON formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátumban ](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. <br><br> Ha azt szeretné, mint a fájlok másolása-van közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem | |
| tömörítés | Adja meg a típus és az adatok tömörítése szintjét. A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. A támogatott szintek a következők: **Optimal** és **leggyorsabb**. További információkért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem | |


> [!NOTE]
> **bucketName + kulcs** S3 objektum, amelyen gyűjtő S3 objektumok a legfelső szintű tárolója, és kulcs a S3 objektum teljes elérési útja helyét adja meg.

### <a name="sample-dataset-with-prefix"></a>Minta-adatkészleteken előtaggal

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
### <a name="sample-dataset-with-version"></a>Minta-adatkészleteken (verziójával)

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

### <a name="dynamic-paths-for-s3"></a>S3 dinamikus útvonalak
Az előző példa rögzített értékeit használja a **kulcs** és **bucketName** az Amazon S3 adatkészlet tulajdonságai.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Adat-előállító kiszámításához ezeket a tulajdonságokat, dinamikusan futásidőben, például a SliceStart rendszerváltozók használatával lehet.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Akkor is képes ugyanerre a **előtag** az Amazon S3 adatkészlet tulajdonság. Támogatott funkciók és változók listájáért lásd: [adat-előállító funkciók és rendszerváltozók](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: [folyamatok létrehozása](data-factory-create-pipelines.md). Az összes tevékenység tulajdonságai, például nevét, leírását, valamint bemeneti és kimeneti táblák és házirendek érhetők el. Tulajdonságok érhetők el a **typeProperties** szakasz a tevékenység tevékenységek minden típusának függenek. A másolási tevékenység tulajdonságai a típusú források és mosdók függenek. Ha van a másolási tevékenység során a forrás típusú **FileSystemSource** (amely tartalmazza az Amazon S3), a következő tulajdonság érhető el **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| rekurzív |Határozza meg, hogy a rekurzív módon listában S3 objektumok a könyvtárban. |Igaz/hamis |Nem |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON-példa: adatok másolása az Amazon S3 az Azure Blob-tároló
Ez a példa bemutatja, hogyan Amazon S3 Adatok másolása az Azure Blob Storage tárolóban. Azonban adatokat másolhat közvetlenül [bármely, a támogatott nyelő](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során a Data Factory használatával.

A minta a következő Data Factory-entitásokhoz JSON-definíciókat biztosítja. E definíciókat hozhat létre egy folyamatot, az adatok másolása az Amazon S3 a Blob storage használatával a [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* A társított szolgáltatás típusa [AwsAccessKey](#linked-service-properties).
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Bemeneti [dataset](data-factory-create-datasets.md) típusú [AmazonS3](#dataset-properties).
* Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta másol adatokat Amazon S3 egy Azure blob minden órában. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

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

Beállítás **"external": true** amelyben arról tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet külső az adat-előállítóban. Ez a tulajdonság igaz értékre be egy bemeneti adatkészlet nem a feldolgozási tevékenység által létrehozott.

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

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap, és a kezdési idő órában részeit.

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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Másolási tevékenység az Amazon S3 és egy blob fogadó egy folyamaton belül

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **FileSystemSource**, és **fogadó** típusúra **BlobSink**.

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
> A fogadó adatkészletből oszlopok egy forrás adatkészletből oszlopok leképezni, lásd: [az Azure Data Factoryben az adatkészlet oszlopok leképezése](data-factory-map-columns.md).


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

* Című témakörben olvashat kulcsfontosságú szerepet játszik az adatátvitelt jelölik a (másolási tevékenység) a Data Factory és különböző módokon optimalizálása azt hatás teljesítmény, a [másolása tevékenység teljesítmény- és hangolási útmutató](data-factory-copy-activity-performance.md).

* A másolási tevékenység során a folyamat létrehozásának részletes leírása, tekintse meg a [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
