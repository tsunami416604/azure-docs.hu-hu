---
title: Egy REST-forrásból származó adatok másolása az Azure Data Factory használatával |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat egy felhőbeli vagy helyszíni REST forrás támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: jingwang
ms.openlocfilehash: ee47f464c59bd9deed98671f19cfcc6d2c3c1b39
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762480"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával REST-végponton

Ez a cikk ismerteti az Azure Data Factory másolási tevékenység adatokat másol egy REST-végpont használata. A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

A különbség a REST-összekötő között [HTTP-összekötő](connector-http.md) és a [webes táblázat összekötő](connector-web-table.md) vannak:

- **REST-összekötő** kifejezetten az adatok másolása a RESTful API-k; támogatása 
- **HTTP-összekötő** általános adatokat lekérni bármilyen HTTP-végpontot, például fájl letöltéséhez. Előtt elérhetővé válik a REST-összekötőt, akkor fordulhat elő, HTTP-összekötő használatával adatait átmásolhatja RESTful API-t, amely akkor támogatott, de kisebb működési összehasonlítva és REST közötti összekötő.
- **Webes tábla összekötő** kivonatok tábla egy HTML-weblap tartalmat.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok bármely támogatott fogadó adattárba egy REST-forrásból másolhatja. Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Pontosabban az általános REST-összekötő támogatja:

- Adatok lekérése a REST-végpont használatával a **első** vagy **POST** módszereket.
- Adatok beolvasása a következő hitelesítések egyikének használatával: **Névtelen**, **alapszintű**, **AAD-szolgáltatásnév**, és **felügyelt identitások az Azure-erőforrások**.
- **[Tördelés](#pagination-support)**  a REST API-k.
- A REST-JSON-válasz másolása [,-van](#export-json-response-as-is) vagy elemezni a használatával [séma-hozzárendelés](copy-activity-schema-and-type-mapping.md#schema-mapping). A csak válasz tartalma **JSON** használata támogatott.

> [!TIP]
> Az adatok beolvasásáért kérelem teszteléséhez a Data Factoryban a REST-összekötő konfigurálása előtt ismerje meg az API-specifikációnak, fejléc és a szervezet követelményeinek. Eszközök, mint például a Postman vagy egy webböngésző segítségével ellenőrzése.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével konkrétan a REST-összekötő a Data Factory-entitások definiálása-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A társított REST-szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **RestService**. | Igen |
| url | Az alap URL-címét a REST-szolgáltatást. | Igen |
| enableServerCertificateValidation | E érvényesíteni a kiszolgálói oldalon SSL-tanúsítvány a végponthoz való csatlakozáskor. | Nem<br /> (az alapértelmezett érték **igaz**) |
| authenticationType | A REST-szolgáltatáshoz való kapcsolódáshoz használt hitelesítés típusa. Engedélyezett értékek a következők **névtelen**, **alapszintű**, **AadServicePrincipal** és **ManagedServiceIdentity**. Tekintse meg a további tulajdonságokat és példákat szakaszokban megfelelő jelölik. | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) kapcsolódni az adattárhoz. Használhatja az Azure integrációs modul és a egy saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha nincs megadva, ezt a tulajdonságot használja az alapértelmezett Azure integrációs modul. |Nem |

### <a name="use-basic-authentication"></a>Alapszintű hitelesítés használata

Állítsa be a **authenticationType** tulajdonságot **alapszintű**. Az előző szakaszban leírt általános tulajdonságaihoz adja meg a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| Felhasználónév | A felhasználónév, a REST-végpont elérésére használhat. | Igen |
| jelszó | A felhasználó jelszava (a **felhasználónév** érték). Jelölje meg a mező egy **SecureString** típus tárolja biztonságos helyen a Data Factoryban. Emellett [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |

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

### <a name="use-aad-service-principal-authentication"></a>Használja az AAD egyszerű szolgáltatásnév hitelesítése

Állítsa be a **authenticationType** tulajdonságot **AadServicePrincipal**. Az előző szakaszban leírt általános tulajdonságaihoz adja meg a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| servicePrincipalId | Adja meg az Azure Active Directory-alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az Azure Active Directory-alkalmazás kulcsa. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| bérlő | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Igen |
| aadResourceId | Adja meg az AAD erőforrás kért használ a hitelesítéshez, pl. `https://management.core.windows.net`.| Igen |

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

### <a name="managed-identity"></a> Felügyelt identitások Azure-erőforrások hitelesítéshez használandó

Állítsa be a **authenticationType** tulajdonságot **ManagedServiceIdentity**. Az előző szakaszban leírt általános tulajdonságaihoz adja meg a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| aadResourceId | Adja meg az AAD erőforrás kért használ a hitelesítéshez, pl. `https://management.core.windows.net`.| Igen |

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

Ez a szakasz felsorolja, amely támogatja a REST-adatkészlet tulajdonságai. 

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). 

Adatok másolása REST, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a tulajdonságot az adatkészlet **RestResource**. | Igen |
| relativeUrl | Az erőforrás, amely tartalmazza az adatok relatív URL-CÍMÉT. Ez a tulajdonság nincs megadva, csak az URL-cím a társított szolgáltatás definíciójában megadott használ. | Nem |
| requestMethod | A HTTP-metódust. Engedélyezett értékek a következők **első** (alapértelmezett), és **Post**. | Nem |
| additionalHeaders | További HTTP-kérelemfejlécek. | Nem |
| RequestBody | A HTTP-kérelem törzsét. | Nem |
| paginationRules | A következő lap kérelmek compose tördelés szabályok. Tekintse meg [tördelés támogatása](#pagination-support) szakaszban talál. | Nem |

**1. példa: A Get metódussal a tördelés**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": {
                "x-user-defined": "helloworld"
            },
            "paginationRules": {
                "AbsoluteUrl": "$.paging.next"
            }
        }
    }
}
```

**2. példa: A Post metódussal**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST REST request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz felsorolja, amely támogatja a REST-forrás tulajdonságai.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST-forrásként

A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrása tulajdonságát **RestSource**. | Igen |
| httpRequestTimeout | Az időtúllépés (a **TimeSpan** érték) válaszol a HTTP-kérelem. Az értéke az időkorlátot kapott választ, nem választ adatokat olvasni az időkorlátot. Az alapértelmezett érték **00:01:40**.  | Nem |
| requestInterval | A következő oldalhoz tartozó kérelem elküldése előtt várakozási időt. Az alapértelmezett érték **00:00:01** |  Nem |

**Példa**

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
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Tördelés támogatása

Általában a REST API-t egyetlen kérelem egy észszerű számú; a válasz hasznos adat mérete korlátozza ideje nagy mennyiségű adatot, vissza az eredményt felosztja a több oldalra, és a szükséges hívóit, az eredmény a következő oldal egymást követő kéréseket küldeni. Általában a kérelem egy oldal a dinamikus és az előző lapon, a válasz által visszaadott információ áll.

Az általános REST-összekötő a következő tördelés mintát támogat: 

* Következő kérelmet abszolút vagy relatív URL-címe = aktuális válasz törzsében tulajdonság értéke
* Következő kérelmet abszolút vagy relatív URL-címe = fejléc értéke az aktuális válaszfejlécek
* Következő kérés lekérdezési paraméter = aktuális válasz törzsében tulajdonság értéke
* Következő kérés lekérdezési paraméter = fejléc értéke az aktuális válaszfejlécek
* Tovább a kérelem fejlécében = aktuális válasz törzsében tulajdonság értéke
* Tovább a kérelem fejlécében = fejléc értéke az aktuális válaszfejlécek

**Tördelés szabályok** egy szótárban az adatkészlet egy vagy több kis-és nagybetűket kulcs-érték párokat tartalmazó vannak meghatározva. A konfiguráció létrehozni a kérést, a második oldal kezdve használható. Az összekötő le fog állni, léptetés, amikor HTTP-állapotkód: 204 (nincs tartalom) kap, vagy a JSONPath kifejezés a "paginationRules" bármelyike null értéket ad vissza.

**Támogatott kulcsok** tördelés szabályok:

| Kulcs | Leírás |
|:--- |:--- |
| AbsoluteUrl | Azt jelzi, hogy az URL-cím a következő kérés kiadása. Ez lehet **vagy abszolút vagy relatív URL-**. |
| QueryParameters. *request_query_parameter* vagy QueryParameters [request_query_parameter] | "request_query_parameter" a felhasználó által definiált hivatkozik egy lekérdezési paraméter neve a következő HTTP-kérelem URL-címében. |
| Fejlécek. *request_header* vagy fejlécek [request_header] | "request_header" a felhasználó által definiált egy fejléc neve a következő HTTP-kérelem hivatkozik. |

**Támogatott értékek** tördelés szabályok:

| Érték | Leírás |
|:--- |:--- |
| Fejlécek. *response_header* vagy fejlécek [response_header] | "response_header" a felhasználó által definiált hivatkozik egy fejléc neve az a jelenlegi HTTP-válasz, a következő kérés kiadása, amely az érték használható. |
| A "$" (a legfelső szintű a választörzs képviselő) kezdődő JSONPath kifejezés | A választörzs tartalmazhat csak egy JSON-objektum. A JSONPath kifejezés által visszaadott egyetlen egyszerű érték, a következő kérelmet használandó. |

**Példa**

A Facebook Graph API adja vissza választ az alábbi struktúrával, amely megkülönbözteti a kis tovább lap URL-címe jelenik meg az ***paging.next***:

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

A megfelelő REST adatkészlet configuration a különösen `paginationRules` következő:

```json
{
    "name": "MyFacebookAlbums",
    "properties": {
            "type": "RestResource",
            "typeProperties": {
                "relativeUrl": "albums",
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                }
            },
            "linkedServiceName": {
                "referenceName": "MyRestService",
                "type": "LinkedServiceReference"
            }
    }
}
```

## <a name="export-json-response-as-is"></a>JSON-válasz való exportálás-van

A REST-összekötő segítségével REST API JSON-válasz való exportálása –, hogy különböző fájlalapú tárolók. Az ilyen sémafüggetlen másolási eléréséhez hagyja ki a "struktúra" (más néven *séma*) szakaszban az adatkészlet és a séma hozzárendelése a másolási tevékenység.

## <a name="schema-mapping"></a>Séma-hozzárendelés

Adatok másolása REST-végpont táblázatos fogadóba, tekintse meg [séma-hozzárendelés](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>További lépések

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).
