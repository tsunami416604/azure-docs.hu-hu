---
title: Adatok másolása OData-forrásokból Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat OData-forrásokból származó adatokból támogatott fogadó adattárakat egy Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: a31f0618f7e9dc8fdb0e9b2988d3d3c32fefcf64
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277671"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Adatok másolása OData-forrásból Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-odata-connector.md)
> * [Aktuális verzió](connector-odata.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factory egy OData-forrásból származó adatok másolásához. A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

A OData-forrásból származó adatok bármely támogatott fogadó adattárba másolhatók. Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Ez a OData-összekötő a következőket támogatja:

- A OData 3,0-es és 4,0-es verziója.
- Adatok másolása a következő hitelesítések egyikének használatával: **Névtelen**, **alapszintű**, **Windows**, **HRE egyszerű szolgáltatásnév**és **felügyelt identitások az Azure-erőforrásokhoz**.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyekkel meghatározhatja Data Factory OData-összekötőhöz tartozó entitásokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Egy OData társított szolgáltatás esetében a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **Type** tulajdonságot **OData**értékre kell beállítani. |Igen |
| url | A OData szolgáltatás gyökerének URL-címe. |Igen |
| authenticationType | A OData-forráshoz való kapcsolódáshoz használt hitelesítés típusa. Az engedélyezett értékek: **Névtelen**, **alapszintű**, **Windows**, **AadServicePrincipal**és **ManagedServiceIdentity**. A felhasználó-alapú OAuth nem támogatott. | Igen |
| userName | Adja meg **userName** alapszintű vagy Windows-hitelesítés használata esetén. | Nem |
| password | Adja meg **password** a felhasználó számára megadott fiók **felhasználónév**. Jelölje meg a mező egy **SecureString** típus tárolja biztonságos helyen a Data Factoryban. [Hivatkozhat a Azure Key Vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md)is. | Nem |
| servicePrincipalId | Azure Active Directory alkalmazás ügyfél-AZONOSÍTÓjának megadásához. | Nem |
| aadServicePrincipalCredentialType | Adja meg az egyszerű szolgáltatás hitelesítéséhez használandó hitelesítő adatokat. Az engedélyezett értékek a `ServicePrincipalKey` következők `ServicePrincipalCert`: vagy. | Nem |
| servicePrincipalKey | A Azure Active Directory alkalmazás kulcsának megadásához. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| servicePrincipalEmbeddedCert | Adja meg az alkalmazás Azure Active Directoryban regisztrált Base64-kódolású tanúsítványát. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| servicePrincipalEmbeddedCertPassword | Adja meg a tanúsítvány jelszavát, ha a tanúsítvány jelszavas védelemmel van ellátva. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md).  | Nem|
| tenant | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Nem |
| aadResourceId | Határozza meg az HRE-erőforrást, amelyhez engedélyt kér.| Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) kapcsolódni az adattárhoz. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**1. példa: Névtelen hitelesítés használata**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**2. példa: Alapszintű hitelesítés használata**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**3. példa: Windows-hitelesítés használata**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

**4. példa: Egyszerű szolgáltatásnév hitelesítésének használata**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**5. példa: A szolgáltatás egyszerű tanúsítvány-hitelesítésének használata**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Ez a szakasz a OData-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). 

Az adatok OData való másolásához állítsa az adatkészlet **Type (típus** ) tulajdonságát **ODataResource**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet **Type** tulajdonságát **ODataResource**értékre kell állítani. | Igen |
| path | A OData-erőforrás elérési útja. | Igen |

**Példa**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz a OData forrás által támogatott tulajdonságok listáját tartalmazza.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData forrásként

Az adatok OData történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **ODataSource**értékre kell állítani. | Igen |
| query | OData-lekérdezési beállítások az adatszűréshez. Példa: `"$select=Name,Description&$top=5"`.<br/><br/>**Megjegyzés**: Az OData-összekötő a következő összetett URL-címről `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`másolja az adatokból:. További információ: [OData URL-összetevők](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ha a beírt forrást használta `RelationalSource` , a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="data-type-mapping-for-odata"></a>Adattípusok leképezése OData

Amikor OData másol az adatokból, a következő leképezések használatosak a OData adattípusok és a Azure Data Factory köztes adattípusok között. Ha meg szeretné tudni, hogyan képezi le a másolási tevékenység a forrás sémáját és az adattípust a fogadóra, tekintse meg a [séma és adattípus-leképezések](copy-activity-schema-and-type-mapping.md)

| OData adattípusa | Data Factory közbenső adattípus |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Az összetett adattípusok (például az **objektum**) nem támogatottak a OData.


## <a name="next-steps"></a>További lépések

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md##supported-data-stores-and-formats).