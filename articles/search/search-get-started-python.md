---
title: 'Python rövid útmutató: Létrehozása, betöltése és lekérdezése az indexek az Azure Search REST API-k – Azure Search használatával'
description: Azt ismerteti, hogyan-index létrehozása, adatok betöltése és a Python, a Jupyter notebookok és az Azure Search REST API-lekérdezések futtatásához.
ms.date: 06/20/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 613879abd4c5c09450b690b793500a99428cff29
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485475"
---
# <a name="quickstart-create-an-azure-search-index-in-python-using-jupyter-notebooks"></a>Gyors útmutató: Az Azure Search-index létrehozása a Python, a Jupyter notebookok használatával
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Portál](search-create-index-portal.md)
> 

Létrehozó, betöltődik, és Python használatával Azure Search-index lekérdezése Jupyter notebook létrehozása és a [Azure Search REST API-k](https://docs.microsoft.com/rest/api/searchservice/). Ez a cikk azt ismerteti, hogyan hozhat létre egy notebookot lépésről lépésre, vág bele. Másik lehetőségként egy befejezett notebook futtatható. Ha szeretné letölteni, nyissa meg a[azure-search-python-samples-tárház](https://github.com/Azure-Samples/azure-search-python-samples).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató a következő szolgáltatásokat és eszközöket használatosak. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), így a Python 3.x és Jupyter-Notebookjait.

+ [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ez a rövid útmutató az ingyenes szint is használhat. 

## <a name="get-a-key-and-url"></a>Egy kulcsot és egy URL-cím beszerzése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-get-started-postman/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="connect-to-azure-search"></a>Kapcsolódás az Azure Search

Ebben a feladatban indíthat egy Jupyter notebookot, és győződjön meg arról, hogy képes-e csatlakozni az Azure Search. Lesz ehhez az indexek listájának lekérésekor a szolgáltatásból. Windows-Anaconda3 Anaconda-kezelő segítségével indítsa el a notebookot.

1. Hozzon létre egy új Python3 notebookot.

1. Az első olyan cellára, az a JSON-fájllal működik, és HTTP-kérések kidolgozásában függvénykönyvtárak betöltésére.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. A második cellába adjon meg a kérelem elemek állandók halasztása minden kérelemnél meg. Cserélje le az érvényes értékek a keresési szolgáltatás nevének (a SEARCH-szolgáltatás neve) és a rendszergazdai API-kulcs (a-ADMIN-API-kulcs). 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. A harmadik cellába állítson össze a kérelmet. A GET-kérés célozza meg a keresési szolgáltatás indexek gyűjteményét, és kiválasztja a létező indexek a name tulajdonság.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Minden lépés futtatásához. Ha létezik indexek, a válasz tartalmaz index nevének listáját. Az alábbi képernyőképen a szolgáltatás már az azureblob-index és a egy realestate-us-sample-index.

   ![Python-szkriptet a Jupyter notebook HTTP-kérelmek Azure Search](media/search-get-started-python/connect-azure-search.png "Python-szkriptet a Jupyter notebook HTTP-kérelmek Azure Search")

   Ellentétben az index üres gyűjteményt adja vissza ezt a választ: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 – Index létrehozása

A portál használata, az index léteznie kell a szolgáltatás adatok betöltése előtt. Ebben a lépésben használja a [Index REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) paranccsal küldje le a szolgáltatást az indexsémát.

Az index szükséges elemek közé tartozik a nevét, a mezők gyűjteményét és a egy kulcsot. A mezők gyűjteménye határozza meg a szerkezete egy *dokumentum*. Minden mező rendelkezik egy név, típus és attribútumok, amelyek meghatározzák, hogyan használja a mezőt (például, hogy-e teljes szöveges kereshető, szűrhető vagy lekérhető a keresési eredmények között). Index, egyes típusú mezők belül `Edm.String` kijelölt a *kulcs* dokumentum identitás.

Ez az index neve "hotels-quickstart" és a Meződefiníciók lentebb látható. A nagyobb része ["Hotels" index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) használt egyéb forgatókönyvek. Hogy vágott ebben a rövid útmutatóban kihagytuk.

1. A következő cellába illessze be az alábbi példa a sémát adjon meg egy cellában. 

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

2. Egy másik cellába állítson össze a kérelmet. A PUT kérés célozza meg a keresési szolgáltatás indexek gyűjteményét, és létrehoz egy indexet az indexsémát az előző cella megadott alapján.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Minden lépés futtatásához.

   A válasz tartalmazza a sémát a JSON-ábrázolását. Az alábbi képernyőképen látható a válasz egy részét.

    ![Az index létrehozására vonatkozó kérelem](media/search-get-started-python/create-index.png "index létrehozására vonatkozó kérelem")

> [!Tip]
> Index létrehozásának ellenőrzéséhez más úgy, hogy ellenőrizze a portálon az indexek listáját.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Küldje le a dokumentumokat, használja a HTTP POST-kérelmet az index URL-cím végponthoz. A REST API [hozzáadása, frissítése vagy törlése dokumentumok](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Dokumentumok származik [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) a Githubon.

1. Egy új cellába adja meg, amelyek megfelelnek az indexséma négy dokumentumokat. Adja meg az egyes dokumentumok feltöltési művelet.

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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
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

2. Egy másik cellába állítson össze a kérelmet. A POST-kérelemhez célozza meg, a szállodák-quickstart index docs gyűjteményét, majd leküldi az előző lépésben meghatározott dokumentumok.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Futtassa az egyes lépések a dokumentum elküldése egy indexbe a search szolgáltatás. Eredmények az alábbi példához hasonlóan kell kinéznie. 

    ![Dokumentumok elküldése egy indexbe](media/search-get-started-python/load-index.png "dokumentumokat küldeni egy indexbe")

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Ez a lépés bemutatja, hogyan kérdezhet le egy index használatával a [Search dokumentumok REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. Egy cellába, adjon meg egy lekérdezési kifejezés, amely végrehajt egy üres keresés (keresési = *), az unranked listájának visszaadása (Keresés a pontszám = 1.0) tetszőleges dokumentumok. Alapértelmezés szerint az Azure Search egyszerre 50 egyezést adja vissza. Strukturált, mint ez a lekérdezés egy teljes dokumentum szerkezete és értékeket ad vissza. Adja hozzá a $count = true összes dokumentum számbavétele az eredményeket.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. Egy új cellába adja meg az alábbi példa a feltételek "hotels" és "Wi-Fi" a kereséshez. Adja hozzá a $select, adja meg a keresési eredmények szerepeltetendő mezőket.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. Egy másik cellába állítson össze egy kérelmet. A GET kérelem a docs-gyűjtemény a szállodák-quickstart index célozza, és csatolja az előző lépésben megadott lekérdezés.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Minden lépés futtatásához. Eredmények a következő kimenet hasonlóan kell kinéznie. 

    ![Keresés az indexekben](media/search-get-started-python/search-index.png "keresés az indexekben")

1. Próbálja meg néhány további lekérdezést példák betekintést nyerhet a szintaxis. Lecserélheti a `searchstring` az alábbi példák és majd futtassa újból a keresési kérelmet. 

   Szűrő alkalmazásához: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Végezze el a felső két eredmény:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Egy adott mezőben Rendezés:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása 

Ha már nincs szüksége lesz rá az index törölni kell. Egy ingyenes szolgáltatás három indexre korlátozódik. Ön nem használja aktívan, hogy helyet biztosítson a többi indexek törölni kell.

Objektumok törlése a legegyszerűbben a portálon keresztül, de mivel Python gyors üzembe helyezés, a következő szintaxist poskytne ugyanaz az eredmény:

   ```python
  url = endpoint + "indexes/hotels-quickstart" + api_version
  response  = requests.delete(url, headers=headers)
   ```

Index törlése ellenőrizheti a létező indexek listájának lekérésekor. Ha a "Hotels" – rövid útmutató eltűnt, majd ismeri a kérelem sikeres volt.

```python
url = endpoint + "indexes" + api_version + "&$select=name"

response  = requests.get(url, headers=headers)
index_list = response.json()
pprint(index_list)
```

## <a name="next-steps"></a>További lépések

Egyszerűsítését, mint az ebben a rövid útmutatóban a "Hotels" index rövidített verzióját használja. A teljes verzió érdekesebb lekérdezések kipróbálására is létrehozhat. A teljes verzió és minden 50 dokumentum lekéréséhez futtassa a **adatimportálás** varázslót, és válassza *hotels-sample* beépített minta adatforrásokból.

> [!div class="nextstepaction"]
> [Rövid útmutató: Index létrehozása az Azure Portalon](search-get-started-portal.md)
