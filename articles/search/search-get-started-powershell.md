---
title: 'Rövid útmutató: Keresési index létrehozása a PowerShellben REST API-k használatával'
titleSuffix: Azure Cognitive Search
description: Ebben a REST API-gyorsindításban megtudhatja, hogyan hozhat létre indexet, tölthet be adatokat, és futtathat lekérdezéseket a PowerShell Invoke-RestMethod és az Azure Cognitive Search REST API használatával.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: 612751c2405cd55ad0b3760aa8e093e434a22f57
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121605"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-powershell-using-rest-apis"></a>Rövid útmutató: Hozzon létre egy Azure Cognitive Search-indexet a PowerShellben a REST API-k használatával
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C #](search-create-index-dotnet.md)
> * [Postás (REST)](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [Portál](search-create-index-portal.md)
> 

Ez a cikk bemutatja az Azure Cognitive Search-index létrehozásának, betöltésének és lekérdezésének folyamatát a PowerShell és az [Azure Cognitive Search REST API-k](https://docs.microsoft.com/rest/api/searchservice/)használatával. Ez a cikk a PowerShell-parancsok interaktív futtatását ismerteti. Másik lehetőségként [letöltheti és futtathatja a Powershell-parancsfájlt,](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) amely ugyanazokat a műveleteket hajtja végre.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A következő szolgáltatásokra és eszközökre van szükség ehhez a rövid útmutatóhoz. 

+ [PowerShell 5.1-es vagy újabb,](https://github.com/PowerShell/PowerShell) [az Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) használatával szekvenciális és interaktív lépésekhez.

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. A rövid útmutatóhoz ingyenes szolgáltatást használhat. 

## <a name="get-a-key-and-url"></a>Kulcs és URL beszerezése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. A keresési szolgáltatás mindkettővel jön létre, így ha hozzáadta az Azure Cognitive Search-et az előfizetéséhez, kövesse az alábbi lépéseket a szükséges információk beszerezéséhez:

1. [Jelentkezzen be az Azure Portalon,](https://portal.azure.com/)és a keresési szolgáltatás **áttekintése** lapon lekell szereznie az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

2. A **Beállítások** > **kulcsok**párbeszédpanelen szerezzen be egy rendszergazdai kulcsot a szolgáltatás teljes jogához. Két cserélhető rendszergazdai kulcs van, amelyek az üzletmenet folytonosságát biztosítják arra az esetre, ha át kell görgetnie egyet. Az elsődleges vagy másodlagos kulcsot objektumok hozzáadására, módosítására és törlésére irányuló kérelmeken használhatja.

![HTTP-végpont és hozzáférési kulcs beszerezni](media/search-get-started-postman/get-url-key.png "HTTP-végpont és hozzáférési kulcs beszerezni")

Minden kérelemhez api-kulcs szükséges a szolgáltatásnak küldött minden kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="connect-to-azure-cognitive-search"></a>Csatlakozás az Azure Cognitive Search szolgáltatáshoz

1. A PowerShellben hozzon létre egy **$headers** objektumot a tartalomtípus és az API-kulcs tárolásához. Cserélje le a felügyeleti API-kulcsot (YOUR-ADMIN-API-KEY) egy olyan kulcsra, amely érvényes a keresési szolgáltatásra. Ezt a fejlécet csak egyszer kell beállítania a munkamenet időtartama alatt, de minden kéréshez hozzá fogja adni. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Hozzon létre egy **$url** objektumot, amely meghatározza a szolgáltatás indexek gyűjteményét. Cserélje le a szolgáltatás nevét (YOUR-SEARCH-SERVICE-NAME) egy érvényes keresési szolgáltatásra.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name"
    ```

3. Futtassa **az Invoke-RestMethod metódust** get-kérelem küldéséhez a szolgáltatásnak, és ellenőrizze a kapcsolatot. Adja hozzá **a ConvertTo-Json-t,** hogy megtekinthesse a szolgáltatásból visszaküldött válaszokat.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Ha a szolgáltatás üres, és nem rendelkezik indexek, az eredmények hasonlóak a következő példában. Ellenkező esetben megjelenik az indexdefiníciók JSON-ábrázolása.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 – Index létrehozása

Ha nem használja a portált, az adatok betöltése előtt léteznie kell egy indexnek a szolgáltatáson. Ez a lépés határozza meg az indexet, és leküldéses, hogy a szolgáltatás. Ehhez a lépéshez az [Index REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) használatos.

Az index kötelező elemei közé tartozik egy név és egy mezőgyűjtemény. A mezőgyűjtemény határozza meg a *dokumentum*szerkezetét. Minden mezőnek van egy neve, típusa és attribútuma, amely meghatározza a használat módját (például, hogy teljes szöveges kereshető, szűrhető vagy visszakereshető a keresési eredmények között). Az indexen belül az egyik `Edm.String` típusú mezőt kell a dokumentumidentitás *kulcsaként* kijelölni.

Ez az index neve "hotel-quickstart", és az alábbi meződefiníciókat tartalmazza. Ez egy nagyobb [Hotel index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) egy része, amelyet más forgatókönyvekben használnak. Mi nyírt, hogy ebben a rövid encikba a rövidség.

1. Illessze be ezt a **$body** példát a PowerShellbe, és hozzon létre egy $body-objektumot, amely tartalmazza az indexsémát.

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

2. Állítsa be az URI-t a szolgáltatás indexgyűjteményére és a *hotel-rövid útmutató indexére.*

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Futtassa a parancsot **a $url**, **$headers**és **$body** paranccsal a szolgáltatás indexének létrehozásához. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Az eredményeknek ehhez hasonlóan kell kinézniük (röviden az első két mezőre csonkolva):

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
> Az ellenőrzéshez ellenőrizheti az Indexek listát is a portálon.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Dokumentumok betöltése

Dokumentumok leküldéses, használjon HTTP POST kérelmet az index URL-végpont. A feladat REST API-ja A [dokumentumok hozzáadása, frissítése vagy törlése](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Illessze be ezt a **$body** példát a PowerShellbe, és hozzon létre egy $body-objektumot, amely tartalmazza a feltölteni kívánt dokumentumokat. 

    Ez a kérelem két teljes és egy részleges rekordot tartalmaz. A részleges rekord azt mutatja, hogy nem teljes dokumentumokat tölthet fel. A `@search.action` paraméter határozza meg az indexelés módját. Az érvényes értékek közé tartozik a feltöltés, az egyesítés, a mergeOrUpload és a törlés. A mergeOrUpload viselkedése vagy létrehoz egy új dokumentumot a hotelId = 3 számára, vagy frissíti a tartalmat, ha már létezik.

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

1. Állítsa be a végpontot a *hotel-quickstart* docs gyűjtemény, és tartalmazza az index művelet (indexek / hotels-quickstart/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Futtassa a parancsot **a $url**, **$headers**és **$body** paranccsal, hogy dokumentumokat töltsön be a hotel-gyorsindítás indexébe.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Az eredményeknek a következő példához hasonlóan kell kinézniük. Meg kell jelennie egy [201-es állapotkódnak.](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)

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

Ez a lépés bemutatja, hogyan lehet indexet lekérdezni a [Search Documents API használatával.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

Ügyeljen arra, hogy egyszeres idézőjeleket használjon a keresési $urls. A lekérdezési **$** karakterláncok karaktereket tartalmaznak, és kihagyhatja a kikerülésüket, ha a teljes karakterlánc idézőjelek közé van foglalva..

1. Állítsa be a végpontot a *hotel-quickstart* docs gyűjtemény, és adjunk hozzá egy **keresési** paramétert átadni egy lekérdezési karakterláncban. 
  
   Ez a karakterlánc üres keresést hajt végre (keresés=*), tetszőleges dokumentumok rangsorolatlan listáját adja vissza (keresési pontszám = 1.0). Alapértelmezés szerint az Azure Cognitive Search egyszerre 50 találatot ad vissza. Strukturáltként ez a lekérdezés egy teljes dokumentumstruktúrát és értékeket ad vissza. Adja hozzá **a $count=true** értéket, hogy megkapassa az összes dokumentumot az eredményekben.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. Futtassa a parancsot, hogy a **$url** küldje a szolgáltatásnak.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Az eredményeknek a következő kimenethez hasonlóan kell kinézniük.

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

Próbáljon ki néhány más lekérdezési példát, hogy megismerjék a szintaxist. Megteheti a karakterlánc-keresést, szó szerint $filter lekérdezéseket, korlátozhatja az eredményhalmazt, a keresést adott mezőkre és egyebekre is kihasználhatja.

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
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak **Minden erőforrás** vagy **Erőforráscsoport** hivatkozásával.

Ha ingyenes szolgáltatást használ, ne feledje, hogy három indexelésre, indexelőre és adatforrásra van korlátozva. Törölheti az egyes elemeket a portálon, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a PowerShell segítségével lépkedett végig az Azure Cognitive Search szolgáltatásban a tartalom létrehozásához és eléréséhez használt alapvető munkafolyamaton. A fogalmakat szem előtt tartva javasoljuk, hogy továbbhaladjon a speciálisabb forgatókönyvek, például az Azure-adatforrásokindexelésre;

> [!div class="nextstepaction"]
> [REST-oktatóanyag: Félig strukturált adatok (JSON-blobok) indexelése és keresése az Azure Cognitive Search szolgáltatásban](search-semi-structured-data.md)