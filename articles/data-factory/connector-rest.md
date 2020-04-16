---
title: Adatok másolása REST-forrásból az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat egy felhőből vagy a helyszíni REST-forrásból a támogatott fogadó adattárakegy Azure Data Factory-folyamat másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 2657f1998e3ca908bc52166154ac3353e1e5a66b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415043"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Adatok másolása REST-végpontról az Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása egy REST-végpontról. A cikk az [Azure Data Factory másolási tevékenységére](copy-activity-overview.md)épül, amely általános áttekintést nyújt a másolási tevékenységről.

A REST-összekötő, [a HTTP-összekötő](connector-http.md) és a [webtábla-összekötő](connector-web-table.md) közötti különbség a következő:

- **A REST-összekötő** kifejezetten támogatja a RESTful API-kból származó adatok másolását; 
- **A HTTP-összekötő** általános fontosságú, ha bármely HTTP-végpontról lekéri az adatokat, például fájl letöltéséhez. Mielőtt ez a REST-összekötő elérhetővé válik, előfordulhat, hogy http-összekötő használatával másolja az adatokat a RESTful API-ból, amely támogatott, de kevésbé funkcionális a REST-összekötővel összehasonlítva.
- **A webtábla-összekötő** html weblapról nyeri ki a táblázat tartalmát.

## <a name="supported-capabilities"></a>Támogatott képességek

A REST-forrásból adatokat bármely támogatott fogadó adattárba másolhat. A Másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak és -formátumok című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)

Ez az általános REST-összekötő különösen a következőket támogatja:

- Adatok beolvasása REST-végpontról a **GET** vagy a **POST** metódusokkal.
- Adatok beolvasása a következő hitelesítések egyikével: **Névtelen**, **Alapszintű**, **AAD egyszerű szolgáltatásés** **felügyelt identitások az Azure-erőforrásokhoz.**
- **[Tördelés](#pagination-support)** a REST API-kban.
- A REST [JSON-válasz](#export-json-response-as-is) másolása a [sémaleképezés](copy-activity-schema-and-type-mapping.md#schema-mapping)használatával. Csak a **json-i** választartalom támogatott.

> [!TIP]
> Ha a DATA-összekötő konfigurálása előtt tesztelje az adatlekérési kérelmet, ismerje meg a fejléc- és törzskövetelmények API-specifikációját. Használhatja eszközök, mint a Postman vagy a webböngésző érvényesítéséhez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a REST-összekötőre jellemző Data Factory-entitások definiálásához használható tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A REST-csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **típustulajdonságot** **RestService**tulajdonságra kell állítani. | Igen |
| url | A REST-szolgáltatás alap URL-címe. | Igen |
| enableServerCertificateValidation | A kiszolgálóoldali TLS/SSL-tanúsítvány ellenőrzése a végponthoz való csatlakozáskor. | Nem<br /> (az alapértelmezett **igaz)** |
| authenticationType | A REST-szolgáltatáshoz való csatlakozáshoz használt hitelesítés típusa. Az engedélyezett értékek: **Névtelen**, **Alap**, **AadServicePrincipal** és **ManagedServiceIdentity**. További tulajdonságokról és példákról az alábbi megfelelő szakaszokban olvashat. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használt [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, ez a tulajdonság az alapértelmezett Azure-integrációs futásidőt használja. |Nem |

### <a name="use-basic-authentication"></a>Egyszerű hitelesítés használata

Állítsa a **authenticationType** tulajdonságot **Alap szintűre.** Az előző szakaszban ismertetett általános tulajdonságokon kívül adja meg a következő tulajdonságokat:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| userName (Felhasználónév) | A REST-végpont eléréséhez használandó felhasználónév. | Igen |
| jelszó | A felhasználó jelszava (a **felhasználónév** értéke). Jelölje meg ezt **SecureString** a mezőt SecureString-típusként, hogy biztonságosan tárolhatja a Data Factory-ban. Az [Azure Key Vaultban tárolt titkos fájlokra](store-credentials-in-key-vault.md)is hivatkozhat. | Igen |

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

### <a name="use-aad-service-principal-authentication"></a>AAD szolgáltatásegyszerű hitelesítés ének használata

Állítsa a **authenticationType** tulajdonságot **AadServicePrincipal**tulajdonságra. Az előző szakaszban ismertetett általános tulajdonságokon kívül adja meg a következő tulajdonságokat:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| servicePrincipalId | Adja meg az Azure Active Directory-alkalmazás ügyfélazonosítóját. | Igen |
| servicePrincipalKey | Adja meg az Azure Active Directory-alkalmazás kulcsát. Jelölje meg ezt a mezőt **SecureStringként** a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| Bérlő | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér egérrel az Azure Portal jobb felső sarkában való lebegtetésével. | Igen |
| aadResourceId | Adja meg az engedélyezésre kért AAD-erőforrást, pl. . `https://management.core.windows.net`| Igen |

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

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Felügyelt identitások használata az Azure-erőforrások hitelesítéséhez

Állítsa a **authenticationType** tulajdonságot **ManagedServiceIdentity**tulajdonságra. Az előző szakaszban ismertetett általános tulajdonságokon kívül adja meg a következő tulajdonságokat:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| aadResourceId | Adja meg az engedélyezésre kért AAD-erőforrást, pl. . `https://management.core.windows.net`| Igen |

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az Adatkészletek és csatolt szolgáltatások című [témakörben található.](concepts-datasets-linked-services.md) 

Adatok REST-ből történő másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet **típustulajdonságát** **RestResource (RestResource**) tulajdonságra kell állítani. | Igen |
| relativeUrl | Az adatokat tartalmazó erőforrás relatív URL-címe. Ha ez a tulajdonság nincs megadva, csak a csatolt szolgáltatásdefinícióban megadott URL-címet használja a program. A HTTP-összekötő adatokat másol `[URL specified in linked service]/[relative URL specified in dataset]`az egyesített URL-címből: . | Nem |

Ha a `requestMethod`, `additionalHeaders` `requestBody` , `paginationRules` és az adatkészletben, továbbra is támogatott, ahogy van, miközben azt javasoljuk, hogy használja az új modell tevékenységforrás megy előre.

**Példa:**

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

## <a name="copy-activity-properties"></a>Tevékenység tulajdonságainak másolása

Ez a szakasz a REST-forrás által támogatott tulajdonságok listáját tartalmazza.

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok című témakörben található.](concepts-pipelines-activities.md) 

### <a name="rest-as-source"></a>REST mint forrás

A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás **típustulajdonságának** **RestSource**beállításra kell állítva. | Igen |
| requestMethod (requestMethod) | A HTTP módszer. Az engedélyezett értékek a következők: **Bek** és a Könyvelés ( **Bek**) és Felad . | Nem |
| további fejlécek | További HTTP-kérelemfejlécek. | Nem |
| requestBody | A HTTP-kérelem törzse. | Nem |
| tördelési szabályok | A következő oldalkérések összeállításához tartozó tördelési szabályok. A [részletekről a Tördelés támogatási](#pagination-support) szakasza jelenik meg. | Nem |
| httpRequestTimeout | A HTTP-kérelem időtúlértéke **(a TimeSpan** érték) a válasz lekéréséhez. Ez az érték a válasz időtúlértéke, nem pedig a válaszadatok olvasásához szükséges időtúlérték. Az alapértelmezett érték **00:01:40**.  | Nem |
| requestInterval | A várakozási idő, mielőtt elküldenék a következő oldalra vonatkozó kérelmet. Az alapértelmezett érték **00:00:01** |  Nem |

>[!NOTE]
>A REST-összekötő figyelmen kívül hagyja `additionalHeaders`a . Mivel a REST-összekötő csak a JSON-ban `Accept: application/json`támogatja a választ, automatikusan létrehoz egy fejlécet.

**1. példa: A Get metódus használata tördeléssel**

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

**2. példa: A Bejegyzés módszer használata**

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

## <a name="pagination-support"></a>Tördelés támogatása

A REST API általában ésszerű számú rakorlátozza egyetlen kérelem válaszhasznos adatának méretét; miközben nagy mennyiségű adatot szeretne visszaadni, az eredményt több oldalra osztja fel, és megköveteli a hívóktól, hogy egymást követő kéréseket küldjenek az eredmény következő oldalának betöltésére. Az egy oldalra vonatkozó kérés általában dinamikus, és az előző oldal válaszából visszaadott információkból áll.

Ez az általános REST-összekötő a következő tördelési mintákat támogatja: 

* A következő kérelem abszolút vagy relatív URL = tulajdonságértéke az aktuális választörzsben
* A következő kérelem abszolút vagy relatív URL=fejlécértéke az aktuális válaszfejlécekben
* A következő kérelem lekérdezési paramétere = tulajdonságérték az aktuális választörzsben
* A következő kérelem lekérdezési paramétere = fejlécérték az aktuális válaszfejlécekben
* Következő kérelem fejléce = tulajdonságértéke az aktuális választörzsben
* Következő kérelem fejléce = fejlécértéke az aktuális válaszfejlécekben

**A tördelési szabályok** az adatkészlet egy szótáraként vannak definiálva, amely egy vagy több kis- és nagybetűvel megkülönböztető kulcsérték-párt tartalmaz. A rendszer a második oldalról kezdődő kérelem létrehozásához használja a konfigurációt. Az összekötő leállítja az iterációt, amikor http-állapotkódot kap 204 (Nincs tartalom), vagy a "paginationRules" jsonpath-kifejezései null értéket ad vissza.

**Támogatott kulcsok** a tördelési szabályokban:

| Kulcs | Leírás |
|:--- |:--- |
| AbsoluteUrl (AbszolútUrl) | A következő kérelem kiadásához legközelebb álló URL-címet jelzi. Ez lehet **abszolút URL vagy relatív URL.** |
| QueryParameters. *request_query_parameter* VAGY QueryParameters['request_query_parameter'] | A "request_query_parameter" a felhasználó által definiált, amely a következő HTTP-kérelem URL-címében egy lekérdezési paraméter nevére hivatkozik. |
| Fejlécek. *request_header* VAGY Fejlécek['request_header'] | A "request_header" a felhasználó által definiált, amely a következő HTTP-kérelemben hivatkozik egy fejlécnévre. |

**Támogatott értékek** a tördelési szabályokban:

| Érték | Leírás |
|:--- |:--- |
| Fejlécek. *response_header* VAGY Fejlécek['response_header'] | A "response_header" a felhasználó által definiált, amely az aktuális HTTP-válaszban egy fejlécnévre hivatkozik, amelynek értéke a következő kérés kiadásához lesz használva. |
| JSONPath-kifejezés, amely "$" -val kezdődik (a választörzs gyökerét jelöli) | A választörzs nek csak egy JSON objektumot kell tartalmaznia. A JSONPath kifejezésnek egyetlen primitív értéket kell visszaadnia, amely a következő kérés kiadására szolgál. |

**Példa:**

A Facebook Graph API a következő struktúrában adja vissza a választ, amely esetben a következő oldal URL-je megjelenik a ***lapozásban.next***:

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

A rest-copy tevékenység forráskonfigurációja különösen a `paginationRules` következő:

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

## <a name="use-oauth"></a>Az OAuth használata
Ez a szakasz ismerteti, hogyan használhat egy megoldássablont adatok másolására REST-összekötőről az Azure Data Lake Storage-ba JSON formátumban OAuth használatával. 

### <a name="about-the-solution-template"></a>A megoldássablon

A sablon két tevékenységet tartalmaz:
- **A webes** tevékenység lekéri a tulajdonosi jogkivonatot, majd átadja azt a következő másolási tevékenységnek engedélyezésként.
- A tevékenység **másolása** adatokat másol a REST-ből az Azure Data Lake Storage-ba.

A sablon két paramétert határoz meg:
- **A SinkContainer** a gyökérmappa elérési útja, ahová az adatok másolása az Azure Data Lake Storage-ban. 
- **A SinkDirectory** a gyökér könyvtárelérési útja, ahová az adatok at másolja az Azure Data Lake Storage. 

### <a name="how-to-use-this-solution-template"></a>A megoldássablon használata

1. Nyissa meg a **Másolás REST-ből vagy a HTTP-t az OAuth** sablon használatával. Hozzon létre egy új kapcsolatot a forráskapcsolathoz. 
    ![Új kapcsolatok létrehozása](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Az alábbiakban az új csatolt szolgáltatás (REST) beállításainak legfontosabb lépéseit olvashatja:
    
     1. Az **Alap URL-cím csoportban**adja meg a saját forrás REST-szolgáltatás url-paraméterét. 
     2. A **Hitelesítés típushoz**válassza a *Névtelen*lehetőséget.
        ![Új REST-kapcsolat](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Hozzon létre egy új kapcsolatot a célkapcsolathoz.  
    ![Új Gen2 kapcsolat](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Válassza **a Sablon használata lehetőséget.**
    ![Sablon használata](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. A következő példában látható módon a ![folyamat a következő példában látható: Pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Válassza a **Webes** tevékenység lehetőséget. A **Beállítások párbeszédpanelen**adja meg a megfelelő **URL-címet**, **metódust**, **fejléceket**és **törzset** az OAuth-tulajdonos token beolvasásához annak a szolgáltatásnak a bejelentkezési API-jából, amelyből adatokat szeretne másolni. A sablon helyőrzője bemutatja az Azure Active Directory (AAD) OAuth mintáját. Megjegyzés: Az AAD-hitelesítést a REST-összekötő natív módon támogatja, itt csak egy példa az OAuth-folyamatra. 

    | Tulajdonság | Leírás |
    |:--- |:--- |:--- |
    | URL-cím |Adja meg azt az URL-címet, amelyből az OAuth-tulajdonosi token lekéréséhez. pl. a mintában itthttps://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | Módszer | A HTTP módszer. Az engedélyezett értékek a **Következők: Post** és **Get**. | 
    | Fejlécek | A fejléc felhasználó által definiált, amely egy fejlécnévre hivatkozik a HTTP-kérelemben. | 
    | Törzs | A HTTP-kérelem törzse. | 

    ![Folyamat](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. Az **Adatmásolási** tevékenység ben válassza a *Forrás* lapot, és láthatja, hogy az előző lépésből beolvasott tulajdonosi token (access_token) átkerül az adattevékenység másolása adatmásolási **engedélyként a** További fejlécek alatt. A folyamat futtatásának megkezdése előtt ellenőrizze a következő tulajdonságok beállításait.

    | Tulajdonság | Leírás |
    |:--- |:--- |:--- | 
    | Kérelem módszere | A HTTP módszer. Az engedélyezett értékek a következők: **Bek** és a Könyvelés ( **Bek**) és Felad . | 
    | További fejlécek | További HTTP-kérelemfejlécek.| 

   ![Forrás hitelesítésének másolása](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Válassza **a Hibakeresés**lehetőséget, írja be a **Paraméterek**, majd a **Befejezés**lehetőséget.
   ![Folyamat futtatása](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. Ha a folyamat sikeresen befejeződik, az eredmény a ![következő példához hasonló lesz: A folyamat futtatási eredménye](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Kattintson a "Kimenet" ikonra webaktivitás **műveletek** oszlopban, akkor megjelenik a access_token vissza a szolgáltatás.

   ![Token kimenete](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Kattintson a CopyActivity "Input" ikonra a **Műveletek** oszlopban, és láthatja, hogy a WebActivity által beolvasott access_token átkerül a CopyActivity-nak hitelesítésre. 

    ![Token bemenet](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Annak elkerülése érdekében, token, hogy egyszerű szöveges naplóba, engedélyezze a "Biztonságos kimenet" a webes tevékenység és a "Biztonságos bemenet" a Másolás tevékenység.


## <a name="export-json-response-as-is"></a>JSON-válasz exportálása

Ezzel a REST-összekötővel exportálhatja a REST API JSON-válaszát különböző fájlalapú tárolókban. Az ilyen séma-független másolás eléréséhez hagyja ki az adatkészlet és a séma leképezésének "struktúra" (más néven *séma)* szakaszát a másolási tevékenységben.

## <a name="schema-mapping"></a>Sémaleképezés

Ha adatokat szeretne másolni a REST-végpontról a táblázatos fogadóba, olvassa el a [sémaleképezést.](copy-activity-schema-and-type-mapping.md#schema-mapping)

## <a name="next-steps"></a>További lépések

Az Azure Data Factory ban a Másolási tevékenység által adatforrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak és -formátumok című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)
