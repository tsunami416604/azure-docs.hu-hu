---
title: Azure Data Factory használatával DB2 adatokat másolni |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a DB2 támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
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
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: f9d1d2181649cf24784dc7ad11638946c9ee4406
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42061384"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Adatok másolása DB2 az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-onprem-db2-connector.md)
> * [Aktuális verzió](connector-db2.md)

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység adatokat másol egy DB2-adatbázis használata. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

DB2-adatbázisból származó adatok másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a DB2-összekötő a következő IBM DB2-platformokat és verziókat az elosztott relációs adatbázis architektúra (DRDA) SQL Access Manager (SQLAM) 9, 10, 11 verzió támogatja:

* Z/os 11.1 IBM DB2-höz
* Z/OS 10.1 IBM DB2-höz
* Az IBM DB2-höz i 7.2
* Az IBM DB2-höz i 7.1
* IBM DB2 LUW 11
* IBM DB2-höz tartozó LUW 10,5
* IBM DB2-höz tartozó LUW 10.1

> [!TIP]
> Ha egy hibaüzenetet küld, hogy "az SQL-utasítás végrehajtási kérelméhez tartozó csomag nem található. SQLSTATE 51002 SQLCODE =-805 = ", a hiba oka szükséges csomag nem jön az ilyen operációs rendszer normál felhasználó számára. Kövesse az alábbi utasításokat a DB2-kiszolgáló típusa szerint:
> - A DB2 i (AS400): lehetővé teszik a kiemelt felhasználó, a másolási tevékenység használata előtt a bejelentkezési felhasználói gyűjtemény létrehozása. A parancs: `create collection <username>`
> - A – z/OS- vagy LUW DB2: egy magas jogosultságú fiók – kiemelt felhasználói vagy a felügyeleti csomag hitelesítésszolgáltatók és a kötési, BINDADD, támogatás hajtsa végre a nyilvános engedélyeket – segítségével a másolási tevékenység futtatása után, majd a szükséges csomag másolása során automatikusan létrejön. Ezt követően válthat vissza a normál felhasználói az ezt követő másolási futtatások.

## <a name="prerequisites"></a>Előfeltételek

Használja az adatok másolása a DB2-adatbázis, amely nem érhető el nyilvánosan, meg kell egy helyi Integration Runtime beállítása. Saját üzemeltetésű integrációs modulok kapcsolatos további információkért lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) cikk. Az Integration Runtime biztosít egy beépített DB2-illesztőprogramot, ezért nem kell manuálisan telepítenie az összes illesztőprogram DB2-adatok másolásakor.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások DB2-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

DB2-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **Db2** | Igen |
| kiszolgáló |A DB2-kiszolgáló neve. Megadhatja, hogy a port számát, a kiszolgáló neve, pontosvesszővel elválasztva például a következő `server:port`. |Igen |
| adatbázis |A DB2-adatbázis neve. |Igen |
| authenticationType |A DB2-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Az érték engedélyezett: **alapszintű**. |Igen |
| felhasználónév |Adja meg a felhasználónevet, a DB2-adatbázishoz való csatlakozáshoz. |Igen |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. (Ha az adattár nyilvánosan hozzáférhető) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a DB2-adathalmaz által támogatott tulajdonságok listáját tartalmazza.

Adatokat másol a DB2, állítsa be a type tulajdonság, az adatkészlet **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **RelationalTable** | Igen |
| tableName | A DB2-adatbázishoz a tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

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

### <a name="db2-as-source"></a>Forrásként DB2

Adatok másolása a DB2, állítsa be a forrás típusaként a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa**

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

## <a name="data-type-mapping-for-db2"></a>Adattípus-leképezés DB2

Ha az adatok másolása a DB2, Azure Data Factory-közbenső adattípusok a következő hozzárendeléseket használtak DB2 adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| DB2-adatbázis típusa | Data factory közbenső adattípus |
|:--- |:--- |
| BigInt |Int64 |
| Bináris |Byte] |
| Blob |Byte] |
| CHAR |Sztring |
| CLOB |Sztring |
| Dátum |Dátum és idő |
| DB2DynArray |Sztring |
| DbClob |Sztring |
| tizedes tört |tizedes tört |
| DecimalFloat |tizedes tört |
| Dupla |Dupla |
| Lebegőpontos |Dupla |
| Kép |Sztring |
| Egész szám |Int32 |
| LongVarBinary |Byte] |
| LongVarChar |Sztring |
| LongVarGraphic |Sztring |
| Numerikus |tizedes tört |
| Real |Önálló |
| SmallInt |Int16 |
| Time |Időtartam |
| Időbélyeg |DateTime |
| VarBinary |Byte] |
| VarChar |Sztring |
| VarGraphic |Sztring |
| Xml |Byte] |


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
