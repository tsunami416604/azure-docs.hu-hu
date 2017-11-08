---
title: "Adatok másolása az Azure Data Factory használatával OData források |} Microsoft Docs"
description: "Ismerje meg az adatok másolása az OData forrásból származó támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával."
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
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: d26adec8c273d015a671c745f2136fc6251fd291
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="copy-data-from-odata-source-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával OData-forrásra
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-odata-connector.md)
> * [2. verzió – Előzetes verzió](connector-odata.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatok másolása az OData-forrásra. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [OData-összekötőt a V1](v1/data-factory-odata-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

OData-forrásra adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az OData-összekötő pontosabban a következőket támogatja:

- OData **3.0 és 4.0-s verzió**.
- A következő hitelesítés használata az adatok másolásának: **névtelen**, **alapvető**, és **Windows**.

## <a name="getting-started"></a>Bevezetés
A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják megadhatók a Data Factory tartozó entitások OData-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Kapcsolódó OData szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **OData** |Igen |
| URL-címe | Az OData-szolgáltatás gyökér URL-címe |Igen |
| AuthenticationType | Az OData-forrásra való kapcsolódáshoz használt hitelesítés típusa.<br/>Két érték engedélyezett: **névtelen**, **alapvető**, és **Windows**. Megjegyzés: OAuth nem támogatott. | Igen |
| Felhasználónév | Adja meg a felhasználónevet Basic vagy Windows-hitelesítés használata. | Nem |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése SecureString. | Nem |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**1. példa: a névtelen hitelesítés használatával**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**2. példa: egyszerű hitelesítést használ.**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
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

**3. példa: a Windows-hitelesítés használatával**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az OData-adatkészlet által támogatott tulajdonságokról.

Adatok másolása az OData, állítsa be a type tulajdonságot az adathalmaz **ODataResource**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **ODataResource** | Igen |
| Elérési út | Az OData-erőforrás elérési útja. | Nem |

**Példa**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az OData-forrásra által támogatott tulajdonságokról.

### <a name="odata-as-source"></a>OData forrásaként

Adatok másolása OData, állítsa be a forrás típusa a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | OData-lekérdezés beállításai adatok szűréséhez. Példa: "? $select neve, leírása és $top = = 5".<br/><br/>Vegye figyelembe a legutóbbi OData összekötő adatainak másolása kombinált URL-címről: `[url specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Tekintse meg [OData URL-címe összetevők](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "query": "?$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-odata"></a>Az adattípus az OData-leképezés

Az adatok másolása OData, amikor az Azure Data Factory ideiglenes adattípusok a következő megfeleltetéseket használtak OData adattípusokat. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| Az OData-adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |
| Edm.Binary | Byte] |
| Edm.Boolean | logikai érték |
| Edm.Byte | Byte] |
| Edm.DateTime | Dátum és idő |
| Edm.Decimal | Decimális |
| Edm.Double | Dupla |
| Edm.Single | Egyetlen |
| Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Karakterlánc |
| Edm.Time | A TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!Note]
> OData összetett adattípusú (például objektum) nem támogatottak.


## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).