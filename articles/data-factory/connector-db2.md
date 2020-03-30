---
title: Adatok másolása a DB2-ből az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat a DB2-ből a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: jingwang
ms.openlocfilehash: 22ecac12e049e58e533cdde0078f4a25f6bb2aa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77423827"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Adatok másolása a DB2-ből az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-db2-connector.md)
> * [Aktuális verzió](connector-db2.md)

Ez a cikk ismerteti, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása egy DB2-adatbázisból. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a DB2 adatbázis-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A DB2 adatbázisból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Pontosabban ez a DB2 csatlakozó a következő IBM DB2 platformokat és verziókat támogatja a DRDA SQL Access Manager (SQLAM) 9-es, 10-es és 11-es verziójával:

* IBM DB2 z/OS 12.1-hez
* IBM DB2 z/OS 11.1-hez
* IBM DB2 z/OS 10.1-hez
* IBM DB2 az i 7.3-hoz
* IBM DB2 az i 7.2-hez
* IBM DB2 az i 7.1-hez
* IBM DB2 a LUW 11-hez
* IBM DB2 a LUW 10.5-hez
* IBM DB2 a LUW 10.1-hez

>[!TIP]
>A DB2-összekötő a Microsoft OLE DB Provider for DB2-re épül. A DB2-összekötő hibáinak elhárításához olvassa el [az adatszolgáltató hibakódjait.](https://docs.microsoft.com/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Az integrációs futásidő beépített DB2-illesztőprogramot biztosít, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot sem a DB2-ből történő másoláskor.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a DB2-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A DB2 csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságnak a következőre kell állítania: **Db2** | Igen |
| kiszolgáló |A DB2 kiszolgáló neve. A portszámot a kettőspont által határolt kiszolgálónév `server:port`után adhatja meg, pl. . |Igen |
| adatbázis |A DB2 adatbázis neve. |Igen |
| authenticationType |A DB2 adatbázishoz való csatlakozáshoz használt hitelesítés típusa.<br/>Megengedett érték: **Alapszintű**. |Igen |
| felhasználónév |Adja meg a DB2-adatbázishoz való csatlakozáshoz használandó felhasználónevet. |Igen |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) |Igen |
| csomagGyűjtemény | Adja meg, hogy az adatbázis lekérdezésekénekkor az ADF hol hozta létre automatikusan a szükséges csomagokat. | Nem |
| certificateCommonName (tanúsítványKözösnév) | A Secure Sockets Layer (SSL) vagy a Transport Layer Security (TLS) titkosítás használatakor meg kell adnia egy értéket a Tanúsítvány köznapi nevéhez. | Nem |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

> [!TIP]
> Ha a következő hibaüzenet `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`jelenik meg, hogy a rendszer azt írja, a rendszer nem hoz létre egy szükséges csomagot a felhasználó számára. Alapértelmezés szerint az ADF megpróbálja létrehozni a gyűjtemény alatt a DB2-hez való csatlakozáshoz használt felhasználóként elnevezett csomagot. Adja meg azt a csomaggyűjtemény-tulajdonságot, amely azt jelzi, hogy az ADF hol hozza létre a szükséges csomagokat az adatbázis lekérdezésekekénél.

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a DB2 adatkészlet által támogatott tulajdonságok listáját tartalmazza.

A DB2 rendszerből történő adatok másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **Db2Table** | Igen |
| Séma | A séma neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Használja `schema` `table` és új munkaterheléshez. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

**Példa**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Ha gépelt adatkészletet használt, `RelationalTable` továbbra is támogatott, amíg a rendszer az újat a jövőben használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a DB2 forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="db2-as-source"></a>DB2 forrásként

A DB2 rendszerből származó adatok másolásához a másolási tevékenység **forrásszakaszában** a következő tulajdonságok at támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **Db2Source** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nem (ha az adatkészletben a "tableName" van megadva) |

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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ha gépelt forrást használt, `RelationalSource` akkor továbbra is támogatott, amíg az újat a jövőben is használhatja.

## <a name="data-type-mapping-for-db2"></a>Adattípus-hozzárendelés a DB2-hez

Adatok másolásakor a DB2, a következő leképezések a DB2 adattípusok az Azure Data Factory köztes adattípusok. A [Séma- és adattípus-hozzárendelések](copy-activity-schema-and-type-mapping.md) című témakörből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrássémát és az adattípust a fogadóhoz.

| DB2 adatbázis típusa | Adatgyár köztes adattípusa |
|:--- |:--- |
| BigInt között |Int64 |
| Bináris |Bájt[] |
| Blob |Bájt[] |
| Char |Sztring |
| Clob között |Sztring |
| Dátum |Datetime |
| DB2DynArray |Sztring |
| DbClob között |Sztring |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Grafikus |Sztring |
| Egész szám |Int32 |
| LongVarBinary között |Bájt[] |
| LongVarChar között |Sztring |
| LongVarGrafika |Sztring |
| Numerikus |Decimal |
| Valódi |Egyirányú |
| SmallInt között |Int16 |
| Time |időtartam |
| Időbélyeg |DateTime |
| VarBináris |Bájt[] |
| Varchar |Sztring |
| Vargrafika |Sztring |
| Xml |Bájt[] |

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
