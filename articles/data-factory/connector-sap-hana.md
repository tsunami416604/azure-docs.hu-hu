---
title: Adatok másolása az Azure Data Factory használatával SAP HANA |} Microsoft Docs
description: 'Útmutató: adatok másolása az SAP HANA támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával.'
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
ms.openlocfilehash: 3afdcbfc2d1d6f40db0f0336be161a06908cb883
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617997"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával SAP HANA
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-sap-hana-connector.md)
> * [2. verzió – Előzetes verzió](connector-sap-hana.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatok másolása az SAP HANA-adatbázisból. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [SAP HANA-összekötőt a V1](v1/data-factory-sap-hana-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok bármely támogatott fogadó adattárolóhoz másolhatja SAP HANA-adatbázisból. A másolási tevékenység által támogatott adatforrások/mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az SAP HANA-összekötő támogatja:

- Az adatok másolása bármely verziója SAP HANA-adatbázisból.
- Az adatok másolásának **HANA információk modellek** (például az elemzési és a számítási nézetek) és **sorhoz/oszlophoz táblák** SQL-lekérdezések használatával.
- Adatok másolása **alapvető** vagy **Windows** hitelesítés.

> [!NOTE]
> Adatok másolása **történő** SAP HANA-adatok tárolására, általános ODBC-összekötő használatára. Lásd: [SAP HANA fogadó](connector-odbc.md#sap-hana-sink) adatokkal. Megjegyzés: a különböző típusú van az összekapcsolt szolgáltatások SAP HANA-összekötő és az ODBC-összekötő így nem használható fel újra.

## <a name="prerequisites"></a>Előfeltételek

Az SAP HANA-összekötő használatához meg kell:

- Állítson be egy Self-hosted integrációs futásidejű. Lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben alább.
- Az SAP HANA ODBC-illesztő telepítse az integrációs futásidejű számítógépen. Az SAP HANA ODBC-illesztő a programot letöltheti a [SAP szoftverletöltő központból](https://support.sap.com/swdc). Keresés a kulcsszóval **SAP HANA ügyfél Windows**.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják megadhatók a Data Factory tartozó entitások SAP HANA-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok SAP HANA kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapHana** | Igen |
| kiszolgáló | A kiszolgálóra az SAP HANA-példány neve. Ha a kiszolgáló egy testreszabott portot használ, adja meg a `server:port`. | Igen |
| authenticationType | Az SAP HANA-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Két érték engedélyezett: **alapvető**, és **Windows** | Igen |
| Felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó nevét. | Igen |
| jelszó | A felhasználó jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Egy Self-hosted integrációs futásidejű szükség, ahogyan az [Előfeltételek](#prerequisites). |Igen |

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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a szakasz egy SAP HANA-adatkészlet által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása az SAP HANA, állítsa be a type tulajdonságot az adathalmaz **RelationalTable**. Miközben nincsenek az SAP HANA-adatkészlet a támogatott típusra vonatkozó tulajdonságok írja be a RelationalTable.

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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz egy SAP HANA-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-hana-as-source"></a>SAP HANA forrásaként

Adatok másolása SAP HANA, állítsa be a forrás típusa a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Adja meg az adatokat olvasni az SAP HANA-példány az SQL-lekérdezést. | Igen |

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

## <a name="data-type-mapping-for-sap-hana"></a>Adattípus-hozzárendelése SAP Hana

Amikor adatokat másol SAP HANA, a következő megfeleltetéseket szolgálnak az SAP HANA-adattípusok Azure Data Factory ideiglenes adattípusok. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| SAP HANA-adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |
| ALPHANUM | Karakterlánc |
| BIGINT | Int64 |
| A BLOB | Byte] |
| LOGIKAI ÉRTÉK | Bájt |
| CLOB | Byte] |
| DATE | DateTime |
| DECIMÁLIS | Decimális |
| DUPLA | Önálló |
| INT | Int32 |
| NVARCHAR | Karakterlánc |
| VALÓS | Önálló |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| IDŐ | A TimeSpan |
| IDŐBÉLYEG | DateTime |
| TINYINT | Bájt |
| VARCHAR | Karakterlánc |

## <a name="known-limitations"></a>Ismert korlátozásai

Ha az adatok másolása SAP HANA, van néhány ismert korlátozásai:

- NVARCHAR karakterláncok csak legfeljebb 4000 Unicode-karaktereket az
- SMALLDECIMAL nem támogatott.
- VARBINARY nem támogatott.
- Érvényes dátumok csak közötti 1899 – 12/30 és 31-9999-12


## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).
