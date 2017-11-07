---
title: "Adatok másolása Search-index Azure Data Factory használatával |} Microsoft Docs"
description: "További tudnivalók leküldéses vagy adatok másolása az Azure search-index használatával a másolási tevékenység során az Azure Data Factory-folyamathoz."
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
ms.date: 09/30/2017
ms.author: jingwang
ms.openlocfilehash: ebf63cd6d0f9e62d1001d74ce06b4849e08c0de0
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Adatok másolása az Azure Search-index Azure Data Factory használatával

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-azure-search-connector.md)
> * [2. verzió – Előzetes verzió](connector-azure-search.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatait átmásolja az Azure Search-index. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Azure Search-összekötőt a V1](v1/data-factory-azure-search-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Bármely támogatott forrás adattár adatok átmásolhatja az Azure Search-index. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

## <a name="getting-started"></a>Bevezetés
A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják megadhatók a Data Factory tartozó entitások Azure Search-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Csatolt Azure Search szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureSearch** | Igen |
| URL-címe | Az Azure Search szolgáltatás URL-címe. | Igen |
| kulcs | Az Azure Search szolgáltatás adminisztrációs kulcsot. Ez a mező megjelölése a SecureString. | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

> [!IMPORTANT]
> Felhőalapú adattároló az adatok másolása az Azure Search-index, az Azure Search szolgáltatás csatolt kell egy Azure integrációs futásidejű connactVia explicit régiót, tekintse meg. A régió állítja be azt az Azure Search helyezkedik el. További az [Azure integrációs futásidejű] (fogalmak-integráció-runtime.md #azure-integráció-futásidejű).

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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az Azure Search dataset által támogatott tulajdonságokról.

Adatok másolása az Azure Search szolgáltatásba történő, állítsa be a type tulajdonságot az adathalmaz **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **AzureSearchIndex** | Igen |
| indexName | Az Azure Search-index neve. Adat-előállító nem hoz létre az indexet. Az index az Azure Search léteznie kell. | Igen |

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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Azure Search forrás által támogatott tulajdonságokról.

### <a name="azure-search-as-sink"></a>Az Azure Search mint fogadó

Adatok másolása az Azure Search szolgáltatásba történő, állítsa be a forrás típusa a másolási tevékenység **AzureSearchIndexSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **AzureSearchIndexSink** | Igen |
| WriteBehavior | Megadja, hogy egyesíteni vagy cserélje le, ha az index már létezik egy dokumentumot. Tekintse meg a [WriteBehavior tulajdonság](#writebehavior-property).<br/><br/>Két érték engedélyezett: **egyesítése** (alapértelmezett), és **feltöltése**. | Nem |
| WriteBatchSize | Amikor a puffer mérete eléri writeBatchSize feltölti az adatok be az Azure Search-index. Tekintse meg a [WriteBatchSize tulajdonság](#writebatchsize-property) részleteiről.<br/><br/>Engedélyezett értékek: 1-1 000; egész szám alapértelmezett érték 1000. | Nem |

### <a name="writebehavior-property"></a>WriteBehavior tulajdonság

AzureSearchSink upserts adatok írásakor. Ez azt jelenti történő írásakor egy dokumentumot, ha a dokumentum kulcs már létezik az Azure Search-index, az Azure Search frissíti a meglévő dokumentumról, hanem egy ütközés Kivétel kiváltása.

A AzureSearchSink az alábbi két upsert viselkedésmódok biztosít a (AzureSearch SDK használatával):

- **Egyesítési**: kombinálhatja az összes oszlop az új dokumentum a meglévővel. Az oszlop, null értéket ad meg az új dokumentum a meglévő ütemezés értékét megőrződik.
- **Töltse fel**: az új dokumentum lecseréli a meglévő fájlt. Nincs megadva az új dokumentum oszlopokhoz a beállítás értéke be NULL értékre van-e egy nem null értéket a meglévő dokumentum vagy sem.

Az alapértelmezett viselkedés **egyesítése**.

### <a name="writebatchsize-property"></a>WriteBatchSize tulajdonság

Az Azure Search szolgáltatás egy kötegelt dokumentumok írása támogatja. A kötegelt 1-1 000 műveletek is tartalmazhat. Egy műveletet a feltöltési/egyesítési művelet egy dokumentum kezeli.

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

Az alábbi táblázat felsorolja, hogy az Azure Search adattípus támogatott-e, vagy nem.

| Az Azure Search-adattípus | Az Azure Search fogadó támogatott |
| ---------------------- | ------------------------------ |
| Karakterlánc | I |
| Int32 | I |
| Int64 | I |
| Dupla | I |
| Logikai érték | I |
| DataTimeOffset | I |
| Karakterlánc-tömbben | N |
| GeographyPoint | N |

## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).