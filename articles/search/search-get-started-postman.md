---
title: 'Gyors útmutató: Létrehozása, betöltése és lekérdezése az indexek Postman és a REST API-k – Azure Search használatával'
description: Ismerje meg, hogyan hívhat meg az Azure Search REST API-k Postman és a mintaadatokat és a definíciókat.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 07/11/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 015dd3631322978d6416041a3eea8390a72b0c17
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840199"
---
# <a name="quickstart-create-an-azure-search-index-in-postman-using-rest-apis"></a>Gyors útmutató: Azure Search-index létrehozása REST API-k használatával postmanben
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portál](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Az egyik legegyszerűbb Fedezze fel a [Azure Search REST API-k](https://docs.microsoft.com/rest/api/searchservice) állítson össze a HTTP-kérelmekre, és vizsgálja meg a válaszok Postman vagy egy másik webes tesztelési eszköz használatával. A megfelelő eszközökkel, ezeket az útmutatásokat követve már azelőtt küldhet kéréseket, és megtekintheti a válaszokat, hogy akár egyetlen sornyi kódot is írna.

Ez a cikk bemutatja, hogyan határozhatja meg interaktív módon kérelmeket. Lehetőségként [töltse le és importálja a Postman-gyűjtemény](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) előre meghatározott kérelmek használatához.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató a következő szolgáltatásokat és eszközöket használatosak. 

+ [Postman asztali alkalmazás](https://www.getpostman.com/) az Azure Search kérelmek küldésére szolgál.

+ [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben a rövid útmutatóban egy ingyenes szolgáltatás használhatja. 

## <a name="get-a-key-and-url"></a>Egy kulcsot és egy URL-cím beszerzése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-get-started-postman/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="connect-to-azure-search"></a>Kapcsolódás az Azure Search

Ebben a szakaszban a webes tetszőleges eszköz használatával állítsa be a kapcsolatot az Azure Search. Mindegyik eszköz megőrzi a munkamenet, ami azt jelenti, csak meg kell adnia az api-kulcs és a Content-Type egyszer fejlécadatait.

Vagy az eszköz, kell választani egy parancsot (GET, POST, PUT és így tovább), adjon meg egy URL-végpontot, és néhány feladatot, adja meg a kérelem törzsében található JSON. Érvényes értéket cserélje le a keresési szolgáltatás neve (a SEARCH-szolgáltatás neve). Adjon hozzá `$select=name` visszaadandó csak egyes index nevét. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Figyelje meg, hogy a HTTPS-előtagot, a szolgáltatás nevét, a-objektum nevét (ebben az esetben az indexek gyűjtemény), és a [api-version](search-api-versions.md). Az api-verzió egy szükséges, a kisbetűs a karakterlánc `?api-version=2019-05-06` az aktuális verzió esetében. API-verziók a rendszeresen frissülnek. Az API-verziót minden kérelemnél adja meg, hogy teljes mértékben szabályozhassa, a rendszer mikor melyik verziót használja.  

Kérelem fejléce tartalomtípusból két elemet, a tartalom típusa, valamint az api-kulcsot az Azure Search hitelesítéséhez használt. Cserélje le a felügyeleti API-kulcs (YOUR-AZURE-SEARCH-ADMIN-API-KEY) egy érvényes értéket. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

A Postman állítson össze egy kérelmet, amely a következő képernyőképhez hasonlóan néz ki. Válasszon **első** parancsot, adja meg az URL-címet, és kattintson a **küldése**. Ezzel a paranccsal összekapcsolja az Azure Search, olvassa az indexek gyűjteményt, és visszaadja a 200-as HTTP-állapotkód: a sikeres kapcsolat. Ha a szolgáltatás már rendelkezik indexekkel, a válasz is index definíciókat tartalmazza.

![Postman-kérelem URL-cím és fejléc](media/search-get-started-postman/postman-url.png "Postman-kérelem URL-CÍMÉT és a fejléc")

## <a name="1---create-an-index"></a>1 – Index létrehozása

Az Azure Search szolgáltatásban, általában hozza létre az indexet, az adatok betöltése előtt. A [Index REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) erre a célra szolgál. 

Az URL-címet kell terjeszteni a `hotels` index neve.

Ehhez a Postman:

1. Módosítsa a kívánt műveleteket **PUT**.

2. Másolja be az URL-cím `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06`.

3. Adja meg az index definícióját (másolási használatra kész kód lejjebb), a kérelem törzsében.

4. Kattintson a **küldése**.

![A kérelem törzsében index JSON-dokumentum](media/search-get-started-postman/postman-request.png "kérelem törzsében szereplő Index JSON-dokumentum")

### <a name="index-definition"></a>Index definíciója

A mezők gyűjteménye határozza meg a dokumentum szerkezete. Mindegyik dokumentumnak rendelkeznie kell ezeket a mezőket, és minden mezőnek kell lennie egy adattípust. A keresésekben a sztringekat tartalmazó mezők vesznek részt, ezért a numerikus adatokat is érdemes sztringként beállítani, ha azt szeretné, hogy a keresés kiterjedjen rájuk.

Az egyes mezők attribútumai határozzák meg az engedélyezett műveleteket. A REST API-k alapértelmezés szerint számos műveletet lehetővé tesznek. Például alapértelmezés szerint minden sztring kereshető, lekérdezhető, szűrhető és kategorizálható. Gyakran csak akkor attribútumainak beállítása, amikor szüksége van egy viselkedés kikapcsolásához.

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

Az index létrehozása és adatokkal való feltöltése két különböző lépés. Az Azure Searchben az index tartalmazza az összes kereshető adatot, amelyeket JSON-dokumentumok formájában adhat be. A [hozzáadása, frissítése vagy törlése a dokumentumok REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) erre a célra szolgál. 

Az URL-címet kell terjeszteni a `docs` gyűjtemények és `index` műveletet.

Ehhez a Postman:

1. Cserélje a parancsot a **POST** parancsra.

2. Másolja be az URL-cím `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`.

3. Adja meg a JSON-dokumentumok (a másolási használatra kész kódot alább található) a kérés törzsében.

4. Kattintson a **küldése**.

![A kéréstörzs JSON-dokumentumok](media/search-get-started-postman/postman-docs.png "kéréstörzs JSON-dokumentumok")

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

Néhány másodpercen belül megtekintheti az egy 201-es HTTP-válasz a munkamenetlistában. Ez azt jelenti, hogy a dokumentumok sikeresen létrejöttek. 

Ha a 207-es HTTP-válasz jelenik meg, legalább egy dokumentumot nem sikerült feltölteni. Ha a 404-es válasz jelenik meg, akkor a kérelem fejlécében vagy törzsében szintaktikai hiba van: ellenőrizze, hogy valóban módosította-e a végpontot, hogy tartalmazza a `/docs/index` útvonalat.

> [!Tip]
> A kiválasztott adatforrások esetében választhatja az alternatív *indexelő* megközelítést, amely egyszerűbb, és csökkenti az indexáláshoz szükséges kódot. További információkért lásd: [Indexelőműveletek](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Most, hogy az index és a dokumentumok is betöltődtek, számára küldhet lekérdezéseket az őket használó [Search dokumentumok REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Az URL-cím eloszlásának, megadva a következő keresési operátor használatával terjeszteni.

Ehhez a Postman:

1. Módosítsa a kívánt műveleteket **első**.

2. Másolja be az URL-cím `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`.

3. Kattintson a **küldése**.

Ez a lekérdezés egy üres, és a dokumentumok számát adja vissza a keresési eredmények között. A kérések és válaszok hasonlóan kell kinéznie az alábbi képernyőképen a Postman számára kattintás után **küldése**. Az állapotkódnak 200-nak kell lennie.

 ![A keresési karakterláncot az URL-cím LEKÉRÉSE](media/search-get-started-postman/postman-query.png "a keresési karakterláncot az URL-cím beszerzése")

Próbálja meg néhány további lekérdezést példák betekintést nyerhet a szintaxis. Egy karakterlánc-keresés, szó $filter lekérdezések, az eredménykészletet, az adott mezők, és egyéb a keresés hatóköre korlátozza.

Lapozófájl-kapacitás ki az aktuális URL-cím olyanokra cserélni az alábbi gombra kattintva **küldése** minden alkalommal, amikor az eredmények megtekintéséhez.

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

## <a name="get-index-properties"></a>Index tulajdonságainak lekérése
Is [statisztika lekérése](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) , a dokumentumok számát és az indexfrissítési mérete: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06`
```

Hozzáadás `/stats` index tárolt információt adhatja vissza az URL-címre. A Postmanben a kérelem az alábbihoz fog hasonlítani, a válaszban pedig szerepleni fog a dokumentumok száma és a felhasznált lemezterület mérete bájtban megadva.

 ![Index információk](media/search-get-started-postman/postman-system-query.png "index információk lekérése")

Figyelje meg, hogy az api-version szintaxisa eltér. Ebben a kérelemben a `?` karakterrel fűzheti hozzá az api-version paramétert. A `?` elkülöníti a lekérdezési karakterláncot az URL-címet, az & elválasztja az egyes "név = érték" párt a lekérdezési karakterláncban. Ebben a lekérdezésben az api-version az első és egyetlen lekérdezési sztring.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Dolgozik, a saját előfizetése, esetén célszerű egy projektet a végén, hogy azonosítani, hogy az erőforrások továbbra is kell-e létrehozott. Erőforrások bal oldali futó is költséget takaríthat meg költséget. Külön-külön törölje az erőforrást, vagy törölje az erőforráscsoportot törli az erőforrások teljes készletében.

Megkeresheti és kezelheti az erőforrásokat a portál használatával a **összes erőforrás** vagy **erőforráscsoportok** hivatkozásra a bal oldali navigációs ablaktáblán.

Ha használ egy ingyenes szolgáltatás, ne feledje, hogy korlátozódnak három indexek, indexelők és adatforrások. A korlátja alatt maradjunk a portál egyes elemeire törölheti. 

## <a name="next-steps"></a>További lépések

A REST-ügyfelek rendkívül hasznosak a rögtönzött vizsgálatokhoz, most azonban, hogy már ismeri a REST API-k működését, folytathatja a kódolást. A következő lépés tekintse meg a következő hivatkozásra:

+ [Rövid útmutató: -Index létrehozása .NET SDK használatával](search-get-started-dotnet.md)
