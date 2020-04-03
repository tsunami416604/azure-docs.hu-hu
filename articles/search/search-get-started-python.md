---
title: 'Rövid útmutató: Keresési index létrehozása pythonban REST API-k használatával'
titleSuffix: Azure Cognitive Search
description: Bemutatja, hogyan hozhat létre indexet, adatokat tölthet be és futtathat lekérdezéseket python, Jupyter notebookok és az Azure Cognitive Search REST API használatával.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 04/01/2020
ms.openlocfilehash: fd87dbe125e84c171cc35a2b242879c44bc50fd9
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585928"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Rövid útmutató: Hozzon létre egy Azure Cognitive Search indexet a Pythonban jupyter-jegyzetfüzetek használatával

> [!div class="op_single_selector"]
> * [Python (TÖBBI)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postás (REST)](search-get-started-postman.md)
> * [Portál](search-create-index-portal.md)
> 

Hozzon létre egy Jupyter-jegyzetfüzetet, amely létrehoz, betölt és lekérdezi az Azure Cognitive Search indexet a Python és az [Azure Cognitive Search REST API-k](https://docs.microsoft.com/rest/api/searchservice/)használatával. Ez a cikk bemutatja, hogyan hozhat létre egy jegyzetfüzetet lépésről lépésre. Másik lehetőségként [letöltheti és futtathatja a kész Jupyter Python notebookot.](https://github.com/Azure-Samples/azure-search-python-samples)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A következő szolgáltatásokra és eszközökre van szükség ehhez a rövid útmutatóhoz. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), amely Python 3.x és Jupyter notebookok.

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Az ingyenes szint segítségével ezt a rövid útmutatót. 

## <a name="get-a-key-and-url"></a>Kulcs és URL beszerezése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. A keresési szolgáltatás mindkettővel jön létre, így ha hozzáadta az Azure Cognitive Search-et az előfizetéséhez, kövesse az alábbi lépéseket a szükséges információk beszerezéséhez:

1. [Jelentkezzen be az Azure Portalon,](https://portal.azure.com/)és a keresési szolgáltatás **áttekintése** lapon lekell szereznie az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások** > **kulcsok**párbeszédpanelen szerezzen be egy rendszergazdai kulcsot a szolgáltatás teljes jogához. Két cserélhető rendszergazdai kulcs van, amelyek az üzletmenet folytonosságát biztosítják arra az esetre, ha át kell görgetnie egyet. Az elsődleges vagy másodlagos kulcsot objektumok hozzáadására, módosítására és törlésére irányuló kérelmeken használhatja.

![HTTP-végpont és hozzáférési kulcs beszerezni](media/search-get-started-postman/get-url-key.png "HTTP-végpont és hozzáférési kulcs beszerezni")

Minden kérelemhez api-kulcs szükséges a szolgáltatásnak küldött minden kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="connect-to-azure-cognitive-search"></a>Csatlakozás az Azure Cognitive Search szolgáltatáshoz

Ebben a feladatban indítsa el a Jupyter-jegyzetfüzetet, és ellenőrizze, hogy csatlakozhat-e az Azure Cognitive Search szolgáltatáshoz. Ezt úgy teheti meg, hogy kéri az indexek listáját a szolgáltatástól. Az Anaconda3 rendszerű Windows on az Anaconda Navigator használatával elindíthat egy jegyzetfüzetet.

1. Hozzon létre egy új Python3-jegyzetfüzetet.

1. Az első cellában töltse be a JSON-nal való munkához használt könyvtárakat, és fogalmazza meg a HTTP-kérelmeket.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. A második cellában adja meg a kéréselemeket, amelyek minden kérésnél állandók lesznek. Cserélje le a keresési szolgáltatás nevét (YOUR-SEARCH-SERVICE-NAME) és a felügyeleti API-kulcsot (YOUR-ADMIN-API-KEY) érvényes értékekre. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Ha ConnectionError `"Failed to establish a new connection"`-ot kap, ellenőrizze, hogy az api-kulcs elsődleges vagy másodlagos felügyeleti`?` `/`kulcs-e, és hogy minden kezdő és záró karakter ( és ) a helyén van-e.

1. A harmadik cellában fogalmazza meg a kérést. Ez a GET-kérelem a keresési szolgáltatás indexgyűjteményét célozza meg, és kiválasztja a meglévő indexek névtulajdonságát.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Futtasson minden lépést. Ha vannak indexek, a válasz az indexnevek listáját tartalmazza. Az alábbi képernyőképen a szolgáltatás már rendelkezik egy azureblob-index és egy realestate-us-minta index.

   ![Python-parancsfájl a Jupyter-jegyzetfüzetben az Azure Cognitive Search HTTP-kérésekkel](media/search-get-started-python/connect-azure-search.png "Python-parancsfájl a Jupyter-jegyzetfüzetben az Azure Cognitive Search HTTP-kérésekkel")

   Ezzel szemben egy üres indexgyűjtemény ezt a választ adja vissza:`{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 – Index létrehozása

Ha nem használja a portált, az adatok betöltése előtt léteznie kell egy indexnek a szolgáltatáson. Ez a lépés az [Index REST API létrehozása segítségével](https://docs.microsoft.com/rest/api/searchservice/create-index) leküldéses indexséma a szolgáltatásba.

Az index kötelező elemei közé tartozik egy név, egy mezőgyűjtemény és egy kulcs. A mezőgyűjtemény határozza meg a *dokumentum*szerkezetét. Minden mezőnek van egy neve, típusa és attribútuma, amely meghatározza a mező használati módját (például, hogy teljes szöveges kereshető, szűrhető vagy visszakereshető a keresési eredmények között). Az indexen belül az egyik `Edm.String` típusú mezőt kell a dokumentumidentitás *kulcsaként* kijelölni.

Ez az index neve "hotel-quickstart", és az alábbi meződefiníciókat tartalmazza. Ez egy nagyobb [Hotel index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) egy része, amelyet más forgatókönyvekben használnak. Mi nyírt, hogy ebben a rövid encikba a rövidség.

1. A következő cellába illessze be a következő példát egy cellába a séma megadásához. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. Egy másik cellában fogalmazza meg a kérést. Ez a POST-kérelem a keresési szolgáltatás indexgyűjteményét célozza meg, és az előző cellában megadott indexséma alapján létrehoz egy indexet.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Futtasson minden lépést.

   A válasz tartalmazza a séma JSON-ábrázolását. A következő képernyőkép csak a válasz egy részét mutatja.

    ![Index létrehozására irányuló kérelem](media/search-get-started-python/create-index.png "Index létrehozására irányuló kérelem")

> [!Tip]
> Az index létrehozásának ellenőrzésének másik módja az Indexek listájának ellenőrzése a portálon.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Dokumentumok betöltése

Dokumentumok leküldéses, használjon HTTP POST kérelmet az index URL-végpont. A REST API [dokumentumok hozzáadása, frissítése vagy törlése](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). A dokumentumok a [HotelsData-ból](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) származnak a GitHubon.

1. Egy új cellában adjon meg négy dokumentumot, amelyek megfelelnek az indexsémának. Adjon meg minden dokumentumhoz feltöltési műveletet.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. Egy másik cellában fogalmazza meg a kérést. Ez a POST-kérelem a hotel-rövidútmutató-index dokumentációgyűjteményét célozza meg, és leküldi az előző lépésben megadott dokumentumokat.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Az egyes lépéseket futtatva a dokumentumokat a keresési szolgáltatás indexébe szeretné vinni. Az eredményeknek a következő példához hasonlóan kell kinézniük. 

    ![Dokumentumok küldése indexbe](media/search-get-started-python/load-index.png "Dokumentumok küldése indexbe")

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Ez a lépés bemutatja, hogyan lehet lekérdezni egy indexet a [Search Documents REST API használatával.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

1. Egy cellában adjon meg egy lekérdezési kifejezést, amely üres keresést (keresés=*) hajt végre, tetszőleges dokumentumok rangsorolatlan listáját (keresési pontszám = 1.0). Alapértelmezés szerint az Azure Cognitive Search egyszerre 50 találatot ad vissza. Strukturáltként ez a lekérdezés egy teljes dokumentumstruktúrát és értékeket ad vissza. Adja hozzá a $count=true értéket, hogy megkapassa az összes dokumentumot az eredményekben.

   ```python
   searchstring = '&search=*&$count=true'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Egy új cellában adja meg a következő példát a "szállodák" és a "wifi" kifejezésekre való kereséshez. Adja meg $select, hogy mely mezők szerepeljenek a keresési eredmények között.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)   
   ```

   Az eredményeknek a következő kimenethez hasonlóan kell kinézniük. 

    ![Keresés az indexekben](media/search-get-started-python/search-index.png "Keresés az indexekben")

1. Ezután alkalmazzon egy $filter kifejezést, amely csak azokat a 4-nél nagyobb minősítésű szállodákat választja ki. 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)     
   ```

1. Alapértelmezés szerint a keresőmotor a top 50 dokumentumot adja vissza, de a felső és a kihagyás segítségével adhat hozzá tördelést, és kiválaszthatja, hogy hány dokumentum van az egyes eredményekben. Ez a lekérdezés minden eredményhalmazban két dokumentumot ad vissza.

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Ebben az utolsó példában $orderby használatával rendezheti az eredményeket város szerint. Ez a példa a Cím gyűjtemény mezőit tartalmazza.

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak **Minden erőforrás** vagy **Erőforráscsoport** hivatkozásával.

Ha ingyenes szolgáltatást használ, ne feledje, hogy három indexelésre, indexelőre és adatforrásra van korlátozva. Törölheti az egyes elemeket a portálon, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Egyszerűsítésként ez a rövid útmutató a Hotels index rövidített verzióját használja. Létrehozhatja a teljes verziót, hogy érdekesebb lekérdezéseket próbáljon ki. A teljes verzió és az összes 50 dokumentum beszerezéséhez futtassa az **Adatok importálása** varázslót, és válassza ki a *hotelminta lehetőséget* a beépített mintaadatforrásokból.

> [!div class="nextstepaction"]
> [Rövid útmutató: Index létrehozása az Azure Portalon](search-get-started-portal.md)
