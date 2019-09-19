---
title: Adatok másolása az SAP ECC-ból Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat az SAP ECC-ból egy Azure Data Factory-folyamat másolási tevékenységének használatával támogatott fogadó adattárakba.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: c2b9fcc3f75b8f310532978061c887776f007ff0
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71089537"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Adatok másolása az SAP ECC-ból Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factory az SAP Enterprise Central Component (ECC) adatainak másolásához. További információ: [másolási tevékenység áttekintése](copy-activity-overview.md).

>[!TIP]
>Az ADF SAP-adatintegrációs forgatókönyvre vonatkozó általános támogatásának megismeréséhez tekintse meg az [SAP-Adatintegráció Azure Data Factory tanulmány használatával](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) részletes bevezetést, comparsion és útmutatást.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SAP ECC-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az SAP ECC-adatok bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Ez az SAP ECC-összekötő a következőket támogatja:

- Adatok másolása az SAP ECC-on az SAP NetWeaver 7,0-es és újabb verzióiban.
- Adatok másolása az SAP ECC OData Services által elérhető bármely objektumból, például:

  - SAP-táblák vagy-nézetek.
  - Üzleti alkalmazások programozási felülete [BAPI] objektumok.
  - Adatkivonatok.
  - Az SAP Process Integration (PI) szolgáltatásba küldött adatvagy köztes dokumentumok (IDOCs-EK), amelyek relatív adaptereken keresztül OData fogadhatók.

- Adatok másolása egyszerű hitelesítés használatával.

>[!TIP]
>Az SAP ECC-adatok SAP-táblán vagy nézeten keresztüli másolásához használja az [SAP Table](connector-sap-table.md) Connectort, amely gyorsabb és skálázható.

## <a name="prerequisites"></a>Előfeltételek

Az SAP ECC általában az SAP Gateway használatával teszi elérhetővé az entitásokat a OData-szolgáltatásokon keresztül. Az SAP ECC-összekötő használatához a következőket kell tennie:

- **Állítsa be az SAP Gatewayt**. Az SAP NetWeaver 7,4-nál újabb verzióval rendelkező kiszolgálók esetében az SAP Gateway már telepítve van. A korábbi verziók esetében telepítenie kell a beágyazott SAP-átjárót vagy az SAP Gateway hub-rendszerét, mielőtt az SAP ECC-adatok OData-szolgáltatásokon keresztül elérhetővé tehetők. Az SAP Gateway beállításához tekintse meg a [telepítési útmutatót](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktiválja és konfigurálja az SAP OData szolgáltatást**. A OData szolgáltatást másodpercek alatt aktiválhatja a TCODE SICF használatával. Azt is beállíthatja, hogy mely objektumokat kell elérhetővé tenni. További információ: [lépésenkénti útmutató](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az SAP ECC-összekötőre jellemző Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az SAP ECC társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| `type` | A `type` tulajdonságot a következőre `SapEcc`kell beállítani:. | Igen |
| `url` | Az SAP ECC OData szolgáltatás URL-címe. | Igen |
| `username` | Az SAP ECC-hoz való kapcsolódáshoz használt Felhasználónév. | Nem |
| `password` | Az SAP ECC-hoz való kapcsolódáshoz használt egyszerű szöveges jelszó. | Nem |
| `connectVia` | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nem ad meg futtatókörnyezetet, a rendszer az alapértelmezett Azure Integration Runtime-t használja. | Nem |

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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md). A következő szakasz az SAP ECC-adatkészlet által támogatott tulajdonságokat tartalmazza.

Az adatok SAP ECC-ból történő másolásához `type` állítsa az `SapEccResource`adatkészlet tulajdonságát a következőre:.

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

A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). A következő szakasz az SAP ECC-forrás által támogatott tulajdonságokat tartalmazza.

### <a name="sap-ecc-as-a-source"></a>SAP ECC forrásként

Az SAP ECC-ból származó adatok másolásához `type` a másolási `source` tevékenység `SapEccSource`szakaszának tulajdonságát állítsa a következőre:.

A másolási tevékenység `source` szakasza a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| `type` | A másolási `source` tevékenység `SapEccSource`szakaszának tulajdonságát be kell állítani. `type` | Igen |
| `query` | Az OData lekérdezési beállításai az adatszűréshez. Példa:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Az SAP ECC-összekötő az összesített URL-címről másolja az adatait:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>További információ: [OData URL-összetevők](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

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

## <a name="data-type-mappings-for-sap-ecc"></a>Az SAP ECC adattípus-hozzárendelései

Az SAP ECC-ból történő adatmásoláskor a következő leképezések használatosak az SAP ECC-adatok OData adattípusaiból Azure Data Factory ideiglenes adattípusokhoz. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadóra, tekintse meg a [séma-és adattípus-leképezéseket](copy-activity-schema-and-type-mapping.md)

| OData adattípusa | Data Factory közbenső adattípus |
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

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>További lépések

A Azure Data Factoryban a másolási tevékenység által a forrásként és a nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
