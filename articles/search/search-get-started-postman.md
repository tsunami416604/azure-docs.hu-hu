---
title: 'Gyors útmutató: keresési index létrehozása a Poster-ben REST API-k használatával – Azure Search'
description: Megtudhatja, hogyan hívhatja meg a Azure Search REST API-kat a Poster és a mintaadatok és a definíciók használatával.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 09/10/2019
ms.author: heidist
ms.openlocfilehash: ffa20599ae57908f9b0ea848ab68f41a3d0e2a14
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176040"
---
# <a name="quickstart-create-an-azure-search-index-in-postman-using-rest-apis"></a>Rövid útmutató: Azure Search index létrehozása a Poster-ben REST API-k használatával
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

A [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice) -k megismerésének egyik legegyszerűbb módja a Poster vagy egy másik webes tesztelési eszköz használata a HTTP-kérelmek összeállításához és a válaszok vizsgálatához. A megfelelő eszközökkel, ezeket az útmutatásokat követve már azelőtt küldhet kéréseket, és megtekintheti a válaszokat, hogy akár egyetlen sornyi kódot is írna.

Ez a cikk bemutatja, hogyan lehet interaktív módon megfogalmazni a kérelmeket. Azt is megteheti, hogy [letölti és importálja a Poster-gyűjteményt](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) az előre meghatározott kérések használatára.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következő szolgáltatások és eszközök szükségesek. 

+ A [Poster Desktop alkalmazás](https://www.getpostman.com/) a kérelmek Azure Search való küldésére szolgál.

+ [Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**területen szerezze be a szolgáltatásra vonatkozó teljes körű jogosultságokat. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

![Http-végpont és hozzáférési kulcs]beszerzése(media/search-get-started-postman/get-url-key.png "http-végpont és elérési kulcs beolvasása")

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="connect-to-azure-search"></a>Kapcsolódás Azure Searchhoz

Ebben a szakaszban a saját webes eszközével állíthatja be a Azure Search kapcsolatait. Minden eszköz megőrzi a kérelmek fejlécére vonatkozó információkat a munkamenethez, ami azt jelenti, hogy csak egyszer kell megadnia az API-kulcs és a Content-Type típust.

Bármelyik eszközhöz ki kell választania egy parancsot (GET, POST, PUT stb.), meg kell adnia egy URL-végpontot, és egyes feladatokhoz meg kell adni a JSON-t a kérelem törzsében. Cserélje le a keresési szolgáltatás nevét (a-SEARCH-SERVICE-NAME) érvényes értékre. Adja hozzá a `$select=name` értéket, hogy csak az egyes indexek nevét adja vissza. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Figyelje meg a HTTPS-előtagot, a szolgáltatás nevét, az objektum nevét (ebben az esetben az indexek gyűjteményét) és az [API-verziót](search-api-versions.md). Az API-Version kötelező, a jelenlegi verzióhoz `?api-version=2019-05-06` értékként megadott kisbetűs karakterlánc. Az API-verziók frissítése rendszeresen megtörténik. Az API-verziót minden kérelemnél adja meg, hogy teljes mértékben szabályozhassa, a rendszer mikor melyik verziót használja.  

A kérelem fejlécének összetétele két elemet tartalmaz: a tartalom típusát, valamint a Azure Search hitelesítéséhez használt API-kulcsot. Cserélje le a felügyeleti API-kulcsot (az-AZURE-SEARCH-ADMIN-API-KEY) érvényes értékre. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

A Poster-ben állítson össze egy, az alábbi képernyőképen láthatóhoz hasonló kérelmet. Válassza a **beolvasás** művelet lehetőséget, adja meg az URL-címet, majd kattintson a **Küldés**gombra. Ez a parancs csatlakozik a Azure Searchhoz, beolvassa az indexek gyűjteményt, és visszaadja a 200-es HTTP-állapotkódot egy sikeres kapcsolaton. Ha a szolgáltatás már rendelkezik indexekkel, a válasz tartalmazni fog index-definíciókat is.

![Poster-kérelem URL-címe és fejléce](media/search-get-started-postman/postman-url.png "postára vonatkozó kérelem URL-címe és fejléce")

## <a name="1---create-an-index"></a>1 – Index létrehozása

Azure Search általában az indexet hozza létre az adatbevitel előtt. Ehhez a feladathoz a [create Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) van használatban. 

Az URL-cím kiterjeszthető, hogy tartalmazza a `hotels` index nevét.

Ehhez tegye a következőket:

1. Módosítsa a műveletet a **put**értékre.

2. Másolás ezen az URL-címen `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06`.

3. Adja meg az index definícióját (a másolásra kész kód a lenti) a kérelem törzsében.

4. Kattintson a **Küldés**gombra.

![JSON-dokumentum indexelése a]kérelem törzsében a(media/search-get-started-postman/postman-request.png "JSON-dokumentum indexelése a kérelem törzsében")

### <a name="index-definition"></a>Index definíciója

A mezők gyűjteménye határozza meg a dokumentum szerkezetét. Minden dokumentumnak tartalmaznia kell ezeket a mezőket, és minden mezőnek adattípussal kell rendelkeznie. A keresésekben a sztringekat tartalmazó mezők vesznek részt, ezért a numerikus adatokat is érdemes sztringként beállítani, ha azt szeretné, hogy a keresés kiterjedjen rájuk.

Az egyes mezők attribútumai határozzák meg az engedélyezett műveleteket. A REST API-k alapértelmezés szerint számos műveletet lehetővé tesznek. Például alapértelmezés szerint minden sztring kereshető, lekérdezhető, szűrhető és kategorizálható. Gyakran csak akkor kell attribútumokat beállítania, ha ki kell kapcsolni a viselkedést.

```json
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
```

A kérelem elküldését követően megjelenik a 201-es HTTP-válasz, amely azt jelzi, hogy az index sikeresen létrejött. A műveletet ellenőrizheti a portálon, azonban vegye figyelembe, hogy a portáloldal frissítési időköze miatt esetleg egy-két percet várakoznia kell.

> [!TIP]
> Ha 504-es HTTP-választ kap, ellenőrizze, hogy az URL-címben a HTTPS előtag van-e megadva. Ha 400-as vagy 404-es HTTP-válasz jelenik meg, akkor ellenőrizze, hogy a kérelem törzsében nincsenek-e beillesztési hibák. A 403-as HTTP-válasz általában az API-kulccsal kapcsolatos hibát jelez (érvénytelen a kulcs vagy szintaktikai hiba van az API-kulcs meghatározásában).

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Az index létrehozása és adatokkal való feltöltése két különböző lépés. Az Azure Searchben az index tartalmazza az összes kereshető adatot, amelyeket JSON-dokumentumok formájában adhat be. Ehhez a feladathoz a [Hozzáadás, frissítés vagy törlés dokumentumok REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) használják. 

Az URL-cím kiterjeszthető, hogy tartalmazza a `docs` gyűjtemények és a `index` műveletet.

Ehhez tegye a következőket:

1. Cserélje a parancsot a **POST** parancsra.

2. Másolás ezen az URL-címen `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`.

3. Adja meg a kérelem törzsében a JSON-dokumentumokat (a másolásra kész kód alatt).

4. Kattintson a **Küldés**gombra.

![JSON-dokumentumok a kérelem]törzsében lévő(media/search-get-started-postman/postman-docs.png "JSON-dokumentumokban")

### <a name="json-documents-to-load-into-the-index"></a>Az indexbe betöltendő JSON-dokumentumok

A kérelem törzse négy, a szállodák indexéhez hozzáadandó dokumentumot tartalmaz.

```json
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
```

Néhány másodpercen belül megjelenik egy HTTP 201-válasz a munkamenetek listájában. Ez azt jelenti, hogy a dokumentumok sikeresen létrejöttek. 

Ha a 207-es HTTP-válasz jelenik meg, legalább egy dokumentumot nem sikerült feltölteni. Ha a 404-es válasz jelenik meg, akkor a kérelem fejlécében vagy törzsében szintaktikai hiba van: ellenőrizze, hogy valóban módosította-e a végpontot, hogy tartalmazza a `/docs/index` útvonalat.

> [!Tip]
> A kiválasztott adatforrások esetében választhatja az alternatív *indexelő* megközelítést, amely egyszerűbb, és csökkenti az indexáláshoz szükséges kódot. További információkért lásd: [Indexelőműveletek](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Most, hogy az index és a dokumentumok betöltődik, a [keresési dokumentumok REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)használatával küldhet lekérdezéseket.

Az URL-cím kiterjesztése a keresési operátor használatával megadott lekérdezési kifejezésre.

Ehhez tegye a következőket:

1. Módosítsa a **lekérdezni**kívánt műveletet.

2. Másolás ezen az URL-címen `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`.

3. Kattintson a **Küldés**gombra.

Ez a lekérdezés üres, és a keresési eredményekben szereplő dokumentumok számát adja vissza. A kérésnek és a válasznak a Poster következő képernyőképéhez hasonlóan kell kinéznie, miután rákattintott a **Send (Küldés**) gombra. Az állapotkódnak 200-nak kell lennie.

 ![Keresési karakterlánc beolvasása az URL-]cím(media/search-get-started-postman/postman-query.png "lekérdezése keresési karakterlánccal az URL-címen")

Néhány további lekérdezési példát is kipróbálhat a szintaxis megszerzéséhez. Megteheti a karakterláncos keresést, a Verbatim $filter lekérdezéseket, korlátozhatja az eredmények készletét, kihasználhatja a keresést adott mezőkre, és így tovább.

A jelenlegi URL-címet cserélje le az alábbira, majd kattintson a **Küldés** minden alkalommal elemre az eredmények megtekintéséhez.

```
# Query example 1 - Search on restaurant and wifi
# Return only the HotelName, Description, and Tags fields
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=restaurant wifi&$count=true&$select=HotelName,Description,Tags&api-version=2019-05-06

# Query example 2 - Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$filter=Rating gt 4&$select=HotelName,Rating&api-version=2019-05-06

# Query example 3 - Take the top two results, and show only HotelName and Category in the results
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=boutique&$top=2&$select=HotelName,Category&api-version=2019-05-06

# Query example 4 - Sort by a specific field (Address/City) in ascending order
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating&api-version=2019-05-06
```

## <a name="get-index-properties"></a>Index tulajdonságainak beolvasása
A [lekérési statisztikákat](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) is használhatja a dokumentumok számának és az index méretének lekérdezéséhez: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06
```

Ha `/stats` értéket ad hozzá az URL-címhez, az index információit adja vissza. A Postmanben a kérelem az alábbihoz fog hasonlítani, a válaszban pedig szerepleni fog a dokumentumok száma és a felhasznált lemezterület mérete bájtban megadva.

 ![Index]információinak lekérése(media/search-get-started-postman/postman-system-query.png "index információi")

Figyelje meg, hogy az api-version szintaxisa eltér. Ebben a kérelemben a `?` karakterrel fűzheti hozzá az api-version paramétert. A `?` elválasztja az URL elérési útját a lekérdezési karakterlánctól, míg a & elválasztja a lekérdezési karakterláncban szereplő "Name = Value" párokat. Ebben a lekérdezésben az api-version az első és egyetlen lekérdezési sztring.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén érdemes megállapítani, hogy továbbra is szüksége van-e a létrehozott erőforrásokra. A már futó erőforrások pénzbe kerülnek. Az erőforrásokat egyenként is törölheti, vagy az erőforráscsoport törlésével törölheti a teljes erőforrás-készletet.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan hajthat végre alapszintű feladatokat, további REST API hívásokat is használhat további speciális funkciókkal, például indexelő szolgáltatásokkal vagy [egy kognitív keresési folyamat beállításával](cognitive-search-tutorial-blob.md). A következő lépésként a következő hivatkozást javasoljuk:

> [!div class="nextstepaction"]
> [REST-oktatóanyag: részben strukturált adatok (JSON-Blobok) indexelése és keresése Azure Search](search-semi-structured-data.md)
