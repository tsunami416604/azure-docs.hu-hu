---
title: Adatok másolása az Azure-adatbázis az Azure Data Factory használatával MySQL |} Microsoft Docs
description: 'Útmutató: adatok másolása az Azure-adatbázis a MySQL támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2018
ms.author: jingwang
ms.openlocfilehash: 17e5ba3637b0ae36412e46166f7f178d4c82d179
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="copy-data-from-azure-database-for-mysql-using-azure-data-factory"></a>Adatok másolása az Azure-adatbázis a MySQL az Azure Data Factory használatával

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatok másolása az Azure-adatbázis a MySQL. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [MySQL-összekötőt a V1](v1/data-factory-onprem-mysql-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Másolhatja adatok Azure-adatbázis a MySQL bármely támogatott fogadó adattárolóhoz. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolódásának engedélyezése beépített illesztőprogramot tartalmaz, ezért nem szükséges manuálisan kell telepítenie minden olyan illesztőprogram ezt az összekötőt használja.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják adat-előállító tartozó entitások meghatározásához az Azure Database-MySQL-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok támogatott Azure-adatbázis kapcsolódó MySQL-szolgáltatás:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureMySql** | Igen |
| connectionString | Adja meg a MySQL-példány az Azure-adatbázishoz való kapcsolódáshoz szükséges adatokat. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

Egy tipikus kapcsolati karakterlánc `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. A case / beállítható további tulajdonságokat:

| Tulajdonság | Leírás | Beállítások | Szükséges |
|:--- |:--- |:--- |:--- |:--- |
| SSLMode | Ez a beállítás megadja, hogy az illesztőprogram használja SSL-titkosítást és ellenőrzési MySQL történő csatlakozás során. Például `SSLMode=<0/1/2/3/4>`| Letiltva (0) / előnyben részesített (1) **(alapértelmezett)** / szükséges (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Nem |
| useSystemTrustStore | Ez a beállítás megadja, hogy CA-tanúsítvány használatára, a rendszer megbízható áruházból vagy a megadott PEM-fájl. Például `UseSystemTrustStore=<0/1>;`| (1) engedélyezve vagy letiltva (0) **(alapértelmezett)** | Nem |

**Példa**

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

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a MySQL adatkészlet Azure-adatbázis által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása az Azure-adatbázis a MySQL, állítsa be a type tulajdonságot az adathalmaz **AzureMySqlTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **AzureMySqlTable** | Igen |
| tableName | A tábla a MySQL-adatbázis neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a MySQL forrás Azure-adatbázis által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-database-for-mysql-as-source"></a>Azure forrásaként MySQL-adatbázis

Adatok másolása az Azure Database MySQL, állítsa be a forrás típusa a másolási tevékenység **AzureMySqlSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **AzureMySqlSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa**

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

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Adattípus-hozzárendelése az Azure Database a MySQL

Ha az adatok másolása az Azure Database MySQL, az Azure Data Factory ideiglenes adattípusok a következő megfeleltetéseket használtak MySQL adattípusok. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| Azure-adatbázis MySQL adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Decimal` |
| `blob` |`Byte[]` |
| `bool` |`Boolean` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal` |
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
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).