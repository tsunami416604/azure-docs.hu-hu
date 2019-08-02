---
title: Adatok másolása az SAP-felhőbe vagy az ügyfél számára a Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatok az SAP-felhőből az ügyfelek által támogatott fogadó adattárakba (vagy) a támogatott forrás-adattárakból az SAP-felhőbe az ügyfelek számára a Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: jingwang
ms.openlocfilehash: 30025499ae3073a04863d711423bd9556e7fc6c4
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726041"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Adatok másolása az SAP Cloud for Customer (C4C) szolgáltatásból Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok az SAP-felhőbe való másolásához az ügyfél számára (C4C). Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Az SAP-felhőből származó adatok az ügyfélnek bármilyen támogatott fogadó adattárba másolhatók, vagy bármely támogatott forrás-adattárból másolhatók adatok az SAP Cloud for Customer szolgáltatásba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Ez az összekötő lehetővé teszi, hogy Azure Data Factory az adatok másolását az SAP-felhőbe, az SAP Cloud for Sales, az SAP Cloud for Service és az SAP Cloud for Service szolgáltatással, valamint a közösségi szerepvállalási megoldásokhoz.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyeket az SAP Cloud for Customer Connector szolgáltatáshoz tartozó entitások definiálásához használtak Data Factory.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az SAP Cloud for Customer társított szolgáltatáshoz a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **SapCloudForCustomer**. | Igen |
| url | Az SAP-C4C OData szolgáltatásának URL-címe. | Igen |
| username | Adja meg az SAP-C4C való kapcsolódáshoz használandó felhasználónevet. | Igen |
| password | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nincs forrás, a fogadó Igen |

>[!IMPORTANT]
>Az SAP felhőbe való adatmásoláshoz az ügyfél számára explicit módon [hozzon létre egy Azure IR](create-azure-integration-runtime.md#create-azure-ir) az SAP-felhő közelében az ügyfél számára, és társítsa a társított szolgáltatáshoz a következő példában látható módon:

**Példa:**

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz az SAP Cloud for Customer adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha az SAP-felhőből szeretne adatokat másolni az ügyfél számára, állítsa az adatkészlet Type (típus) tulajdonságát **SapCloudForCustomerResource**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **SapCloudForCustomerResource** |Igen |
| path | Az SAP C4C OData entitás elérési útjának megadása. |Igen |

**Példa:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az SAP Cloud által az ügyfél forrásaként támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-c4c-as-source"></a>SAP-C4C forrásként

Az SAP felhőből az ügyfélnek történő adatmásoláshoz állítsa a forrás típusát a másolás tevékenység **SapCloudForCustomerSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **SapCloudForCustomerSource**  | Igen |
| query | Az adatolvasáshoz válassza az egyéni OData-lekérdezést. | Nem |

Példa lekérdezésre egy adott napra vonatkozó adat lekéréséhez:`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Példa:**

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

### <a name="sap-c4c-as-sink"></a>SAP-C4C fogadóként

Ha az ügyfél számára szeretne Adatmásolást készíteni az SAP-felhőbe, a másolási tevékenységben állítsa be a fogadó típusát a **SapCloudForCustomerSink**értékre. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **SapCloudForCustomerSink**  | Igen |
| writeBehavior | A művelet írási viselkedése. Lehet "Insert", "Update". | Nem. Alapértelmezett "Beszúrás". |
| writeBatchSize | Az írási művelet kötegének mérete. A legjobb teljesítmény eléréséhez használt köteg mérete eltérő lehet a különböző táblák vagy kiszolgálók esetében. | Nem. Alapértelmezett 10. |

**Példa:**

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
            "dataIntegrationUnits": 4,
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

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Adattípusok leképezése az SAP Cloud ügyfél számára

Az SAP-felhőből az ügyfélnek történő adatmásoláskor a következő leképezések használhatók az SAP Cloud for Customer adattípusokhoz az ideiglenes adattípusok Azure Data Factoryához. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| Az SAP C4C OData adattípusa | Data factory közbenső adattípus |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | Datetime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
