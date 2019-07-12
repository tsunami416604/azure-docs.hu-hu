---
title: SAP ECC adatokat másol az Azure Data Factory használatával |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a SAP ECC támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 7ccd2e7a804c6495f6caf5e264b1f7c2a36cb02e
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827769"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>SAP ECC adatokat másol az Azure Data Factory használatával

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolásához az SAP vállalati központi összetevő (ECC). További információkért lásd: [másolási tevékenység áttekintése](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat SAP ECC bármely támogatott fogadó adattárba. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az SAP ECC összekötő támogatja:

- Az adatok másolása az SAP ECC on SAP NetWeaver 7.0-s és újabb verziók.
- Az adatok másolása az SAP ECC OData-szolgáltatásaival, például elérhető objektumok:

  - Az SAP-táblák vagy nézetek.
  - Üzleti Application Programming Interface [BAPI] objektumot.
  - Adatok információkinyerők.
  - Adatok vagy az SAP folyamat integrációs (PI) keresztül relatív adapterek, OData fogadott küldött köztes dokumentumok (Idoc).

- Adatok másolása az egyszerű hitelesítés használatával.

>[!TIP]
>Adatok másolása az SAP ECC egy SAP tábla vagy nézet keresztül, használja a [SAP tábla](connector-sap-table.md) összekötőt, amely gyorsabb és jobban skálázható.

## <a name="prerequisites"></a>Előfeltételek

SAP ECC általában entitások OData-szolgáltatásaival, SAP-átjárón keresztül tesz elérhetővé. Az SAP ECC-összekötő használatához meg kell:

- **Állítsa be SAP-átjáróhoz**. SAP NetWeaver verziók 7.4 később-kiszolgálókat SAP-átjáróhoz már telepítve van. A korábbi verziók előtt telepítenie kell a beágyazott SAP-átjáróhoz vagy az SAP-átjáróhoz hub rendszer adatokhoz hozzáférést biztosító SAP ECC-adatok OData-szolgáltatásokon keresztül. Az SAP-átjáró beállításával, tekintse meg a [telepítési útmutató](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktiválhatja és konfigurálhatja az SAP OData-szolgáltatás**. Az OData-szolgáltatás TCODE SICF keresztül aktiválhatja másodpercek alatt. Beállíthatja azt is, mely objektumoknak van szüksége, kell tenni. További információkért lásd: a [lépésenként](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott és az SAP ECC-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A SAP ECC társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| `type` | A `type` tulajdonságot állítsa `SapEcc`. | Igen |
| `url` | Az SAP ECC OData-szolgáltatás URL-címe | Igen |
| `username` | A SAP ECC csatlakozásra használt felhasználónév. | Nem |
| `password` | A titkosítatlan szöveges jelszó, a SAP ECC csatlakozásra használt. | Nem |
| `connectVia` | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja a saját üzemeltetésű integrációs modult vagy az Azure-beli integrációs modul (ha az adattár nyilvánosan elérhető). Ha nem adja meg a futtatókörnyezet `connectVia` használja az alapértelmezett Azure integrációs modul. | Nem |

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

A szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md). A következő szakasz az SAP ECC adatkészlet által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása az SAP ECC, állítsa be a `type` tulajdonság, az adatkészlet `SapEccResource`.

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
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
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A szakaszok és a tevékenységek definiálását tulajdonságainak teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). A következő szakasz a SAP ECC forrás által támogatott tulajdonságokról listáját tartalmazza.

### <a name="sap-ecc-as-a-source"></a>SAP ECC forrásként

Adatok másolása az SAP ECC, állítsa be a `type` tulajdonságot a `source` a másolási tevékenység szakaszában `SapEccSource`.

A következő tulajdonságok támogatottak a másolási tevékenység `source` szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| `type` | A `type` tulajdonság a másolási tevékenység `source` szakaszban állítsa `SapEccSource`. | Igen |
| `query` | Az OData lekérdezési beállítások szűrje az adatokat. Példa:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Az SAP ECC-összekötő adatokat másol a kombinált URL-címe:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>További információkért lásd: [OData URL-címe összetevők](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

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

## <a name="data-type-mappings-for-sap-ecc"></a>Adattípus-leképezések alkalmazását a SAP ECC

SAP ECC amelyből másolunk adatokat, amikor a következő hozzárendeléseket szolgálnak az OData-adattípusok SAP ECC adatok Azure Data Factory-közbenső adattípusok. Hogyan a másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó kapcsolatban lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md).

| OData-adattípus | Data Factory közbenső adattípus |
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
> Összetett adattípusok jelenleg nem támogatottak.

## <a name="next-steps"></a>További lépések

Az a másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
