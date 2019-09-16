---
title: Adatok másolása a Sybase-ból Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhatja át a Sybase adatait egy Azure Data Factoryi folyamat másolási tevékenységének használatával támogatott fogadó adattárakba.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 26eb3852106fdacd30425abd14aa2c594aed978c
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71007798"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Adatok másolása Sybase használatával Azure Data Factory
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-sybase-connector.md)
> * [Aktuális verzió](connector-sybase.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factory egy Sybase-adatbázisból származó adatok másolásához. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Sybase-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás-mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Sybase-adatbázisból bármilyen támogatott fogadó adattárba másolhat adatok. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Ez a Sybase-összekötő a következőket támogatja:

- SAP Sybase SQL Anywhere (ASA) **16. és újabb verzió**; Az IQ és a bemutató nem támogatott.
- Adatok másolása az alapszintű vagy a **Windows** -hitelesítéssel.

## <a name="prerequisites"></a>Előfeltételek

A Sybase-összekötő használatához a következőket kell tennie:

- Saját üzemeltetésű Integration Runtime beállítása. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.
- Telepítse az [adatszolgáltatót a Sybase iAnywhere. SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 vagy újabb verzióra a Integration Runtime gépen.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a Sybase-összekötőhöz tartozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Sybase társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **Sybase** | Igen |
| server | A Sybase-kiszolgáló neve. |Igen |
| database | A Sybase-adatbázis neve. |Igen |
| authenticationType | A Sybase-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Engedélyezett értékek a következők: **Basic**, és **Windows**. |Igen |
| username | Adja meg a Sybase-adatbázishoz való kapcsolódáshoz használandó felhasználónevet. |Igen |
| password | Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. A saját üzemeltetésű Integration Runtime az [Előfeltételek](#prerequisites)szakaszban említettek szerint kell megadni. |Igen |

**Példa:**

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Sybase-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

A Sybase-adatok másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **SybaseTable** | Igen |
| tableName | A Sybase-adatbázisban található tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "SybaseTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Ha a beírt adatkészletet használta `RelationalTable` , a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Sybase-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sybase-as-source"></a>Sybase forrásként

A Sybase adatainak másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **SybaseSource** | Igen |
| query | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa:**

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
                "type": "SybaseSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ha a beírt forrást használta `RelationalSource` , a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="data-type-mapping-for-sybase"></a>Sybase adattípusának leképezése

A Sybase-adatok másolása során a rendszer a következő leképezéseket használja a Sybase-adattípusokból Azure Data Factory ideiglenes adattípusokra. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

A Sybase támogatja a T-SQL-típusokat. Az SQL-típusokból Azure Data Factory köztes adattípusokra vonatkozó leképezési táblázathoz lásd: [Azure SQL Database Connector-adattípus-leképezési](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database) szakasz.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).



## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
