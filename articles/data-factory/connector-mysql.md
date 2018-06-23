---
title: Adatok másolása az Azure Data Factory használatával MySQL |} Microsoft Docs
description: Ismerje meg, amely lehetővé teszi az adatok másolása az MySQL-adatbázis támogatott, a fogadó adattárat az Azure Data Factory MySQL-összekötőjét.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/23/2018
ms.author: jingwang
ms.openlocfilehash: 43a27b98d8b53523bee8694ed3071e65a03355a6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335873"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával MySQL
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-onprem-mysql-connector.md)
> * [2. verzió – Előzetes verzió](connector-mysql.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatokat másolni a MySQL-adatbázis. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [MySQL-összekötőt a V1](v1/data-factory-onprem-mysql-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

A MySQL-adatbázis adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, a MySQL-összekötő támogatja a MySQL **5.1-es verzió vagy újabb verzió**.

## <a name="prerequisites"></a>Előfeltételek

Ha a MySQL-adatbázis nincs nyilvánosan elérhető, állítson be egy Self-hosted integrációs futásidejű szeretné. Önálló üzemeltetett integrációs futtatókörnyezetek kapcsolatos további tudnivalókért lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md) cikk. A integrációs futásidejű biztosít egy beépített MySQL-illesztőprogram verziója 3.7-től kezdődő, ezért nem, manuálisan kell telepítenie minden olyan illesztőprogram kell.

Alacsonyabb, mint 3.7 Self-hosted IR verziójához, telepítenie kell a [MySQL összekötő/Net számára a Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) verzió 6.6.5 és az integrációs futásidejű gépen 6.10.7 között. Ez az 32 bites illesztőprogram nem kompatibilis a 64 bites infravörös

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják megadhatók a Data Factory tartozó entitások MySQL-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Kapcsolódó MySQL-szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **MySql** | Igen |
| connectionString | Adja meg a MySQL-példány az Azure-adatbázishoz való kapcsolódáshoz szükséges adatokat. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Self-hosted integrációs futásidejű (ha az adattároló magánhálózaton található) vagy Azure integrációs futásidejű is használhatja. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

Egy tipikus kapcsolati karakterlánc `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. A case / beállítható további tulajdonságokat:

| Tulajdonság | Leírás | Beállítások | Szükséges |
|:--- |:--- |:--- |:--- |:--- |
| SSLMode | Ez a beállítás megadja, hogy az illesztőprogram használja SSL-titkosítást és ellenőrzési MySQL történő csatlakozás során. Például `SSLMode=<0/1/2/3/4>`| Letiltva (0) / előnyben részesített (1) **(alapértelmezett)** / szükséges (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Nem |
| useSystemTrustStore | Ez a beállítás megadja, hogy CA-tanúsítvány használatára, a rendszer megbízható áruházból vagy a megadott PEM-fájl. Például `UseSystemTrustStore=<0/1>;`| (1) engedélyezve vagy letiltva (0) **(alapértelmezett)** | Nem |

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

Ha a kapcsolódó MySQL-szolgáltatás a következő tartalom használta, az továbbra is támogatott-van, amíg az újjal továbbítja használandó javasoltak.

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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a szakasz a MySQL dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása MySQL, állítsa be a adatkészlet type tulajdonsága **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **RelationalTable** | Igen |
| tableName | A tábla a MySQL-adatbázis neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a MySQL forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="mysql-as-source"></a>MySQL forrásaként

Adatok másolása MySQL, állítsa be a forrás típusa a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

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

## <a name="data-type-mapping-for-mysql"></a>Adattípus-leképezést MySQL

Az adatok másolása MySQL, amikor az Azure Data Factory ideiglenes adattípusok a következő megfeleltetéseket használtak MySQL adattípusokat. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| MySQL-adattípus | Data factory ideiglenes adattípus |
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
| `year` |`Int` |


## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).
