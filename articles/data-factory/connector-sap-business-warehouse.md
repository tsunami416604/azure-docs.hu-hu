---
title: Adatok másolása az SAP BW-ből
description: Megtudhatja, hogyan másolhatja az adatokat az SAP Business Warehouse-ból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 2f8406038be10ba3bdc207bf447fecb86a376fe8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418065"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Adatok másolása az SAP Business Warehouse-ból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-sap-business-warehouse-connector.md)
> * [Jelenlegi verzió](connector-sap-business-warehouse.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása egy SAP Üzleti Raktár (BW). A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

>[!TIP]
>Ha meg szeretné tudni, hogy az ADF általános támogatást nyújt az SAP-adatintegrációs forgatókönyvhöz, tekintse meg az [SAP-adatok integrálását az Azure Data Factory használatával című tanulmányban,](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) részletes bevezetéssel, összevetéssel és útmutatással.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SAP Business Warehouse-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

Az SAP Business Warehouse-ból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Ez az SAP Business Warehouse-összekötő különösen a következőket támogatja:

- SAP Business Warehouse **7.x verzió.**
- Adatok másolása **InfoCubes és QueryCubes** (beleértve a BEx lekérdezések) mdx-lekérdezések használatával.
- Adatok másolása egyszerű hitelesítéssel.

## <a name="prerequisites"></a>Előfeltételek

Az SAP Business Warehouse összekötő használatához a következőket kell használnia:

- Állítsa be a saját üzemeltetésű integrációs futásidejű. A [részleteket a saját üzemeltetésű integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben találja.
- Telepítse az **SAP NetWeaver könyvtárat** az integrációs futásidejű gépen. Az SAP Netweaver könyvtárat az SAP-rendszergazdától vagy közvetlenül az [SAP szoftverletöltő központból](https://support.sap.com/swdc)szerezheti be. Keresse meg az **SAP Note #1025361** a legújabb verzió letöltési helyét. Győződjön meg arról, hogy a **64 bites** SAP NetWeaver könyvtárat választotta, amely megfelel az integrációs futásidejű telepítésnek. Ezután telepítse az SAP NetWeaver RFC SDK-ban található összes fájlt az SAP Note szerint. Az SAP NetWeaver könyvtár is szerepel az SAP Ügyféleszközök telepítése.

>[!TIP]
>Az SAP BW kapcsolódási problémája elhárításához győződjön meg a következőkről:
>- A NetWeaver RFC SDK-ból kinyert összes függőségi könyvtár a %windir%\system32 mappában található. Általában icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- Az SAP Server kiszolgálóhoz való csatlakozáshoz szükséges portok engedélyezve vannak a saját üzemeltetésű infravörös gépen, amely általában a 3300-as és 3201-es port.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az SAP Business Warehouse-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az SAP Business Warehouse (BW) kapcsolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **SapBw** | Igen |
| kiszolgáló | Annak a kiszolgálónak a neve, amelyen az SAP BW-példány található. | Igen |
| systemNumber (rendszerszám) | Az SAP BW rendszer rendszerszáma.<br/>Megengedett érték: karakterláncként ábrázolt kétjegyű tizedesszám. | Igen |
| ügyfél-azonosító | Az SAP W rendszerben lévő ügyfél ügyfélazonosítója.<br/>Megengedett érték: karakterláncként ábrázolt háromjegyű tizedesszám. | Igen |
| userName (Felhasználónév) | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve. | Igen |
| jelszó | A felhasználó jelszava. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Az előfeltételek ben említettek szerint saját üzemeltetésű [integrációs](#prerequisites)futásidejű re van szükség. |Igen |

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz az SAP BW adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni az SAP BW-ből, állítsa az adatkészlet típustulajdonságát **SapBwCube**-re. Bár a RelationalTable típusú SAP BW adatkészlethez nem támogatottak típusspecifikus tulajdonságok.

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

Ha gépelt adatkészletet használt, `RelationalTable` továbbra is támogatott, amíg a rendszer az újat a jövőben használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az SAP BW-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-bw-as-source"></a>SAP BW forrásként

Az SAP BW-ből történő adatok másolásához a másolási tevékenység **forrásszakaszában** a következő tulajdonságok at támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A copy tevékenységforrás típustulajdonságának a következőre kell alapozni: **SapBwSource** | Igen |
| lekérdezés | Megadja az SAP BW-példányból adatokat beolvasandó MDX-lekérdezést. | Igen |

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

Ha gépelt forrást használt, `RelationalSource` akkor továbbra is támogatott, amíg az újat a jövőben is használhatja.

## <a name="data-type-mapping-for-sap-bw"></a>Adattípus-leképezés az SAP BW-hez

Az SAP BW-ből történő adatok másolásakor a következő leképezések az SAP BW adattípusokból az Azure Data Factory köztes adattípusaiba kerülnek. A [Séma- és adattípus-hozzárendelések](copy-activity-schema-and-type-mapping.md) című témakörből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrássémát és az adattípust a fogadóhoz.

| SAP BW adattípus | Adatgyár köztes adattípusa |
|:--- |:--- |
| ACCP | Int |
| Char | Sztring |
| CLNT | Sztring |
| CURR | Decimal |
| CUKY között | Sztring |
| December | Decimal |
| FLTP | Double |
| INT1 | Bájt |
| INT2 | Int16 |
| INT4 | Int |
| Lang | Sztring |
| LCHR | Sztring |
| LRAW között | Bájt[] |
| PREC | Int16 |
| Quan | Decimal |
| Nyers | Bájt[] |
| RAWSTRING | Bájt[] |
| Karakterlánc | Sztring |
| Egység | Sztring |
| DATS | Sztring |
| NUMC között | Sztring |
| TIMS Között | Sztring |


## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
