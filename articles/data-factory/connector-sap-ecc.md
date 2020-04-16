---
title: Adatok másolása az SAP ECC-ből
description: Megtudhatja, hogyan másolhatja az adatokat az SAP ECC-ről a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: ad26fca94527864af10bb0051336c372ea65b3e0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413797"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Adatok másolása az SAP ECC-ből az Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk ismerteti, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása az SAP Enterprise Central Component (ECC). További információt a [Tevékenység másolása – áttekintés című témakörben talál.](copy-activity-overview.md)

>[!TIP]
>Ha meg szeretné tudni, hogy az ADF általános támogatást nyújt az SAP-adatintegrációs forgatókönyvhöz, tekintse meg az [SAP-adatok integrálását az Azure Data Factory használatával című tanulmányban,](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) részletes bevezetéssel, összevetéssel és útmutatással.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SAP ECC-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

Az SAP ECC-ből adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként vagy fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában láthatja.

Ez az SAP ECC-összekötő kifejezetten a következőket támogatja:

- Adatok másolása az SAP ECC-ből az SAP NetWeaver 7.0-s és újabb verzióján.
- Adatok másolása az SAP ECC OData-szolgáltatások által elérhetővé tett objektumokról, például:

  - SAP-táblák vagy nézetek.
  - Üzleti alkalmazásprogramozási felület [BAPI] objektumai.
  - Adatkivonók.
  - Az SAP-folyamatintegrációba (PI) küldött adatok vagy köztes dokumentumok, amelyek relatív adaptereken keresztül OData-ként fogadhatók.

- Adatok másolása egyszerű hitelesítéssel.

>[!TIP]
>Az SAP ECC-ből származó adatok SAP-táblán vagy nézeten keresztül történő másolásához használja az [SAP-táblaösszekötőt,](connector-sap-table.md) amely gyorsabb és méretezhetőbb.

## <a name="prerequisites"></a>Előfeltételek

Általában az SAP ECC az SAP Gateway-en keresztül teszi elérhetővé az entitásokat az OData-szolgáltatásokon keresztül. Az SAP ECC-összekötő használatához a következőket kell használnia:

- **Állítsa be az SAP Gateway -t.** Az SAP NetWeaver 7.4-nél későbbi verzióval rendelkező kiszolgálók esetében az SAP Gateway már telepítve van. A korábbi verziók, telepítenie kell a beágyazott SAP Gateway vagy az SAP Gateway hub rendszer, mielőtt az SAP ECC-adatok az OData-szolgáltatásokon keresztül. Az SAP Gateway beállításához olvassa el a [telepítési útmutatót.](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)

- **Az SAP OData szolgáltatás aktiválása és konfigurálása**. Az OData szolgáltatást másodpercek alatt aktiválhatja a TCODE SICF-en keresztül. Azt is beállíthatja, hogy mely objektumok legyenek elérhetők. További információt a [részletes útmutatóban](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)talál.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az SAP ECC-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az SAP ECC csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| `type` | A `type` tulajdonságot a `SapEcc`ikonra kell állítani. | Igen |
| `url` | Az SAP ECC OData szolgáltatás URL-címe. | Igen |
| `username` | Az SAP ECC-hez való csatlakozáshoz használt felhasználónév. | Nem |
| `password` | Az SAP ECC-hez való csatlakozáshoz használt egyszerű szöveges jelszó. | Nem |
| `connectVia` | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nem ad meg futásidejű, az alapértelmezett Azure-integrációs futásidejű használatos. | Nem |

### <a name="example"></a>Példa

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az Adatkészletek című [témakörben található.](concepts-datasets-linked-services.md) A következő szakasz az SAP ECC adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni `type` az SAP ECC-ből, állítsa az adatkészlet tulajdonságát a-ra. `SapEccResource`

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| `path` | Az SAP ECC OData entitás elérési útja. | Igen |

### <a name="example"></a>Példa

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a Folyamatok című [témakörben található.](concepts-pipelines-activities.md) A következő szakasz az SAP ECC-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-ecc-as-a-source"></a>SAP ECC mint forrás

Ha adatokat szeretne másolni `type` az SAP `source` ECC-ből, `SapEccSource`állítsa a tulajdonságot a másolási tevékenység szakaszában a-ra.

A másolási tevékenység `source` szakasza a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| `type` | A `type` másolási tevékenység szakaszának tulajdonságát `source` `SapEccSource`a beállításra kell állítani. | Igen |
| `query` | Az Adatok lekérdezési beállításai az adatok szűréséhez. Például:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Az SAP ECC-összekötő adatokat másol a kombinált URL-címről:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>További információ: [OData URL-összetevők](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

### <a name="example"></a>Példa

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Adattípus-leképezések az SAP ECC-hez

Amikor adatokat másol az SAP ECC-ről, a következő leképezések az SAP ECC-adatok OData-adattípusaiból az Azure Data Factory köztes adattípusaihoz használatosak. Ha meg szeretné tudni, hogy a másolási tevékenység hogyan rendeli le a forrássémát és az adattípust a fogadóhoz, olvassa el a [Séma- és adattípus-hozzárendelések című témakört.](copy-activity-schema-and-type-mapping.md)

| OData-adattípus | Adatgyár köztes adattípusa |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Az összetett adattípusok jelenleg nem támogatottak.

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések

Az Azure Data Factory másolási tevékenysége által forrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak című témakörben tetszését.](copy-activity-overview.md#supported-data-stores-and-formats)
