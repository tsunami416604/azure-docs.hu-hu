---
title: Adatok másolása a Dynamics AX alkalmazásból
description: Megtudhatja, hogyan másolhatja az adatokat a Dynamics AX-ből a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
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
ms.date: 08/01/2019
ms.openlocfilehash: 4dd82eea0a80ef81a0f972d1964a62e6c17a80c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417361"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Adatok másolása a Dynamics AX-ből az Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása dynamics AX forrásból. A cikk az [Azure Data Factory másolási tevékenységére](copy-activity-overview.md)épül, amely általános áttekintést nyújt a másolási tevékenységről.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Dynamics AX-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

A Dynamics AX-ből adatokat másolhat bármely támogatott fogadó adattárba. A Másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak és -formátumok című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)

Pontosabban ez a Dynamics AX-összekötő támogatja az adatok másolását a Dynamics AX-ből az Egyszerű **szolgáltatás hitelesítésével**rendelkező **OData protokoll** használatával.

>[!TIP]
>Ezzel az összekötővel adatokat is másolhat a **Dynamics 365 Finance and Operations programból.** Lásd a Dynamics 365 [OData-támogatási](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) és [hitelesítési módszerét.](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication)

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Dynamics AX-összekötőre jellemző Data Factory-entitások definiálásához használható tulajdonságok részleteit ismertetik.

## <a name="prerequisites"></a>Előfeltételek

Az egyszerű szolgáltatáshitelesítés használatához hajtsa végre az alábbi lépéseket:

1. Regisztráljon egy alkalmazásentitást az Azure Active Directoryban (Azure AD) az [alkalmazás regisztrálása egy Azure AD-bérlővel](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)című könyvbe való regisztrálása című dal. Jegyezze fel a következő értékeket, amelyek segítségével definiálja a csatolt szolgáltatást:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Nyissa meg a Dynamics AX alkalmazást, és adjon megfelelő engedélyt ennek a szolgáltatásnak a Dynamics AX eléréséhez.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A Dynamics AX csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **típustulajdonságot** **DynamicsAX (DynamicsAX**. |Igen |
| url | A Dynamics AX (vagy Dynamics 365 Finance and Operations) oData-végpontja. |Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfélazonosítóját. | Igen |
| servicePrincipalKey | Adja meg az alkalmazás kulcsát. Jelölje meg ezt a mezőt **SecureStringként** a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| Bérlő | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér egérrel az Azure Portal jobb felső sarkában való lebegtetésével. | Igen |
| aadResourceId | Adja meg az engedélyezésre kért AAD-erőforrást. Ha például a Dynamics `https://sampledynamics.sandbox.operations.dynamics.com/data/`URL-címe a megfelelő `https://sampledynamics.sandbox.operations.dynamics.com`AAD-erőforrás. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használt [integrációs futásidő.](concepts-integration-runtime.md) Választhat az Azure Integration Runtime vagy a self-hosted Integration Runtime (ha az adattár található egy magánhálózat). Ha nincs megadva, a rendszer az alapértelmezett Azure-integrációs futásidőt használja. |Nem |

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

Ez a szakasz a Dynamics AX adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az Adatkészletek és csatolt szolgáltatások című [témakörben található.](concepts-datasets-linked-services.md) 

Ha adatokat szeretne másolni a Dynamics AX alkalmazásból, állítsa az adatkészlet **típustulajdonságát** **DynamicsAXResource (DynamicsAXResource) tulajdonságra.** A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet **típustulajdonságát** **DynamicsAXResource (DynamicsAXResource**) tulajdonságra kell állítani. | Igen |
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

## <a name="copy-activity-properties"></a>Tevékenység tulajdonságainak másolása

Ez a szakasz a Dynamics AX-forrás által támogatott tulajdonságok listáját tartalmazza.

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok című témakörben található.](concepts-pipelines-activities.md) 

### <a name="dynamics-ax-as-source"></a>Dynamics AX mint forrás

Ha adatokat szeretne másolni a Dynamics AX alkalmazásból, állítsa be a **forrástípust** a **DynamicsAXSource**másolása mezőben. A következő tulajdonságokat támogatja a Másolási tevékenység **forrásszakasz:**

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Másolási tevékenység **forrástípus** tulajdonságának **DynamicsAXSource**beállításra kell legyen állítva. | Igen |
| lekérdezés | OData lekérdezési beállítások az adatok szűréséhez. Példa: `"?$select=Name,Description&$top=5"`.<br/><br/>**Megjegyzés:** Az összekötő az egyesített URL-címből másolja az adatokat: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. További információ: [OData URL-összetevők](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

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


## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések

Az Azure Data Factory ban a Másolási tevékenység által adatforrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak és -formátumok című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)
