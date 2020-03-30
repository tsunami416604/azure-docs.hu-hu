---
title: 'Rövid útmutató: Keresési index létrehozása postásban REST API-k használatával'
titleSuffix: Azure Cognitive Search
description: Ebben a REST API-rövid útmutatóban megtudhatja, hogyan hívhatja meg az Azure Cognitive Search REST API-kat postás és mintaadatok és definíciók használatával.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: c502886aac9d13f7a470a9b83f1fc12334913beb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121630"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-postman-using-rest-apis"></a>Rövid útmutató: Hozzon létre egy Azure Cognitive Search indexet a Postmanben rest API-k használatával
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C #](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portál](search-get-started-portal.md)
> * [Powershell](search-howto-dotnet-sdk.md)
>*

Az [Azure Cognitive Search REST API-k](https://docs.microsoft.com/rest/api/searchservice) megismerésének egyik legegyszerűbb módja a Postman vagy egy másik webes tesztelési eszköz használata a HTTP-kérések megfogalmazásához és a válaszok vizsgálatához. A megfelelő eszközökkel, ezeket az útmutatásokat követve már azelőtt küldhet kéréseket, és megtekintheti a válaszokat, hogy akár egyetlen sornyi kódot is írna.

Ez a cikk bemutatja, hogyan fogalmazható meg interaktívan a kérelmek. Azt is megteheti, hogy [letölti és importálja a Postman-gyűjteményt](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) az előre definiált kérések használatához.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A következő szolgáltatásokra és eszközökre van szükség ehhez a rövid útmutatóhoz. 

+ [Postman asztali alkalmazás](https://www.getpostman.com/) az Azure Cognitive Search kérelmek küldésére szolgál.

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. A rövid útmutatóhoz ingyenes szolgáltatást használhat. 

## <a name="get-a-key-and-url"></a>Kulcs és URL beszerezése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. A keresési szolgáltatás mindkettővel jön létre, így ha hozzáadta az Azure Cognitive Search-et az előfizetéséhez, kövesse az alábbi lépéseket a szükséges információk beszerezéséhez:

1. [Jelentkezzen be az Azure Portalon,](https://portal.azure.com/)és a keresési szolgáltatás **áttekintése** lapon lekell szereznie az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások** > **kulcsok**párbeszédpanelen szerezzen be egy rendszergazdai kulcsot a szolgáltatás teljes jogához. Két cserélhető rendszergazdai kulcs van, amelyek az üzletmenet folytonosságát biztosítják arra az esetre, ha át kell görgetnie egyet. Az elsődleges vagy másodlagos kulcsot objektumok hozzáadására, módosítására és törlésére irányuló kérelmeken használhatja.

![HTTP-végpont és hozzáférési kulcs beszerezni](media/search-get-started-postman/get-url-key.png "HTTP-végpont és hozzáférési kulcs beszerezni")

Minden kérelemhez api-kulcs szükséges a szolgáltatásnak küldött minden kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="connect-to-azure-cognitive-search"></a>Csatlakozás az Azure Cognitive Search szolgáltatáshoz

Ebben a szakaszban használja a választott webes eszközt az Azure Cognitive Search kapcsolatok beállításához. Minden eszköz megőrzi a munkamenet fejlécének adatait, ami azt jelenti, hogy csak egyszer kell megadnia az api-kulcsot és a Content-Type-ot.

Bármelyik eszköz höz ki kell választania egy parancsot (GET, POST, PUT stb.), meg kell adnia egy URL-végpontot, és bizonyos feladatokhoz meg kell adnia a JSON-t a kérelem törzsében. Cserélje le a keresési szolgáltatás nevét (YOUR-SEARCH-SERVICE-NAME) érvényes értékre. Add `$select=name` vissza csak a nevét az egyes indexek. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Figyelje meg a HTTPS-előtagot, a szolgáltatás nevét, egy objektum nevét (ebben az esetben az indexgyűjteményt) és az [api-verziót.](search-api-versions.md) Az api-verzió egy szükséges, kisméretű `?api-version=2019-05-06` karakterlánc, amely et az aktuális verzióhoz hasonlóan adták meg. Az API-verziókat a rendszer rendszeresen frissíti. Az API-verziót minden kérelemnél adja meg, hogy teljes mértékben szabályozhassa, a rendszer mikor melyik verziót használja.  

A kérelemfejléc-összeállítás két elemet tartalmaz, a tartalomtípust, valamint az Azure Cognitive Search hitelesítéséhez használt api-kulcsot. Cserélje le a felügyeleti API-kulcsot (YOUR-AZURE-SEARCH-ADMIN-API-KEY) érvényes értékre. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

A Postman, fogalmazzon meg egy kérelmet, hogy néz ki, mint a következő screenshot. Válassza a **GET** mint ige lehetőséget, adja meg az URL-címet, majd kattintson a **Küldés**gombra. Ez a parancs csatlakozik az Azure Cognitive Search, beolvassa az indexek gyűjteménye, és visszaadja a HTTP-állapotkód 200 egy sikeres kapcsolat. Ha a szolgáltatás indexek már, a válasz is tartalmazni fogja az index definíciók.

![Postás kérelem URL-címe és fejléce](media/search-get-started-postman/postman-url.png "Postás kérelem URL-címe és fejléce")

## <a name="1---create-an-index"></a>1 – Index létrehozása

Az Azure Cognitive Search általában hozza létre az indexet, mielőtt betölti az adatokkal. Az [Index REST LÉTREHOZÁSA API-t](https://docs.microsoft.com/rest/api/searchservice/create-index) használja ehhez a feladathoz. 

Az URL-cím ki `hotels` van terjesztve az index nevével.

Ehhez a Postman:

1. Módosítsa az igét **PUT**névre.

2. Másolás ebben `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06`az URL-ben .

3. Adja meg az indexdefiníciót (a másolásra kész kódot alább találja) a kérelem törzsében.

4. Kattintson a **Küldés** gombra.

![Index JSON-dokumentum a kérelemtörzsben](media/search-get-started-postman/postman-request.png "Index JSON-dokumentum a kérelemtörzsben")

### <a name="index-definition"></a>Index definíciója

A mezőgyűjtemény határozza meg a dokumentum szerkezetét. Minden dokumentumnak rendelkeznie kell ezekkel a mezőkkel, és minden mezőnek adattípussal kell rendelkeznie. A keresésekben a sztringekat tartalmazó mezők vesznek részt, ezért a numerikus adatokat is érdemes sztringként beállítani, ha azt szeretné, hogy a keresés kiterjedjen rájuk.

Az egyes mezők attribútumai határozzák meg az engedélyezett műveleteket. A REST API-k alapértelmezés szerint számos műveletet lehetővé tesznek. Például alapértelmezés szerint minden sztring kereshető, lekérdezhető, szűrhető és kategorizálható. Gyakran csak akkor kell attribútumokat beállítania, ha ki kell kapcsolnia egy viselkedést.

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

## <a name="2---load-documents"></a>2 - Dokumentumok betöltése

Az index létrehozása és adatokkal való feltöltése két különböző lépés. Az Azure Cognitive Search, az index tartalmazza az összes kereshető adatokat, amelyek json-dokumentumokként is megadhat. Ehhez a feladathoz [a Dokumentumok hozzáadása, frissítése vagy törlése REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) használatos. 

Az URL-cím a `docs` gyűjtemények `index` és a műveletek közé tartozik.

Ehhez a Postman:

1. Cserélje a parancsot a **POST** parancsra.

2. Másolás ebben `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`az URL-ben .

3. Adja meg a JSON dokumentumokat (a másolásra kész kód alább van) a kérelem törzsében.

4. Kattintson a **Küldés** gombra.

![JSON-dokumentumok a kérelemtörzsben](media/search-get-started-postman/postman-docs.png "JSON-dokumentumok a kérelemtörzsben")

### <a name="json-documents-to-load-into-the-index"></a>JSON-dokumentumok betöltése az indexbe

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

Néhány másodperc en belül meg kell jelennie egy HTTP 201 válasz a munkamenet listában. Ez azt jelenti, hogy a dokumentumok sikeresen létrejöttek. 

Ha a 207-es HTTP-válasz jelenik meg, legalább egy dokumentumot nem sikerült feltölteni. Ha a 404-es válasz jelenik meg, akkor a kérelem fejlécében vagy törzsében szintaktikai hiba van: ellenőrizze, hogy valóban módosította-e a végpontot, hogy tartalmazza a `/docs/index` útvonalat.

> [!Tip]
> A kiválasztott adatforrások esetében választhatja az alternatív *indexelő* megközelítést, amely egyszerűbb, és csökkenti az indexáláshoz szükséges kódot. További információkért lásd: [Indexelőműveletek](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Most, hogy egy index és a dokumentumok betöltődnek, a [Search Documents REST API használatával](https://docs.microsoft.com/rest/api/searchservice/search-documents)lekérdezéseket adhat ki velük szemben.

Az URL-cím kiterjesztve egy lekérdezési kifejezéssel is, amelyet a keresési operátor ral adnak meg.

Ehhez a Postman:

1. Módosítsa az igét **get**gombra.

2. Másolás ebben `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`az URL-ben .

3. Kattintson a **Küldés** gombra.

Ez a lekérdezés üres, és a keresési eredményekben szereplő dokumentumok számát adja vissza. A kérelemnek és a válasznak a Küldés gombra kattintás után a Postman következő képernyőképéhez hasonlóan kell **kinéznie.** Az állapotkódnak 200-nak kell lennie.

 ![GET az URL-címen lévő keresési karakterlánccal](media/search-get-started-postman/postman-query.png "GET az URL-címen lévő keresési karakterlánccal")

Próbáljon ki néhány más lekérdezési példát, hogy megismerjék a szintaxist. Megteheti a karakterlánc-keresést, szó szerint $filter lekérdezéseket, korlátozhatja az eredményhalmazt, a keresést adott mezőkre és egyebekre is kihasználhatja.

Cserélje ki az aktuális URL-t az alábbiakkal, és minden alkalommal a **Küldés** gombra kattintva tekintse meg az eredményeket.

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

## <a name="get-index-properties"></a>Indextulajdonságok beszereznie
A Statisztika [bekérdezése](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) segítségével lekérdezheti a dokumentumok számát és az index méretét: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06
```

Az `/stats` URL-címhez való hozzáadás indexadatokat ad vissza. A Postmanben a kérelem az alábbihoz fog hasonlítani, a válaszban pedig szerepleni fog a dokumentumok száma és a felhasznált lemezterület mérete bájtban megadva.

 ![Indexadatok beszereznie](media/search-get-started-postman/postman-system-query.png "Indexadatok beszereznie")

Figyelje meg, hogy az api-version szintaxisa eltér. Ebben a kérelemben a `?` karakterrel fűzheti hozzá az api-version paramétert. A `?` rendszer elválasztja az URL elérési útját a lekérdezési karakterlánctól, míg & a lekérdezési karakterláncban minden egyes "name=value" párt elválasztja. Ebben a lekérdezésben az api-version az első és egyetlen lekérdezési sztring.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak **Minden erőforrás** vagy **Erőforráscsoport** hivatkozásával.

Ha ingyenes szolgáltatást használ, ne feledje, hogy három indexelésre, indexelőre és adatforrásra van korlátozva. Törölheti az egyes elemeket a portálon, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Most, hogy már tudja, hogyan kell elvégezni az alapvető feladatokat, további REST API-val további REST API-hívásokat tehet le, például indexelőket vagy [kognitív keresési folyamatot.](cognitive-search-tutorial-blob.md) A következő lépéshez az alábbi linket javasoljuk:

> [!div class="nextstepaction"]
> [REST-oktatóanyag: Félig strukturált adatok (JSON-blobok) indexelése és keresése az Azure Cognitive Search szolgáltatásban](search-semi-structured-data.md)
