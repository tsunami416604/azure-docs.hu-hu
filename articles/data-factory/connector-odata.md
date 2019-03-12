---
title: Adatmásolás az OData-források az Azure Data Factory használatával |} A Microsoft Docs
description: 'Útmutató: adatok másolása az OData-források a támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: jingwang
ms.openlocfilehash: eeab0311519d7e3410ddb625c1801101ad63ad18
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550416"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>OData forrásból származó adatok másolása az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-odata-connector.md)
> * [Aktuális verzió](connector-odata.md)

Ez a cikk ismerteti az Azure Data Factory másolási tevékenység használatával adatokat másol egy OData-forráshoz. A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat egy OData-forráshoz bármely támogatott fogadó adattárba. Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Pontosabban az OData-összekötő támogatja:

- OData 3.0 és 4.0-s verzióját.
- Adatok másolása a következő hitelesítések egyikének használatával: **Névtelen**, **alapszintű**, **Windows**, **AAD-szolgáltatásnév**, és **felügyelt identitások az Azure-erőforrások**.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével konkrétan az OData-összekötő a Data Factory-entitások definiálása-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Egy társított OData-szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **OData**. |Igen |
| url | A gyökér URL-címe az OData-szolgáltatás. |Igen |
| authenticationType | Az OData-forráshoz való kapcsolódáshoz használt hitelesítés típusa. Engedélyezett értékek a következők **névtelen**, **alapszintű**, **Windows**, **AadServicePrincipal**, és **ManagedServiceIdentity** . A felhasználó alapján OAuth nem támogatott. | Igen |
| Felhasználónév | Adja meg **felhasználónév** alapszintű vagy Windows-hitelesítés használata esetén. | Nem |
| jelszó | Adja meg **jelszó** a felhasználó számára megadott fiók **felhasználónév**. Jelölje meg a mező egy **SecureString** típus tárolja biztonságos helyen a Data Factoryban. Emellett képes [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| servicePrincipalId | Adja meg az Azure Active Directory-alkalmazás ügyfél-azonosítót. | Nem |
| aadServicePrincipalCredentialType | Adja meg a szolgáltatás egyszerű hitelesítéshez használandó hitelesítő adatok típusa. Engedélyezett értékek a következők: `ServicePrincipalKey` vagy `ServicePrincipalCert`. | Nem |
| servicePrincipalKey | Adja meg az Azure Active Directory-alkalmazás kulcsa. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| servicePrincipalEmbeddedCert | Adja meg az alkalmazás az Azure Active Directoryban a base64-kódolású tanúsítványt. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| servicePrincipalEmbeddedCertPassword | Adja meg a tanúsítvány jelszavát, ha a tanúsítvány jelszóval védett. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md).  | Nem|
| bérlő | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Nem |
| aadResourceId | Adja meg az AAD erőforrás kért használ a hitelesítéshez.| Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) kapcsolódni az adattárhoz. Kiválaszthatja az Azure integrációs modul vagy a saját üzemeltetésű integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**1. példa: A névtelen hitelesítés használatával**

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

**3. példa: Windows-hitelesítés használatával**

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

**4. példa: Szolgáltatás egyszerű kulcsos hitelesítés használatával**

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

**5. példa: Szolgáltatás egyszerű tanúsítvány-hitelesítés használatával**

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

Ez a szakasz felsorolja, amely támogatja az OData-adatkészlet tulajdonságai.

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). 

Adatok másolása az OData, állítsa be a **típus** tulajdonság, az adatkészlet **ODataResource**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a tulajdonságot az adatkészlet **ODataResource**. | Igen |
| elérési út | Az OData-erőforrás elérési útja. | Igen |

**Példa**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
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

Ez a szakasz az OData-forráshoz támogató tulajdonságok listáját tartalmazza.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData forrásként

Adatok másolása az OData, állítsa be a **forrás** írja be a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrás tulajdonság **RelationalSource**. | Igen |
| lekérdezés | Adatok szűrése az OData-lekérdezés beállításai. Példa: `"?$select=Name,Description&$top=5"`.<br/><br/>**Megjegyzés**: Az OData-összekötő adatokat másol a kombinált URL-cím: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. További információkért lásd: [OData URL-címe összetevők](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

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
                "type": "RelationalSource",
                "query": "?$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-odata"></a>Adattípus OData-leképezés

OData-adatok másolása esetén a következő hozzárendeléseket használják OData-adattípusok és az Azure Data Factory közbenső adattípusok között. Hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó kapcsolatban lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md).

| OData-adattípus | Data Factory közbenső adattípus |
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
> Összetett adattípusok OData (például **objektum**) nem támogatottak.


## <a name="next-steps"></a>További lépések

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md##supported-data-stores-and-formats).