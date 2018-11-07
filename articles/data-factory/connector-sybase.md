---
title: Adatok másolása az Azure Data Factory használatával Sybase |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Sybase támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
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
ms.openlocfilehash: 349fd1f129d93a2893e2585267d17661e141ad0f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242211"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Sybase
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-onprem-sybase-connector.md)
> * [Aktuális verzió](connector-sybase.md)

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység adatokat másol egy Sybase-adatbázis használata. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Sybase-adatbázisból adatokat másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a Sybase-összekötő támogatja:

- SAP Sybase SQL bárhol (ASA) **16 verzió vagy újabb**; Sweetiq és az ASE nem támogatottak.
- Másolja az adatokat az **alapszintű** vagy **Windows** hitelesítést.

## <a name="prerequisites"></a>Előfeltételek

A Sybase-összekötő használatára, kell tennie:

- Egy helyi Integration Runtime beállítása. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.
- Telepítse a [data provider Pro Sybase iAnywhere.Data.SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 vagy újabb a saját üzemeltetésű integrációs gépen.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Sybase-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Sybase-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **Sybase** | Igen |
| kiszolgáló | A Sybase-kiszolgáló neve. |Igen |
| adatbázis | A Sybase-adatbázis neve. |Igen |
| authenticationType | A Sybase-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Engedélyezett értékek a következők: **alapszintű**, és **Windows**. |Igen |
| felhasználónév | Adja meg a felhasználónevet a Sybase-adatbázishoz való csatlakozáshoz. |Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Egy helyi Integration Runtime szükség, az említett [Előfeltételek](#prerequisites). |Igen |

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a Sybase-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatmásolás Sybase, állítsa be a type tulajdonság, az adatkészlet **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **RelationalTable** | Igen |
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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Sybase-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sybase-as-source"></a>Sybase-forrásként

Adatok másolása a Sybase, állítsa be a forrás típusaként a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

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

Ha az adatok másolása a Sybase, Azure Data Factory-közbenső adattípusok a következő hozzárendeléseket használtak Sybase adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

Sybase a T-SQL-típusokat támogatja. Az egy hozzárendelési táblázatot SQL-típusok közül az Azure Data Factory-közbenső adattípusok [Azure SQL Database-összekötő - adattípus-leképezés](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database) szakaszban.


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
