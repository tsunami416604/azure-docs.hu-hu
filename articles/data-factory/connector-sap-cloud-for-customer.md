---
title: Adatok másolása Azure blobból vagy az SAP-felhő ügyfél az Azure Data Factory használatával |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat az ügyfél a támogatott fogadó adattárakba SAP-felhő (vagy) támogatott forrás adattárakból származó SAP-felhő ügyfél Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: jingwang
ms.openlocfilehash: 360cf92686682e65cf5348ee717bbb80cdc85cb4
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016171"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Azure Data Factory használatával az ügyfél (C4C) SAP-felhő adatok másolása

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység használatával adatokat másolni a/SAP-felhő ügyfél (C4C). Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok másolása az SAP-felhő ügyfél bármely támogatott fogadó adattárba, vagy adatok másolása az bármely támogatott forrásadattárból az SAP-felhő ügyfél számára. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Kimondottan az összekötő teszi lehetővé az Azure Data Factoryben az adatok /, az SAP-felhő, többek között az SAP-felhő az értékesítés, az SAP Cloud Service és az SAP Cloud közösségi Engagement megoldások ügyfél.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg Data Factory-entitások egyedi ügyfél-összekötő az SAP Cloud-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő tulajdonságok SAP-felhő az ügyfélszolgálat társított támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapCloudForCustomer**. | Igen |
| url | Az SAP C4C OData-szolgáltatás URL-címe | Igen |
| felhasználónév | Adja meg a felhasználónevet, a SAP C4C csatlakozni. | Igen |
| jelszó | Adja meg a felhasználónév megadott felhasználói fiók jelszavát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nincs forrás, a fogadó Igen |

>[!IMPORTANT]
>Adatmásolás céljából az SAP-felhő ügyfél, explicit módon [hozzon létre egy Azure integrációs modul](create-azure-integration-runtime.md#create-azure-ir) közel az SAP-felhő, a vevő és a hivatkozott szolgáltatásban található társítható egy helyre a következő példa:

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz az ügyfél az adatkészlethez az SAP-felhő által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása az SAP-felhő ügyfél, állítsa be a type tulajdonság, az adatkészlet **SapCloudForCustomerResource**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **SapCloudForCustomerResource** |Igen |
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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az ügyfél forrás SAP-felhő által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-c4c-as-source"></a>SAP C4C forrásként

Adatok másolása az SAP Cloud ügyfél, állítsa be a forrás típusaként a másolási tevékenység **SapCloudForCustomerSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapCloudForCustomerSource**  | Igen |
| lekérdezés | Adja meg az egyéni OData-lekérdezés adatokat olvasni. | Nem |

Adatok beszerzése egy adott nap mintalekérdezés: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

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

### <a name="sap-c4c-as-sink"></a>SAP C4C pedig a fogadó

Adatok másolása az SAP Cloud ügyfél, állítsa be a fogadó típusa a másolási tevékenység **SapCloudForCustomerSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapCloudForCustomerSink**  | Igen |
| WriteBehavior | A művelet írási viselkedését. "Insert", "Frissítés" lehet. | Nem. Alapértelmezett "Insert". |
| WriteBatchSize | A Köteg mérete írási művelet. A legjobb teljesítmény kötegméret különböző tábla vagy a kiszolgáló eltérő lehet. | Nem. Alapértelmezés szerint a 10. |

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

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Adattípus-ügyfél SAP-felhő leképezése

Az adatok másolása az SAP Cloud ügyfél számára, amikor a következő hozzárendeléseket használják az SAP-felhő ügyfél adattípusok Azure Data Factory-közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| SAP C4C OData-adatok típusa | Data factory közbenső adattípus |
|:--- |:--- |
| Edm.Binary | Byte] |
| Edm.Boolean | Logikai |
| Edm.Byte | Byte] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Tizedes tört |
| Edm.Double | Dupla |
| Edm.Single | Önálló |
| Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Karakterlánc |
| Edm.Time | Időtartam |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
