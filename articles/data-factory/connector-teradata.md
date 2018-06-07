---
title: Adatok másolása az Azure Data Factory használatával Teradata |} Microsoft Docs
description: További információk a Teradata-összekötő a Data Factory szolgáltatásnak, amely lehetővé teszi a Teradata-adatbázishoz mosdók adat-előállító által támogatott adattárolókhoz adatok másolása.
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
ms.openlocfilehash: 4360ff12a435afc4347fa97bba4506ccd81618aa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618980"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával teradata rendszerhez
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-onprem-teradata-connector.md)
> * [2. verzió – Előzetes verzió](connector-teradata.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatokat másolni egy Teradata-adatbázishoz. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Teradata-összekötőt a V1](v1/data-factory-onprem-teradata-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok bármely támogatott fogadó adattárolóhoz másolhatja Teradata-adatbázishoz. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Konkrétan ez Teradata az összekötő támogatja:

- Teradata **12-es vagy újabb verzió**.
- Adatok másolása **alapvető** vagy **Windows** hitelesítés.

## <a name="prerequisites"></a>Előfeltételek

A Teradata-összekötő használata esetén meg kell:

- Állítson be egy Self-hosted integrációs futásidejű. Lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben alább.
- Telepítse a [.NET-adatszolgáltató a teradata rendszerhez](http://go.microsoft.com/fwlink/?LinkId=278886) 14 verzió vagy újabb a integrációs futásidejű gépen.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják megadhatók a Data Factory tartozó entitások Teradata-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Az alábbi tulajdonságokat a teradata rendszerhez kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **teradata rendszerhez** | Igen |
| kiszolgáló | A Teradata-kiszolgáló neve. | Igen |
| authenticationType | A Teradata-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Két érték engedélyezett: **alapvető**, és **Windows**. | Igen |
| felhasználónév | Adja meg a felhasználónevet a Teradata-adatbázishoz való kapcsolódáshoz. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Egy Self-hosted integrációs futásidejű szükség, ahogyan az [Előfeltételek](#prerequisites). |Igen |

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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a szakasz a Teradata-adatkészlet által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása Teradata, az adatkészlet típus tulajdonságának beállítása **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **RelationalTable** | Igen |
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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Teradata-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="teradata-as-source"></a>Forrásként teradata rendszerhez

Adatok másolása Teradata, állítsa be a forrás típusa a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

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

Az adatok másolása Teradata, amikor az Azure Data Factory ideiglenes adattípusok a következő megfeleltetéseket használtak Teradata adattípusokat. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| Teradata-adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte] |
| Bájt |Byte] |
| ByteInt |Int16 |
| Karakter |Karakterlánc |
| CLOB |Karakterlánc |
| Dátum |DateTime |
| Decimális |Decimális |
| Dupla |Dupla |
| Kép |Karakterlánc |
| Egész szám |Int32 |
| Időköz nap |A TimeSpan |
| Időköz nap, óra |A TimeSpan |
| Naponta időköz percben |A TimeSpan |
| Második intervallum naponta |A TimeSpan |
| Időköz óra |A TimeSpan |
| Időköz óra, perc alatt |A TimeSpan |
| Második intervallum óra |A TimeSpan |
| Időköz percben |A TimeSpan |
| Másik időköz percben |A TimeSpan |
| Időköz hónap |Karakterlánc |
| Időköz második |A TimeSpan |
| Időköz év |Karakterlánc |
| Időköz hónap év |Karakterlánc |
| Szám |Dupla |
| Period(date) |Karakterlánc |
| Period(Time) |Karakterlánc |
| Időtartam (idő időzóna) |Karakterlánc |
| Period(Timestamp) |Karakterlánc |
| Időtartam (időbélyegzője az időzóna) |Karakterlánc |
| SmallInt |Int16 |
| Time |A TimeSpan |
| Időzóna idő |Karakterlánc |
| Időbélyeg |DateTime |
| Az időzóna időbélyeg |DateTimeOffset |
| VarByte |Byte] |
| VarChar |Karakterlánc |
| VarGraphic |Karakterlánc |
| Xml |Karakterlánc |


## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).
