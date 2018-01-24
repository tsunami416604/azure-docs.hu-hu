---
title: "Helyezze át az adatokat a PostgreSQL Azure Data Factory használatával |} Microsoft Docs"
description: "Tudnivalók az adatok áthelyezése az Azure Data Factory használatával PostgreSQL-adatbázisból."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4cec177456b007fd7c6721380c00a622b43af677
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Adatok áthelyezése az Azure Data Factory használatával PostgreSQL
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-onprem-postgresql-connector.md)
> * [2. verzió – Előzetes verzió](../connector-postgresql.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [PostgreSQL-összekötőt, a V2](../connector-postgresql.md).


Ez a cikk ismerteti, hogyan a másolási tevékenység az Azure Data Factoryben az adatok mozgatása egy helyszíni PostgreSQL-adatbázishoz. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.

Egy helyszíni PostgreSQL adattároló adatok bármely támogatott fogadó adattárolóhoz másolhatja. A másolási tevékenység által támogatott mosdók adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Adat-előállító jelenleg áthelyezése adatokat egy PostgreSQL-adatbázisból az egyéb adattárakhoz, de nem az adatok áthelyezése az egyéb adattárakhoz PostgreSQL-adatbázishoz. 

## <a name="prerequisites"></a>Előfeltételek

Data Factory szolgáltatásnak a helyszíni PostgreSQL adatforrások az adatkezelési átjáró használatával történő csatlakozást támogatja. Lásd: [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikkben tájékozódhat az adatkezelési átjáró és az átjáró beállításával kapcsolatos részletes útmutatás.

Átjáróra szükség, akkor is, ha egy Azure IaaS virtuális gép a PostgreSQL-adatbázisban tárolódik. Átjárót telepítheti adattárként ugyanazon infrastruktúra-szolgáltatási virtuális gép vagy egy másik virtuális gép mindaddig, amíg az átjáró képes kapcsolódni az adatbázishoz.

> [!NOTE]
> Lásd: [átjáró elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolati/átjáró hibaelhárítási tippek a kapcsolódó problémákat.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Az adatkezelési átjáró a PostgreSQL-adatbázishoz való kapcsolódáshoz, telepítse a [PostgreSQL-Ngpsql adatszolgáltatója](http://go.microsoft.com/fwlink/?linkid=282716) 2.0.12 és az adatkezelési átjáró ugyanazon a rendszeren 3.1.9 közötti verziójával. 7.4 verzió PostgreSQL és újabb verzió esetén támogatott.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, mely az adatok egy helyszíni PostgreSQL adattároló különböző eszközök/API-k használatával létrehozhat egy folyamatot. 

- Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást. 
- A következő eszközök segítségével hozzon létre egy folyamatot: 
    - Azure Portal
    - Visual Studio
    - Azure PowerShell
    - Azure Resource Manager-sablon
    - .NET API
    - REST API

     Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban:

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. 
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. 

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Adatok másolása egy helyszíni PostgreSQL adattároló használt adat-előállító entitások JSON-definíciók minta, lásd: [JSON-példa: adatok másolása az PostgreSQL az Azure Blob](#json-example-copy-data-from-postgresql-to-azure-blob) című szakaszát. 

A következő szakaszok részletesen bemutatják a PostgreSQL-tárolóban való adat-előállító tartozó entitások meghatározásához használt JSON tulajdonságokat:

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A következő táblázat a JSON-elemek szerepelnek PostgreSQL kapcsolódó szolgáltatásra vonatkozó leírást.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **OnPremisesPostgreSql** |Igen |
| kiszolgáló |A PostgreSQL-kiszolgáló neve. |Igen |
| adatbázis |A PostgreSQL-adatbázis neve. |Igen |
| Séma |Az adatbázisban séma neve. A séma neve a kis-és nagybetűket. |Nem |
| authenticationType |A PostgreSQL-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek a következők: névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Adja meg a felhasználónevet Basic vagy Windows-hitelesítés használata. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| gatewayName |Az átjáró, amely használatával a Data Factory szolgáltatásnak csatlakoznia a helyszíni PostgreSQL-adatbázishoz való kapcsolódáshoz neve. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében.

A typeProperties szakasz más adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A typeProperties szakasz típusú adatkészlet **RelationalTable** (amely tartalmazza a PostgreSQL dataset) tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla PostgreSQL-adatbázispéldány, amelyre a társított szolgáltatás neve hivatkozik. A táblanév a kis-és nagybetűket. |Nem (Ha **lekérdezés** a **RelationalSource** van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Például a nevét, leírását, valamint bemeneti és kimeneti táblák és házirend tulajdonságai minden típusú tevékenységek érhetők el.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően.

Ha a forrás típusa van **RelationalSource** (amely tartalmazza a PostgreSQL), a következő tulajdonságok érhetők el typeProperties szakaszában:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: `"query": "select * from \"MySchema\".\"MyTable\""`. |Nem (Ha **tableName** a **dataset** van megadva) |

> [!NOTE]
> Séma-és tábla-és nagybetűk. Tegye őket `""` (idézőjelek) a lekérdezésben.  

**Példa**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>JSON-példa: adatok másolása az PostgreSQL az Azure-Blobba
Ebben a példában a minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot biztosít [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása PostgreSQL-adatbázisból az Azure Blob Storage mutatnak. Azonban adatok átmásolhatók a megadott mosdók bármelyikét [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.   

> [!IMPORTANT]
> Ez a minta JSON kódtöredékek biztosít. Nem tartalmazza az adat-előállítóban létrehozásának részletes leírása. Lásd: [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikk lépéseit.

A minta a következő data factory entitások rendelkezik:

1. A társított szolgáltatás típusa [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta másol adatokat egy lekérdezés eredményét PostgreSQL-adatbázisban egy blob minden órában. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

Első lépésként, állítsa be az adatkezelési átjáró. Az utasítások szerepelnek a [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**PostgreSQL társított szolgáltatáshoz:**

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
**Az Azure Blob storage társított szolgáltatásnak:**

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

A példa azt feltételezi, hogy létrehozott egy tábla "MyTable" PostgreSQL és egy "időbélyeg" nevű adatsorozat időadatok oszlopot tartalmaz.

Beállítás `"external": true` tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet data factoryval való külső, és egy tevékenység adat-előállító nem hozzák.

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

**Az Azure Blob kimeneti adatkészlet:**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját és nevét a BLOB dinamikusan értékeli ki a kezdési időt a szelet által feldolgozott alapján. A mappa elérési útját használja, év, hónap, nap és a kezdési idő órában részeit.

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

**A másolási tevékenység során a következő feldolgozási sorban:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. Az adatcsatorna JSON-definícióból a **forrás** típusúra **RelationalSource** és **fogadó** típusúra **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztja az adatokat a public.usstates táblából a PostgreSQL-adatbázisban.

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
## <a name="type-mapping-for-postgresql"></a>Típus identitástagok esetén PostgreSQL
Ahogyan az a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk másolási tevékenység az eseményforrás-típusnak gyűjtése módszert használja a következő 2. lépés típusok automatikus típuskonverziók hajtja végre:

1. A natív eseményforrás-típusnak átalakítása .NET-típusa
2. .NET-típus konvertálása natív a fogadó típusa

Amikor adatokat PostgreSQL helyezi át, a következő megfeleltetéseket segítségével PostgreSQL típusból .NET-típusa.

| PostgreSQL-adatbázisból típusa | PostgresSQL aliasok | .NET-keretrendszer típusa |
| --- | --- | --- |
| abstime | |Dátum és idő | &nbsp;
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bit [(n)] | |Byte [], karakterlánc | &nbsp;
| bit különböző [(n)] |varbit |Byte [], karakterlánc |
| logikai |logikai érték |Logikai |
| Mezőbe | |Byte [], karakterlánc |&nbsp;
| bytea | |Byte [], karakterlánc |&nbsp;
| [(n)] karakter |a char [(n)] |Karakterlánc |
| [(n)] eltérő karaktert |varchar [(n)] |Karakterlánc |
| CID | |Karakterlánc |&nbsp;
| CIDR | |Karakterlánc |&nbsp;
| kör | |Byte [], karakterlánc |&nbsp;
| dátum | |Dátum és idő |&nbsp;
| DateRange | |Karakterlánc |&nbsp;
| a kétszeres pontosság |FLOAT8 |Dupla |
| INet | |Byte [], karakterlánc |&nbsp;
| intarry | |Karakterlánc |&nbsp;
| int4range | |Karakterlánc |&nbsp;
| int8range | |Karakterlánc |&nbsp;
| egész szám |int, int4 |Int32 |
| időköz [mezők] [(p)] | |Időtartomány |&nbsp;
| JSON | |Karakterlánc |&nbsp;
| jsonb | |Byte] |&nbsp;
| sor | |Byte [], karakterlánc |&nbsp;
| lseg | |Byte [], karakterlánc |&nbsp;
| macaddr | |Byte [], karakterlánc |&nbsp;
| pénz | |Decimális |&nbsp;
| numerikus [(p, s)] |Decimal [(p, s)] |Decimális |
| numrange | |Karakterlánc |&nbsp;
| oid | |Int32 |&nbsp;
| elérési út | |Byte [], karakterlánc |&nbsp;
| pg_lsn | |Int64 |&nbsp;
| pont | |Byte [], karakterlánc |&nbsp;
| Sokszög | |Byte [], karakterlánc |&nbsp;
| valós |float4 |Egyedülálló |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| gyártási |serial4 |Int32 |
| Szöveg | |Karakterlánc |&nbsp;

## <a name="map-source-to-sink-columns"></a>Térkép forrás oszlopok gyűjtése
A forrás oszlop szerepel a fogadó dataset adatkészlet leképezési oszlopok, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források ismételhető Olvasás
Ha az adatok másolását a relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja úgy is egy adatkészlet újrapróbálkozási házirendje, hogy a szelet akkor fut újra, ha hiba történik. A szelet akkor fut újra, vagy módon, ha győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy a szelet futtatása hány alkalommal kell. Lásd: [relációs források olvasni Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.
