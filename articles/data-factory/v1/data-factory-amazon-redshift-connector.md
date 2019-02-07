---
title: Amazon Redshift adatok áthelyezése az Azure Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, az Amazon redshiftből adatok áthelyezése az Azure Data Factory másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b7a785cc506f12360edc14555b7241a557dc400c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817335"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Adatok áthelyezése az Amazon Redshift Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-amazon-redshift-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-amazon-redshift.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [Amazon Redshift-összekötő a v2-ben](../connector-amazon-redshift.md).

Ez a cikk bemutatja, hogyan használható a másolási tevékenység az Azure Data Factoryban adatok áthelyezése az Amazon redshiftből. A cikk épül, amely a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

A Data Factory jelenleg támogatja az Amazon Redshift csak helyez át adatokat egy [támogatott fogadó adattárba](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Adatok áthelyezése a másik adattárakból származó Amazon Redshift nem támogatott.

> [!TIP]
> Az a legjobb teljesítményt nyújtsák, amikor nagy mennyiségű adat átmásolása Amazon Redshift, fontolja meg a beépített Redshift **UNLOAD** keresztül az Amazon Simple Storage Service (Amazon S3) parancsot. További információkért lásd: [használata UNLOAD adatokat másol az Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Előfeltételek
* Ha adatok egy helyszíni adattárolóban helyez át, telepítse a [adatkezelési átjáró](data-factory-data-management-gateway.md) egy a helyszíni gépen. Hozzáférést biztosít egy átjáróhoz, az Amazon Redshift-fürtön a helyi gép IP-cím használatával. Útmutatásért lásd: [engedélyezik a hozzáférést a fürthöz](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Adatok áthelyezése az Azure-adattárba, tekintse meg a [számítási IP-cím és a Microsoft Azure-adatközpontok által használt SQL-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Első lépések
Adatok áthelyezése a különböző eszközök és API-k segítségével az Amazon Redshift-forrás egy másolási tevékenységgel rendelkező folyamatot hozhat létre.

A folyamat létrehozásának legegyszerűbb módja, hogy az Azure Data Factory Copy varázslót használja. A folyamatot a másolás varázsló használatával történő létrehozásának egy gyors bemutatóért lásd: a [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md).

Az Azure Portalon, a Visual Studio, az Azure PowerShell vagy más eszközök használatával is létrehozhat egy folyamatot. Az Azure Resource Manager-sablonok, a .NET API-t vagy a REST API is használható a folyamat létrehozásához. Egy másolási tevékenységgel ellátott adatcsatorna létrehozása a lépésenkénti útmutatójáért lásd: a [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. A bemeneti és kimeneti adattárak az adat-előállítóhoz társított szolgáltatások létrehozása.
2. Adatkészleteket hoz létre, a másolási művelet bemeneti és kimeneti adatokat képviselik.
3. Létrehoz egy folyamatot egy másolási tevékenység, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként.

A másolás varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitások JSON-definíciói. Amikor az eszközök vagy az API-k (kivéve a .NET API), meghatározhatja a Data Factory-entitások a JSON formátumban. A JSON-példa: Amazon Redshift az Azure Blob storage-adatok másolása jeleníti meg, amely adatokat másol egy Amazon Redshift-adattár segítségével a Data Factory-entitások a JSON-definíciói.

A következő szakaszok ismertetik az Amazon Redshift a Data Factory-entitások definiálásához használt JSON-tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az alábbi táblázat ismerteti a JSON-elemek, amelyek egy Amazon Redshift-beli társított szolgáltatásra.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **type** |Ezt a tulajdonságot állítsa **AmazonRedshift**. |Igen |
| **server** |The IP address or host name of the Amazon Redshift server. |Igen |
| **port** |Az Amazon Redshift-kiszolgáló az ügyfélkapcsolatok figyeléséhez használt TCP-port száma. |Nem (alapértelmezés szerint a 5439) |
| **database** |Az Amazon Redshift-adatbázis neve. |Igen |
| **felhasználónév** |A felhasználó, aki hozzáfér az adatbázis neve. |Igen |
| **jelszó** |A felhasználói fiók jelszava. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

A szakaszok és definiálása az adatkészletek rendelkezésre álló tulajdonságok listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. A **struktúra**, **rendelkezésre állási**, és **házirend** szakaszok hasonlóak az összes adatkészlet esetében. Adatkészlet típusok közé tartoznak az Azure SQL, Azure Blob storage és Azure Table storage.

A **typeProperties** szakasz eltérő az egyes adatkészlet, és a tárolóban lévő adatok helyét ismerteti. **A typeProperties** szakasz egy adatkészlet típusú **RelationalTable**, amely tartalmazza az Amazon Redshift-adatkészletek, a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **Táblanév** |Az Amazon Redshift-adatbázisban, amelyre a társított szolgáltatás hivatkozik a tábla neve. |Nem (Ha a **lekérdezés** egy másolási tevékenységgel típusú tulajdonsága **RelationalSource** van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a tevékenységek meghatározása rendelkezésre álló tulajdonságok listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. A **neve**, **leírása**, **bemenetek** tábla, **kimenete** táblát, és **házirend** a tulajdonságok akkor vannak minden típusú tevékenységek esetén érhető el. Az elérhető tulajdonságok a **typeProperties** szakasz eltérőek lehetnek a tevékenységek minden típusának. A másolási tevékenység a tulajdonságok a típusú adatok forrásként és fogadóként változhat.

A másolási tevékenység, ha a forrás típusa **AmazonRedshiftSource**, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **Lekérdezés** | Az egyéni lekérdezés használata az adatok olvasásához. |Nem (Ha a **tableName** adatkészlet tulajdonság meg van adva) |
| **redshiftUnloadSettings** | A tulajdonságcsoport tartalmazza a Redshift használatakor **UNLOAD** parancsot. | Nem |
| **s3LinkedServiceName** | Az Amazon S3-ideiglenes tárolóként használni. A társított szolgáltatás típusa egy Azure Data Factory neve van megadva **AwsAccessKey**. | Használata esetén szükséges a **redshiftunloadsettings beállításaiban** tulajdonság |
| **bucketName** | Azt jelzi, hogy az Amazon S3 gyűjtőt tárolja a köztes adatokat. Ha ez a tulajdonság nincs megadva, a másolási tevékenység automatikusan létrehozza a gyűjtőbe. | Használata esetén szükséges a **redshiftunloadsettings beállításaiban** tulajdonság |

Másik lehetőségként használhatja a **RelationalSource** típus, amely tartalmazza az Amazon Redshift, a következő tulajdonság a **typeProperties** szakaszban. Megjegyzés: az adatforrás típusa nem támogatja a Redshift **UNLOAD** parancsot.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **Lekérdezés** |Az egyéni lekérdezés használata az adatok olvasásához. | Nem (Ha a **tableName** adatkészlet tulajdonság meg van adva) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Használja az adatok másolása az Amazon Redshift eltávolítása

Az Amazon Redshift [ **UNLOAD** ](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) parancs eltávolítja az adott, az Amazon S3 egy vagy több fájl egy lekérdezés eredményeit. Ez a parancs által az Amazon redshiftből nagy mennyiségű adat másolása ajánlott.

**Példa: Adatok másolása az Amazon Redshift az Azure SQL Data warehouse-bA**

Ebben a példában az Amazon redshiftből adatokat másol az Azure SQL Data Warehouse. A példában a Redshift **UNLOAD** parancs, a szakaszos másolás adatok és a Microsoft PolyBase.

A példa használati esetekhez, a másolási tevékenység először eltávolítja az adott Amazon S3, Amazon Redshift származó adatok konfigurált a **redshiftunloadsettings beállításaiban** lehetőséget. Következő lépésként, az adatokat másolja az Amazon S3-ból az Azure Blob storage-megadott a **stagingSettings** lehetőséget. Végül a PolyBase betölti az adatokat az SQL Data Warehouse-bA. A másolási tevékenység az összes a köztes formátumok kezeli.

![Az SQL Data Warehouse az Amazon redshiftből másolás munkafolyamat](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON-példa: Amazon Redshift az Azure Blob storage-adatok másolása
Ez a példa bemutatja, hogyan adatok másolása az Amazon Redshift-adatbázishoz az Azure Blob Storage. Minden közvetlenül az adatok átmásolhatók [támogatott fogadó](data-factory-data-movement-activities.md#supported-data-stores-and-formats) másolási tevékenység használatával.

A minta az alábbi data factory-entitások rendelkezik:

* A társított szolgáltatás típusa [AmazonRedshift](#linked-service-properties)
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [RelationalTable](#dataset-properties)
* Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [Azure Blobba](data-factory-azure-blob-connector.md#dataset-properties)
* A [folyamat](data-factory-create-pipelines.md) egy másolási tevékenységgel, amely használja a [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) tulajdonságai

A minta adatokat másol egy lekérdezés eredménye az Amazon Redshift Azure-blobba óránként. A példában használt JSON-tulajdonságokat az entitás meghatározásokat a következő szakaszok ismertetik.

**Amazon Redshift linked service**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Az Azure Blob storage-beli társított szolgáltatás**

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
**Amazon Redshift bemeneti adatkészlet**

A **külső** tulajdonság értéke "true" tájékoztatja a Data Factory szolgáltatásban, hogy a data factory a külső-e az adatkészlethez. A tulajdonság értéke azt jelzi, hogy az adatkészlet nem hozzák az adat-előállító adott tevékenységéhez. A tulajdonság igaz értékre állítható be, amely nem hozzák a folyamat egyik tevékenységének bemeneti adatkészlet a.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure blobkimeneti adatkészlet**

Adatok írása egy új blob minden órában beállításával a **gyakorisága** "Hour" tulajdonságot és a **időköz** tulajdonságot 1-re. A **folderPath** tulajdonság a BLOB dinamikusan értékeli ki. A tulajdonság értéke a feldolgozás alatt álló szelet kezdő időpontja alapul. A mappa elérési útját használja, az év, hónap, nap és óra részei a kezdési időpontot.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Másolási tevékenység a folyamat egy Azure Redshift-forrás (típusú RelationalSource) és a egy Azure Blob-fogadó**

A folyamat tartalmazza egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva. A folyamat minden órában van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **RelationalSource** és a **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonságot választja az adatok másolása az elmúlt egy órában.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
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
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Amazon Redshift-leképezés típusa
Ahogy korábban már említettük, az a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) cikk, a másolási tevékenység hajt végre automatikus típuskonverziók forrás típusa, a fogadó típusa. A típusok alakít át egy kétlépéses módszer használatával:

1. Egy natív forrás típusa átalakítása typ .NET
2. Typ .NET átalakítása egy natív fogadó típusa

A következő hozzárendeléseket használják, amikor a másolási tevékenységgel alakítja át az adatokat egy Amazon Redshift-típusból typ .NET:

| Amazon Redshift type | Typ .NET |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimal |
| VALÓDI |Single |
| A KÉTSZERES PONTOSSÁG |Double |
| LOGIKAI ÉRTÉK |String |
| CHAR |String |
| VARCHAR |String |
| DATE |DateTime |
| IDŐBÉLYEG |DateTime |
| SZÖVEG |String |

## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása
A forrásadatkészlet oszlopok leképezése a fogadó-adatkészlet az oszlopok kapcsolatban lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>A relációs források megismételhető olvasások
Amikor adatokat másol egy relációs adattároló, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az ismételt **házirend** szelet újrafuttatása, ha hiba történik egy adatkészlethez. Győződjön meg arról, hogy ugyanazokat az adatokat olvasható, függetlenül attól, hogy hány alkalommal fut újra a szeletet. Győződjön meg arról, hogy ugyanazokat az adatokat függetlenül attól, hogy futtassa újra a szeletet olvasható is. További információkért lásd: [Repeatable beolvassa a relációs források](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
További információ a másolási tevékenység és a teljesítmény optimalizálása teljesítményét befolyásoló legfontosabb tényezők a [másolási tevékenységek teljesítményéről és finomhangolási útmutató](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>További lépések
Folyamat létrehozása másolási tevékenységgel rendelkező részletes ismertetését lásd: a [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
