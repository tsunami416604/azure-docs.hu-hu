---
title: 'Python rövid útmutató: Indexek létrehozása, betöltése és lekérdezése REST API-k használatával – Azure Search'
description: Ismerteti, hogyan hozhat létre indexet, tölthet be és futtathat lekérdezéseket Python, Jupyter notebookok és a Azure Search REST API használatával.
ms.date: 07/11/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.openlocfilehash: 9e12133179ca02c3474e768305feb8ae19a6178e
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840762"
---
# <a name="quickstart-create-an-azure-search-index-in-python-using-jupyter-notebooks"></a>Gyors útmutató: Azure Search index létrehozása a Pythonban Jupyter-jegyzetfüzetek használatával
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Poster (REST)](search-get-started-postman.md)
> * [Portál](search-create-index-portal.md)
> 

Hozzon létre egy Jupyter-jegyzetfüzetet, amely létrehoz, betölt és lekérdez egy Azure Search indexet a Python és a [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)-k használatával. Ez a cikk bemutatja, hogyan hozhat létre egy jegyzetfüzetet lépésről lépésre. Azt is megteheti, hogy [letölti és futtatja a kész Jupyter Python notebookot](https://github.com/Azure-Samples/azure-search-python-samples).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következő szolgáltatások és eszközök szükségesek. 

+ [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section), amely Python 3. x és Jupyter jegyzetfüzeteket biztosít.

+ [Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz az ingyenes szintet használhatja. 

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások** > **kulcsaiban**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

![Http-végpont és elérési kulcs](media/search-get-started-postman/get-url-key.png "Http-végpont és elérési kulcs") beszerzése

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="connect-to-azure-search"></a>Kapcsolódás Azure Searchhoz

Ebben a feladatban indítson el egy Jupyter-jegyzetfüzetet, és ellenőrizze, hogy tud-e csatlakozni Azure Searchhoz. Ezt úgy teheti meg, hogy az indexek listáját kéri le a szolgáltatásból. A Anaconda3-mel rendelkező Windows rendszeren a anaconda Navigator használatával indíthat el egy jegyzetfüzetet.

1. Hozzon létre egy új Python3 notebookot.

1. Az első cellában töltse be a JSON-kezeléshez használt kódtárakat és a HTTP-kérelmek összeállítását.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. A második cellában adja meg azokat a kérelmeket, amelyek minden kérelemnél állandók lesznek. Cserélje le a keresési szolgáltatás nevét (a-SEARCH-SERVICE-NAME) és a felügyeleti API-kulcsot (a-ADMIN-API-KEY) érvényes értékekkel. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. A harmadik cellában alakítsa ki a kérelmet. Ez a GET kérelem célja a keresési szolgáltatás indexek gyűjteménye, és kiválasztja a meglévő indexek Name (név) tulajdonságát.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Futtassa az egyes lépéseket. Ha az indexek léteznek, a válasz az indexek neveinek listáját tartalmazza. Az alábbi képernyőképen a szolgáltatás már rendelkezik egy azureblob és egy Realestate-US-Sample indextel.

   ![Python-szkript a Jupyter notebookon a Azure Search http-kérésekkel](media/search-get-started-python/connect-azure-search.png "Python-szkript a Jupyter notebookon a Azure Search http-kérésekkel")

   Ezzel szemben egy üres index-gyűjtemény adja vissza ezt a választ:`{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 – Index létrehozása

Ha nem használja a portált, akkor az adatgyűjtés előtt léteznie kell egy indexnek a szolgáltatáson. Ez a lépés az index [létrehozása REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) használatával küldi el az indexelési sémát a szolgáltatásnak.

Az index kötelező elemei közé tartozik a név, a mezők gyűjteménye és a kulcs. A mezők gyűjteménye meghatározza a *dokumentumok*szerkezetét. Minden mező rendelkezik egy névvel, típussal és attribútummal, amely meghatározza a mező használatát (például hogy teljes szöveges kereshető, szűrhető vagy kereshető a keresési eredmények között). Egy indexen belül az egyik típusú `Edm.String` mezőt meg kell jelölni a dokumentum-identitás *kulcsaként* .

Az index neve "Hotels-Gyorsindítás", és az alább látható mező-definíciók szerepelnek. Ez egy nagyobb, más forgatókönyvekben használt [szállodák indexének](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) részhalmaza. Ebben a rövid útmutatóban lerövidítjük.

1. A következő cellában illessze be a következő példát egy cellába a séma megadásához. 

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

2. Egy másik cellában alakítsa ki a kérelmet. Ez a PUT-kérelem a keresési szolgáltatás indexek gyűjteményét célozza meg, és az előző cellában megadott index séma alapján létrehoz egy indexet.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Futtassa az egyes lépéseket.

   A válasz tartalmazza a séma JSON-ábrázolását. Az alábbi képernyőképen csak a válasz egy része látható.

    ![Index létrehozásához szükséges kérelem](media/search-get-started-python/create-index.png "Index létrehozásához szükséges kérelem")

> [!Tip]
> Az indexek létrehozásának egy másik módja az indexek listájának ellenőrzése a portálon.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

A dokumentumok leküldéséhez használjon HTTP POST-kérést az index URL-címének végpontján. A REST API [dokumentumok hozzáadása, frissítése vagy törlése](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). A dokumentumok a GitHubon lévő [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) származnak.

1. Egy új cellában négy olyan dokumentumot adjon meg, amelyek megfelelnek az index sémának. Minden dokumentumhoz meg kell adni egy feltöltési műveletet.

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

2. Egy másik cellában alakítsa ki a kérelmet. Ez a POST-kérelem a Hotels-gyors ismertető indexét célozza meg, és leküldi az előző lépésben megadott dokumentumokat.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Futtassa az egyes lépéseket a dokumentumok a keresési szolgáltatásban lévő indexbe való leküldéséhez. Az eredményeknek az alábbi példához hasonlóan kell kinéznie. 

    ![Dokumentumok elküldése egy indexbe](media/search-get-started-python/load-index.png "Dokumentumok elküldése egy indexbe")

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Ez a lépés bemutatja, hogyan kérdezheti le az indexeket a [keresési dokumentumok REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)használatával.

1. Egy cellában adjon meg egy olyan lekérdezési kifejezést, amely üres keresést hajt végre (Search = *), és nem rangsorolt listát (keresési pontszám = 1,0) ad vissza tetszőleges dokumentumokhoz. Alapértelmezés szerint a Azure Search a 50-es egyezést adja vissza egyszerre. Strukturált módon a lekérdezés egy teljes dokumentum-struktúrát és-értéket ad vissza. Adja hozzá a $count = True értéket az eredményekben található összes dokumentum számának beolvasásához.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. Egy új cellában adja meg a következő példát a "Hotels" és a "WiFi" kifejezésre való kereséshez. $Select hozzáadása lehetőséggel megadhatja, hogy mely mezők szerepeljenek a keresési eredmények között.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. Egy másik cellában állítson össze egy kérelmet. Ehhez a GET kérelemhez a Hotels-Gyorsindítás index, valamint az előző lépésben megadott lekérdezés csatolva van.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Futtassa az egyes lépéseket. Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie. 

    ![Keresés az indexben](media/search-get-started-python/search-index.png "Keresés az indexben")

1. Néhány további lekérdezési példát is kipróbálhat a szintaxis megszerzéséhez. A következő példákkal `searchstring` lecserélheti a kifejezést, majd újra futtathatja a keresési kérelmet. 

   Szűrő alkalmazása: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Tegye meg az első két eredményt:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Sorrend egy adott mező szerint:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén érdemes megállapítani, hogy továbbra is szüksége van-e a létrehozott erőforrásokra. A már futó erőforrások pénzbe kerülnek. Az erőforrásokat egyenként is törölheti, vagy az erőforráscsoport törlésével törölheti a teljes erőforrás-készletet.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Ennek egyszerűsítése érdekében ez a rövid útmutató a szállodák indexének rövidített verzióját használja. A teljes verziót a további érdekes lekérdezések kipróbálásához is létrehozhatja. A teljes verzió és az összes 50-dokumentum lekéréséhez futtassa az **adatok importálása** varázslót, és válassza a *Hotels-Sample* lehetőséget a beépített mintául szolgáló adatforrásokból.

> [!div class="nextstepaction"]
> [Rövid útmutató: Index létrehozása a Azure Portalban](search-get-started-portal.md)
