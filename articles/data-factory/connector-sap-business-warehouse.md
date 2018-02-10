---
title: "Adatok másolása az Azure Data Factory használatával SAP BW |} Microsoft Docs"
description: "Útmutató: adatok másolása az SAP Business Warehouse támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: c05d94c8dce51d922be3782380d4a883717bf032
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Adatok másolása az SAP Business Warehouse Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-sap-business-warehouse-connector.md)
> * [2. verzió – Előzetes verzió](connector-sap-business-warehouse.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory egy SAP Business Warehouse (BW) az adatok másolása. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [SAP BW-összekötőt a V1](v1/data-factory-sap-business-warehouse-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

SAP Business Warehouse adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az SAP Business Warehouse-összekötő támogatja:

- SAP Business Warehouse **verzió 7.x**.
- Az adatok másolásának **előforduló infocubes-értékeket és QueryCubes** (beleértve a BEx lekérdezések) segítségével MDX-lekérdezésekben.
- Alapszintű hitelesítést használó adatok másolását.

## <a name="prerequisites"></a>Előfeltételek

Az SAP Business Warehouse-összekötő használatához meg kell:

- Állítson be egy Self-hosted integrációs futásidejű. Lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben alább.
- Telepítse a **SAP NetWeaver könyvtár** a integrációs futásidejű gépen. A SAP rendszergazdájától, vagy közvetlenül az SAP Netweaver könyvtár kaphat a [SAP szoftverletöltő központból](https://support.sap.com/swdc). Keresse meg a **SAP Megjegyzés #1025361** lekérni a legújabb verziót a letöltési helyét. Győződjön meg arról, hogy válasszon a **64 bites** SAP NetWeaver függvénytár, amely megfelel az integrációs futásidejű telepítése. Telepítse az SAP NetWeaver RFC SDK az SAP Megjegyzés szereplő összes fájlt. Az SAP NetWeaver könyvtár is szerepel a SAP Client Tools telepítését.

> [!TIP]
> Helyezze a NetWeaver RFC SDK kinyert a system32 mappába a dll-fájl.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják való SAP Business Warehouse-összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

SAP Business Warehouse (BW) kapcsolódó szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapBw** | Igen |
| kiszolgáló | A kiszolgálóra az SAP BW-példány neve. | Igen |
| systemNumber | Az SAP BW rendszer rendszer száma.<br/>Érték engedélyezett: kétjegyű tizedes tört karakterláncból. | Igen |
| clientId | Az ügyfél számára a SAP W rendszer ügyfél-azonosítója.<br/>Érték engedélyezett: háromjegyű tizedes tört karakterláncból. | Igen |
| Felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó nevét. | Igen |
| jelszó | A felhasználó jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Egy Self-hosted integrációs futásidejű szükség, ahogyan az [Előfeltételek](#prerequisites). |Igen |

**Példa**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az SAP BW dataset által támogatott tulajdonságokról.

Adatok másolása az SAP BW Programhoz, állítsa be a type tulajdonságot az adathalmaz **RelationalTable**. Miközben nincsenek az SAP BW adatkészlet támogatott típusra vonatkozó tulajdonságok írja be a RelationalTable.

**Példa**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az SAP BW forrás által támogatott tulajdonságokról.

### <a name="sap-bw-as-source"></a>SAP BW forrásaként

Adatok másolása az SAP BW Programhoz, állítsa be a forrás típusa a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Meghatározza az MDX-lekérdezés adatokat olvasni az SAP BW-példány. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw"></a>Adattípus-hozzárendelése az SAP BW Programhoz

Ha az adatok másolása az SAP BW Programhoz, a következő megfeleltetéseket szolgálnak az SAP BW adattípusok Azure Data Factory ideiglenes adattípusok. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| SAP BW-adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |
| ACCP | Int |
| KARAKTER | Karakterlánc |
| CLNT | Karakterlánc |
| CURR | Decimális |
| CUKY | Karakterlánc |
| DEC | Decimális |
| FLTP | Dupla |
| INT1 | Bájt |
| INT2 | Int16 |
| INT4 | Int |
| LANG | Karakterlánc |
| LCHR | Karakterlánc |
| LRAW | Byte] |
| PREC | Int16 |
| QUAN | Decimális |
| RAW | Byte] |
| RAWSTRING | Byte] |
| STRING | Karakterlánc |
| EGYSÉG | Karakterlánc |
| DATS | Karakterlánc |
| NUMC | Karakterlánc |
| TIMS | Karakterlánc |


## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).