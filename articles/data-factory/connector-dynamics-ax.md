---
title: Adatok másolása a Dynamics AX-ből Azure Data Factory használatával (előzetes verzió) | Microsoft Docs
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
ms.openlocfilehash: 2ecb19f86c665e89e9326c160596a8f14a169dba
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009404"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory-preview"></a>Adatok másolása a Dynamics AX-ből Azure Data Factory használatával (előzetes verzió)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Dynamics AX-forrásból való másolásához. A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Dynamics AX-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás-mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a Dynamics AX-ből bármely támogatott fogadó adattárba másolhatók. Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Pontosabban, ez a Dynamics AX-összekötő támogatja a Dynamics AX-adatok másolását a **OData protokoll** használatával az **egyszerű szolgáltatás hitelesítésével**.

>[!TIP]
>Ezzel az összekötővel az adatok a **Dynamics 365-pénzügyi és-műveletekből**is másolhatók. Tekintse át a Dynamics 365 [OData-támogatás](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) és- [hitelesítés módszerét](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a Dynamics AX-összekötőhöz tartozó Data Factory entitások definiálásához használható tulajdonságokat.

## <a name="prerequisites"></a>Előfeltételek

Egyszerű szolgáltatásnév hitelesítése használatához kövesse az alábbi lépéseket:

1. Regisztráljon az Azure Active Directoryban (Azure AD) application entitás a következő [regisztrálja az alkalmazást az Azure AD-bérlő](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Nyissa meg a Dynamics AX-t, és adjon neki megfelelő engedélyt a szolgáltatásnak a Dynamics AX eléréséhez.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Dynamics AX társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **Type** tulajdonságot **DynamicsAX**értékre kell beállítani. |Igen |
| url | A Dynamics AX (vagy a Dynamics 365 pénzügyi és műveleti) példányának OData-végpontja. |Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| tenant | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Igen |
| aadResourceId | Határozza meg az HRE-erőforrást, amelyhez engedélyt kér. Ha például a Dynamics URL-cím `https://sampledynamics.sandbox.operations.dynamics.com/data/`, a megfelelő HRE-erőforrás általában. `https://sampledynamics.sandbox.operations.dynamics.com` | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) kapcsolódni az adattárhoz. Kiválaszthatja Azure Integration Runtime vagy a saját üzemeltetésű Integration Runtime (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). 

Ha adatokat szeretne másolni a Dynamics AX-ból, állítsa az adatkészlet **Type (típus** ) tulajdonságát **DynamicsAXResource**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet **Type** tulajdonságát **DynamicsAXResource**értékre kell állítani. | Igen |
| path | A Dynamics AX OData entitás elérési útja. | Igen |

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

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX forrásként

Az adatok Dynamics AX-ből való másolásához állítsa a **forrás** típusát a másolás tevékenység **DynamicsAXSource**értékére. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **DynamicsAXSource**értékre kell állítani. | Igen |
| query | OData-lekérdezési beállítások az adatszűréshez. Példa: `"?$select=Name,Description&$top=5"`.<br/><br/>**Megjegyzés**: Az összekötő a következő összevont URL-címről `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`másolja az adatait:. További információ: [OData URL-összetevők](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

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

## <a name="next-steps"></a>További lépések

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md##supported-data-stores-and-formats).
