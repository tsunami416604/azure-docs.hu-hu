---
title: Helyezze át az adatokat a PostgreSQL az Azure Data Factory használatával |} A Microsoft Docs
description: Tudnivalók az adatok áthelyezése az Azure Data Factory használatával PostgreSQL-adatbázisból.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2f964ac77ade69f14692a337f17011e93f85f68c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025708"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Adatok áthelyezése a postgresql-hez az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-postgresql-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-postgresql.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [PostgreSQL-összekötő a v2-ben](../connector-postgresql.md).


Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység használatával helyezheti át egy helyszíni PostgreSQL-adatbázishoz. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

A helyszíni PostgreSQL adattárolókból adatokat másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats). A Data factory jelenleg helyez át adatokat egy PostgreSQL-adatbázishoz pedig más adattárakban, de más adattárakból származó adatok áthelyezése a PostgreSQL-adatbázis esetében nem támogatja. 

## <a name="prerequisites"></a>Előfeltételek

A Data Factory szolgáltatás támogatja a csatlakozást egy helyszíni PostgreSQL forrásra az adatkezelési átjáró segítségével. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk további információt talál az adatkezelési átjáró-lépésenként az átjáró beállítása.

Átjáróra szükség, akkor is, ha a PostgreSQL-adatbázishoz az Azure IaaS virtuális gép üzemel. Telepíthet átjárót adattárként azonos IaaS virtuális Gépen vagy egy másik virtuális gép mindaddig, amíg az átjáró képes kapcsolódni az adatbázishoz.

> [!NOTE]
> Lásd: [gateway hibáinak elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolódási/átjáró hibaelhárítási tippek a kapcsolatos problémákat.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Adatkezelési átjáró csatlakozni a PostgreSQL-adatbázishoz, telepítse a [Ngpsql adatszolgáltató a PostgreSQL-hez készült](https://go.microsoft.com/fwlink/?linkid=282716) 2.0.12-es és az adatkezelési átjárót ugyanazon a rendszeren 3.1.9 közötti verziójával. PostgreSQL 7.4 verzió újabb támogatott.

## <a name="getting-started"></a>Első lépések
Egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez a helyszíni PostgreSQL adattárolókból más eszközök/API-k használatával is létrehozhat. 

- A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával. 
- A következő eszközök használatával hozzon létre egy folyamatot: 
    - Azure Portal
    - Visual Studio
    - Azure PowerShell
    - Azure Resource Manager-sablon
    - .NET API
    - REST API

     Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját. 

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. 
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A Data Factory-entitások, amely adatokat másol egy helyszíni PostgreSQL adattár használt JSON-definíciói egy minta: [JSON-példa: Adatok másolása az Azure Blob PostgreSQL](#json-example-copy-data-from-postgresql-to-azure-blob) című szakaszát. 

Az alábbi szakaszok nyújtanak egy PostgreSQL-adattárba adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a JSON-elemeket társított PostgreSQL szolgáltatás leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **OnPremisesPostgreSql** |Igen |
| kiszolgáló |A PostgreSQL-kiszolgáló neve. |Igen |
| adatbázis |A PostgreSQL-adatbázis neve. |Igen |
| séma |A séma az adatbázis neve. A séma neve a kis-és nagybetűket. |Nem |
| authenticationType |A PostgreSQL-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Ha alapszintű vagy Windows-hitelesítést használ, adja meg a felhasználónevet. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjáró neve |Az átjáró által a Data Factory szolgáltatás a helyi PostgreSQL-adatbázishoz való kapcsolódáshoz használandó neve. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében.

A typeProperties szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyére vonatkozó információkat. A typeProperties szakasz típusú adatkészlet **RelationalTable** (amely tartalmazza a PostgreSQL-adatkészlet) a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A PostgreSQL-adatbázis példányában, amelyek a társított szolgáltatás hivatkozik a tábla neve. A tableName a kis-és nagybetűket. |Nem (Ha **lekérdezés** , **RelationalSource** van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és a házirend az összes típusú tevékenységek érhetők el.

Mivel a tevékenység a typeProperties szakasz tulajdonságai tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

Ha a forrás típusa van **RelationalSource** (amely tartalmazza a PostgreSQL), typeProperties szakasz érhető el az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: `"query": "select * from \"MySchema\".\"MyTable\""`. |Nem (Ha **tableName** , **adatkészlet** van megadva) |

> [!NOTE]
> Séma-és tábla-és nagybetűk. Tegye őket a `""` (dupla idézőjel) a lekérdezésben.  

**Példa**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>JSON-példa: Adatok másolása az Azure Blob PostgreSQL
Ebben a példában biztosít, amellyel létrehoz egy folyamatot használatával példa JSON-definíciók [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása a PostgreSQL-adatbázisból az Azure Blob Storage mutatnak. Azonban adatok átmásolhatók a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.   

> [!IMPORTANT]
> Ez a példa JSON-kódrészletek biztosít. Nem tartalmaz részletes útmutató az adat-előállító létrehozásához. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) részletesen ismertető cikket.

A minta az alábbi data factory-entitások rendelkezik:

1. A társított szolgáltatás típusa [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol egy PostgreSQL-adatbázishoz a lekérdezés eredménye egy blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

Első lépésként, állítsa be az adatkezelési átjárót. A rendszer az utasításokat a [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**PostgreSQL-beli társított szolgáltatást:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
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
    "type": "AzureStorage",
    "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
    }
    }
}
```
**PostgreSQL bemeneti adatkészlet:**

A minta azt feltételezi, létrehozott egy táblát "MyTable" PostgreSQL és a egy idősorozat-adatok a "timestamp" nevű oszlopot tartalmaz.

Beállítás `"external": true` a Data Factory szolgáltatás tájékoztatja, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
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

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1.). A mappa elérési útját és nevét a BLOB dinamikusan a feldolgozás alatt álló szelet kezdő időpontja alapján értékeli ki. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Másolási tevékenységgel rendelkező folyamat:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és a tervek szerint óránkénti tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **RelationalSource** és **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztja az adatokat a PostgreSQL-adatbázishoz a public.usstates táblából.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
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
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>Adattípus-leképezés a PostgreSQL-hez
Említetteknek megfelelően az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) cikk másolási tevékenység hajt végre automatikus típuskonverziók a fogadó-típusokat az alábbi 2. lépés – a módszert használja a forrás típusa:

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

Adatok áthelyezése a postgresql-hez, amikor a következő hozzárendeléseket használják PostgreSQL típusból typ .NET.

| PostgreSQL-adatbázis típusa | PostgresSQL aliasok | .NET-keretrendszer típusa |
| --- | --- | --- |
| abstime | |Dátum és idő | &nbsp;
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bit [(n)] | |Byte [], karakterlánc | &nbsp;
| bit különböző [(n)] |varbit |Byte [], karakterlánc |
| logikai |Logikai |Logikai |
| Box | |Byte [], karakterlánc |&nbsp;
| bytea | |Byte [], karakterlánc |&nbsp;
| [(n)] karakter |char [(n)] |Karakterlánc |
| [(n)] eltérő karaktert |varchar [(n)] |Karakterlánc |
| CID | |Karakterlánc |&nbsp;
| CIDR | |Karakterlánc |&nbsp;
| Kör | |Byte [], karakterlánc |&nbsp;
| dátum | |Dátum és idő |&nbsp;
| DateRange | |Karakterlánc |&nbsp;
| a kétszeres pontosság |FLOAT8 |Dupla |
| inet | |Byte [], karakterlánc |&nbsp;
| intarry | |Karakterlánc |&nbsp;
| int4range | |Karakterlánc |&nbsp;
| int8range | |Karakterlánc |&nbsp;
| egész szám |int, int4 |Int32 |
| intervallum [mezők] [(p)] | |Időtartomány |&nbsp;
| JSON | |Karakterlánc |&nbsp;
| jsonb | |Byte] |&nbsp;
| Vonal | |Byte [], karakterlánc |&nbsp;
| lseg | |Byte [], karakterlánc |&nbsp;
| macaddr | |Byte [], karakterlánc |&nbsp;
| költséget takaríthat meg | |Tizedes tört |&nbsp;
| numerikus [(p, s)] |tizedes tört [(p, s)] |Tizedes tört |
| numrange | |Karakterlánc |&nbsp;
| objektumazonosító | |Int32 |&nbsp;
| elérési út | |Byte [], karakterlánc |&nbsp;
| pg_lsn | |Int64 |&nbsp;
| Pont | |Byte [], karakterlánc |&nbsp;
| Sokszög | |Byte [], karakterlánc |&nbsp;
| valódi |float4 |Önálló |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| gyártási |serial4 |Int32 |
| szöveg | |Karakterlánc |&nbsp;

## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása
Fogadó-adatkészlet oszlopaihoz forrásadatkészlet leképezés oszlopai kapcsolatos további információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források megismételhető olvasása
Amikor adatmásolásra, relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az újrapróbálkozási szabályzat egy adatkészlethez, úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy módon, győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy hány alkalommal fut egy szeletet, kell. Lásd: [olvasni a relációs források Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
