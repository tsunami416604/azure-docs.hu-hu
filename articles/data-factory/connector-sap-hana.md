---
title: Adatok másolása az SAP HANA az Azure Data Factory használatával |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a SAP HANA támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: cdd83c3ff9d34a5e8b7f2c164136ab82f498ffb5
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022971"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával az SAP HANA
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-sap-hana-connector.md)
> * [Aktuális verzió](connector-sap-hana.md)

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység adatokat másol egy SAP HANA-adatbázis használata. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Az SAP HANA-adatbázis adatok másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által források/fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az SAP HANA összekötő támogatja:

- Az adatok másolása az SAP HANA-adatbázis bármely verzióját.
- Másolhat adatokat **HANA információk modellek** (például az elemzési és számítási nézeteket), és **sorhoz/oszlophoz táblák** SQL-lekérdezések használatával.
- Másolja az adatokat az **alapszintű** vagy **Windows** hitelesítést.

> [!NOTE]
> Adatok másolása **be** SAP HANA-adatok tárolására, általános ODBC-összekötő használatára. Lásd: [SAP HANA fogadó](connector-odbc.md#sap-hana-sink) adatokkal. Megjegyzés: a társított szolgáltatások, a SAP HANA-összekötő és az ODBC-összekötő rendelkező más típusú, ezért nem használható fel újra.

## <a name="prerequisites"></a>Előfeltételek

Az SAP HANA-összekötő használatához meg kell:

- Egy helyi Integration Runtime beállítása. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.
- Telepítse az SAP HANA ODBC-illesztőt a saját üzemeltetésű integrációs gépen. Az SAP HANA ODBC-illesztőprogramot az letöltheti a [SAP Software Download Center](https://support.sap.com/swdc). A keresést a kulcsszóval **SAP HANA CLIENT for Windows**.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások SAP HANA-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

SAP HANA-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapHana** | Igen |
| kiszolgáló | A kiszolgálóra, amelyen az SAP HANA-példány neve. Ha a kiszolgáló egy egyéni portot használ, adja meg a `server:port`. | Igen |
| authenticationType | Az SAP HANA-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Engedélyezett értékek a következők: **Alapszintű**, és **Windows** | Igen |
| Felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó nevét. | Igen |
| jelszó | A felhasználó jelszava. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Egy helyi Integration Runtime szükség, az említett [Előfeltételek](#prerequisites). |Igen |

**Példa**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz az SAP HANA-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása az SAP HANA, állítsa be a type tulajdonság, az adatkészlet **RelationalTable**. Noha nem támogatott, az SAP HANA-adatkészlet típusa jellemző tulajdonságok írja be a RelationalTable.

**Példa**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az SAP HANA-adatforrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-hana-as-source"></a>SAP HANA forrásként

Adatok másolása az SAP HANA, állítsa be a forrás típusaként a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Adja meg az SQL-lekérdezést az SAP HANA-példány adatokat olvasni. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>Adattípus-leképezés az SAP Hana-hoz

Példatípust az adatok SAP HANA-ból, a következő hozzárendeléseket használják az SAP HANA-adattípusok Azure Data Factory-közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| SAP HANA-adattípus | Data factory közbenső adattípus |
|:--- |:--- |
| ALPHANUM | Karakterlánc |
| BIGINT | Int64 |
| BLOB | Byte] |
| LOGIKAI ÉRTÉK | Bájt |
| CLOB | Byte] |
| DATE | DateTime |
| TIZEDES TÖRT | Tizedes tört |
| DUPLA | Önálló |
| INT | Int32 |
| NVARCHAR | Karakterlánc |
| VALÓDI | Önálló |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| TIME | Időtartam |
| IDŐBÉLYEG | DateTime |
| TINYINT | Bájt |
| VARCHAR | Karakterlánc |

## <a name="known-limitations"></a>Ismert korlátozások

Ha az adatok másolása az SAP HANA, van néhány ismert korlátozásai:

- Az NVARCHAR karakterláncokat csonkolja hosszabb 4000 Unicode karakter
- A SMALLDECIMAL nem támogatott.
- A VARBINARY nem támogatott
- Érvényes dátumok 1899/12/30 közötti és 9999/12/31 közöttiek


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
