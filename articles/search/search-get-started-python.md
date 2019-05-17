---
title: 'Gyors útmutató: Python és a REST API-k – Azure Search szolgáltatásban'
description: Hozzon létre, betöltését és a Python, a Jupyter notebookok és az Azure Search REST API-index lekérdezése.
ms.date: 05/15/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 1ab6bb069f60f4d2dbb4cfaecda54c3c2ef20adc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806431"
---
# <a name="quickstart-create-an-azure-search-index-using-jupyter-python-notebooks"></a>Gyors útmutató: Jupyter Python notebookok használatával egy Azure Search-index létrehozása
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Portál](search-create-index-portal.md)
> 

Hozhat létre, amely hoz létre, betöltése és lekérdezése az Azure Search Jupyter notebook [index](search-what-is-an-index.md) pythonnal és a [Azure Search szolgáltatás REST API-k](https://docs.microsoft.com/rest/api/searchservice/). Ez a cikk azt ismerteti, hogyan hozhat létre saját notebook lépésről lépésre. Igény szerint futtathatja egy befejezett notebookot. Ha szeretné letölteni, nyissa meg [Azure-Search-python-samples-tárház](https://github.com/Azure-Samples/azure-search-python-samples).

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a munka megkezdése előtt, majd [regisztrálhat az Azure Searchre](search-create-service-portal.md).

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató a következő szolgáltatásokat és eszközöket használatosak. 

+ [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben a rövid útmutatóban egy ingyenes szolgáltatás használhatja. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), így a Python 3.x és Jupyter-Notebookjait.

## <a name="get-a-key-and-url"></a>Egy kulcsot és egy URL-cím beszerzése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-fiddler/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="connect-to-azure-search"></a>Kapcsolódás az Azure Search

Jupyter notebook megnyitásához, és ellenőrizze a helyi munkaállomáson való kapcsolat által a szolgáltatás indexei lista lekérése. Windows-Anaconda3 Anaconda-kezelő segítségével indítsa el a notebookot.

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

1. A harmadik cellába állítson össze a kérelmet. A GET-kérés a keresési szolgáltatás indexek gyűjtésére irányul, és kiválasztja a name tulajdonság.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Minden lépés futtatásához. Ha létezik indexek, a válasz indexek listáját tartalmazza. Az alábbi képernyőképen a szolgáltatás része az Azure BLOB-index és a egy realestate-us-sample-index.

   ![Python-szkriptet a Jupyter notebook HTTP-kérelmek Azure Search](media/search-get-started-python/connect-azure-search.png "Python-szkriptet a Jupyter notebook HTTP-kérelmek Azure Search")

   Az index üres gyűjteményt adja vissza ezt a választ: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

> [!Tip]
> Egy ingyenes szolgáltatás, az Ön legfeljebb három indexek, indexelők és adatforrások. Ez a rövid útmutató létrehoz egy. Győződjön meg arról, hogy a hely az új objektumokat hozhat létre, mielőtt továbblép bármely.

## <a name="1---create-an-index"></a>1 – Index létrehozása

A portál használata, az index léteznie kell a szolgáltatás adatok betöltése előtt. Ebben a lépésben használja a [Index REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) paranccsal küldje le a szolgáltatás létrehozása

A mezők gyűjteménye határozza meg a szerkezete egy *dokumentum*. Az index szükséges elemek közé tartozik, egy nevet és egy mezőt. Minden mező rendelkezik egy név, típus és attribútumokat, amelyek meghatározzák, hogyan használja fel azokat (például, hogy-e teljes szöveges kereshető, szűrhető vagy lekérhető a keresési eredmények között). Index, egyes típusú mezők belül `Edm.String` kijelölt a *kulcs* dokumentum identitás.

Ez az index neve "hotels-py" és a Meződefiníciók lentebb látható. A nagyobb része ["Hotels" index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) használt egyéb forgatókönyvek. Hogy vágott ebben a rövid útmutatóban kihagytuk.


1. A következő cellába illessze be az alábbi példa a sémát adjon meg egy cellában. 

    ```python
    index_schema = {
       "name": "hotels-py",  
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

2. Egy másik cellába állítson össze a kérelmet. A PUT kérés célozza meg a keresési szolgáltatás indexek gyűjteményét, és létrehoz egy indexet az indexsémát az előző lépésben megadott alapján.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Minden lépés futtatásához.

   A válasz tartalmazza a sémát a JSON-ábrázolását. Az alábbi képernyőképen az indexséma részeit levágja, láthatják többet a választ.

    ![Az index létrehozására vonatkozó kérelem](media/search-get-started-python/create-index.png "index létrehozására vonatkozó kérelem")

> [!Tip]
> Az ellenőrzéshez sikerült is a portálon az indexek listában, vagy a szolgáltatáskérés kapcsolat megtekintéséhez futtassa újból a *hotels-py* az indexek gyűjteményben felsorolt index.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Küldje le a dokumentumokat, használja a HTTP POST-kérelmet az index URL-cím végponthoz. A REST API [hozzáadása, frissítése vagy törlése dokumentumok](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Dokumentumok származik [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) a Githubon.

1. Egy új cellába adja meg, amelyek megfelelnek az indexséma három dokumentumokat. Adja meg az egyes dokumentumok feltöltési művelet.

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
      }
     ]
    }
    ```

2. Egy másik cellába állítson össze a kérelmet. A POST-kérelemhez célozza meg, a szállodák-py index docs gyűjteményét, majd leküldi az előző lépésben meghatározott dokumentumok.

   ```python
   url = endpoint + "indexes/hotels-py/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Futtassa az egyes lépések a dokumentum elküldése egy indexbe a search szolgáltatás. Eredmények az alábbi példához hasonlóan kell kinéznie. 

   ```
   {'@odata.context': "https://mydemo.search.windows.net/indexes('hotels-py')/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
    'value': [{'errorMessage': None,
            'key': '1',
            'status': True,
            'statusCode': 201},
           {'errorMessage': None,
            'key': '2',
            'status': True,
            'statusCode': 201},
           {'errorMessage': None,
            'key': '3',
            'status': True,
            'statusCode': 201}]}
     ```


## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Ez a lépés bemutatja, hogyan kérdezhet le egy index használatával a [Search dokumentumok REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).


1. Adja meg a lekérdezés egy új cellára. Az alábbi példa a használati "hotels" és "Wi-Fi" keres. Azt adja vissza egy *száma* egyeznek, dokumentumok és *kiválasztja* mezőket a keresési eredmények tartalmazzák.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

2. Állítson össze egy kérelmet. A GET kérelem a docs-gyűjteményt a szállodák-py index célozza, és csatolja az előző lépésben megadott lekérdezés.

   ```python
   url = endpoint + "indexes/hotels-py/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

   Eredmények a következő kimenet hasonlóan kell kinéznie.

   ```
   {'@odata.context': "https://mydemo.search.windows.net/indexes('hotels-py')/$metadata#docs(*)",
    '@odata.count': 3,
    'value': [{'@search.score': 1.0,
               'HotelId': '1',
               'HotelName': 'Secret Point Motel'},
              {'@search.score': 1.0,
               'HotelId': '2',
               'HotelName': 'Twin Dome Motel'},
              {'@search.score': 1.0,
               'HotelId': '3',
               'HotelName': 'Triple Landscape Hotel'}]}
   ```

3. Próbálja meg néhány további lekérdezést példák betekintést nyerhet a szintaxis. Szűrő alkalmazása, az első két találatot is magával egy adott mezőben rendezés vagy 

   + `searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description'`

   + `searchstring = '&search=hotel&$top=2&$select=HotelId,HotelName,Description'`

   + `searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'`

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása 

Ha már nincs szüksége lesz rá az index törölni kell. Egy ingyenes szolgáltatás három indexre korlátozódik. Előfordulhat, hogy törölni kívánja azt nem használja aktívan, hogy helyet biztosítson a többi indexekkel.

   ```python
  url = endpoint + "indexes/hotels-py" + api_version
  response  = requests.delete(url, headers=headers)
   ```

Index törlése ellenőrizheti, ha a meglévő indexeket listájának visszaadása. Ha a "Hotels"-py eltűnt, majd ismeri a kérelem sikeres volt.

```python
url = endpoint + "indexes" + api_version + "&$select=name"

response  = requests.get(url, headers=headers)
index_list = response.json()
pprint(index_list)
```

## <a name="next-steps"></a>További lépések

További információ a lekérdezés szintaxisa és forgatókönyvek.

> [!div class="nextstepaction"]
> [Hozzon létre egy egyszerű lekérdezést](search-query-overview.md)
