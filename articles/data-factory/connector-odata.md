---
title: Adatok másolása OData-forrásokból az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat az OData-forrásokból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: c2fe6b6cc7b52dda9f2beffa444f1965723ea92a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416931"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Adatok másolása OData-forrásból az Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-odata-connector.md)
> * [Aktuális verzió](connector-odata.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása egy OData-forrásból. A cikk az [Azure Data Factory másolási tevékenységére](copy-activity-overview.md)épül, amely általános áttekintést nyújt a másolási tevékenységről.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az OData-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

Az OData-forrásból adatokat bármely támogatott fogadó adattárba másolhat. A Másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak és -formátumok című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)

Ez az OData-összekötő különösen a következőket támogatja:

- OData 3.0-s és 4.0-s verziója.
- Adatok másolása a következő hitelesítések egyikével: **Névtelen**, **Alapszintű**, **Windows**és **AAD szolgáltatásnév**.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az OData-összekötőre jellemző Data Factory-entitások definiálásához használható tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az OData-kapcsolt szolgáltatások a következő tulajdonságokat támogatják:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **típustulajdonságot** **OData (OData**) tulajdonságra kell állítani. |Igen |
| url | Az OData szolgáltatás gyökér URL-címe. |Igen |
| authenticationType | Az OData-forráshoz való csatlakozáshoz használt hitelesítés típusa. Az engedélyezett értékek: **Névtelen**, **Alap**, **Windows**és **AadServicePrincipal**. A felhasználó-alapú OAuth nem támogatott. | Igen |
| userName (Felhasználónév) | Adja meg **a felhasználónév,** ha alapszintű vagy Windows-hitelesítést használ. | Nem |
| jelszó | Adja meg a **felhasználónévhez**megadott felhasználói fiók **jelszavát.** Jelölje meg ezt **SecureString** a mezőt SecureString-típusként, hogy biztonságosan tárolhatja a Data Factory-ban. Az [Azure Key Vaultban tárolt titkos fájlokra](store-credentials-in-key-vault.md)is hivatkozhat. | Nem |
| servicePrincipalId | Adja meg az Azure Active Directory-alkalmazás ügyfélazonosítóját. | Nem |
| aadServicePrincipalCredentialType | Adja meg az egyszerű szolgáltatáshitelesítéshez használandó hitelesítő adatok típusát. Az engedélyezett `ServicePrincipalKey` értékek `ServicePrincipalCert`a következők: vagy . | Nem |
| servicePrincipalKey | Adja meg az Azure Active Directory-alkalmazás kulcsát. Jelölje meg ezt a mezőt **SecureStringként** a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Nem |
| szolgáltatásPrincipalEmbeddedCert | Adja meg az Azure Active Directoryban regisztrált alkalmazás base64 kódolású tanúsítványát. Jelölje meg ezt a mezőt **SecureStringként** a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Nem |
| servicePrincipalEmbeddedCertPassword | Adja meg a tanúsítvány jelszavát, ha a tanúsítvány jelszóval van biztosítva. Jelölje meg ezt a mezőt **SecureStringként** a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md)  | Nem|
| Bérlő | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér egérrel az Azure Portal jobb felső sarkában való lebegtetésével. | Nem |
| aadResourceId | Adja meg az engedélyezésre kért AAD-erőforrást.| Nem |
| connectVia | Az adattárhoz való csatlakozáshoz használt [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, a rendszer az alapértelmezett Azure-integrációs futásidőt használja. |Nem |

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

**2. példa: Alapfokú hitelesítés használata**

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

**4. példa: Egyszerű szolgáltatáskulcs-hitelesítés használata**

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

**5. példa: Egyszerű szolgáltatástanúsítvány-hitelesítés használata**

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

Ez a szakasz az OData adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az Adatkészletek és csatolt szolgáltatások című [témakörben található.](concepts-datasets-linked-services.md) 

Ha adatokat szeretne másolni az OData-ból, állítsa az adatkészlet **típustulajdonságát** **ODataResource (ODataResource)** tulajdonságra. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet **típustulajdonságát** **ODataResource (ODataResource**) tulajdonságra kell állítani. | Igen |
| path | Az OData erőforrás elérési útja. | Igen |

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

## <a name="copy-activity-properties"></a>Tevékenység tulajdonságainak másolása

Ez a szakasz az OData-forrás által támogatott tulajdonságok listáját tartalmazza.

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok című témakörben található.](concepts-pipelines-activities.md) 

### <a name="odata-as-source"></a>OData forrásként

Az Adatok OData-ból történő másolásához a következő tulajdonságokat támogatja a Tevékenység másolása **forrásszakasz:**

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Másolási tevékenység **forrástípus** tulajdonságát **ODataSource (ODataSource)** típusú tulajdonságra kell állítani. | Igen |
| lekérdezés | OData lekérdezési beállítások az adatok szűréséhez. Példa: `"$select=Name,Description&$top=5"`.<br/><br/>**Megjegyzés:** Az OData-összekötő az egyesített `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`URL-címről másolja az adatokat: . További információ: [OData URL-összetevők](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nem |

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

Ha gépelt forrást használt, `RelationalSource` akkor továbbra is támogatott, amíg az újat a jövőben is használhatja.

## <a name="data-type-mapping-for-odata"></a>Adattípus-hozzárendelés az OData-hoz

Amikor adatokat másol az OData-ból, a következő leképezések az OData-adattípusok és az Azure Data Factory köztes adattípusok között használatosak. Ha meg szeretné tudni, hogy a Tevékenység másolása hogyan rendeli le a forrássémát és az adattípust a fogadóhoz, olvassa el a [Séma- és adattípus-hozzárendelések című témakört.](copy-activity-schema-and-type-mapping.md)

| OData-adattípus | Adatgyár köztes adattípusa |
|:--- |:--- |
| Edm.Bináris | Bájt[] |
| Edm.Boolean | Logikai |
| Edm.Bájt | Bájt[] |
| Edm.DateTime | DateTime |
| Edm.Decim | Decimal |
| Edm.Double | Double |
| Edm.Egyetlen | Egyirányú |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Sztring |
| Edm.Idő | időtartam |
| Edm.DateTimeOffset | DateTimeOffset (Dátumidő-eltolás) |

> [!NOTE]
> Az OData-összetett adattípusok (például **az objektum)** nem támogatottak.


## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések

Az Azure Data Factory ban a Másolási tevékenység által adatforrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak és -formátumok című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)