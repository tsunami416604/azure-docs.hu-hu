---
title: Adatok másolása DB2-ből Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhatja át az adatait a DB2-ből a támogatott fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 2bdec0c70e9f11ca40e0ff9e1aa87898c94e119c
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232988"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Adatok másolása a DB2-ből Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-db2-connector.md)
> * [Aktuális verzió](connector-db2.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok DB2-adatbázisból történő másolásához. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Az adatok a DB2-adatbázisból bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, ez a DB2-összekötő a következő IBM DB2 platformokat és verziókat támogatja az elosztott kapcsolati adatbázis-architektúra (DRDA) SQL Access Manager (SQLAM) 9-es, 10-es és 11-es verziójával:

* IBM DB2 for z/OS 11,1
* IBM DB2 for z/OS 10,1
* IBM DB2 az i 7,3-hez
* IBM DB2 az i 7,2-hez
* IBM DB2 az i 7,1-hez
* IBM DB2 a LUW 11 rendszerhez
* IBM DB2 a LUW 10,5
* IBM DB2 a LUW 10,1

> [!TIP]
> Ha a következő hibaüzenet jelenik meg: "az SQL-utasítás végrehajtási kérelmének megfelelő csomag nem található. SQLSTATE = 51002 SQLCODE =-805 ", ezért a szükséges csomag nem jön létre a normál felhasználó számára az adott operációs rendszeren. Kövesse az alábbi utasításokat a DB2-kiszolgáló típusa szerint:
> - DB2 for i (AS400): hagyja, hogy a Power User a másolási tevékenység használata előtt hozzon létre gyűjteményt a bejelentkezési felhasználó számára. Parancs`create collection <username>`
> - DB2 a z/OS-vagy LUW-hez: magas jogosultsági szintű fiók használata – Kiemelt felhasználó vagy rendszergazda a csomaggal rendelkező hatóságok és a kötés, a BINDADD, a hozzáférés engedélyezése a nyilvános engedélyekhez – a másolási tevékenység futtatásához a rendszer automatikusan létrehozza a szükséges csomagot a másolás során. Ezt követően visszaválthat a normál felhasználóra a későbbi másolási futtatásokhoz.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

A Integration Runtime beépített DB2-illesztőprogramot biztosít, ezért nem kell manuálisan telepítenie az illesztőprogramot az adatok DB2-ből való másolása során.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a DB2-összekötőhöz tartozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A DB2 társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **Db2** | Igen |
| server |A DB2-kiszolgáló neve. Megadhatja azt a portszámot, amelyet a kiszolgáló neve a kettősponttal elválasztva, például `server:port`:. |Igen |
| database |A DB2-adatbázis neve. |Igen |
| authenticationType |A DB2-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Az engedélyezett érték:Alapszintű. |Igen |
| username |Adja meg a DB2-adatbázishoz való kapcsolódáshoz használandó felhasználónevet. |Igen |
| password |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**Példa**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a DB2-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok DB2-ből való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **RelationalTable**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **RelationalTable** | Igen |
| tableName | A tábla neve a DB2-adatbázisban. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a DB2-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="db2-as-source"></a>DB2 forrásként

Az adatok DB2-ből való másolásához állítsa a forrás típusát a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **source** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **RelationalSource** | Igen |
| query | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-db2"></a>Adattípusok leképezése DB2-hez

Az adatok DB2-ből való másolása során a rendszer a következő leképezéseket használja az DB2-adattípusokból Azure Data Factory köztes adattípusokhoz. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| DB2-adatbázis típusa | Data factory közbenső adattípus |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| Blob |Byte[] |
| Char |Sztring |
| Clob |Sztring |
| Date |Datetime |
| DB2DynArray |Sztring |
| DbClob |Sztring |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |Sztring |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Sztring |
| LongVarGraphic |Sztring |
| Numeric |Decimal |
| Valós szám |Single |
| SmallInt |Int16 |
| Time |TimeSpan |
| Timestamp |Datetime |
| VarBinary |Byte[] |
| VarChar |Sztring |
| VarGraphic |Sztring |
| Xml |Byte[] |


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
