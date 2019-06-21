---
title: 'Gyors útmutató: PowerShell és REST API-k – Azure Search szolgáltatásban'
description: Hozzon létre, betöltését és a PowerShell-lel index lekérdezése az Azure Search REST API és az Invoke-RestMethod.
ms.date: 06/10/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 64252f42f5b3690e8ba6d929e27f7f41b649922e
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302290"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell"></a>Gyors útmutató: Egy PowerShell-lel az Azure Search-index létrehozása
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Python](search-get-started-python.md)
> * [Portál](search-create-index-portal.md)
> 

Ez a cikk végigvezeti a folyamat létrehozása, betöltése és lekérdezése az Azure Search-index PowerShell-lel és a [Azure Search REST API-k](https://docs.microsoft.com/rest/api/searchservice/). Ez a cikk bemutatja, hogyan interatively PowerShell-parancsok futtatásához. Azt is megteheti futtathat egy befejezett parancsfájlt. Ha szeretné letölteni, nyissa meg a [azure-search-powershell-minták](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) tárház.

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a munka megkezdése előtt, majd [regisztrálhat az Azure Searchre](search-create-service-portal.md).

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató a következő szolgáltatásokat és eszközöket használatosak. 

+ [A PowerShell 5.1-es vagy újabb](https://github.com/PowerShell/PowerShell)révén [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) szekvenciális és interaktív lépéseit.

+ [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben a rövid útmutatóban egy ingyenes szolgáltatás használhatja. 

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

Ez az index neve "hotels-quickstart" és a Meződefiníciók lentebb látható. A nagyobb része ["Hotels" index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) használt egyéb forgatókönyvek. Hogy vágott ebben a rövid útmutatóban kihagytuk.

1. Ebben a példában illessze be a PowerShell segítségével hozzon létre egy **$body** objektum, amely tartalmazza az indexsémát.

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. Állítsa be az URI-t a indexek gyűjteményhez, a szolgáltatás és a *hotels-quickstart* index.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Futtassa a parancsot a **$url**, **$headers**, és **$body** a szolgáltatásban az index létrehozásához. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Eredmények (csak az első két mezőjét kivonatosan) ehhez hasonlóan kell kinéznie:

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> Az ellenőrzéshez a portálon az indexek lista sikerült is ellenőrizheti.

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
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. Állítsa a végpontot a *hotels-quickstart* docs-gyűjtemény, és az indexelési műveletet (indexek/hotels – rövid útmutató/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Futtassa a parancsot a **$url**, **$headers**, és **$body** dokumentumok betöltése a szállodák-quickstart indexbe.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Eredmények az alábbi példához hasonlóan kell kinéznie. Megjelenik egy [201-es állapotkódot](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
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
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Ez a lépés bemutatja, hogyan kérdezhet le egy index használatával a [Search API-dokumentumok](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Ügyeljen arra, szimpla idézőjelek között a keresési $urls. Lekérdezési karakterláncok tartalmazzák **$** karaktereket, és kihagyhatja kellene formában, ha a teljes sztring aposztrófok közé van...

1. Állítsa a végpontot a *hotels-quickstart* docs-gyűjtemény, és adja hozzá a **keresési** egy lekérdezési karakterlánc paramétert. 
  
   Ez a karakterlánc egy üres keresés végrehajtása (keresési = *), az unranked listájának visszaadása (Keresés a pontszám = 1,0) tetszőleges dokumentumok. Alapértelmezés szerint az Azure Search egyszerre 50 egyezést adja vissza. Strukturált, mint ez a lekérdezés egy teljes dokumentum szerkezete és értékeket ad vissza. Adjon hozzá **$count = true** összes dokumentum számbavétele az eredményeket.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. Futtassa a parancsot küldhet a **$url** a szolgáltatáshoz.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Eredmények a következő kimenet hasonlóan kell kinéznie.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Próbálja meg néhány további lekérdezést példák betekintést nyerhet a szintaxis. Egy karakterlánc-keresés, szó $filter lekérdezések, az eredménykészletet, az adott mezők, és egyéb a keresés hatóköre korlátozza.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása 

Ha már nincs szüksége lesz rá az index törölni kell. Egy ingyenes szolgáltatás három indexre korlátozódik. Előfordulhat, hogy törölni kívánja azt nem használja aktívan, hogy a többi elolvasásával indexekkel.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban a Powershellt használta végighaladhat az alapvető munkafolyamat létrehozásához, és az Azure Search tartalom elérése. Vegye figyelembe a fogalmakat javasoljuk, hogy helyezze át egy speciális forgatókönyvek, például az Azure-adatforrás; indexelő

> [!div class="nextstepaction"]
> [REST-Útmutató: Index, és részben strukturált adatok (JSON-blobok) keresése az Azure Search szolgáltatásban](search-semi-structured-data.md)