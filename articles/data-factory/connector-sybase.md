---
title: Adatok másolása az Azure Data Factory használatával Sybase |} Microsoft Docs
description: 'Útmutató: adatok másolása Sybase támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával.'
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 0eb74cee8fb1f4c5d301693a4d53e5d564e12a00
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053949"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Sybase
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](v1/data-factory-onprem-sybase-connector.md)
> * [Aktuális verzió](connector-sybase.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatokat másolni egy Sybase-adatbázishoz. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok bármely támogatott fogadó adattárolóhoz másolhatja Sybase-adatbázishoz. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Konkrétan ez Sybase az összekötő támogatja:

- SAP Sybase SQL tetszőleges helyre (ASA) **16 verzió vagy újabb verzió**; IQ és ASE nem támogatottak.
- Adatok másolása **alapvető** vagy **Windows** hitelesítés.

## <a name="prerequisites"></a>Előfeltételek

A Sybase-összekötő használata esetén meg kell:

- Állítson be egy Self-hosted integrációs futásidejű. Lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben alább.
- Telepítse a [Sybase iAnywhere.Data.SQLAnywhere adatszolgáltatója](http://go.microsoft.com/fwlink/?linkid=324846) 16 vagy újabb a integrációs futásidejű gépen.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják megadhatók a Data Factory tartozó entitások Sybase-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok Sybase kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Sybase** | Igen |
| kiszolgáló | A Sybase-kiszolgáló neve. |Igen |
| adatbázis | Neve a Sybase-adatbázishoz. |Igen |
| authenticationType | A Sybase-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Két érték engedélyezett: **alapvető**, és **Windows**. |Igen |
| felhasználónév | Adja meg a felhasználónevet a Sybase-adatbázishoz való kapcsolódáshoz. |Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Egy Self-hosted integrációs futásidejű szükség, ahogyan az [Előfeltételek](#prerequisites). |Igen |

**Példa**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "authenticationType": "Basic",
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a szakasz a Sybase dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása Sybase, állítsa be a adatkészlet type tulajdonsága **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **RelationalTable** | Igen |
| tableName | A Sybase-adatbázishoz a tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Sybase forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sybase-as-source"></a>Sybase forrásaként

Adatok másolása Sybase, állítsa be a forrás típusa a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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

## <a name="data-type-mapping-for-sybase"></a>Adattípus-leképezés a Sybase rendszerhez

Az adatok másolása Sybase, amikor az Azure Data Factory ideiglenes adattípusok a következő megfeleltetéseket használtak Sybase adattípusokat. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

Sybase T-SQL-típusokat támogatja. Leképezési tábla való SQL Azure Data Factory ideiglenes adattípusok, lásd: [Azure SQL Database Connector - adattípus-hozzárendelése](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database) szakasz.


## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).
