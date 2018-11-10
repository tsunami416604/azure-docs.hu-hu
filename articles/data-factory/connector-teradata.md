---
title: Adatok másolása az Azure Data Factory használatával Teradata |} A Microsoft Docs
description: Ismerje meg a Data Factory szolgáltatás, amely lehetővé teszi a Teradata-összekötő által a Data Factory fogadóként támogatott adattárak Teradata-adatbázisból adatok másolása.
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
ms.openlocfilehash: 37e7281af87a8cfc57aae95411eb2d4cce9eef65
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228062"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Teradata
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuális verzió](connector-teradata.md)

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység adatokat másol egy Teradata-adatbázis használata. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok bármely támogatott fogadó adattárba másolhatja Teradata-adatbázisból. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

A Teradata-összekötőt, a következőket támogatja:

- Teradata **12-es verzió vagy újabb**.
- Másolja az adatokat az **alapszintű** vagy **Windows** hitelesítést.

## <a name="prerequisites"></a>Előfeltételek

A Teradata-összekötő használatára, kell tennie:

- Egy helyi Integration Runtime beállítása. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.
- Telepítse a [.NET-adatszolgáltató a teradata rendszerhez](https://go.microsoft.com/fwlink/?LinkId=278886) 14-es verziót vagy újabb a saját üzemeltetésű integrációs gépen.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Teradata-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Teradata-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **Teradata** | Igen |
| kiszolgáló | A Teradata-kiszolgáló neve. | Igen |
| authenticationType | A Teradata-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Engedélyezett értékek a következők: **alapszintű**, és **Windows**. | Igen |
| felhasználónév | Adja meg a felhasználónevet a Teradata-adatbázishoz való csatlakozáshoz. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Egy helyi Integration Runtime szükség, az említett [Előfeltételek](#prerequisites). |Igen |

**Példa**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a Teradata adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatmásolás Teradata, állítsa be a type tulajdonság, az adatkészlet **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **RelationalTable** | Igen |
| tableName | A Teradata-adatbázishoz a tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Teradata-adatforrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="teradata-as-source"></a>Teradata forrásként

Adatok másolása a Teradata, állítsa be a forrás típusaként a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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

## <a name="data-type-mapping-for-teradata"></a>Adattípus-leképezés a teradata rendszerhez

Ha az adatok másolása a Teradata, Azure Data Factory-közbenső adattípusok a következő hozzárendeléseket használtak Teradata adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| Teradata-adattípus | Data factory közbenső adattípus |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte] |
| Bájt |Byte] |
| ByteInt |Int16 |
| CHAR |Karakterlánc |
| CLOB |Karakterlánc |
| Dátum |DateTime |
| tizedes tört |tizedes tört |
| Dupla |Dupla |
| Kép |Karakterlánc |
| Egész szám |Int32 |
| Napi időköz |Időtartam |
| Intervallum nap – óra |Időtartam |
| Intervallum nap – perc |Időtartam |
| Intervallum nap – másodperc |Időtartam |
| Intervallum óra |Időtartam |
| Intervallum óra – perc |Időtartam |
| Intervallum óra – másodperc |Időtartam |
| Időköz percben |Időtartam |
| Második időköz percben |Időtartam |
| Intervallum hónap |Karakterlánc |
| Intervallum második |Időtartam |
| Intervallum év |Karakterlánc |
| Intervallum év, hónap |Karakterlánc |
| Szám |Dupla |
| Period(date) |Karakterlánc |
| Period(Time) |Karakterlánc |
| Időszak (idő időzónával együtt) |Karakterlánc |
| Period(Timestamp) |Karakterlánc |
| Időszak (Timestamp időzónával együtt) |Karakterlánc |
| SmallInt |Int16 |
| Time |Időtartam |
| Idő időzónával együtt |Karakterlánc |
| Időbélyeg |DateTime |
| Az időzóna időbélyeg |DateTimeOffset |
| VarByte |Byte] |
| VarChar |Karakterlánc |
| VarGraphic |Karakterlánc |
| Xml |Karakterlánc |


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
