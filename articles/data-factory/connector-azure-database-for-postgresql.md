---
title: Adatok másolása a PostgreSQL Azure-adatbázisába és onnan
description: Megtudhatja, hogyan másolhat adatokat az Azure Database for PostgreSQL-be és onnan egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/16/2019
ms.openlocfilehash: b85e72ae6698cd9fa018c940e158bfcf25279ed5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410463"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Adatok másolása a PostgreSQL Azure-adatbázisába és onnan az Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk ismerteti, hogyan használhatja a másolási tevékenység funkció az Azure Data Factory adatok másolása az Azure Database for PostgreSQL. Az [Azure Data Factory-ban végzett másolási tevékenységre](copy-activity-overview.md) épül, amely általános áttekintést nyújt a másolási tevékenységről.

Ez az összekötő az [Azure Database for PostgreSQL szolgáltatásra](../postgresql/overview.md)specializálódott. Ha adatokat szeretne másolni egy általános PostgreSQL adatbázisból, amely a helyszínen vagy a felhőben található, használja a [PostgreSQL-összekötőt.](connector-postgresql.md)

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Database for PostgreSQL-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

Az Azure Database for PostgreSQL-ből adatokat másolhat bármely támogatott fogadó adattárba. Vagy bármely támogatott forrásadat-tárból másolhat adatokat az Azure Database for PostgreSQL-be. Az adattárak listáját, amelyeka másolási tevékenység támogatja a források és a fogadók, lásd: [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez. Ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot sem az összekötő használatához.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Data Factory-entitások meghatározására használt, a PostgreSQL-összekötőhöz tartozó Data Factory entitások definiálására használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Azure Database for PostgreSQL csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságnak a következőre kell állítania: **AzurePostgreSql**. | Igen |
| connectionString (kapcsolati karakterlánc) | ODBC-kapcsolati karakterlánc a PostgreSQL Azure Database for PostgreSQL-hez való csatlakozáshoz.<br/>Az Azure Key Vaultban is elhelyezhet `password` egy jelszót, és kihúzhatja a konfigurációt a kapcsolati karakterláncból. További részletekért tekintse meg az alábbi mintákat és [az Áruházhitelesítő adatokat az Azure Key Vaultban.](store-credentials-in-key-vault.md) | Igen |
| connectVia | Ez a tulajdonság az adattárhoz való csatlakozáshoz használandó [integrációs futásidőt](concepts-integration-runtime.md) jelöli. Használhatja az Azure-integrációs runtime vagy saját üzemeltetésű integrációs runtime (ha az adattár található magánhálózat). Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

Egy tipikus kapcsolati karakterlánc a `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Az esethez képest további tulajdonságokat állíthat be:

| Tulajdonság | Leírás | Beállítások | Kötelező |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Az illesztőprogram által az illesztőprogram és az adatbázis-kiszolgáló között küldött adatok titkosítására használt módszer. Például,`EncryptionMethod=<0/1/6>;`| 0 (nincs titkosítás) **(alapértelmezett)** / 1 (SSL) / 6 (RequestSSL) | Nem |
| ValidateServerCertificate (VSC) | Azt határozza meg, hogy az illesztőprogram érvényesítse-e az adatbázis-kiszolgáló által küldött tanúsítványt, ha az SSL-titkosítás engedélyezve van (Titkosítási módszer=1). Például,`ValidateServerCertificate=<0/1>;`| 0 (letiltva) **(alapértelmezett)** / 1 (engedélyezve) | Nem |

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

***Jelszó tárolása az Azure Key Vaultban***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek az Azure Data Factory ban](concepts-datasets-linked-services.md)című témakörben tartalmazza. Ez a szakasz azoknak a tulajdonságoknak a listáját tartalmazza, amelyeket az Azure Database for PostgreSQL támogat adatkészletekben.

Ha adatokat szeretne másolni az Azure Database for PostgreSQL szolgáltatásból, állítsa az adatkészlet típustulajdonságát **az AzurePostgreSqlTable beállításra.** A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát **AzurePostgreSqlTable-re** kell állítani. | Igen |
| tableName | A tábla neve | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Azure Data Factory folyamatai és tevékenységei című témakörben tartalmazza.](concepts-pipelines-activities.md) Ez a szakasz az Azure Database for PostgreSQL-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-database-for-postgresql-as-source"></a>Azure Database for PostgreSql mint forrás

Ha adatokat szeretne másolni az Azure Database for PostgreSQL szolgáltatásból, állítsa be a forrástípusát a másolási tevékenységben az **AzurePostgreSqlSource mezőre.** A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát **az AzurePostgreSqlSource** mezőre kell állítani. | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például:`"SELECT * FROM MyTable"` | Nem (ha a tableName tulajdonság meg van adva) |

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
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>A PostgreSQL Azure-adatbázisa fogadóként

Adatok másolásához az Azure Database for PostgreSQL, a következő tulajdonságokat támogatja a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának típustulajdonságát **az AzurePostgreSQLSink**beállításra kell állítani. | Igen |
| preCopyScript | Adja meg az SQL-lekérdezést a másolási tevékenység végrehajtásához, mielőtt minden futtatáskor adatokat írna a PostgreSQL Azure-adatbázisába. Ezzel a tulajdonsággal megtisztíthatja az előre betöltött adatokat. | Nem |
| writeBatchSize | Adatokat szúr be az Azure Database for PostgreSQL táblába, amikor a puffer mérete eléri a writeBatchSize-ot.<br>Az engedélyezett érték egy egész szám, amely a sorok számát jelöli. | Nem (az alapértelmezett érték 10 000) |
| writeBatchTimeout | Várjon időt a kötegbehelyezési művelet befejezésére, mielőtt az időtúljárna.<br>Az engedélyezett értékek Timespan karakterláncok. Ilyen például a 00:30:00 (30 perc). | Nem (az alapértelmezett érték 00:00:30) |

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

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokról további információt az [Azure Data Factory keresgupatevékenységcímű témakörben talál.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [Támogatott adattárak című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)
