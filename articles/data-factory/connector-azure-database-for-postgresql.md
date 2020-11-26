---
title: Adatok másolása Azure Database for PostgreSQL
description: Megtudhatja, hogyan másolhat adatok Azure Database for PostgreSQLba és onnan a Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2020
ms.openlocfilehash: 11e0d3336f085ccae9a7fb83ed050d69a15ce42b
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296505"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Adatok másolása Azure Database for PostgreSQLba és onnan a Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a Azure Data Factory másolási tevékenység funkciója az adatok Azure Database for PostgreSQLból történő másolásához. A [másolási tevékenységre épül Azure Data Factory](copy-activity-overview.md) cikkben, amely a másolási tevékenység általános áttekintését jeleníti meg.

Ez az összekötő a [Azure Database for PostgreSQL szolgáltatásra](../postgresql/overview.md)specializálódott. A helyszíni vagy a felhőben található általános PostgreSQL-adatbázisból származó adatok másolásához használja a [PostgreSQL-összekötőt](connector-postgresql.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Database for PostgreSQL-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Azure Database for PostgreSQL adatait átmásolhatja bármely támogatott fogadó adattárba. Vagy bármilyen támogatott forrás adattárból is másolhat adatok Azure Database for PostgreSQLba. A másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

A Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez. Ezért nem kell manuálisan telepítenie az illesztőprogramot az összekötő használatához.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő fejezetekben a Azure Database for PostgreSQL-összekötőhöz tartozó entitások definiálásához használt tulajdonságok részletes ismertetését találhatja Data Factory meg.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Azure Database for PostgreSQL társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **AzurePostgreSql**. | Yes |
| connectionString | Az Azure Database for PostgreSQLhoz való kapcsolódáshoz használandó ODBC-kapcsolati karakterlánc.<br/>A jelszót a Azure Key Vaultban is elhelyezheti, és lekérheti a `password` konfigurációt a kapcsolatok karakterláncáról. További részletekért tekintse meg a következő mintákat, és [tárolja Azure Key Vault a hitelesítő adatokat](store-credentials-in-key-vault.md) . | Yes |
| Connectvia tulajdonsággal | Ez a tulajdonság az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modult jelöli. Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár a magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |No |

Egy tipikus kapcsolatok karakterlánca: `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Az alábbiakban több tulajdonságot is beállíthat:

| Tulajdonság | Leírás | Beállítások | Kötelező |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Az illesztőprogram és az adatbázis-kiszolgáló között továbbított adattitkosításhoz használt metódus. Például:  `EncryptionMethod=<0/1/6>;`| 0 (nincs titkosítás) **(alapértelmezett)** /1 (SSL)/6 (RequestSSL) | No |
| ValidateServerCertificate (VSC) | Meghatározza, hogy az illesztőprogram érvényesítse-e az adatbázis-kiszolgáló által az SSL-titkosítás engedélyezésekor eljuttatott tanúsítványt (titkosítási módszer = 1). Például:  `ValidateServerCertificate=<0/1>;`| 0 (letiltva) **(alapértelmezett)** /1 (engedélyezve) | No |

**Példa**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Példa**:

**_A Jelszó tárolása Azure Key Vault_* _

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listájáért lásd: [Adatkészletek Azure Data Factoryban](concepts-datasets-linked-services.md). Ez a szakasz azoknak a tulajdonságoknak a listáját tartalmazza, amelyeket a Azure Database for PostgreSQL támogat az adatkészletekben.

Az adatok Azure Database for PostgreSQLból való másolásához állítsa az adatkészlet Type (típus) tulajdonságát _ * AzurePostgreSqlTable * * értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát **AzurePostgreSqlTable** értékre kell beállítani. | Yes |
| tableName | A tábla neve | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját a következő témakörben találja: [folyamatok és tevékenységek a Azure Data Factoryban](concepts-pipelines-activities.md). Ez a szakasz a Azure Database for PostgreSQL forrása által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-database-for-postgresql-as-source"></a>Azure-adatbázis PostgreSql-hez forrásként

Az adatok Azure Database for PostgreSQLból való másolásához állítsa a forrás típusát a másolás tevékenység **AzurePostgreSqlSource**. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát **AzurePostgreSqlSource** értékre kell állítani. | Yes |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `SELECT * FROM mytable` vagy `SELECT * FROM "MyTable"` . Megjegyzés a PostgreSQL-ben a rendszer az entitás nevét kis-és nagybetűket nem megkülönböztetőként kezeli, ha nem idézi elő. | Nem (ha az adatkészlet táblanév tulajdonsága meg van adva) |

**Példa**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL fogadóként

Az adatAzure Database for PostgreSQLba való másoláshoz a másolási tevékenység fogadója szakasz a **sink** következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának Type tulajdonságát **AzurePostgreSQLSink** értékre kell állítani. | Yes |
| preCopyScript | Adja meg azt az SQL-lekérdezést, amelyet végre szeretne hajtani a másolási tevékenységhez, mielőtt az egyes futtatások Azure Database for PostgreSQLba írna. Ennek a tulajdonságnak a használatával törölheti az előre feltöltött adatkészleteket. | No |
| writeBatchSize | Az Azure Database for PostgreSQL táblázatba szúrja be az adatmennyiséget, amikor a puffer mérete eléri a writeBatchSize.<br>Az engedélyezett érték egy egész szám, amely a sorok számát jelöli. | Nem (az alapértelmezett érték 10 000) |
| writeBatchTimeout | Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt.<br>Az engedélyezett értékek a TimeSpan karakterláncok. Például 00:30:00 (30 perc). | Nem (az alapértelmezett érték 00:00:30) |

**Példa**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságokkal kapcsolatos további információkért lásd: [keresési tevékenység Azure Data Factoryban](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
