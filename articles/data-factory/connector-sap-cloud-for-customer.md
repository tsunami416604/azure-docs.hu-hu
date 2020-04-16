---
title: Adatok másolása az SAP Cloud szolgáltatásból/-be az ügyfél számára
description: Megtudhatja, hogyan másolhat adatokat az SAP Cloud for Customer szolgáltatásból a támogatott fogadó adattárakba (vagy) a támogatott forrásadat-tárolókból az SAP Cloud for Customer szolgáltatásba a Data Factory használatával.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: 1d3772a17d0429d9b3a5bf95d2060f2dfbbbafe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418048"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Adatok másolása az SAP Cloud for Customer (C4C) szolgáltatásból az Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása/az SAP Cloud for Customer (C4C). A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

>[!TIP]
>Ha meg szeretné tudni, hogy az ADF általános támogatást nyújt az SAP-adatintegrációs forgatókönyvhöz, tekintse meg az [SAP-adatok integrálását az Azure Data Factory használatával című tanulmányban,](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) részletes bevezetéssel, összevetéssel és útmutatással.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SAP Cloud for Customer-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

Az SAP Cloud for Customer szolgáltatásból adatokat másolhat bármely támogatott fogadóadattárba, vagy adatokat másolhat bármely támogatott forrásadattárból az SAP Cloud for Customer szolgáltatásba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Pontosabban ez az összekötő lehetővé teszi az Azure Data Factory számára, hogy adatokat másoljon/az SAP Cloud for Customer,beleértve az SAP Cloud for Sales, az SAP Cloud for Service és az SAP Cloud for Social Engagement megoldásokat.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az SAP Cloud for Customer-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az SAP Cloud for Customer linked szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **sapCloudForCustomer**tulajdonságra kell állítani. | Igen |
| url | Az SAP C4C OData szolgáltatás URL-címe. | Igen |
| felhasználónév | Adja meg az SAP C4C-hez csatlakozni kívánt felhasználónevet. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. | Nem a forráshoz, Igen a mosogatóhoz |

>[!IMPORTANT]
>Az SAP Cloud for Customer szolgáltatásba történő másoláshoz hozzon létre kifejezetten [egy Azure-kapcsolati csatolót](create-azure-integration-runtime.md#create-azure-ir) az SAP Cloud for Customer közelében lévő hellyel, és társítsa a csatolt szolgáltatáshoz a következő példaként:

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz az SAP Cloud által az ügyféladatkészlethez támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni az SAP Cloud for Customer szolgáltatásból, állítsa az adatkészlet típustulajdonságát az **SapCloudForCustomerResource beállításra.** A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **SapCloudForCustomerResource** |Igen |
| path | Adja meg az SAP C4C OData entitás elérési útját. |Igen |

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az SAP Cloud for Customer forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-c4c-as-source"></a>SAP C4C forrásként

Ha adatokat szeretne másolni az SAP Cloud for Customer szolgáltatásból, állítsa be a forrástípust a másolási tevékenységben az **SapCloudForCustomerSource mezőre.** A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **SapCloudForCustomerSource**  | Igen |
| lekérdezés | Adja meg az adatok olvasásához az egyéni OData lekérdezést. | Nem |

Mintalekérdezés egy adott nap adatainak bekéséhez:`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

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

### <a name="sap-c4c-as-sink"></a>SAP C4C mosogatóként

Ha adatokat szeretne másolni az SAP Cloud for Customer szolgáltatásba, állítsa be a fogadó típusát a másolási tevékenységben az **SapCloudForCustomerSink**beállításra. A másolási tevékenység **fogadója** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **SapCloudForCustomerSink**  | Igen |
| writeBehavior (írási viselkedés) | A művelet írási viselkedése. Lehet "Beszúrás", "Frissítés". | Nem. Alapértelmezett "Beszúrás". |
| writeBatchSize | Az írási művelet kötegmérete. A kötegméretet, hogy a legjobb teljesítményt eltérő lehet a különböző tábla vagy kiszolgáló. | Nem. Alapértelmezett 10. |

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

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Adattípus-leképezés az SAP Cloud számára az ügyfél számára

Amikor adatokat másol az SAP Cloud for Customer szolgáltatásból, a következő leképezéseket használja a rendszer az SAP Cloud for Customer data types-ből az Azure Data Factory köztes adattípusaiba. A [Séma- és adattípus-hozzárendelések](copy-activity-schema-and-type-mapping.md) című témakörből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrássémát és az adattípust a fogadóhoz.

| SAP C4C adattípus | Adatgyár köztes adattípusa |
|:--- |:--- |
| Edm.Bináris | Bájt[] |
| Edm.Boolean | Logikai |
| Edm.Bájt | Bájt[] |
| Edm.DateTime | DateTime |
| Edm.Decim | Decimal |
| Edm.Double | Double |
| Edm.Egyetlen | Egyirányú |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Sztring |
| Edm.Idő | időtartam |
| Edm.DateTimeOffset | DateTimeOffset (Dátumidő-eltolás) |


## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
