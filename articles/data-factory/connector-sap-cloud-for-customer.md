---
title: Adatok másolása/SAP felhőbe az Azure Data Factory használatával |} Microsoft Docs
description: 'Útmutató: adatok másolása SAP felhő ügyfél támogatott fogadó adattárolókhoz (vagy) a támogatott forráshierarchiából adattárolókhoz SAP felhő ügyfél Data Factory használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jingwang
ms.openlocfilehash: 300ae2a9dd788ea7d0259d9ae9a6f4d52494836e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Azure Data Factory használatával SAP felhőből ügyfél (C4C) adatok másolása

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatok másolása az/SAP felhő ügyfél (C4C). Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok másolása az SAP felhő ügyfél az egyetlen támogatott fogadó adattár, vagy adatok másolása az bármely támogatott forrás adattár az SAP-felhő ügyfél. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az összekötő lehetővé teszi, hogy Azure Data Factoryben az adatok másolása az/SAP felhő többek között a SAP felhő az értékesítés, SAP felhőalapú szolgáltatás és a közösségi Engagement megoldások SAP felhő ügyfél.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják SAP felhőalapú ügyfél-összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Az alábbi tulajdonságokat az ügyfélszolgálat kapcsolódó SAP felhő támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapCloudForCustomer**. | Igen |
| url | Az SAP C4C OData-szolgáltatás URL-CÍMÉT. | Igen |
| felhasználónév | Adja meg a felhasználónevet, a SAP C4C való kapcsolódáshoz. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nem a forrást, a fogadó Igen |

>[!IMPORTANT]
>Adatok másolása az SAP-felhő ügyfél, explicit módon [hozzon létre egy Azure-IR](create-azure-integration-runtime.md#create-azure-ir) után, például saját SAP-alapú ügyfél és a hivatkozott szolgáltatásban található társítható közelében hellyel rendelkező:

**Példa**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a témakör az ügyfél az adatkészlethez SAP felhő által támogatott tulajdonságokról.

Adatok másolása SAP felhő ügyfél, az adatkészlet típus tulajdonságának beállítása **SapCloudForCustomerResource**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **SapCloudForCustomerResource** |Igen |
| elérési út | Adja meg az SAP C4C OData entitás elérési útját. |Igen |

**Példa**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typePoperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör a felhasználói adatforrás SAP felhő által támogatott tulajdonságokról.

### <a name="sap-c4c-as-source"></a>SAP C4C forrásaként

SAP felhő a felhasználói adatok másolása, állítsa be a forrás típusa a másolási tevékenység **SapCloudForCustomerSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapCloudForCustomerSource**  | Igen |
| lekérdezés | Adja meg az egyéni OData-lekérdezési adatokat olvasni. | Nem |

Az adatok lekérése egy adott napjára mintalekérdezés: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C mint fogadó

Adatok másolása SAP felhő ügyfél, állítsa be a fogadó típusa a másolási tevékenység **SapCloudForCustomerSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapCloudForCustomerSink**  | Igen |
| WriteBehavior | A művelet írási viselkedését. "Insert", "Update" lehet. | Nem. Alapértelmezett "Insert". |
| WriteBatchSize | A Köteg mérete írási művelet. A Köteg mérete, a legjobb teljesítmény különböző tábla vagy a kiszolgáló eltérőek lehetnek. | Nem. Alapértelmezés szerint 10. |

**Példa**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "cloudDataMovementUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Adattípus-hozzárendelése SAP felhő ügyfél

Amikor adatokat másol SAP felhő ügyfél, a következő megfeleltetéseket segítségével SAP felhőből ügyfél adattípus Azure Data Factory ideiglenes adattípusok. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| SAP C4C OData adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |
| Edm.Binary | Byte] |
| Edm.Boolean | Logikai |
| Edm.Byte | Byte] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimális |
| Edm.Double | Dupla |
| Edm.Single | Önálló |
| Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Karakterlánc |
| Edm.Time | A TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).
