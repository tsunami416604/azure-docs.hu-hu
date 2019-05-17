---
title: 'Gyors útmutató: PowerShell és REST API-k – Azure Search szolgáltatásban'
description: Hozzon létre, betöltését és a PowerShell-lel index lekérdezése az Azure Search REST API és az Invoke-RestMethod.
ms.date: 05/16/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: a82ee51a168a018a4df537c05d987974e775b6cc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795940"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell"></a>Gyors útmutató: Egy PowerShell-lel az Azure Search-index létrehozása
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Python](search-get-started-python.md)
> * [Portál](search-create-index-portal.md)
> 

Ez a cikk végigvezeti a folyamat létrehozása, betöltése és lekérdezése az Azure Search [index](search-what-is-an-index.md) PowerShell-lel és a [Azure Search szolgáltatás REST API-k](https://docs.microsoft.com/rest/api/searchservice/). Az index definícióját és a kereshető tartalmak szerepelnek a kérelem törzsében helytelen formátumú JSON-tartalomként.

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a munka megkezdése előtt, majd [regisztrálhat az Azure Searchre](search-create-service-portal.md).

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató a következő szolgáltatásokat és eszközöket használatosak. 

+ [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben a rövid útmutatóban egy ingyenes szolgáltatás használhatja. 

+ [A PowerShell 5.1-es vagy újabb](https://github.com/PowerShell/PowerShell)révén [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) szekvenciális és interaktív lépéseit.

## <a name="get-a-key-and-url"></a>Egy kulcsot és egy URL-cím beszerzése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

2. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-fiddler/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="connect-to-azure-search"></a>Kapcsolódás az Azure Search

1. A PowerShell-lel, hozzon létre egy **$headers** objektum a tartalomtípust és API-kulcs tárolására. Cserélje le a felügyeleti API-kulcs (a-ADMIN-API-kulcsot) egy kulcsot, amely érvényes a keresési szolgáltatáshoz. Csak akkor kell egyszer állítsa be ezt a fejlécet a munkamenet időtartama, de fel fog venni azt minden kérelemnél. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Hozzon létre egy **$url** objektum, amely meghatározza a szolgáltatás indexeli a gyűjteményben. Cserélje le egy érvényes keresési szolgáltatás a szolgáltatás neve (a SEARCH-szolgáltatás neve).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06"
    ```

3. Futtatás **Invoke-RestMethod** egy GET kérelmet küld a szolgáltatást, és ellenőrizze a kapcsolatot. Adjon hozzá **ConvertTo-Json** így megtekintheti a válaszokat küldi vissza a szolgáltatásból.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Ha a szolgáltatás üres és nem az indexek, eredmények az alábbi példához hasonló. Ellenkező esetben index definíciók JSON-reprezentációja látható.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 – Index létrehozása

A portál használata, az index léteznie kell a szolgáltatás adatok betöltése előtt. Ebben a lépésben az index határozza meg, majd leküldi azt a szolgáltatást. A [Index REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) ebben a lépésben használt.

Az index szükséges elemek közé tartozik, egy nevet és egy mezőt. A mezők gyűjteménye határozza meg a szerkezete egy *dokumentum*. Minden mező rendelkezik egy név, típus és attribútumokat, amelyek meghatározzák, hogyan használja fel azokat (például, hogy-e teljes szöveges kereshető, szűrhető vagy lekérhető a keresési eredmények között). Index, egyes típusú mezők belül `Edm.String` kijelölt a *kulcs* dokumentum identitás.

Ez az index "hotels-powershell" nevű, és rendelkezik a Meződefiníciók lentebb látható. A nagyobb része ["Hotels" index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) használt egyéb forgatókönyvek. Hogy vágott ebben a rövid útmutatóban kihagytuk.

1. Ebben a példában illessze be a PowerShell segítségével hozzon létre egy **$body** objektum, amely tartalmazza az indexsémát.

    ```powershell
    $body = @"
    {
        "name": "hotels-powershell",  
        "fields": [
            {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
            {"name": "baseRate", "type": "Edm.Double"},
            {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
            {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
            {"name": "hotelName", "type": "Edm.String", "facetable": false},
            {"name": "category", "type": "Edm.String"},
            {"name": "tags", "type": "Collection(Edm.String)"},
            {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
            {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
            {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
            {"name": "rating", "type": "Edm.Int32"},
            {"name": "location", "type": "Edm.GeographyPoint"}
        ]
    }
    "@
    ```

2. Állítsa be az URI-t a indexek gyűjteményhez, a szolgáltatás és a *hotels-powershell* index.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-powershell?api-version=2019-05-06"
    ```

3. Futtassa a parancsot a **$url**, **$headers**, és **$body** a szolgáltatásban az index létrehozásához. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Eredmények (csak az első két mezőjét kivonatosan) ehhez hasonlóan kell kinéznie:

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6A99E2DED96B0\"",
        "name":  "hotels-powershell",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "hotelId",
                        "type":  "Edm.String",
                        "searchable":  false,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  false,
                        "facetable":  false,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "baseRate",
                        "type":  "Edm.Double",
                        "searchable":  false,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> Az ellenőrzéshez sikerült is a portálon az indexek listában, vagy futtassa újra a parancsot szolgáltatáskapcsolódási ellenőrzéséhez használt megtekintéséhez a *hotels-powershell* az indexek gyűjteményben felsorolt index.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Küldje le a dokumentumokat, használja a HTTP POST-kérelmet az index URL-cím végponthoz. Ebben a feladatban a REST API [hozzáadása, frissítése vagy törlése dokumentumok](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Ebben a példában illessze be a PowerShell segítségével hozzon létre egy **$body** objektum, amely tartalmazza a feltölteni kívánt dokumentumokat. 

    A kérelem két teljes és a egy részleges rekordot tartalmaz. A részleges rekord azt mutatja be, hogy nem teljes dokumentumokat feltölthet. A `@search.action` paraméter határozza meg, hogyan indexelő történik. Érvényes értékek a következők: feltöltés, egyesítés, mergeOrUpload és törlése. MergeOrUpload viselkedésének vagy hoz létre egy új dokumentumot hotelId a = 3, vagy frissíti a tartalmat, ha már létezik.

    ```powershell
    $body = @"
    {
        "value": [
            {
                "@search.action": "upload",
                "hotelId": "1",
                "baseRate": 199.0,
                "description": "Best hotel in town",
                "hotelName": "Fancy Stay",
                "category": "Luxury",
                "tags": ["pool", "view", "wifi", "concierge"],
                "parkingIncluded": false,
                "smokingAllowed": false,
                "lastRenovationDate": "2010-06-27T00:00:00Z",
                "rating": 5,
                "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
            },
            {
                "@search.action": "upload",
                "hotelId": "2",
                "baseRate": 79.99,
                "description": "Cheapest hotel in town",
                "hotelName": "Roach Motel",
                "category": "Budget",
                "tags": ["motel", "budget"],
                "parkingIncluded": true,
                "smokingAllowed": true,
                "lastRenovationDate": "1982-04-28T00:00:00Z",
                "rating": 1,
                "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
            },
            {
                "@search.action": "mergeOrUpload",
                "hotelId": "3",
                "baseRate": 129.99,
                "description": "Close to town hall and the river"
            }
        ]
    }
    "@
    ```

1. Állítsa a végpontot a *hotels-powershell* docs-gyűjtemény, és az indexelési műveletet (indexek/hotels-powershell/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-powershell/docs/index?api-version=2019-05-06"
    ```

1. Futtassa a parancsot a **$url**, **$headers**, és **$body** dokumentumok betöltése a szállodák-powershell indexbe.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Eredmények az alábbi példához hasonlóan kell kinéznie. Megjelenik a 201-es állapotkódot. Az összes állapotkódok ismertetését lásd: [HTTP-állapotkódok (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes/hotels-powershell/$metadata#Collection(Microsoft.Azure.Search.V2017_11_11.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Ez a lépés bemutatja, hogyan kérdezhet le egy index használatával a [Search API-dokumentumok](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. Állítsa a végpontot a *hotels-powershell* docs-gyűjtemény, és adja hozzá a **keresési** paraméter tartalmazza a lekérdezési karakterláncokat. Ez a karakterlánc egy üres keresés és a egy unranked listában szereplő összes dokumentumot visszaadja.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-powershell/docs?api-version=2019-05-06&search=*'
    ```

1. Futtassa a parancsot küldhet a **$url** a szolgáltatáshoz.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Eredmények a következő kimenet hasonlóan kell kinéznie.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes/hotels-powershell/$metadata#docs(*)",
        "value":  [
                    {
                        "@search.score":  1.0,
                        "hotelId":  "1",
                        "baseRate":  199.0,
                        "description":  "Best hotel in town",
                        "description_fr":  null,
                        "hotelName":  "Fancy Stay",
                        "category":  "Luxury",
                        "tags":  "pool view wifi concierge",
                        "parkingIncluded":  false,
                        "smokingAllowed":  false,
                        "lastRenovationDate":  "2010-06-27T00:00:00Z",
                        "rating":  5,
                        "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                    },
                    {
                        "@search.score":  1.0,
                        "hotelId":  "2",
                        "baseRate":  79.99,
                        "description":  "Cheapest hotel in town",
                        "description_fr":  null,
                        "hotelName":  "Roach Motel",
                        "category":  "Budget",
                        "tags":  "motel budget",
                        "parkingIncluded":  true,
                        "smokingAllowed":  true,
                        "lastRenovationDate":  "1982-04-28T00:00:00Z",
                        "rating":  1,
                        "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                    },
                    {
                        "@search.score":  1.0,
                        "hotelId":  "3",
                        "baseRate":  129.99,
                        "description":  "Close to town hall and the river",
                        "description_fr":  null,
                        "hotelName":  null,
                        "category":  null,
                        "tags":  "",
                        "parkingIncluded":  null,
                        "smokingAllowed":  null,
                        "lastRenovationDate":  null,
                        "rating":  null,
                        "location":  null
                    }
                ]
    }
    ```

Próbálja meg néhány további lekérdezést példák betekintést nyerhet a szintaxis. Egy karakterlánc-keresés, szó $filter lekérdezések, az eredménykészletet, az adott mezők, és egyéb a keresés hatóköre korlátozza.

```powershell
# Query example 1
# Search the entire index for the term 'budget'
# Return only the `hotelName` field, "Roach hotel"
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-powershell/docs?api-version=2019-05-06&search=budget&$select=hotelName'

# Query example 2 
# Apply a filter to the index to find hotels cheaper than $150 per night
# Returns the `hotelId` and `description`. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-powershell/docs?api-version=2019-05-06&search=*&$filter=baseRate lt 150&$select=hotelId,description'

# Query example 3
# Search the entire index, order by a specific field (`lastRenovationDate`) in descending order
# Take the top two results, and show only `hotelName` and `lastRenovationDate`
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-powershell/docs?api-version=2019-05-06&search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate'
```
## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása 

Ha már nincs szüksége lesz rá az index törölni kell. Egy ingyenes szolgáltatás három indexre korlátozódik. Előfordulhat, hogy törölni kívánja azt nem használja aktívan, hogy a többi elolvasásával indexekkel.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels-powershell?api-version=2019-05-06'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>További lépések

Próbáljon meg francia leírások az indexbe. Az alábbi példa francia karakterláncokat tartalmazza, és további keresési műveleteket mutatja be. MergeOrUpload hozhat létre, és a meglévő mezőket ad hozzá. A következő karakterláncot kell lennie az UTF-8 kódolású.

```json
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "1",
            "description_fr": "Meilleur hôtel en ville"
        },
        {
            "@search.action": "merge",
            "hotelId": "2",
            "description_fr": "Hôtel le moins cher en ville"
        }
    ]
}
```
