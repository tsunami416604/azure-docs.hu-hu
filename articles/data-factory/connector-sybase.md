---
title: Adatok másolása a Sybase-ből az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat a Sybase-ről a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 495d16efcc26fc336a87c0f2d88f5202ab0b4a3e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416622"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Adatok másolása a Sybase-ből az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-sybase-connector.md)
> * [Jelenlegi verzió](connector-sybase.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk ismerteti, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása egy Sybase-adatbázisból. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Sybase-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

A Sybase adatbázisból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Ez a Sybase-összekötő különösen a következőket támogatja:

- SAP Sybase SQL Anywhere (ASA) **16-os és újabb verzió;** Az IQ és az ASE nem támogatott.
- Adatok másolása **alapfokú** vagy Windows-hitelesítéssel. **Windows**

## <a name="prerequisites"></a>Előfeltételek

A Sybase-összekötő használatához a következőket kell használnia:

- Állítsa be a saját üzemeltetésű integrációs futásidejű. A [részleteket a saját üzemeltetésű integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben találja.
- Telepítse a [Sybase iAnywhere.Data.SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 vagy újabb rendszer adatszolgáltatóját az integrációs futásidejű gépen.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Sybase-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A Sybase csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **Sybase** | Igen |
| kiszolgáló | A Sybase-kiszolgáló neve. |Igen |
| adatbázis | A Sybase adatbázis neve. |Igen |
| authenticationType | A Sybase adatbázishoz való csatlakozáshoz használt hitelesítés típusa.<br/>Az engedélyezett értékek a következők: **Alapszintű**és **Windows**. |Igen |
| felhasználónév | Adja meg a Sybase adatbázishoz való csatlakozáshoz kívánt felhasználónevet. |Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) |Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Az előfeltételek ben említettek szerint saját üzemeltetésű [integrációs](#prerequisites)futásidejű re van szükség. |Igen |

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a Sybase adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok Sybase-ből történő másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **SybaseTable** | Igen |
| tableName | A Sybase adatbázisban lévő tábla neve. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

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

Ha gépelt adatkészletet használt, `RelationalTable` továbbra is támogatott, amíg a rendszer az újat a jövőben használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a Sybase-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sybase-as-source"></a>Sybase mint forrás

Adatok Sybase-ből történő másolásához a következő tulajdonságokat támogatja a másolási tevékenység **forrása** szakasz:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **SybaseSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM MyTable"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

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

Ha gépelt forrást használt, `RelationalSource` akkor továbbra is támogatott, amíg az újat a jövőben is használhatja.

## <a name="data-type-mapping-for-sybase"></a>A Sybase adattípus-leképezése

Adatok másolásakor a Sybase, a következő leképezések a Sybase adattípusok az Azure Data Factory köztes adattípusok. A [Séma- és adattípus-hozzárendelések](copy-activity-schema-and-type-mapping.md) című témakörből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrássémát és az adattípust a fogadóhoz.

A Sybase támogatja a T-SQL típusokat. Az SQL-típusoktól az Azure Data Factory köztes adattípusaiig lévő leképezési tábláról az [Azure SQL Database Connector – adattípus-leképezés](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database) című szakaszban.

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)



## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
