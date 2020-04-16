---
title: Adatok másolása a keresési indexbe
description: Megtudhatja, hogyan lehet leküldéses vagy másoló adatokat egy Azure keresési index segítségével a másolási tevékenység egy Azure Data Factory-folyamat használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/13/2019
ms.openlocfilehash: dfa1ad318ccc9e891b646ec050f6a0776e108206
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418235"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Adatok másolása Azure Cognitive Search indexébe az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-search-connector.md)
> * [Aktuális verzió](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása az Azure Cognitive Search index. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Bármely támogatott forrásadattárból adatokat másolhat a keresési indexbe. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az Azure Cognitive Search-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Azure Cognitive Search kapcsolt szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **AzureSearch** | Igen |
| url | A keresési szolgáltatás URL-címe. | Igen |
| kulcs | A keresési szolgáltatás felügyeleti kulcsa. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy saját üzemeltetésű integrációs runtime (ha az adattár található magánhálózat). Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

> [!IMPORTANT]
> Amikor adatokat másol egy felhőbeli adattárból a keresési indexbe, az Azure Cognitive Search kapcsolt szolgáltatásában egy Azure-integrációs futásidejűt kell átutalnia a connactVia explicit régiójával. Állítsa be a régiót úgy, amelyikben a keresési szolgáltatás található. További információ az [Azure Integration Runtime-ról.](concepts-integration-runtime.md#azure-integration-runtime)

**Példa:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz az Azure Cognitive Search adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolásához az Azure Cognitive Search, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következő re kell beállítani: **AzureSearchIndex** | Igen |
| indexName | A keresési index neve. A Data Factory nem hozza létre az indexet. Az indexnek léteznie kell az Azure Cognitive Search szolgáltatásban. | Igen |

**Példa:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az Azure Cognitive Search-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-cognitive-search-as-sink"></a>Azure Cognitive Search mosogatóként

Ha adatokat szeretne másolni az Azure Cognitive Search szolgáltatásba, állítsa be a forrástípusát a másolási tevékenységben az **AzureSearchIndexSink mezőre.** A másolási tevékenység **fogadója** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **AzureSearchIndexSink** | Igen |
| writeBehavior (írási viselkedés) | Itt adható meg, hogy egyesítse vagy cserélje le a programot, ha már létezik dokumentum az indexben. Lásd a [WriteBehavior tulajdonságot](#writebehavior-property).<br/><br/>Az engedélyezett értékek a következők: **Egyesítés** (alapértelmezett) és **Feltöltés**. | Nem |
| writeBatchSize | Adatokat tölt fel a keresési indexbe, amikor a puffer mérete eléri a writeBatchSize-ot. A részleteket a [WriteBatchSize tulajdonságban](#writebatchsize-property) találja.<br/><br/>Az engedélyezett értékek a következők: 1-től 1000-ig egész szám; az alapértelmezett érték 1000. | Nem |

### <a name="writebehavior-property"></a>WriteBehavior tulajdonság

Az AzureSearchSink upserts adatok írásakor. Más szóval, amikor egy dokumentum írásakor, ha a dokumentumkulcs már létezik a keresési indexben, az Azure Cognitive Search frissíti a meglévő dokumentumot, nem pedig ütközési kivétel.

Az AzureSearchSink a következő két upsert viselkedést biztosítja (az AzureSearch SDK használatával):

- **Egyesítés:** az új dokumentum összes oszlopának egyesítése a meglévővel. Az új dokumentumban null értékű oszlopok esetén a meglévő értéke megmarad.
- **Feltöltés**: Az új dokumentum felváltja a meglévőt. Az új dokumentumban nem megadott oszlopok esetén az érték null értékű, függetlenül attól, hogy a meglévő dokumentumban nem null érték van-e vagy sem.

Az alapértelmezett viselkedés az **Egyesítés**.

### <a name="writebatchsize-property"></a>WriteBatchSize tulajdonság

Az Azure Cognitive Search szolgáltatás támogatja a dokumentumok kötegként i. Egy köteg 1–1000 műveletet tartalmazhat. Egy művelet egy dokumentumot kezel a feltöltési/egyesítési művelet végrehajtásához.

**Példa:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Cognitive Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>Adattípus-támogatás

Az alábbi táblázat azt határozza meg, hogy az Azure Cognitive Search adattípusa támogatott-e vagy sem.

| Az Azure Cognitive Search adattípusa | Az Azure Cognitive Search mosogatótámogatja |
| ---------------------- | ------------------------------ |
| Sztring | I |
| Int32 | I |
| Int64 | I |
| Double | I |
| Logikai | I |
| DataTimeOffset | I |
| Karakterlánc-tömb | N |
| FöldrajzPont | N |

Jelenleg más adattípusok, például a ComplexType nem támogatottak. Az Azure Cognitive Search által támogatott adattípusok teljes listáját a [Támogatott adattípusok (Azure Cognitive Search)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)című témakörben olvashat.

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
