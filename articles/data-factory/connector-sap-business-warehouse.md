---
title: Adatok másolása az Azure Data Factory használatával az SAP BW |} A Microsoft Docs
description: 'Útmutató: adatok másolása az SAP Business warehouse-hoz a támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: jingwang
ms.openlocfilehash: 9a0abcd70b4aeb2369604bafa924136122206e0a
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022288"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Adatok másolása az SAP Business warehouse-hoz az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-sap-business-warehouse-connector.md)
> * [Aktuális verzió](connector-sap-business-warehouse.md)

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban másolhat adatokat egy SAP Business Warehouse (BW) a. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

SAP Business Warehouse adatait átmásolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az SAP Business Warehouse-összekötő támogatja:

- SAP Business Warehouse **verzió 7.x**.
- Az adatok másolásának **InfoCubes-értékeket és QueryCubes** (beleértve a BEx lekérdezések) segítségével MDX-lekérdezésben.
- Alapszintű hitelesítés használata az adatok másolását.

## <a name="prerequisites"></a>Előfeltételek

Az SAP Business Warehouse-összekötő használatához meg kell:

- Egy helyi Integration Runtime beállítása. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.
- Telepítse a **SAP NetWeaver könyvtár** az Integration Runtime gépen. Az SAP Netweaver-kódtár az SAP-rendszergazdától, vagy közvetlenül a beszerezheti a [SAP Software Download Center](https://support.sap.com/swdc). Keresse meg a **SAP Note #1025361** a letöltési hely a legújabb verziójának beolvasásához. Győződjön meg arról, hogy válasszon a **64 bites** SAP NetWeaver-könyvtár, amely megfelel az integrációs modul telepítése. Ezután telepítse az összes fájlt is tartalmaz az SAP NetWeaver RFC SDK, az SAP-Jegyzetnek megfelelően. Az SAP NetWeaver-kódtár az SAP Client Tools telepítése is megtalálható.

>[!TIP]
>SAP BW-vel megoldásához győződjön meg arról, hogy:
>- A NetWeaver RFC SDK kinyert tárakat függőségi %windir%\system32 mappában érvényben vannak. Rendelkezik általában icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, helyezze az librfc32.dll fájl, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- Az SAP-kiszolgálóhoz való csatlakozáshoz használt szükséges portok vannak engedélyezve a gépen helyi integrációs modul, amely általában 3300 és 3201 port.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások SAP Business Warehouse-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

SAP Business Warehouse (BW) társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapBw** | Igen |
| kiszolgáló | A kiszolgálóra, amelyen az SAP BW-példány neve. | Igen |
| systemNumber | Az SAP BW-rendszer rendszer száma.<br/>Érték engedélyezett: kétjegyű tizedes tört egy karakterláncból. | Igen |
| clientId | Az SAP W rendszerben az ügyfél ügyfél-azonosítója.<br/>Érték engedélyezett: háromjegyű tizedes tört egy karakterláncból. | Igen |
| Felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó nevét. | Igen |
| jelszó | A felhasználó jelszava. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Egy helyi Integration Runtime szükség, az említett [Előfeltételek](#prerequisites). |Igen |

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz az SAP BW-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása az SAP BW, állítsa be a type tulajdonság, az adatkészlet **RelationalTable**. Noha nem támogatott, az SAP BW-adatkészlet típusa jellemző tulajdonságok írja be a RelationalTable.

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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az SAP BW forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-bw-as-source"></a>SAP BW forrás szerint

Adatok másolása az SAP BW, állítsa be a forrás típusaként a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **RelationalSource** | Igen |
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

## <a name="data-type-mapping-for-sap-bw"></a>Adattípus-leképezés az SAP BW Programhoz

Ha az adatok másolása az SAP BW, a következő hozzárendeléseket a rendszer az SAP BW-adattípusok Azure Data Factory közbenső adattípusok használja. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| SAP BW-adattípus | Data factory közbenső adattípus |
|:--- |:--- |
| ACCP | Int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| NYELV | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | String |
| EGYSÉG | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
