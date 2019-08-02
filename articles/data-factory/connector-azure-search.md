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
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: edf475ac11168c33a6b11ccda3482ac44579e8d8
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726222"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Adatmásolás Azure Search-indexbe Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-search-connector.md)
> * [Aktuális verzió](connector-azure-search.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az Adatmásolás Azure Search indexbe. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

A Azure Search indexbe bármilyen támogatott forrás-adattárból másolhat adatok. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek Data Factory Azure Search-összekötőhöz tartozó entitások definiálásához használatosak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Azure Search társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **AzureSearch** | Igen |
| url | A Azure Search szolgáltatás URL-címe. | Igen |
| key | Az Azure Search szolgáltatás rendszergazdai kulcsa. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

> [!IMPORTANT]
> Ha egy felhőalapú adattárból Azure Search indexbe másol adatokból, Azure Search társított szolgáltatásban, akkor a connactVia-ben egy explicit régióval rendelkező Azure Integration Runtime kell hivatkoznia. Állítsa be a régiót, amely a Azure Search található. További információ: [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a Azure Search adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatAzure Searchba való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **AzureSearchIndex** | Igen |
| indexName | A Azure Search index neve. Data Factory nem hozza létre az indexet. Az indexnek léteznie kell a Azure Searchban. | Igen |

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
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Azure Search forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-search-as-sink"></a>Azure Search fogadóként

Az adatAzure Searchba való másolásához állítsa a forrás típusát a másolás tevékenység **AzureSearchIndexSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **AzureSearchIndexSink** | Igen |
| writeBehavior | Meghatározza, hogy a rendszer egyesítse vagy lecserélje, ha már létezik dokumentum az indexben. Tekintse meg a [WriteBehavior tulajdonságot](#writebehavior-property).<br/><br/>Engedélyezett értékek a következők: **Egyesítés** (alapértelmezett) és **feltöltés**. | Nem |
| writeBatchSize | Adatok feltöltése a Azure Search indexbe, ha a puffer mérete eléri a writeBatchSize. A részletekért tekintse meg a [WriteBatchSize tulajdonságot](#writebatchsize-property) .<br/><br/>Az engedélyezett értékek: integer 1 – 1 000; az alapértelmezett érték a 1000. | Nem |

### <a name="writebehavior-property"></a>WriteBehavior tulajdonság

AzureSearchSink upsert az adatírás során. Más szóval, ha a dokumentum írásakor a dokumentum kulcsa már létezik a Azure Search indexben, Azure Search a meglévő dokumentumot frissíti, nem pedig ütközést okozó kivételt.

A AzureSearchSink a következő két upsert-viselkedést biztosítja (a AzureSearch SDK használatával):

- **Egyesítés**: az új dokumentum összes oszlopát egyesítheti a meglévővel. Az új dokumentumban null értékű oszlopok esetén a meglévő érték megmarad.
- **Feltöltés**: Az új dokumentum lecseréli a meglévőt. Az új dokumentumban nem megadott oszlopok esetében az érték null, ha a meglévő dokumentumban nem null érték van megadva, vagy nem.

Az alapértelmezett viselkedés az **Egyesítés**.

### <a name="writebatchsize-property"></a>WriteBatchSize tulajdonság

A Azure Search szolgáltatás támogatja a dokumentumok kötegként való írását. Egy köteg 1 – 1 000 műveletet tartalmazhat. Egy művelet kezeli az egyik dokumentumot a feltöltési/egyesítési művelet végrehajtásához.

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

### <a name="data-type-support"></a>Adattípusok támogatása

A következő táblázat meghatározza, hogy támogatott-e Azure Search adattípusa.

| Azure Search adattípus | Azure Search fogadóban támogatott |
| ---------------------- | ------------------------------ |
| Sztring | I |
| Int32 | I |
| Int64 | I |
| Double | I |
| Boolean | I |
| DataTimeOffset | I |
| String Array | N |
| Geographypoint adattípuson | N |

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
