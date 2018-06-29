---
title: Adatok másolása az Azure Data Factory használatával SAP ECC |} Microsoft Docs
description: 'Útmutató: adatok másolása az SAP ECC támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával.'
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
ms.date: 04/26/2018
ms.author: jingwang
ms.openlocfilehash: f9f6d2e43fff9a3e57145f39863f66eed64869b2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048583"
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával SAP ECC

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatok másolása az SAP ECC (SAP vállalati központi összetevő). Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességei

SAP ECC adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a SAP ECC összekötő támogatja:

- Az adatok másolása SAP ECC SAP NetWeaver 7.0-s verzió vagy újabb rendszeren. 
- Az adatok másolása SAP ECC OData-szolgáltatások (például SAP-tábla/nézetek, BAPI, vagyis adatok stb.) által elérhetővé tett objektumok, és mint OData relatív adaptereken keresztül fogadott SAP-PI küldött adatok/szolgál.
- Alapszintű hitelesítést használó adatok másolását.

## <a name="prerequisites"></a>Előfeltételek

SAP ECC általában entitások keresztül OData-szolgáltatásaival SAP-átjárón keresztül tesz elérhetővé. Az SAP ECC-összekötő használatához meg kell:

- **Állítsa be az SAP-átjáróhoz**. SAP NetWeaver verziója magasabb, mint 7.4 kiszolgálók az SAP-átjáróhoz már telepítve van. Ellenkező esetben szeretne telepíteni a beágyazott átjáró vagy az átjáró hub előtt SAP ECC adatok OData szolgáltatásokon keresztül. Ismerje meg, hogyan állíthat be az SAP-átjáróhoz [a telepítési útmutató](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktiválhatja és konfigurálhatja az SAP OData szolgáltatás**. Az OData-szolgáltatásaival TCODE SICF keresztül aktiválhatja másodpercben. Milyen objektumokat kell tenni igényeknek is konfigurálhatja. Íme egy minta [részletes útmutatás](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="getting-started"></a>Első lépések

A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják megadhatók a Data Factory tartozó entitások SAP ECC-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

SAP ECC kapcsolódó szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapEcc** | Igen |
| url | Az SAP ECC OData-szolgáltatás URL-címét. | Igen |
| felhasználónév | Az SAP ECC való csatlakozáshoz használt felhasználónév. | Nem |
| jelszó | A titkosítatlan szöveges jelszó, a SAP ECC való kapcsolódáshoz használt. | Nem |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja Self-hosted integrációs futásidejű vagy Azure integrációs futásidejű (ha az adattároló nyilvánosan elérhető). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a témakör az SAP ECC dataset által támogatott tulajdonságokról.

Adatok másolása az SAP ECC, állítsa be a type tulajdonságot az adathalmaz **SapEccResource**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| elérési út | Az SAP ECC OData entitás elérési útját. | Igen |

**Példa**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typePoperties": {
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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az SAP ECC forrás által támogatott tulajdonságokról.

### <a name="sap-ecc-as-source"></a>SAP ECC forrásaként

Adatok másolása SAP ECC, állítsa be a forrás típusa a másolási tevékenység **SapEccSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **SapEccSource** | Igen |
| lekérdezés | OData-lekérdezés beállításai adatok szűréséhez. Példa: "$select = név, leírás & $top = 10".<br/><br/>SAP ECC összekötő adatainak másolása kombinált URL-címről: (társított szolgáltatás megadott URL-cím) / (dataset megadott elérési út)? (a másolási tevékenység forrásban megadott lekérdezés). Tekintse meg [OData URL-címe összetevők](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

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

## <a name="data-type-mapping-for-sap-ecc"></a>SAP ECC adattípus-leképezés

SAP ECC történő másolás, ha a következő megfeleltetéseket szolgálnak az OData-adattípusok SAP ECC adatok Azure Data Factory ideiglenes adattípusokat. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| Az OData-adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |:--- |
| Edm.Binary | Sztring |
| Edm.Boolean | Logikai érték |
| Edm.Byte | Sztring |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimális |
| Edm.Double | Dupla |
| Edm.Single | Önálló |
| Edm.Guid | Sztring |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Sztring |
| Edm.Time | A TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Összetett adattípusú most nem támogatottak.

## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).
