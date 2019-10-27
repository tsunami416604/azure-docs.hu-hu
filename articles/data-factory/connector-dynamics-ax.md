---
title: Adatok másolása a Dynamics AX-ból Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Dynamics AX-ből a fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
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
ms.openlocfilehash: 02080388cdc6e408164de1eff208d19e4b5da60f
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935671"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Adatok másolása a Dynamics AX-ból Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Dynamics AX-forrásból való másolásához. A cikk a [másolási tevékenységre épül Azure Data Factoryban](copy-activity-overview.md), amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Dynamics AX-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a Dynamics AX-ből bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként és a fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Pontosabban, ez a Dynamics AX-összekötő támogatja a Dynamics AX-adatok másolását a **OData protokoll** használatával az **egyszerű szolgáltatás hitelesítésével**.

>[!TIP]
>Ezzel az összekötővel az adatok a **Dynamics 365-pénzügyi és-műveletekből**is másolhatók. Tekintse át a Dynamics 365 [OData-támogatás](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) és- [hitelesítés módszerét](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication).

## <a name="get-started"></a>Az első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a Dynamics AX-összekötőhöz tartozó Data Factory entitások definiálásához használható tulajdonságokat.

## <a name="prerequisites"></a>Előfeltételek

Az egyszerű szolgáltatás hitelesítésének használatához kövesse az alábbi lépéseket:

1. Az alkalmazás [Azure ad-Bérlővel való regisztrálását](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)követően regisztráljon egy Azure Active Directory (Azure ad) alkalmazásbeli entitást. Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához használ:

    - Alkalmazásazonosító
    - Alkalmazás kulcsa
    - Bérlőazonosító

2. Nyissa meg a Dynamics AX-t, és adjon neki megfelelő engedélyt a szolgáltatásnak a Dynamics AX eléréséhez.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Dynamics AX társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **Type** tulajdonságot **DynamicsAX**értékre kell beállítani. |Igen |
| url | A Dynamics AX (vagy a Dynamics 365 pénzügyi és műveleti) példányának OData-végpontja. |Igen |
| servicePrincipalId | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját. | Igen |
| servicePrincipalKey | Az alkalmazás kulcsának meghatározása. Megjelöli ezt a mezőt **SecureString** , hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| Bérlő | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. A Azure Portal jobb felső sarkában lévő egér fölé helyezve lekéri. | Igen |
| aadResourceId | Határozza meg az HRE-erőforrást, amelyhez engedélyt kér. Ha például a Dynamics URL-cím `https://sampledynamics.sandbox.operations.dynamics.com/data/`, a megfelelő HRE-erőforrás általában `https://sampledynamics.sandbox.operations.dynamics.com`. | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . Kiválaszthatja Azure Integration Runtime vagy a saját üzemeltetésű Integration Runtime (ha az adattár egy magánhálózaton található). Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime használja. |Nem |

**Példa**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Ez a szakasz a Dynamics AX-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md). 

Ha adatokat szeretne másolni a Dynamics AX-ból, állítsa az adatkészlet **Type (típus** ) tulajdonságát **DynamicsAXResource**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet **Type** tulajdonságát **DynamicsAXResource**értékre kell állítani. | Igen |
| elérési útja | A Dynamics AX OData entitás elérési útja. | Igen |

**Példa**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz a Dynamics AX-forrás által támogatott tulajdonságok listáját tartalmazza.

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX forrásként

Az adatok Dynamics AX-ből való másolásához állítsa a **forrás** típusát a másolás tevékenység **DynamicsAXSource**értékére. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **DynamicsAXSource**értékre kell állítani. | Igen |
| lekérdezés | OData-lekérdezési beállítások az adatszűréshez. Példa: `"?$select=Name,Description&$top=5"`.<br/><br/>**Megjegyzés**: az összekötő a következő összevont URL-címről másolja az adatait: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. További információ: [OData URL-összetevők](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések

A másolási tevékenység által támogatott adattárak listáját a Azure Data Factoryban található forrásként és nyelőként tekintse meg a [támogatott adattárak és-formátumok](copy-activity-overview.md##supported-data-stores-and-formats)című témakörben.
