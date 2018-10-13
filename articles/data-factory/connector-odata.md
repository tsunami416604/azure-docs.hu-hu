---
title: Adatmásolás az OData-források az Azure Data Factory használatával |} A Microsoft Docs
description: 'Útmutató: adatok másolása az OData-források a támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.'
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
ms.date: 05/22/2018
ms.author: jingwang
ms.openlocfilehash: c8bee6902fb74cb77c34395fd05c1c861b4f630e
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166134"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>OData forrásból származó adatok másolása az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-odata-connector.md)
> * [Aktuális verzió](connector-odata.md)

Ez a cikk ismerteti az Azure Data Factory másolási tevékenység használatával adatokat másol egy OData-forráshoz. A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat egy OData-forráshoz bármely támogatott fogadó adattárba. Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Pontosabban az OData-összekötő támogatja:

- OData 3.0 és 4.0-s verzióját.
- Adatok másolása a következő hitelesítések egyikének használatával: **névtelen**, **alapszintű**, vagy **Windows**.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével konkrétan az OData-összekötő a Data Factory-entitások definiálása-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Egy társított OData-szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **OData**. |Igen |
| url | A gyökér URL-címe az OData-szolgáltatás. |Igen |
| authenticationType | Az OData-forráshoz való kapcsolódáshoz használt hitelesítés típusa. Engedélyezett értékek a következők **névtelen**, **alapszintű**, és **Windows**. OAuth nem támogatott. | Igen |
| Felhasználónév | Adja meg **felhasználónév** alapszintű vagy Windows-hitelesítés használata esetén. | Nem |
| jelszó | Adja meg **jelszó** a felhasználó számára megadott fiók **felhasználónév**. Jelölje meg a mező egy **SecureString** típus tárolja biztonságos helyen a Data Factoryban. Emellett képes [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) kapcsolódni az adattárhoz. Kiválaszthatja az Azure integrációs modul vagy a saját üzemeltetésű integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**1. példa: Névtelen hitelesítés használatával**

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

**2. példa: Alapszintű hitelesítést használ**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**3. példa: Windows-hitelesítés használatával**

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

Ez a szakasz felsorolja, amely támogatja az OData-adatkészlet tulajdonságai.

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). 

Adatok másolása az OData, állítsa be a **típus** tulajdonság, az adatkészlet **ODataResource**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a tulajdonságot az adatkészlet **ODataResource**. | Igen |
| elérési út | Az OData-erőforrás elérési útja. | Igen |

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

Ez a szakasz az OData-forráshoz támogató tulajdonságok listáját tartalmazza.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData forrásként

Adatok másolása az OData, állítsa be a **forrás** írja be a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrás tulajdonság **RelationalSource**. | Igen |
| lekérdezés | Adatok szűrése az OData-lekérdezés beállításai. Példa: `"?$select=Name,Description&$top=5"`.<br/><br/>**Megjegyzés:**: az OData-összekötő adatokat másol a kombinált URL-cím: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. További információkért lásd: [OData URL-címe összetevők](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

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

## <a name="data-type-mapping-for-odata"></a>Adattípus OData-leképezés

OData-adatok másolása esetén a következő hozzárendeléseket használják OData-adattípusok és az Azure Data Factory közbenső adattípusok között. Hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó kapcsolatban lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md).

| OData-adattípus | Data Factory közbenső adattípus |
|:--- |:--- |
| Edm.Binary | Byte] |
| Edm.Boolean | Logikai |
| Edm.Byte | Byte] |
| Edm.DateTime | DateTime |
| Edm.Decimal | tizedes tört |
| Edm.Double | Dupla |
| Edm.Single | Önálló |
| Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Sztring |
| Edm.Time | Időtartam |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Összetett adattípusok OData (például **objektum**) nem támogatottak.


## <a name="next-steps"></a>További lépések

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md##supported-data-stores-and-formats).