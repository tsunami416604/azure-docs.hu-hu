---
title: Adatok másolása SAP BWból Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatok az SAP Business Warehouse-ból egy Azure Data Factory adatcsatorna másolási tevékenységének használatával támogatott fogadó adattárakba.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 4ee9048202a5953ff27ea83cfb39cb1af0739e7a
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277507"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Adatok másolása az SAP Business Warehouse-ból Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-sap-business-warehouse-connector.md)
> * [Aktuális verzió](connector-sap-business-warehouse.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok egy SAP Business Warehouse-ból (BW) történő másolásához. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

>[!TIP]
>Az ADF SAP-adatintegrációs forgatókönyvre vonatkozó általános támogatásának megismeréséhez tekintse meg az [SAP-Adatintegráció Azure Data Factory tanulmány használatával](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) részletes bevezetést, comparsion és útmutatást.

## <a name="supported-capabilities"></a>Támogatott képességek

Az SAP Business Warehouse-ból származó adatok bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Ez az SAP Business Warehouse-összekötő a következőket támogatja:

- SAP Business Warehouse **7-es verzió. x**.
- Az adatok másolása az **InfoCubes és a QueryCubes** (beleértve a többek között a többek között a következőt: MDX-lekérdezések
- Adatok másolása egyszerű hitelesítés használatával.

## <a name="prerequisites"></a>Előfeltételek

Az SAP Business Warehouse-összekötő használatához a következőket kell tennie:

- Saját üzemeltetésű Integration Runtime beállítása. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.
- Telepítse az **SAP NetWeaver Library** -t a Integration Runtime gépre. Az SAP NetWeaver kódtárat az SAP-rendszergazdától, vagy közvetlenül az [SAP Software Download Center](https://support.sap.com/swdc)webhelyről szerezheti be. Keresse meg az **SAP-megjegyzés #1025361** a legfrissebb verzió letöltési helyének lekéréséhez. Ügyeljen arra, hogy az Integration Runtime-telepítésnek megfelelő **64 bites** SAP NetWeaver könyvtárat válassza. Ezután telepítse az SAP NetWeaver RFC SDK-ban található összes fájlt az SAP-Megjegyzés alapján. Az SAP NetWeaver könyvtárat az SAP-ügyféleszközök telepítése is tartalmazza.

>[!TIP]
>A SAP BW kapcsolódási problémáinak megoldásához ügyeljen a következőre:
>- A NetWeaver RFC SDK-ból kinyert összes függőségi könyvtár a%windir%\System32 mappában található. Általában a icudt34. dll, a icuin34. dll, a icuuc34. dll, a libicudecnumber. dll, a librfc32. dll, a libsapucum. dll, a sapcrypto. dll, a sapcryto_old. dll, a sapnwrfc. dll.
>- Az SAP-kiszolgálóhoz való csatlakozáshoz használt szükséges portok engedélyezve vannak a saját üzemeltetésű IR-gépen, amely általában a 3300-es és a 3201-es port.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az SAP Business Warehouse-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az SAP Business Warehouse (BW) társított szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **SapBw** | Igen |
| server | Annak a kiszolgálónak a neve, amelyen az SAP BW-példány található. | Igen |
| systemNumber | A SAP BWrendszer rendszerszáma.<br/>Engedélyezett érték: két számjegyből álló decimális szám karakterláncként megadva. | Igen |
| clientId | A-ügyfél ügyfél-azonosítója az SAP W rendszeren.<br/>Engedélyezett érték: a háromjegyű decimális szám karakterláncként van megadva. | Igen |
| userName | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve. | Igen |
| password | A felhasználó jelszava. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. A saját üzemeltetésű Integration Runtime az [Előfeltételek](#prerequisites)szakaszban említettek szerint kell megadni. |Igen |

**Példa:**

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a SAP BW adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok SAP BWból való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **SapBwCube**értékre. A RelationalTable típusú SAP BW adatkészlet esetében nem támogatottak a típus-specifikus tulajdonságok.

**Példa:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Ha a beírt adatkészletet használta `RelationalTable` , a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a SAP BW forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-bw-as-source"></a>SAP BW forrásként

Az adatok SAP BWból történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **SapBwSource** | Igen |
| query | Megadja az MDX-lekérdezést, amely beolvassa az SAP BW-példány adatait. | Igen |

**Példa:**

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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ha a beírt forrást használta `RelationalSource` , a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="data-type-mapping-for-sap-bw"></a>SAP BW adattípusának leképezése

Az adatok SAP BWból való másolása során a rendszer a következő leképezéseket használja SAP BW adattípusokból Azure Data Factory köztes adattípusokra. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| SAP BW adattípus | Data factory közbenső adattípus |
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
| LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | String |
| UNIT | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
