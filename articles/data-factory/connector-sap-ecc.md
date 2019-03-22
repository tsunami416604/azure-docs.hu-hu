---
title: Adatok másolása az Azure Data Factory használatával az SAP ECC |} A Microsoft Docs
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
ms.date: 04/26/2018
ms.author: jingwang
ms.openlocfilehash: d86264b632daa09a899fae28e73e117b16322617
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121961"
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával az SAP ECC

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység használatával adatokat másol a SAP ECC (SAP vállalati központi összetevő). Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat SAP ECC bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az SAP ECC összekötő támogatja:

- Az adatok másolása az SAP NetWeaver 7.0-s verzió vagy újabb SAP ECC. 
- Az adatok másolása SAP ECC OData-szolgáltatásaival (például SAP tábla/nézetek, BAPI, adatok Információkinyerők, stb.) által elérhetővé tett objektumok, és az SAP-PI, OData relatív adapterek keresztül kapott küldött adatok/Idoc.
- Alapszintű hitelesítés használata az adatok másolását.

## <a name="prerequisites"></a>Előfeltételek

SAP ECC általában entitások OData-szolgáltatásaival, SAP-átjárón keresztül tesz elérhetővé. Az SAP ECC-összekötő használatához meg kell:

- **Állítsa be SAP-átjáróhoz**. SAP NetWeaver verziója magasabb, mint 7.4-kiszolgálókat az SAP-átjáró már telepítve van. Ellenkező esetben kell beágyazott átjáró vagy az átjáró telepítése előtt is közzéteheti az SAP ECC-adatok OData-szolgáltatásokon keresztül hub. Ismerje meg, hogyan állítható be SAP-átjáróhoz való [telepítési útmutató](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktiválhatja és konfigurálhatja az SAP OData-szolgáltatás**. Az OData-szolgáltatásaival TCODE SICF keresztül aktiválhatja másodpercek alatt. Beállíthatja azt is, milyen objektumokat igényeinek megfelelően kell tenni. Íme egy példa [lépésenként](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="getting-started"></a>Első lépések

.NET SDK-t, Python SDK-t, az Azure PowerShell, REST API-t vagy az Azure Resource Manager-sablon használatával másolási tevékenységgel rendelkező folyamatot hozhat létre. Lásd: [másolási tevékenység oktatóanyagát](quickstart-create-data-factory-dot-net.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az alábbi szakaszok nyújtanak, amelyek adott Data Factory-entitások meghatározzák az összekötő az SAP ECC-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

SAP ECC társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapEcc** | Igen |
| url | Az SAP ECC OData-szolgáltatás URL-címe | Igen |
| felhasználónév | A SAP ECC való kapcsolódáshoz használt felhasználónév. | Nem |
| jelszó | A titkosítatlan szöveges jelszó, a SAP ECC csatlakozásra használt. | Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. (Ha az adattár nyilvánosan hozzáférhető) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**Példa**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a SAP ECC adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása az SAP ECC, állítsa be a type tulajdonság, az adatkészlet **SapEccResource**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| elérési út | Az SAP ECC OData entitás elérési útja. | Igen |

**Példa**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a SAP ECC forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-ecc-as-source"></a>SAP ECC forrásként

Adatok másolása az SAP ECC, állítsa be a forrás típusaként a másolási tevékenység **SapEccSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **SapEccSource** | Igen |
| lekérdezés | Adatok szűrése OData-lekérdezés beállításai. Example: "$select=Name,Description&$top=10".<br/><br/>SAP ECC összekötő adatokat másol a kombinált URL-cím: (a társított szolgáltatás megadott URL-címe) / (adatkészletben megadott elérési út)? (a másolási tevékenység forrásban megadott lekérdezést). Tekintse meg [OData URL-címe összetevők](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

**Példa**

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

## <a name="data-type-mapping-for-sap-ecc"></a>A SAP ECC adattípus-leképezés

Amikor az adatok másolása az SAP ECC, a következő hozzárendeléseket használtak OData adattípusok SAP ECC adatok Azure Data Factory-közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| OData-adattípus | Data factory közbenső adattípus |
|:--- |:--- |
| Edm.Binary | String |
| Edm.Boolean | Bool |
| Edm.Byte | String |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | String |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Összetett adattípusok nem támogatottak most.

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
