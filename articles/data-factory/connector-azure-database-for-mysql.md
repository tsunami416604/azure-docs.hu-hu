---
title: Adatok másolása a MySQL Azure-adatbázisába és onnan
description: Megtudhatja, hogyan másolhat adatokat az Azure Database for MySQL-be és onnan egy Azure Data Factory-folyamat másolási tevékenységhasználatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/25/2019
ms.openlocfilehash: 16778000855b7859e261d8b996b081f779e579f9
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991449"
---
# <a name="copy-data-to-and-from-azure-database-for-mysql-using-azure-data-factory"></a>Adatok másolása a MySQL Azure-adatbázisába és onnan az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása az Azure Database for MySQL. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

Ez az összekötő az [Azure Database for MySQL szolgáltatásra](../mysql/overview.md)specializálódott. Ha adatokat szeretne másolni a helyszínen vagy a felhőben található általános MySQL-adatbázisból, használja a [MySQL-összekötőt.](connector-mysql.md)

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Database for MySQL-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az Azure Database for MySQL-ből adatokat másolhat bármely támogatott fogadó adattárba. Vagy bármely támogatott forrásadat-tárból másolhat adatokat az Azure Database for MySQL-be. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Az Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot is ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az Azure Database for MySQL-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Azure Database for MySQL csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **AzureMySql** | Igen |
| connectionString (kapcsolati karakterlánc) | Adja meg a MySQL-példány azure-adatbázisához való csatlakozáshoz szükséges információkat. <br/> Az Azure Key Vaultban is felhelyezhet idúra, és kihúzhatja a `password` konfigurációt a kapcsolati karakterláncból. További részleteket az [Azure Key Vault-cikkben](store-credentials-in-key-vault.md) a következő minták és áruházi hitelesítő adatok című cikkben talál. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy saját üzemeltetésű integrációs runtime (ha az adattár található magánhálózat). Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

Egy tipikus kapcsolati karakterlánc a `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. További tulajdonságok, amelyeket a tokszerint állíthat be:

| Tulajdonság | Leírás | Beállítások | Kötelező |
|:--- |:--- |:--- |:--- |
| SSLMode (SSLmode) | Ez a beállítás azt határozza meg, hogy az illesztőprogram TLS titkosítást és -ellenőrzést használ-e a MySQL-hez való csatlakozáskor. Például `SSLMode=<0/1/2/3/4>`| DISABLED (0) / PREFERÁLT (1) **(Alapértelmezett)** / KÖTELEZŐ (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Nem |
| UseSystemTrustStore | Ez a beállítás határozza meg, hogy a hitelesítésszolgáltatói tanúsítványt a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból használja-e. Például `UseSystemTrustStore=<0/1>;`| Engedélyezve (1) / Letiltva (0) **(alapértelmezett)** | Nem |

**Példa:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: jelszó tárolása az Azure Key Vaultban**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz az Azure Database által a MySQL-adatkészlethez támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni az Azure Database for MySQL szolgáltatásból, állítsa az adatkészlet típustulajdonságát **az AzureMySqlTable beállításra.** A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **AzureMySqlTable** | Igen |
| tableName | A MySQL adatbázisban lévő tábla neve. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

**Példa**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az Azure Database for MySQL-forrás- és -fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-database-for-mysql-as-source"></a>Azure Database for MySQL mint forrás

Az Azure Database for MySQL-ből történő adatok másolásához a következő tulajdonságokat támogatja a másolási tevékenység **forrása** szakasz:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **AzureMySqlSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM MyTable"`. | Nem (ha az adatkészletben a "tableName" van megadva) |
| queryCommandTimeout | A lekérdezési kérelem előtti várakozási idő idővel idővel elévül. Az alapértelmezett érték 120 perc (02:00:00) | Nem |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>A MySQL Azure-adatbázisa fogadóként

Adatok másolásához az Azure Database for MySQL, a következő tulajdonságokat támogatja a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A copy tevékenységfogadó típustulajdonságának a következőre kell beállítható: **AzureMySqlSink** | Igen |
| preCopyScript | Adjon meg egy SQL-lekérdezést a másolási tevékenység végrehajtásához, mielőtt minden futtatáskor adatokat írna az Azure Database for MySQL-be. Ezzel a tulajdonsággal megtisztíthatja az előre betöltött adatokat. | Nem |
| writeBatchSize | Adatokat szúr be az Azure Database for MySQL táblába, amikor a puffer mérete eléri a writeBatchSize-ot.<br>Az engedélyezett érték egész szám, amely a sorok számát jelöli. | Nem (az alapértelmezett érték 10 000) |
| writeBatchTimeout | Várjon időt a kötegbehelyezési művelet befejezésére, mielőtt az időtúljárna.<br>Az engedélyezett értékek timespan. Ilyen például a 00:30:00 (30 perc). | Nem (az alapértelmezett érték 00:00:30) |

**Példa:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Adattípus-leképezés az Azure Database for MySQL-hez

Amikor adatokat másol az Azure Database for MySQL-ből, a következő leképezések a MySQL adattípusokból az Azure Data Factory köztes adattípusaiba kerülnek. A [Séma- és adattípus-hozzárendelések](copy-activity-schema-and-type-mapping.md) című témakörből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrássémát és az adattípust a fogadóhoz.

| Azure Database a MySQL adattípushoz | Adatgyár köztes adattípusa |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
