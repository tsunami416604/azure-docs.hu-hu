---
title: Az Adatmásolás a keresési indexbe Azure Data Factory használatával | Microsoft Docs
description: Útmutató az Azure Search-indexek adatainak leküldéséhez vagy másolásához egy Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: jingwang
ms.openlocfilehash: 703dcb5fee0c31328eee91c9673e62653d7aa698
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286345"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Adatmásolás Azure Search-indexbe Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-search-connector.md)
> * [Aktuális verzió](connector-azure-search.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az Adatmásolás Azure Search indexbe. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

A Azure Search indexbe bármilyen támogatott forrás-adattárból másolhat adatok. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

## <a name="getting-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek Data Factory Azure Search-összekötőhöz tartozó entitások definiálásához használatosak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Azure Search társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **AzureSearch** | Igen |
| url | A Azure Search szolgáltatás URL-címe. | Igen |
| kulcs | A Azure Search szolgáltatáshoz tartozó rendszergazdai kulcs. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár a magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem |

> [!IMPORTANT]
> Ha egy felhőalapú adattárból Azure Search indexbe másol adatokból, Azure Search társított szolgáltatásban, akkor a connactVia-ben egy explicit régióval rendelkező Azure Integration Runtime kell hivatkoznia. Állítsa be a régiót, amely a Azure Search található. További információ: [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Példa**

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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Azure Search adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatAzure Searchba való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **AzureSearchIndex** | Igen |
| indexName | A Azure Search index neve. Data Factory nem hozza létre az indexet. Az indexnek léteznie kell a Azure Searchban. | Igen |

**Példa**

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
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Azure Search forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-search-as-sink"></a>Azure Search fogadóként

Az adatAzure Searchba való másolásához állítsa a forrás típusát a másolás tevékenység **AzureSearchIndexSink**. A másolási tevékenység fogadója szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **AzureSearchIndexSink** | Igen |
| WriteBehavior | Meghatározza, hogy a rendszer egyesítse vagy lecserélje, ha már létezik dokumentum az indexben. Tekintse meg a [WriteBehavior tulajdonságot](#writebehavior-property).<br/><br/>Az engedélyezett értékek: **merge** (default) és **upload**. | Nem |
| writeBatchSize | Adatok feltöltése a Azure Search indexbe, ha a puffer mérete eléri a writeBatchSize. A részletekért tekintse meg a [WriteBatchSize tulajdonságot](#writebatchsize-property) .<br/><br/>Az engedélyezett értékek: integer 1 – 1 000; az alapértelmezett érték a 1000. | Nem |

### <a name="writebehavior-property"></a>WriteBehavior tulajdonság

AzureSearchSink upsert az adatírás során. Más szóval, ha a dokumentum írásakor a dokumentum kulcsa már létezik a Azure Search indexben, Azure Search a meglévő dokumentumot frissíti, nem pedig ütközést okozó kivételt.

A AzureSearchSink a következő két upsert-viselkedést biztosítja (a AzureSearch SDK használatával):

- **Egyesítés**: az új dokumentum összes oszlopát egyesítheti a meglévővel. Az új dokumentumban null értékű oszlopok esetén a meglévő érték megmarad.
- **Feltöltés**: az új dokumentum lecseréli a meglévőt. Az új dokumentumban nem megadott oszlopok esetében az érték null, ha a meglévő dokumentumban nem null érték van megadva, vagy nem.

Az alapértelmezett viselkedés az **Egyesítés**.

### <a name="writebatchsize-property"></a>WriteBatchSize tulajdonság

A Azure Search szolgáltatás támogatja a dokumentumok kötegként való írását. Egy köteg 1 – 1 000 műveletet tartalmazhat. Egy művelet kezeli az egyik dokumentumot a feltöltési/egyesítési művelet végrehajtásához.

**Példa**

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
                "referenceName": "<Azure Search output dataset name>",
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

## <a name="data-type-support"></a>Adattípusok támogatása

A következő táblázat meghatározza, hogy támogatott-e Azure Search adattípusa.

| Azure Search adattípus | Azure Search fogadóban támogatott |
| ---------------------- | ------------------------------ |
| Sztring | I |
| Int32 | I |
| Int64 | I |
| duplán | I |
| Logikai | I |
| DataTimeOffset | I |
| Karakterlánc-tömb | N |
| Geographypoint adattípuson | N |

Jelenleg más adattípusok, például a ComplexType nem támogatottak. A Azure Search támogatott adattípusok teljes listáját lásd: [támogatott adattípusok (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
