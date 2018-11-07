---
title: Adatok áthelyezése az Azure Data Factory használatával Teradata |} A Microsoft Docs
description: További információk a Teradata-összekötő a Data Factory szolgáltatás, amely lehetővé teszi az adatok áthelyezése a Teradata-adatbázisból
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 98eb76d8-5f3d-4667-b76e-e59ed3eea3ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 901b44b829398ef92e63f94e0b35549e63cdd3db
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262250"
---
# <a name="move-data-from-teradata-using-azure-data-factory"></a>Adatok áthelyezése az Azure Data Factory használatával Teradata
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-onprem-teradata-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-teradata.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [Teradata-összekötő a v2-ben](../connector-teradata.md).

Ez a cikk bemutatja, hogyan használható a másolási tevékenység az Azure Data Factoryban adatok áthelyezése egy helyszíni Teradata-adatbázisból. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

A Teradata a helyszíni adattárolókból adatokat másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. A Data factory jelenleg csak helyez át adatokat a Teradata-tárolóban pedig más adattárakban, de más adattárakból származó adatok áthelyezése a Teradata adattár esetében nem támogatja. 

## <a name="prerequisites"></a>Előfeltételek
A Data factory támogatja a helyszíni Teradata források az adatkezelési átjáró-n keresztül csatlakozik. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk további információt talál az adatkezelési átjáró-lépésenként az átjáró beállítása.

Átjáróra szükség, akkor is, ha a Teradata az Azure IaaS virtuális gép üzemel. Telepítheti az átjáró adattárként azonos IaaS virtuális Gépen vagy egy másik virtuális gép mindaddig, amíg az átjáró képes kapcsolódni az adatbázishoz.

> [!NOTE]
> Lásd: [gateway hibáinak elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolódási/átjáró hibaelhárítási tippek a kapcsolatos problémákat.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Az adatkezelési átjárót a Teradata-adatbázishoz csatlakozni, telepítenie kell a [.NET-adatszolgáltató a teradata rendszerhez](https://go.microsoft.com/fwlink/?LinkId=278886) 14-es verziót vagy a fenti az adatkezelési átjárót ugyanazon a rendszeren. Teradata 12-es és újabb támogatott.

## <a name="getting-started"></a>Első lépések
Egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez a helyszíni Cassandra adattárolókból más eszközök/API-k használatával is létrehozhat. 

- A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: folyamat létrehozása a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával. 
- -Folyamatok létrehozására is használhatja az alábbi eszközöket: **az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját. 

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. 
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A Data Factory-entitások, amely adatokat másol egy helyszíni Teradata adattár használt JSON-definíciói egy minta: [példa JSON: adatok másolása az Teradata az Azure Blob](#json-example-copy-data-from-teradata-to-azure-blob) című szakaszát. 

A következő szakaszok a Teradata adattárba adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az alábbi táblázatban a JSON-elemeket Teradata-beli társított szolgáltatás leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonság értékre kell állítani: **OnPremisesTeradata** |Igen |
| kiszolgáló |A Teradata-kiszolgáló neve. |Igen |
| authenticationType |A Teradata-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek a következők: névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Ha alapszintű vagy Windows-hitelesítést használ, adja meg a felhasználónevet. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjáró neve |Az átjáró által a Data Factory szolgáltatás a helyszíni Teradata-adatbázishoz való kapcsolódáshoz használandó neve. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. Jelenleg nem támogatott a Teradata-adatkészlet tulajdonságait.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és szabályzatok minden típusú tevékenységek érhetők el.

Mivel a tevékenység a typeProperties szakasz tulajdonságai tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

Ha a forrása típusa **RelationalSource** (amely tartalmazza a Teradata), a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: válassza ki * from tábla. |Igen |

### <a name="json-example-copy-data-from-teradata-to-azure-blob"></a>JSON-példa: adatok másolása az Teradata az Azure Blob
Az alábbi példa mintául szolgáló JSON-definíciók, amelyek segítségével létrehoz egy folyamatot használatával tartalmaz [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Ezek bemutatják, hogyan Teradata adatokat másol az Azure Blob Storage. Azonban adatok átmásolhatók a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.   

A minta az alábbi data factory-entitások rendelkezik:

1. A társított szolgáltatás típusa [OnPremisesTeradata](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [RelationalTable](#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol egy lekérdezés eredménye a Teradata-adatbázis egy blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

Első lépésként a telepítő az adatkezelési átjárót. A rendszer az utasításokat a [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**Teradata-beli társított szolgáltatást:**

```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Az Azure Blob storage-beli társított szolgáltatást:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Teradata bemeneti adatkészlet:**

A minta azt feltételezi, létrehozott egy táblát "MyTable" Teradata és a egy idősorozat-adatok a "timestamp" nevű oszlopot tartalmaz.

"External" beállítása: igaz, hogy a tábla a data factory a külső, és nem egy adat-előállító tevékenység által előállított arról tájékoztatja a Data Factory szolgáltatásban.

```json
{
    "name": "TeradataDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure blobkimeneti adatkészlet:**

Adatok írása egy új blob minden órában (frequency: óra, az interval: 1). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

```json
{
    "name": "AzureBlobTeradataDataSet",
    "properties": {
        "published": false,
        "location": {
            "type": "AzureBlobLocation",
            "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "linkedServiceName": "AzureStorageLinkedService"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
**Másolási tevékenységgel rendelkező folyamat:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és a tervek szerint óránkénti tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **RelationalSource** és **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "TeradataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobTeradataDataSet"
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
                "name": "TeradataToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z",
        "isPaused": false
    }
}
```
## <a name="type-mapping-for-teradata"></a>Adattípus-leképezés a teradata rendszerhez
Említetteknek megfelelően az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) a cikkben a másolási tevékenység végzi az automatikus típuskonverziók a fogadó-típusokat az alábbi 2. lépés – a módszert használja a forrás típusa:

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

Adatok áthelyezése a Teradata, amikor a következő hozzárendeléseket használják a Teradata-típusból typ .NET.

| Teradata-adatbázis típusa | .NET-keretrendszer típusa |
| --- | --- |
| CHAR |Karakterlánc |
| CLOB |Karakterlánc |
| Kép |Karakterlánc |
| VarChar |Karakterlánc |
| VarGraphic |Karakterlánc |
| Blob |Byte] |
| Bájt |Byte] |
| VarByte |Byte] |
| BigInt |Int64 |
| ByteInt |Int16 |
| tizedes tört |tizedes tört |
| Dupla |Dupla |
| Egész szám |Int32 |
| Szám |Dupla |
| SmallInt |Int16 |
| Dátum |DateTime |
| Time |Időtartam |
| Idő időzónával együtt |Karakterlánc |
| Időbélyeg |DateTime |
| Az időzóna időbélyeg |DateTimeOffset |
| Napi időköz |Időtartam |
| Intervallum nap – óra |Időtartam |
| Intervallum nap – perc |Időtartam |
| Intervallum nap – másodperc |Időtartam |
| Intervallum óra |Időtartam |
| Intervallum óra – perc |Időtartam |
| Intervallum óra – másodperc |Időtartam |
| Időköz percben |Időtartam |
| Második időköz percben |Időtartam |
| Intervallum második |Időtartam |
| Intervallum év |Karakterlánc |
| Intervallum év, hónap |Karakterlánc |
| Intervallum hónap |Karakterlánc |
| Period(date) |Karakterlánc |
| Period(Time) |Karakterlánc |
| Időszak (idő időzónával együtt) |Karakterlánc |
| Period(Timestamp) |Karakterlánc |
| Időszak (Timestamp időzónával együtt) |Karakterlánc |
| Xml |Karakterlánc |

## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása
Fogadó-adatkészlet oszlopaihoz forrásadatkészlet leképezés oszlopai kapcsolatos további információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források megismételhető olvasása
Amikor adatmásolásra, relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az újrapróbálkozási szabályzat egy adatkészlethez, úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy módon, győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy hány alkalommal fut egy szeletet, kell. Lásd: [olvasni a relációs források Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
