---
title: Adatok másolása Azure Database for MySQLba és onnan a Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatok Azure Database for MySQLba és onnan a Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: jingwang
ms.openlocfilehash: c15a60b7329359ee8d3e429159eb178c0c9b4782
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018762"
---
# <a name="copy-data-to-and-from-azure-database-for-mysql-using-azure-data-factory"></a>Adatok másolása Azure Database for MySQLba és onnan a Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Azure Database for MySQLból való másolásához. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

Ez az összekötő [Azure Database for MySQL szolgáltatásra](../mysql/overview.md)specializálódott. A helyszínen vagy a felhőben található általános MySQL-adatbázisból származó adatok másolásához használja a [MySQL-összekötőt](connector-mysql.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Database for MySQL-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Azure Database for MySQL adatait átmásolhatja bármely támogatott fogadó adattárba. Vagy bármilyen támogatott forrás adattárból is másolhat adatok Azure Database for MySQLba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolat beépített illesztőprogramot tartalmaz, ezért nem kell manuálisan telepítenie az összes illesztőprogram ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek Data Factory Azure Database for MySQL-összekötőhöz tartozó entitások definiálásához használatosak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Azure Database for MySQL társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **AzureMySql** | Igen |
| connectionString | Az Azure Database for MySQL-példányhoz való kapcsolódáshoz szükséges adatok meghatározása. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A jelszót a Azure Key Vaultban is elhelyezheti, és `password` lekérheti a konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

Egy tipikus kapcsolati karakterlánc `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. További tulajdonságok beállíthatja, hogy az eset száma:

| Tulajdonság | Leírás | Beállítások | Kötelező |
|:--- |:--- |:--- |:--- |
| SSLMode | Ez a beállítás határozza meg, hogy az illesztőprogram SSL-titkosítást és ellenőrzést használ-e a MySQL-hez való kapcsolódáskor. Például `SSLMode=<0/1/2/3/4>`| Letiltva (0)/ELŐNYben részesített (1) **(alapértelmezett)** /kötelező (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | Nem |
| UseSystemTrustStore | Ezzel a beállítással adható meg, hogy a rendszer egy HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használ-e a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból. Például `UseSystemTrustStore=<0/1>;`| Engedélyezve (1)/Letiltva (0) **(alapértelmezett)** | Nem |

**Példa:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: a Jelszó tárolása Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;"
            },
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Azure Database for MySQL adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Azure Database for MySQLból való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **AzureMySqlTable**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **AzureMySqlTable** | Igen |
| tableName | A MySQL-adatbázisban található tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Azure Database for MySQL forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-database-for-mysql-as-source"></a>Azure Database for MySQL forrásként

Az adatok Azure Database for MySQLból történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **AzureMySqlSource** | Igen |
| query | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |
| queryCommandTimeout | A lekérdezési kérelem időtúllépése előtti várakozási idő. Az alapértelmezett érték 120 perc (02:00:00) | Nem |

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

### <a name="azure-database-for-mysql-as-sink"></a>Azure Database for MySQL fogadóként

Az adatAzure Database for MySQLba való másoláshoz a másolási tevékenység fogadója szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát a következőre kell beállítani: **AzureMySqlSink** | Igen |
| preCopyScript | Adja meg a másolási tevékenység végrehajtásához szükséges SQL-lekérdezést, mielőtt az egyes futtatások Azure Database for MySQLbe írna. Ennek a tulajdonságnak a használatával törölheti az előre feltöltött adatkészleteket. | Nem |
| writeBatchSize | Az Azure Database for MySQL táblázatba szúrja be az adatmennyiséget, amikor a puffer mérete eléri a writeBatchSize.<br>Az engedélyezett érték olyan egész szám, amely a sorok számát jelöli. | Nem (az alapértelmezett érték 10 000) |
| writeBatchTimeout | Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt.<br>Az engedélyezett értékek a TimeSpan. Például 00:30:00 (30 perc). | Nem (az alapértelmezett érték 00:00:30) |

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

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Azure Database for MySQL adattípusának leképezése

Az adatok Azure Database for MySQLból való másolása során a rendszer a következő leképezéseket használja a MySQL-adattípusokból Azure Data Factory ideiglenes adattípusokhoz. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| Azure Database for MySQL adattípus | Data factory közbenső adattípus |
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
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
