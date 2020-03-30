---
title: Adatok áthelyezése az Amazon Redshift szolgáltatásból az Azure Data Factory használatával
description: Ismerje meg, hogyan helyezhetát át az adatokat az Amazon Redshift-ből az Azure Data Factory Copy Activity használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c2e2394bbcee5294bfb752a0af2969457ffff0ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260526"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Adatok áthelyezése az Amazon Redshift szolgáltatásból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-amazon-redshift-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-amazon-redshift.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [Amazon Redshift összekötő t a V2-ben.](../connector-amazon-redshift.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése az Amazon Redshift. A cikk az [Adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

A Data Factory jelenleg csak az Amazon Redshift-ről egy [támogatott fogadóadattárba](data-factory-data-movement-activities.md#supported-data-stores-and-formats)való átcsoportosítást támogatja. Más adattárakból az Amazon Redshiftbe történő áthelyezés nem támogatott.

> [!TIP]
> A legjobb teljesítmény elérése érdekében, amikor nagy mennyiségű adatot másol az Amazon Redshift-ből, fontolja meg a beépített Redshift **UNLOAD** parancsot az Amazon Simple Storage Service (Amazon S3) szolgáltatáson keresztül. További információt a Kirakodás használata az [Amazon Redshift adatainak másolásához című témakörben talál.](#use-unload-to-copy-data-from-amazon-redshift)

## <a name="prerequisites"></a>Előfeltételek
* Ha adatokat helyez át egy helyszíni adattárba, telepítse az [adatkezelési átjárót](data-factory-data-management-gateway.md) egy helyszíni számítógépre. Hozzáférést biztosít az Amazon Redshift fürt höz a helyszíni számítógép IP-címének használatával átjáró. További információt [a Fürthöz való hozzáférés engedélyezése című témakörben talál.](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html)
* Az adatok Azure-adattárba való áthelyezéséhez tekintse meg a [Microsoft Azure adatközpontok által használt Számítási IP-cím és SQL-tartományok című témakört.](https://www.microsoft.com/download/details.aspx?id=41653)

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységet, amelynek segítségével adatokat helyezhet át egy Amazon Redshift forrásból különböző eszközök és API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az Azure Data Factory Copy wizard használata. A folyamat másolása varázslóval történő létrehozásáról az [Oktatóanyag: Folyamat létrehozása a Másolás varázslóval című oktatóanyagcímű témakörben](data-factory-copy-data-wizard-tutorial.md)tud hatja el a folyamatot.

Folyamatot is létrehozhat a Visual Studio, az Azure PowerShell vagy más eszközök használatával. Az Azure Resource Manager-sablonok, a .NET API vagy a REST API is használható a folyamat létrehozásához. A másolási tevékenységgel rendelkező folyamat létrehozásának lépésenkénti útmutatója a [Tevékenység másolása oktatóanyagban található.](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. Összekapcsolt szolgáltatások létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. Adatkészletek létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására.
3. Hozzon létre egy folyamatot egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel.

A Másolás varázsló használatakor a Data Factory entitásokhoz automatikusan létrejönnek a JSON-definíciók. Eszközök vagy API-k használataesetén (a .NET API kivételével) a Data Factory entitásokat a JSON formátum használatával definiálhatja. A JSON-példa: Adatok másolása az Amazon Redshift az Azure Blob storage-ba a Data Factory-entitások JSON-definícióit jeleníti meg, amelyek az Amazon Redshift-adattárból adatok másolására szolgálnak.

A következő szakaszok ismertetik a JSON-tulajdonságokat, amelyek az Amazon Redshift Data Factory entitások meghatározásához használatosak.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az alábbi táblázat az Amazon Redshift-hez csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **Típus** |Ezt a tulajdonságot **AmazonRedshift**-re kell állítani. |Igen |
| **Szerver** |Az Amazon Redshift kiszolgáló IP-címe vagy állomásneve. |Igen |
| **port** |Az Amazon Redshift kiszolgáló által az ügyfélkapcsolatok figyelésére használt TCP-port száma. |Nem (az alapértelmezett érték 5439) |
| **Adatbázis** |Az Amazon Redshift adatbázis neve. |Igen |
| **Felhasználónév** |Az adatbázishoz hozzáféréssel rendelkező felhasználó neve. |Igen |
| **alaphelyzetbe állítása** |A felhasználói fiók jelszava. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A **szerkezet,** **a rendelkezésre állás**és a **házirendszakaszok** minden adatkészlettípushoz hasonlóak. Adatkészlet-típusok például az Azure SQL, az Azure Blob storage és az Azure Table storage.

A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok tárolóban való helyéről. A **RelationalTable**típusú adatkészlet **typeProperties** szakasza, amely az Amazon Redshift adatkészletet tartalmazza, a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **táblaneve** |A tábla neve az Amazon Redshift adatbázisban, amelyre a csatolt szolgáltatás hivatkozik. |Nem (ha a **RelationalSource** típusú másolási tevékenység **lekérdezési** tulajdonsága meg van adva) |

## <a name="copy-activity-properties"></a>Tevékenység tulajdonságainak másolása

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. A **név**, **leírás**, **bemeneti** tábla, **kimeneti** tábla és **házirend-tulajdonságok** minden típusú tevékenységhez elérhetők. A **typeProperties** szakaszban elérhető tulajdonságok tevékenységtípusonként eltérőek lehetnek. A Másolási tevékenység esetében a tulajdonságok az adatforrások és fogadók típusától függően változnak.

A Másolási tevékenység esetében, ha a forrás **AmazonRedshiftSource**típusú, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **Lekérdezés** | Az adatok olvasásához használja az egyéni lekérdezést. |Nem (ha egy adatkészlet **tableName** tulajdonsága meg van adva) |
| **redshiftUnloadSettings** | A tulajdonságcsoportot tartalmazza a Redshift **UNLOAD** parancs használatakor. | Nem |
| **s3LinkedServiceName** | Az Amazon S3 használni, mint egy ideiglenes boltban. A csatolt szolgáltatás **a wsAccessKey**típusú Azure Data Factory névvel van megadva. | A **redshiftUnloadSettings** tulajdonság használataesetén szükséges |
| **bucketName (vödörneve)** | Az átmeneti adatok tárolására használt Amazon S3 gyűjtőt jelzi. Ha ez a tulajdonság nincs megadva, a Másolási tevékenység automatikusan létrehoz egy gyűjtőt. | A **redshiftUnloadSettings** tulajdonság használataesetén szükséges |

Másik lehetőségként használhatja a **RelationalSource** típust, amely tartalmazza az Amazon Redshift-et, a következő tulajdonsággal a **typeProperties** szakaszban. Ne feledje, hogy ez a forrástípus nem támogatja a Redshift **UNLOAD** parancsot.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **Lekérdezés** |Az adatok olvasásához használja az egyéni lekérdezést. | Nem (ha egy adatkészlet **tableName** tulajdonsága meg van adva) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Adatok másolása az Amazon Redshift-ből az UNLOAD használatával

Az Amazon Redshift [**UNLOAD**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) parancs eltávolítja a lekérdezés eredményeit egy vagy több fájlba az Amazon S3-on. Ezt a parancsot az Amazon nagy adatkészletek Redshift ből történő másolásához ajánlott.

**Példa: Adatok másolása az Amazon Redshift-ből az Azure SQL Data Warehouse-ba**

Ez a példa adatokat másol az Amazon Redshift-ből az Azure SQL Data Warehouse-ba. A példa a Redshift **UNLOAD** parancsot, a szakaszos másolási adatokat és a Microsoft PolyBase parancsot használja.

Ebben a mintahasználati esetben a Másolási tevékenység először eltávolítja az adatokat az Amazon Redshift-ről az Amazon S3-ra a **redshiftUnloadSettings** beállításban konfigurálva. Ezután az adatok at az Amazon S3-ból az Azure Blob storage-ba másolja az **átmenetibeállítások** beállításban megadottmódon. Végül a PolyBase betölti az adatokat az SQL Data Warehouse-ba. Az összes köztes formátumot a Másolási tevékenység kezeli.

![Munkafolyamat másolása az Amazon Redshift-ről az SQL Data Warehouse-ra](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON-példa: Adatok másolása az Amazon Redshift szolgáltatásból az Azure Blob storage-ba
Ez a minta bemutatja, hogyan másolhat adatokat egy Amazon Redshift adatbázisból az Azure Blob Storage-ba. Az adatok közvetlenül bármely [támogatott fogadóba](data-factory-data-movement-activities.md#supported-data-stores-and-formats) másolhatók a Másolási tevékenység használatával.

A minta a következő adatfeldolgozó entitásokkal rendelkezik:

* [AmazonRedshift](#linked-service-properties) típusú összekapcsolt szolgáltatás
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
* [RelationalTable](#dataset-properties) típusú bemeneti [adatkészlet](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) típusú kimeneti [adatkészlet](data-factory-create-datasets.md)
* A [RelationalSource](#copy-activity-properties) és a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) tulajdonságokat használó másolási tevékenységet használó [folyamat](data-factory-create-pipelines.md)

A minta adatokat másol egy lekérdezéseredmény az Amazon Redshift egy Azure blob óránként. A mintában használt JSON-tulajdonságokat az entitásdefiníciókat követő szakaszok ismertetik.

**Amazon Redshift kapcsolt szolgáltatás**

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

**Azure Blob storage-hoz csatolt szolgáltatás**

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

A **külső** tulajdonság értéke "true", hogy tájékoztassa a Data Factory szolgáltatást arról, hogy az adatkészlet az adat-előállítón kívül található. Ez a tulajdonságbeállítás azt jelzi, hogy az adatkészletet nem az adat-előállító tevékenység hozza létre. Állítsa a tulajdonságot igaz értékre egy olyan bemeneti adatkészleten, amelyet nem a folyamatban lévő tevékenység hoz létre.

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

Az adatok óránként egy új blobba írásra kerül, ha a **gyakorisági** tulajdonságot "Óra" értékre, az **intervallum** tulajdonságot pedig 1-re állítja. A blob **folderPath** tulajdonsága dinamikusan kiértékelésre kerül. A tulajdonság értéke a feldolgozás alatt álló szelet kezdési időpontjától függ. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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

**Tevékenység másolása egy Azure Redshift-forrással (RelationalSource típusú) és egy Azure Blob-fogadóval rendelkező folyamatban**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva. A folyamat óránként fog futni. A folyamat JSON-definíciójában a **forrástípus** **RelationalSource,** a **fogadó** típusa pedig **BlobSink**lesz. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés kiválasztja az elmúlt órából másolandó adatokat.

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
### <a name="type-mapping-for-amazon-redshift"></a>Típusleképezés az Amazon Redshift hez
Az [adatmozgatási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben említettek szerint a Másolási tevékenység automatikus típuskonverziót hajt végre a forrástípustól a fogadótípusig. A típusok konvertálása kétlépéses megközelítéssel:

1. Konvertálás natív forrástípusból .NET típussá
2. Konvertálás .NET típusból natív fogadótípussá

A következő leképezések akkor használatosak, amikor a Másolási tevékenység az amazonredshift típusból .NET típusúvá alakítja az adatokat:

| Amazon Redshift típus | .NET típus |
| --- | --- |
| SMALLINT között |Int16 |
| EGÉSZ SZÁM |Int32 |
| BIGINT között |Int64 |
| Decimális |Decimal |
| VALÓS SZÁM |Egyirányú |
| DUPLA PONTOSSÁG |Double |
| Logikai |Sztring |
| Char |Sztring |
| Varchar |Sztring |
| DATE |DateTime |
| Időbélyeg |DateTime |
| TEXT |Sztring |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése oszlopokhoz
Ha meg szeretné tudni, hogyan képezheti le a forrásadatkészlet oszlopait a fogadó adatkészlet oszlopaihoz, olvassa [el az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="repeatable-reads-from-relational-sources"></a>Relációs forrásokból megismételhető olvasások
Amikor egy relációs adattárból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryban manuálisan futtathatja a szeletet. Az adatkészlet újrapróbálkozási **házirendje** is konfigurálható úgy, hogy hiba esetén újrafusson egy szeletet. Győződjön meg arról, hogy ugyanazokat az adatokat olvassa be a rendszer, függetlenül attól, hogy hányszor fut újra a szeletet. Győződjön meg arról is, hogy ugyanazokat az adatokat olvassa be a rendszer, függetlenül attól, hogy hogyan futtatja újra a szeletet. További információ: [Relációs forrásokból származó Megismételhető olvasások](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Ismerje meg a másolási tevékenység teljesítményét befolyásoló legfontosabb tényezőket, valamint a teljesítmény optimalizálásának módjait a [Másolási teljesítmény és a hangolás útmutatójában.](data-factory-copy-activity-performance.md)

## <a name="next-steps"></a>További lépések
A folyamat másolási tevékenységgel való létrehozásáról a [Másolási tevékenység oktatóanyagcímű témakörben](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)talál részletes útmutatást.
