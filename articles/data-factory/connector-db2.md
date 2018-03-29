---
title: Másolja az Azure Data Factory használatával DB2 adatait |} Microsoft Docs
description: 'Útmutató: adatok másolása DB2 támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: jingwang
ms.openlocfilehash: 7713e1b6a74fd099206804133d2dc8140fe83a8d
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Adatok másolása az DB2 Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-onprem-db2-connector.md)
> * [2. verzió – Előzetes verzió](connector-db2.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatokat másolni egy DB2-adatbázishoz. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [DB2-összekötő a V1](v1/data-factory-onprem-db2-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok bármely támogatott fogadó adattárolóhoz másolhatja DB2-adatbázishoz. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a DB2-összekötő a következő IBM DB2-platformok és verziók rendelkező elosztott relációs adatbázis architektúra (DRDA) SQL Access Manager (SQLAM) 9, 10, 11 verziót támogatja:

* IBM DB2 z/os 11.1
* IBM DB2 z/OS 10.1
* Az IBM DB2 i 7.2
* Az IBM DB2 i 7.1
* IBM DB2 LUW 11
* IBM DB2 LUW 10.5 számára
* IBM DB2 LUW 10.1 számára

> [!TIP]
> Ha kap olyan hibaüzenetet, amely szerint az "a megfelelő SQL-utasítás végrehajtása kérelmet csomag nem található. SQLSTATE 51002 SQLCODE =-805 = ", a hiba oka egy szükséges csomag nem lesz létrehozva normál felhasználói ilyen operációs rendszeren. Ezek a lépések alapján a DB2-kiszolgáló típusa:
> - A DB2 i (AS400): lehetővé teszik a kiemelt felhasználó létrehozása a másolási tevékenység használata előtt a bejelentkezési felhasználói gyűjteményt. Parancs: `create collection <username>`
> - A z/OS- vagy LUW DB2: egy magas jogosultságú fiók - kiemelt felhasználói vagy a felügyeleti csomag hitelesítésszolgáltatók és a kötési, BINDADD, GRANT hajtható végre a nyilvános engedélyeket - használatával futtassa egyszer a másolási tevékenység során, majd a szükséges csomag másolása során automatikusan létrejön. Ezután válthat vissza a normál felhasználói a későbbi másolási kísérletekhez.

## <a name="prerequisites"></a>Előfeltételek

Szeretné használni az adatok másolása egy DB2-adatbázisból, amely nincs nyilvánosan elérhető, akkor be kell állítania egy Self-hosted integrációs futásidejű. Önálló üzemeltetett integrációs futtatókörnyezetek kapcsolatos további tudnivalókért lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md) cikk. Az integrációs futásidejű biztosít egy beépített DB2-illesztőprogramot, ezért nem, manuálisan kell telepítenie minden olyan illesztőprogram DB2-adatok másolásakor kell.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják való DB2-összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok DB2 kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Db2** | Igen |
| kiszolgáló |A DB2-kiszolgáló neve. |Igen |
| adatbázis |Neve a DB2-adatbázishoz. |Igen |
| authenticationType |A DB2-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Az érték engedélyezett: **alapvető**. |Igen |
| felhasználónév |Adja meg a felhasználónevet a DB2-adatbázishoz való kapcsolódáshoz. |Igen |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja Self-hosted integrációs futásidejű vagy Azure integrációs futásidejű (ha az adattároló nyilvánosan elérhető). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername>",
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a szakasz a DB2 dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása DB2, az adatkészlet típus tulajdonságának beállítása **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **RelationalTable** | Igen |
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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a DB2-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="db2-as-source"></a>DB2 forrásaként

Adatok másolása DB2, állítsa be a forrás típusa a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

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

## <a name="data-type-mapping-for-db2"></a>Adattípus-leképezés a DB2 rendszerhez

Az adatok másolása DB2, amikor az Azure Data Factory ideiglenes adattípusok a következő megfeleltetéseket használtak DB2 adattípusokat. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| DB2-adatbázishoz típusa | Data factory ideiglenes adattípus |
|:--- |:--- |
| BigInt |Int64 |
| Bináris |Byte] |
| Blob |Byte] |
| Karakter |Karakterlánc |
| Clob |Karakterlánc |
| Dátum |Dátum és idő |
| DB2DynArray |Karakterlánc |
| DbClob |Karakterlánc |
| Decimális |Decimális |
| DecimalFloat |Decimális |
| Dupla |Dupla |
| Lebegőpontos |Dupla |
| Kép |Karakterlánc |
| Egész szám |Int32 |
| LongVarBinary |Byte] |
| LongVarChar |Karakterlánc |
| LongVarGraphic |Karakterlánc |
| Numerikus |Decimális |
| Real |Egyedülálló |
| SmallInt |Int16 |
| Time |TimeSpan |
| Időbélyeg |DateTime |
| VarBinary |Byte] |
| VarChar |Karakterlánc |
| VarGraphic |Karakterlánc |
| Xml |Byte] |


## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).