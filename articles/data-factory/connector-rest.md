---
title: Adatok másolása REST-forrásból Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatok egy felhőből vagy helyszíni REST-forrásból a fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 1178c18b29c5e38d33e51ff0da5db683990daed3
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546957"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Adatok másolása REST-végpontból Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok REST-végpontból történő másolásához. A cikk a [másolási tevékenységre épül Azure Data Factoryban](copy-activity-overview.md), amely a másolási tevékenység általános áttekintését mutatja be.

A REST-összekötő, a [http-összekötő](connector-http.md) és a [webtábla-összekötő](connector-web-table.md) közötti különbség a következő:

- A **Rest-összekötő** kifejezetten támogatja a REST API-k adatainak másolását; 
- A **http-összekötő** általános az adatok bármely http-végpontból való lekéréséhez, például a fájl letöltéséhez. A REST-összekötő elérhetővé válása előtt előfordulhat, hogy a HTTP-összekötőt használja az adatok REST API-ból való másolásához, amely támogatott, de kevésbé működik a REST-összekötőhöz képest.
- A **web Table Connector** kibontja a táblázat tartalmát egy HTML-weboldalról.

## <a name="supported-capabilities"></a>Támogatott képességek

A REST-forrásból származó adatok bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként és a fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Ez az általános REST-összekötő a következőket támogatja:

- Adatok beolvasása egy REST-végpontból a **Get** vagy a **post** metódusok használatával.
- Adatok beolvasása a következő hitelesítések egyikével: **Névtelen**, **alapszintű**, **HRE egyszerű szolgáltatásnév**és **felügyelt identitások az Azure-erőforrásokhoz**.
- **[Tördelés](#pagination-support)** a REST API-kon.
- Másolja a REST JSON-választ a [-ként](#export-json-response-as-is) , vagy elemezze azt a [séma-hozzárendelés](copy-activity-schema-and-type-mapping.md#schema-mapping)használatával. Csak a **JSON** -beli válasz-adattartalom támogatott.

> [!TIP]
> Az adatok lekérésére vonatkozó kérelem teszteléséhez, mielőtt a REST-összekötőt a Data Factoryban konfigurálja, ismerkedjen meg a fejléc és a törzs követelményeivel kapcsolatos API-specifikációval. Az érvényesítéshez használhatja a Poster vagy a webböngésző eszközeit.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek segítségével meghatározható Data Factory a REST-összekötőre jellemző entitások.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A REST társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **Type** tulajdonságot **RestService**értékre kell beállítani. | Igen |
| url | A REST-szolgáltatás alap URL-címe. | Igen |
| enableServerCertificateValidation | Ellenőrzi, hogy a kiszolgálóoldali SSL-tanúsítvány érvényesíthető-e a végponthoz való csatlakozáskor. | Nem<br /> (az alapértelmezett érték **igaz**) |
| authenticationType | A REST-szolgáltatáshoz való kapcsolódáshoz használt hitelesítés típusa. Az engedélyezett értékek: **Névtelen**, **alapszintű**, **AadServicePrincipal** és **ManagedServiceIdentity**. Tekintse meg az alábbi, a további tulajdonságok és példák című szakaszt. | Igen |
| connectVia | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, ez a tulajdonság az alapértelmezett Azure Integration Runtime használja. |Nem |

### <a name="use-basic-authentication"></a>Egyszerű hitelesítés használata

Állítsa a **authenticationType** tulajdonságot **alapértékre.** Az előző szakaszban leírt általános tulajdonságok mellett a következő tulajdonságokat is meg kell adni:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| userName | A REST-végpont eléréséhez használandó Felhasználónév. | Igen |
| jelszó | A felhasználó jelszava (a **Felhasználónév** értéke). A mező megjelölése **SecureString** -típusként, hogy biztonságosan tárolja azt Data Factoryban. [Hivatkozhat a Azure Key Vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md)is. | Igen |

**Példa**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
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

### <a name="use-aad-service-principal-authentication"></a>HRE-szolgáltatás egyszerű hitelesítésének használata

Állítsa a **authenticationType** tulajdonságot **AadServicePrincipal**értékre. Az előző szakaszban leírt általános tulajdonságok mellett a következő tulajdonságokat is meg kell adni:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| servicePrincipalId | Azure Active Directory alkalmazás ügyfél-AZONOSÍTÓjának megadásához. | Igen |
| servicePrincipalKey | A Azure Active Directory alkalmazás kulcsának megadásához. Megjelöli ezt a mezőt **SecureString** , hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| tenant | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Igen |
| aadResourceId | Határozza meg az HRE-erőforrást, amelyet az engedélyezéshez kér, például `https://management.core.windows.net`.| Igen |

**Példa**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Felügyelt identitások használata az Azure-erőforrások hitelesítéséhez

Állítsa a **authenticationType** tulajdonságot **ManagedServiceIdentity**értékre. Az előző szakaszban leírt általános tulajdonságok mellett a következő tulajdonságokat is meg kell adni:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| aadResourceId | Határozza meg az HRE-erőforrást, amelyet az engedélyezéshez kér, például `https://management.core.windows.net`.| Igen |

**Példa**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Ez a szakasz a REST-adatkészlet által támogatott tulajdonságok listáját tartalmazza. 

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md). 

Az adatok REST-ből való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet **Type** tulajdonságát **RestResource**értékre kell állítani. | Igen |
| relativeUrl | Az adatforrást tartalmazó erőforrás relatív URL-címe. Ha nincs megadva ez a tulajdonság, a rendszer csak a társított szolgáltatás definíciójában megadott URL-címet használja. A HTTP-összekötő a következő összevont URL-címről másolja az adatait: `[URL specified in linked service]/[relative URL specified in dataset]`. | Nem |

Ha `requestMethod`, `additionalHeaders`, `requestBody` és `paginationRules` beállítást állított be az adatkészletben, akkor továbbra is támogatott, miközben az új modellt a tevékenység forrásaként fogja használni.

**Példa**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz a REST-forrás által támogatott tulajdonságok listáját tartalmazza.

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST forrásként

A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **RestSource**értékre kell állítani. | Igen |
| requestMethod | A HTTP-metódus. Az engedélyezett értékek: **Get** (alapértelmezett) és **post**. | Nem |
| additionalHeaders | További HTTP-kérelmek fejlécei. | Nem |
| requestBody | A HTTP-kérelem törzse. | Nem |
| paginationRules | A következő lapra irányuló kérelmek összeállításához szükséges tördelési szabályok. A részletekért tekintse meg a [tördelés támogatását](#pagination-support) ismertető szakaszt. | Nem |
| httpRequestTimeout | A válasz kéréséhez szükséges HTTP-kérelem időkorlátja (a **TimeSpan** érték). Ez az érték a válasz lekérésének időtúllépése, nem pedig a válaszüzenetek olvasásának időtúllépése. Az alapértelmezett érték a **00:01:40**.  | Nem |
| requestInterval | Az a várakozási idő, ameddig a következő lapra küldött kérelem elküldése előtt elküldve. Az alapértelmezett érték **00:00:01** |  Nem |

>[!NOTE]
>A REST-összekötő figyelmen kívül hagyja a `additionalHeaders`ban megadott "elfogadás" fejlécet. Mivel a REST-összekötő csak a JSON-beli választ támogatja, automatikusan létrehozza a `Accept: application/json`fejlécét.

**1. példa: a Get metódus használata tördeléssel**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**2. példa: a post metódus használata**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Tördelési támogatás

A REST API általában egy ésszerű számú kérelemre korlátozza a válasz adattartalom-méretét. míg nagy mennyiségű adattal tér vissza, az eredmény több oldalra van bontva, és a hívóknak egymást követő kéréseket kell küldeniük az eredmény következő oldalának beolvasásához. Az egyik oldalra vonatkozó kérelem általában dinamikus, és az előző oldal válaszában visszaadott információkból áll.

Ez az általános REST-összekötő a következő tördelési mintákat támogatja: 

* A következő kérelem abszolút vagy relatív URL-címe = tulajdonság értéke az aktuális válasz törzsében
* A következő kérelem abszolút vagy relatív URL-címe = fejléc értéke az aktuális válasz fejlécekben
* A következő kérelem lekérdezési paramétere = tulajdonság értéke az aktuális válasz törzsében
* Következő kérelem lekérdezési paramétere = fejléc értéke az aktuális válasz fejlécekben
* Következő kérelem fejléce = tulajdonság értéke az aktuális válasz törzsében
* Következő kérelem fejléce = fejléc értéke az aktuális válasz fejlécekben

A **tördelési szabályok** az adatkészletben olyan szótárként vannak definiálva, amely egy vagy több kis-és nagybetűket megkülönböztető kulcs-érték párokat tartalmaz. A rendszer a konfigurációt fogja használni a második lapról kezdődő kérelem létrehozásához. Az összekötő leáll, ha az 204-as HTTP-állapotkód (nincs tartalom), vagy a "paginationRules" JSONPath egyik kifejezése null értéket ad vissza.

A tördelési szabályokban **támogatott kulcsok** :

| Paraméter | Leírás |
|:--- |:--- |
| AbsoluteUrl | Azt az URL-címet adja meg, amely a következő kérést adja ki. **Abszolút URL-cím vagy relatív URL-cím**is lehet. |
| QueryParameters. *request_query_parameter* VAGY QueryParameters [' request_query_parameter '] | a "request_query_parameter" felhasználó által definiált, amely egy lekérdezési paraméter nevére hivatkozik a következő HTTP-kérelem URL-címében. |
| Fejlécek. *request_header* VAGY fejlécek ["request_header"] | a "request_header" felhasználó által definiált, amely egy fejléc nevére hivatkozik a következő HTTP-kérelemben. |

A tördelési szabályokban **támogatott értékek** :

| Érték | Leírás |
|:--- |:--- |
| Fejlécek. *response_header* VAGY fejlécek ["response_header"] | a "response_header" felhasználó által definiált, amely a jelenlegi HTTP-válaszban egy fejléc nevére hivatkozik, amelynek értéke a következő kérelem kibocsátására szolgál majd. |
| A "$" kezdetű JSONPath kifejezés (amely a válasz törzsének gyökerét jelöli) | A válasz törzsének csak egy JSON-objektumot kell tartalmaznia. A JSONPath kifejezésnek egyetlen primitív értéket kell visszaadnia, amely a következő kérelem kibocsátására szolgál majd. |

**Példa**

A Facebook Graph API a következő struktúra válaszát adja vissza, amely esetben a következő oldal URL-címe szerepel a ***lapozásban. tovább***:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

A megfelelő REST másolási tevékenység forrásának konfigurációja, különösen a `paginationRules` a következő:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="export-json-response-as-is"></a>JSON-válasz exportálása a következőképpen:

Ezt a REST-összekötőt használhatja a REST API JSON-válasz exportálására a különböző file-alapú tárolók esetében. Az ilyen sémák és sémák közötti másoláshoz ugorjon a másolási tevékenységben a "Structure" (más néven *séma*) szakaszra.

## <a name="schema-mapping"></a>Séma-hozzárendelés

Ha adatokat szeretne másolni a REST-végpontról a táblázatos fogadóba, tekintse meg a [séma-hozzárendelést](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Következő lépések

A másolási tevékenység által támogatott adattárak listáját a Azure Data Factoryban található forrásként és nyelőként tekintse meg a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben.
