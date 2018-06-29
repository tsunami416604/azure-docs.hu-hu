---
title: Adatok áthelyezése az Amazon Redshift Azure Data Factory használatával |} Microsoft Docs
description: Megtudhatja, hogyan tárolt adatok mozgatása az Amazon Redshift Azure Data Factory másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7ece34809734478ddb52c12d5dbd92291231f439
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045687"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Helyezze át az adatokat az Amazon Redshift Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](data-factory-amazon-redshift-connector.md)
> * [(Az aktuális verzió) 2-es verzió](../connector-amazon-redshift.md)

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [Amazon Redshift összekötőt, a V2](../connector-amazon-redshift.md).

Ez a cikk ismerteti, hogyan a másolási tevékenység során az Azure Data Factoryben az adatok mozgatása Amazon Redshift. A cikk épít, a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést. 

Adat-előállító jelenleg támogatja az Amazon Redshift csak áthelyezése adatait egy [támogatott fogadó adattár](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Adatok áthelyezése az egyéb adattárakhoz Amazon Redshift nem támogatott.

> [!TIP]
> A legjobb teljesítmény eléréséhez amikor nagy mennyiségű adat másolása Amazon Redshift, fontolja meg a beépített Redshift **UNLOAD** Amazon egyszerű tárolási szolgáltatás (Amazon S3) parancsot. További információkért lásd: [használata UNLOAD adatok másolása Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Előfeltételek
* Ha a helyszíni adattárolóihoz adatokat helyez át, telepítse [az adatkezelési átjáró](data-factory-data-management-gateway.md) a helyi gépen. Az Amazon Redshift fürt átjáró hozzáférés engedélyezése a helyi gép IP-cím használatával. Útmutatásért lásd: [engedélyezi a hozzáférést a fürthöz](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Adatok áthelyezése az Azure data tárolóhoz, tekintse meg a [számítási IP-cím és a Microsoft Azure Adatközpontjaiban által használt SQL-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Első lépések
A másolási tevékenység áthelyezni az adatokat Amazon Redshift forrásból származó különböző eszközöket és API-k segítségével létrehozhat egy folyamatot.

Hozzon létre egy folyamatot legegyszerűbb módja az Azure Data Factory másolása varázsló használatával. A folyamat létrehozása a varázsló segítségével gyorsan útmutatást lásd: a [oktatóanyag: hozzon létre egy folyamatot a másolása varázslóval](data-factory-copy-data-wizard-tutorial.md).

Az Azure-portálon, a Visual Studio, az Azure PowerShell vagy a más eszközök segítségével is létrehozhat egy folyamatot. Az Azure Resource Manager-sablonok, a .NET API-t vagy a REST API-t létrehozni a feldolgozási sor is használható. Hozzon létre egy folyamatot a másolási tevékenység lépésenkénti útmutatójáért tekintse meg a [másolási tevékenység az oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban: 

1. Bemeneti hivatkozásra, és a kimeneti adatok az áruházakkal, a data factory társított szolgáltatások létrehozásához.
2. A másolási művelet bemeneti és kimeneti adatok adatkészletek létrehozása. 
3. Hozzon létre egy folyamatot, amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet a másolási tevékenység. 

A varázsló használatakor a Data Factory entitások JSON-definíciók automatikusan jönnek létre. Eszközök vagy API-k (kivéve a .NET API-t) használ, amikor az a JSON formátum használatával adja meg a Data Factory entitások. A [JSON-példa: adatok másolása az Amazon Redshift az Azure Blob Storage tárolóban](#json-example-copy-data-from-amazon-redshift-to-azure-blob) jeleníti meg a JSON-definíciókat a Data Factory entitások adatok másolása az Amazon Redshift adattár használt.

A következő szakaszok ismertetik a JSON tulajdonságokat, amelyeket a Data Factory entitások meghatározásához az Amazon Redshift használják.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő táblázat ismerteti a JSON-elemek szerepelnek, amelyek az Amazon Redshift kapcsolódó szolgáltatásra.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **type** |Ez a tulajdonság értékre kell állítani **AmazonRedshift**. |Igen |
| **server** |A kiszolgáló IP-címét vagy állomásnevét kiszolgálónevét az Amazon Redshift. |Igen |
| **port** |A TCP-portot, amelyen az Amazon Redshift kiszolgáló ügyfélkapcsolatokat száma. |Nem (alapértelmezett érték 5439) |
| **database** |Az Amazon Redshift adatbázis nevét. |Igen |
| **felhasználónév** |A felhasználó, aki hozzáféréssel rendelkezik az adatbázishoz neve. |Igen |
| **jelszó** |A felhasználói fiók jelszavát. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

A szakaszok és meghatározásához adatkészletek rendelkezésre álló tulajdonságok listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. A **struktúra**, **rendelkezésre állási**, és **házirend** hasonlítanak minden adatkészlet esetében. Adatkészlet típusok például az Azure SQL, az Azure Blob storage és az Azure Table storage.

A **typeProperties** szakasz eltérő adatkészlet egyes típusai és tájékoztatást ad azokról a tárolóban lévő adatok helyét. **A typeProperties** szakasz egy adatkészlet típusú **RelationalTable**, mely tartalmazza az Amazon Redshift adatkészlet tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **Táblanév** |A tábla az Amazon Redshift adatbázisban, amely hivatkozik a társított szolgáltatás neve. |Nem (Ha a **lekérdezés** tulajdonság típusa másolási tevékenység **RelationalSource** van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tevékenységek meghatározásához rendelkezésre álló tulajdonságok listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. A **neve**, **leírás**, **bemenetek** tábla, **kimenete** tábla, és **házirend** tulajdonság minden típusú tevékenységek esetén érhető el. Az elérhető tulajdonságok a **typeProperties** szakasz eltérőek az egyes tevékenységhez. A másolási tevékenység során a tulajdonságok az adatforrások és mosdók függenek.

A másolási tevékenység, ha az adatforrás típusú **AmazonRedshiftSource**, a következő tulajdonságok érhetők el **typeProperties** szakasz:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **lekérdezés** | Az egyéni lekérdezés segítségével olvassa el az adatokat. |Nem (Ha a **tableName** a DataSet adatkészlet tulajdonság meg van adva) |
| **redshiftUnloadSettings** | A tulajdonság csoportot tartalmaz, a Redshift használatakor **UNLOAD** parancsot. | Nem |
| **s3LinkedServiceName** | Az Amazon S3 ideiglenes tárolóként történő használatához. A társított szolgáltatás típusa egy Azure Data Factory neve megadott **AwsAccessKey**. | Szükséges a használata esetén a **redshiftUnloadSettings** tulajdonság |
| **bucketName** | Azt jelzi, hogy az Amazon S3 gyűjtő használni az átmeneti adatok tárolására. Ha ez a tulajdonság nem áll rendelkezésre, másolási tevékenység automatikus-hoz létre egy gyűjtőjét. | Szükséges a használata esetén a **redshiftUnloadSettings** tulajdonság |

Másik lehetőségként használhatja a **RelationalSource** típusa, Amazon Redshift, beleértve a következő tulajdonság a **typeProperties** szakasz. Ez a forrástípus nem támogatja a Redshift **UNLOAD** parancsot.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **lekérdezés** |Az egyéni lekérdezés segítségével olvassa el az adatokat. | Nem (Ha a **tableName** a DataSet adatkészlet tulajdonság meg van adva) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Adatok másolása az Amazon Redshift UNLOAD segítségével

Az Amazon Redshift [ **UNLOAD** ](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) parancs eltávolítja az Amazon S3 egy vagy több fájlt lekérdezés eredményeit. Ez a parancs által Amazon Redshift nagy adatkészletek másolása ajánlott.

**Példa: Adatok másolása az Amazon Redshift az Azure SQL Data Warehouse**

Ebben a példában lévő Amazon Redshift adatokat az Azure SQL Data Warehouse másolja. A példában a Redshift **UNLOAD** parancs, az előkészített adatok és a Microsoft PolyBase.

Ez jelen példában használja a másolási tevékenység során először eltávolítja az Amazon S3 Amazon Redshift adatokat be a **redshiftUnloadSettings** lehetőséget. Ezt követően az adatokat a rendszer átmásolja az Amazon S3 Azure Blob Storage a a **stagingSettings** lehetőséget. Végezetül PolyBase az SQL Data Warehouse betölti az adatokat. A közbenső formátumok összes másolási tevékenység kezeli.

![Az SQL Data Warehouse Amazon Redshift munkafolyamat másolása](media\data-factory-amazon-redshift-connector\redshift-to-sql-dw-copy-workflow.png)

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

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON-példa: adatok másolása az Amazon Redshift az Azure Blob-tároló
Ez a példa bemutatja az Amazon Redshift adatbázisból származó adatok másolása az Azure Blob Storage. Adatok átmásolhatók közvetlenül bármely [fogadó támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) másolási tevékenység használatával.  

A minta a következő data factory entitások rendelkezik:

* A társított szolgáltatás típusa [AmazonRedshift](#linked-service-properties)
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Bemeneti [dataset](data-factory-create-datasets.md) típusú [RelationalTable](#dataset-properties)
* Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* A [csővezeték](data-factory-create-pipelines.md) , a másolási tevékenység által használt a [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) tulajdonságai

A minta másol adatokat az Amazon Redshift lekérdezés eredményeként egy Azure blob óránként. A JSON-tulajdonságok a mintában használt entitás definíciókat Ez a rész ismerteti.

**Amazon Redshift társított szolgáltatás**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Az Azure Blob storage társított szolgáltatás**

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

A **külső** tulajdonság értéke "true" tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet nem az adat-előállítóban belső. Ezzel a beállítással azt jelzi, hogy az adatkészlet nem egy adat-előállító tevékenység által létrehozott. Tulajdonságának beállítása TRUE egy bemeneti adatkészlet nem a feldolgozási tevékenység által létrehozott.

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

Adatot ír egy új blob óránként úgy, hogy a **gyakoriság** "Hour" tulajdonságot, és a **időköz** 1 tulajdonság. A **folderPath** tulajdonság a BLOB dinamikusan történik. A tulajdonság értékének a kezdési időt a szelet által feldolgozott alapul. A mappa elérési útját használja, év, hónap, nap, és a kezdési idő órában részeit.

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

**A folyamat egy Azure Redshift (típusú RelationalSource) és az Azure Blob fogadó másolási tevékenység**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva. A folyamat minden órában van ütemezve. Az adatcsatorna JSON-definícióban a **forrás** típusúra **RelationalSource** és a **fogadó** típusúra **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztása az adatok másolása az elmúlt egy órában.

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
### <a name="type-mapping-for-amazon-redshift"></a>Az Amazon Redshift leképezésének
Ahogyan az a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, a másolási tevékenység automatikus típuskonverziók forrástípus típus gyűjtése hajt végre. A adattípusára kétlépcsős megközelítést használatával:

1. Egy natív forrástípus átalakítása .NET-típus
2. .NET-típus konvertálása a natív a fogadó típusa

A következő megfeleltetéseket szolgálnak, amikor a másolási tevékenység konvertálja az adatokat az Amazon Redshift típusból .NET-típus:

| Amazon Redshift típusa | .NET-típusa |
| --- | --- |
| SMALLINT |Int16 |
| EGÉSZ SZÁM |Int32 |
| BIGINT |Int64 |
| DECIMÁLIS |Decimális |
| VALÓS |Önálló |
| A KÉTSZERES PONTOSSÁG |Dupla |
| LOGIKAI ÉRTÉK |Sztring |
| KARAKTER |Sztring |
| VARCHAR |Sztring |
| DATE |DateTime |
| IDŐBÉLYEG |DateTime |
| SZÖVEG |Sztring |

## <a name="map-source-to-sink-columns"></a>Térkép forrás oszlopok gyűjtése
A forrás adatkészletben levő oszlopok hozzárendelése oszlop szerepel a fogadó dataset, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>A relációs források ismételhető olvasási műveletek
Ha az adatokat másolni relációs adattároló, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Úgy konfigurálhatja az újrapróbálkozási **házirend** egy adatkészlet szelet újrafuttathatja, amikor hiba történik. Győződjön meg arról, hogy ugyanazokat az adatokat olvasható, függetlenül attól, hogy hány alkalommal fordult elő a szelet futtassa újra. Ellenőrizze azt is, hogy ugyanazokat az adatokat hogyan futtassa újból a szeletet függetlenül olvasható. További információkért lásd: [Repeatable olvassa be az relációs források](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
További tudnivalók a másolási tevékenység és a teljesítmény optimalizálása teljesítményt befolyásoló legfontosabb tényezők a [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md). 

## <a name="next-steps"></a>További lépések
Folyamat létrehozása a másolási tevékenység részletes ismertetését lásd: a [másolási tevékenység az oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
