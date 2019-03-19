---
title: Adatok másolása a Search-indexbe az Azure Data Factory használatával |} A Microsoft Docs
description: További leküldéses vagy az adatok másolása az Azure search-index által az Azure Data Factory-folyamatot másolási tevékenységgel kapcsolatban.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 1c8cbcd2e5f137b1e8381dcce164ae9a4b87e804
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852841"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Adatok másolása az Azure Search-index, az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-azure-search-connector.md)
> * [Aktuális verzió](connector-azure-search.md)

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolása az Azure Search-indexbe. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Bármely támogatott forrásadattárból adatokat másolhatja az Azure Search-index. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott Azure Search-összekötőre-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Társított Azure Search szolgáltatás az alábbi tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureSearch** | Igen |
| url | Az Azure Search szolgáltatás URL-címe. | Igen |
| kulcs | Az Azure Search szolgáltatás rendszergazdai kulcsa. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

> [!IMPORTANT]
> Ha az Azure Search-index, az Azure Search szolgáltatásban az adatok másolása egy felhőalapú adattár társított szolgáltatást, tekintse meg az Azure integrációs modul connactVia explicit régiónként kell. A régió állítja be az Azure Search található egy. További információkat talál a [Azure integrációs modul](concepts-integration-runtime.md#azure-integration-runtime).

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz az Azure Search adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása az Azure Search szolgáltatásba, állítsa be a type tulajdonság, az adatkészlet **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **AzureSearchIndex** | Igen |
| indexName | Az Azure Search-index neve. Adat-előállító nem hoz létre az indexet. Az index léteznie kell az Azure Search szolgáltatásban. | Igen |

**Példa**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Azure Search-adatforrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-search-as-sink"></a>Az Azure Search pedig a fogadó

Az adatok másolása az Azure Search szolgáltatásba, állítsa be a forrás típusaként a másolási tevékenység **AzureSearchIndexSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **AzureSearchIndexSink** | Igen |
| WriteBehavior | Megadja, hogy egyesítéséhez, vagy cserélje le, amikor a dokumentum az indexben már létezik. Tekintse meg a [WriteBehavior tulajdonság](#writebehavior-property).<br/><br/>Engedélyezett értékek a következők: **Egyesítse** (alapértelmezett), és **feltöltése**. | Nem |
| WriteBatchSize | Amikor a puffer mérete eléri a writeBatchSize feltölti az adatokat az Azure Search-indexbe. Tekintse meg a [WriteBatchSize tulajdonság](#writebatchsize-property) részleteiről.<br/><br/>Engedélyezett értékek a következők: egész szám 1, 1000; alapértelmezett érték 1000. | Nem |

### <a name="writebehavior-property"></a>WriteBehavior tulajdonság

Az adatok írásakor AzureSearchSink upserts. Más szóval ha ír egy dokumentumot, ha az Azure Search-index már létezik a dokumentum kulcsaként, Azure Search frissíti egy ütközést kivétel értesítő helyett a meglévő dokumentumot.

A AzureSearchSink biztosítja az alábbi két upsert viselkedések (SDK-val AzureSearch):

- **Egyesítse**: összes oszlop az új dokumentum a egyesítése. Az új dokumentum null értékű oszlopokhoz a rendszer megőrzi az értéket a meglévőt.
- **Töltse fel**: Az új dokumentum felülírja a meglévőt. Nincs megadva az új dokumentum oszlopokhoz a értéke van-e egy nem null értéket a meglévő dokumentum vagy nem null értékű.

Alapértelmezés szerint **egyesítése**.

### <a name="writebatchsize-property"></a>WriteBatchSize tulajdonság

Az Azure Search szolgáltatás támogatja a kötegelt dokumentumok írása. A batch 1-1 000 műveletek is tartalmazhat. Egy műveletet kezeli egy dokumentumot a feltöltési/merge művelet végrehajtásához.

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

### <a name="data-type-support"></a>Adattípus-támogatás

Az alábbi tábla meghatározza, hogy egy Azure Search adattípus támogatott-e, vagy nem.

| Az Azure Search-adattípus | Az Azure Search fogadó támogatott |
| ---------------------- | ------------------------------ |
| String | I |
| Int32 | I |
| Int64 | I |
| Double | I |
| Logikai | I |
| DataTimeOffset | I |
| Karakterlánc-tömbben | N |
| GeographyPoint | N |

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
