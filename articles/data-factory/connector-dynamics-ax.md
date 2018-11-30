---
title: Adatok másolása a Dynamics AX használatával az Azure Data Factory (előzetes verzió) |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Dynamics AX támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: jingwang
ms.openlocfilehash: 94358ffde697b8122e65aefcbe1dd97385ca5b3a
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621808"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory-preview"></a>Adatok másolása a Dynamics AX használatával az Azure Data Factory (előzetes verzió)

Ez a cikk az Azure Data Factory másolási tevékenység használatával adatokat másol a forrásadattárból Dynamics AX módját ismerteti. A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat Dynamics AX bármely támogatott fogadó adattárba. Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Pontosabban, a Dynamics AX-összekötő támogatja-e a Dynamics AX használatával adatmásolásra **OData protokoll** a **szolgáltatásnév hitelesítési**.

>[!TIP]
>Az összekötő használatához adatokat másolni **Dynamics 365 Finance and Operations**. Tekintse meg a Dynamics 365 [OData-támogatás](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) és [hitelesítési módszer](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozza meg a Data Factory-entitások, konkrétan a következő Dynamics AX-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="prerequisites"></a>Előfeltételek

Egyszerű szolgáltatásnév hitelesítése használatához kövesse az alábbi lépéseket:

1. Regisztráljon az Azure Active Directoryban (Azure AD) application entitás a következő [regisztrálja az alkalmazást az Azure AD-bérlő](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Ugrás a Dynamics AX, és adja meg a szolgáltatás egyszerű megfelelő hozzáférése a Dynamics AX.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak, a Dynamics AX-beli társított szolgáltatást:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **Dynamics AX**. |Igen |
| url | A Dynamics AX (vagy Dynamics 365 Finance and Operations) példány OData-végpont. |Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| bérlő | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Igen |
| aadResourceId | Adja meg az engedélyt a kért erőforrás. | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) kapcsolódni az adattárhoz. Kiválaszthatja az Azure integrációs modul vagy a saját üzemeltetésű integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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
            "aadResourceId": "<Dynamics AX resource url>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Ez a szakasz felsorolja, amely támogatja a Dynamics AX-adatkészlet tulajdonságai.

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). 

Adatok másolása a Dynamics AX, állítsa be a **típusa** tulajdonságot az adatkészlet, **DynamicsAXResource**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a tulajdonságot az adatkészlet **DynamicsAXResource**. | Igen |
| elérési út | A Dynamics AX OData entitás elérési útja. | Igen |

**Példa**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typePoperties": {     
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz felsorolja, amely támogatja a Dynamics AX-forrás tulajdonságai.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>A Dynamics AX forrásként

Adatok másolása a Dynamics AX, állítsa be a **forrás** írja be a másolási tevékenység **DynamicsAXSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrás tulajdonság **DynamicsAXSource**. | Igen |
| lekérdezés | Adatok szűrése az OData-lekérdezés beállításai. Példa: `"?$select=Name,Description&$top=5"`.<br/><br/>**Megjegyzés:**: az összekötő adatokat másol a kombinált URL-cím: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. További információkért lásd: [OData URL-címe összetevők](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

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

## <a name="next-steps"></a>További lépések

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md##supported-data-stores-and-formats).