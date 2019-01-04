---
title: Adatok másolása az Azure Data Factory segítségével MySQL |} A Microsoft Docs
description: Ismerje meg a MySQL-összekötő az Azure Data Factoryben, amely adatokat másol egy MySQL-adatbázishoz a fogadóként támogatott adattárak teszi lehetővé.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/23/2018
ms.author: jingwang
ms.openlocfilehash: 1f3d6434c7226465f9e054d5e5bf35fbb228b311
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014487"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory segítségével MySQL
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-onprem-mysql-connector.md)
> * [Aktuális verzió](connector-mysql.md)

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység adatokat másol egy MySQL-adatbázis használata. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat a MySQL-adatbázis bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, a MySQL-összekötő támogatja a MySQL **5.1-es és újabb**.

## <a name="prerequisites"></a>Előfeltételek

Ha a MySQL-adatbázis nem érhető el nyilvánosan, meg kell egy helyi Integration Runtime beállítása. Saját üzemeltetésű integrációs modulok kapcsolatos további információkért lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) cikk. Az integrációs modul biztosít beépített egy MySQL-illesztőprogram kezdve 3.7-es verziója, ezért nem kell manuálisan telepítenie az összes illesztőprogramot.

A helyi integrációs modul alacsonyabb, mint 3.7 esetén telepítenie kell a [MySQL Connector/Net a Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) verzió 6.6.5 és a saját üzemeltetésű integrációs gépen 6.10.7 között. Ez az 32 bites illesztőprogram nem kompatibilis a 64 bites IR.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások MySQL-connector-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

MySQL-társított szolgáltatást a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **MySql** | Igen |
| kapcsolati Sztringje | Adja meg az Azure Database for MySQL-példányhoz való kapcsolódáshoz szükséges adatokat. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. (Ha az adattár nyilvánosan hozzáférhető) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

Egy tipikus kapcsolati karakterlánc `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. További tulajdonságok beállíthatja, hogy az eset száma:

| Tulajdonság | Leírás | Beállítások | Szükséges |
|:--- |:--- |:--- |:--- |:--- |
| SSLMode | Ez a beállítás megadja, hogy az illesztőprogram használja az SSL-titkosítás és ellenőrzési MySQL-hez való kapcsolódáskor. Például `SSLMode=<0/1/2/3/4>`| Le van tiltva (0) / előnyben részesített (1) **(alapértelmezett)** / szükséges (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Nem |
| useSystemTrustStore | Ez a beállítás megadja, hogy egy hitelesítésszolgáltató tanúsítvány használatára, a rendszer megbízható áruházból vagy egy adott PEM-fájl. Például `UseSystemTrustStore=<0/1>;`| (1) engedélyezve / letiltva (0) **(alapértelmezett)** | Nem |

**Példa**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Ha az alábbi hasznos adattal MySQL társított szolgáltatást használja, továbbra is támogatott-van, amíg, a jövőben újat használata javasolt.

**Előző hasznos:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ebben a szakaszban a MySQL-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatokat másol a MySQL, állítsa be a type tulajdonság, az adatkészlet **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **RelationalTable** | Igen |
| tableName | A MySQL-adatbázisban a tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ebben a szakaszban a MySQL-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="mysql-as-source"></a>MySQL forrásként

Adatok másolása a MySQL, állítsa be a forrás típusaként a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-mysql"></a>Adattípus-leképezés MySQL-hez

Ha az adatok másolása a MySQL, Azure Data Factory-közbenső adattípusok a következő hozzárendeléseket használtak MySQL adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| MySQL-adattípus | Data factory közbenső adattípus |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
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
| `year` |`Int` |


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
