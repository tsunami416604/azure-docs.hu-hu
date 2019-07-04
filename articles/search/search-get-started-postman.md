---
title: 'Gyors útmutató: Létrehozása, betöltése és lekérdezése az indexek Postman és a REST API-k – Azure Search használatával'
description: Ismerje meg, hogyan hívhat meg az Azure Search REST API-k Postman és a mintaadatokat és a definíciókat.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b3c5c08e9862d59481573b501045466d6d691a77
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485508"
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

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a munka megkezdése előtt, majd [regisztrálhat az Azure Searchre](search-create-service-portal.md).

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató a következő szolgáltatásokat és eszközöket használatosak. 

+ [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben a rövid útmutatóban egy ingyenes szolgáltatás használhatja. 

+ [Postman asztali alkalmazás](https://www.getpostman.com/) vagy [Telerik Fiddler](https://www.telerik.com/fiddler) az Azure Search kérelmek küldésére szolgál.

## <a name="get-a-key-and-url"></a>Egy kulcsot és egy URL-cím beszerzése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-get-started-postman/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="connect-to-azure-search"></a>Kapcsolódás az Azure Search

Ebben a szakaszban a webes tetszőleges eszköz használatával állítsa be a kapcsolatot az Azure Search. Mindegyik eszköz megőrzi a munkamenet, ami azt jelenti, csak meg kell adnia az api-kulcs és a Content-Type egyszer fejlécadatait.

Vagy az eszköz, kell választani egy parancsot (GET, POST, PUT és így tovább), adjon meg egy URL-végpontot, és néhány feladatot, adja meg a kérelem törzsében található JSON. Érvényes értéket cserélje le a keresési szolgáltatás neve (a SEARCH-szolgáltatás neve). 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06

Figyelje meg, hogy a HTTPS-előtagot, a szolgáltatás nevét, a-objektum nevét (ebben az esetben az indexek gyűjtemény), és a [api-version](search-api-versions.md). Az api-verzió egy szükséges, a kisbetűs a karakterlánc `?api-version=2019-05-06` az aktuális verzió esetében. API-verziók a rendszeresen frissülnek. Az API-verziót minden kérelemnél adja meg, hogy teljes mértékben szabályozhassa, a rendszer mikor melyik verziót használja.  

Kérelem fejléce tartalomtípusból két elemet, a tartalom típusa, valamint az api-kulcsot az Azure Search hitelesítéséhez használt. Cserélje le a felügyeleti API-kulcs (a-ADMIN-API-kulcsot) egy érvényes értéket. 

    api-key: <YOUR-ADMIN-API-KEY>
    Content-Type: application/json

A Postman állítson össze egy kérelmet, amely a következő képernyőképhez hasonlóan néz ki. Válasszon **első** parancsot, adja meg az URL-címet, és kattintson a **küldése**. Ezzel a paranccsal összekapcsolja az Azure Search, olvassa az indexek gyűjteményt, és visszaadja a 200-as HTTP-állapotkód: a sikeres kapcsolat. Ha a szolgáltatás már rendelkezik indexekkel, a válasz is index definíciókat tartalmazza.

![Postman-kérelem fejléce][6]

## <a name="1---create-an-index"></a>1 – Index létrehozása

Az Azure Search szolgáltatásban, általában hozza létre az indexet, az adatok betöltése előtt. A [Index REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) erre a célra szolgál. 

Az URL-címet kell terjeszteni a `hotels` index neve.

Ehhez a Postman:

1. Módosítsa a kívánt műveleteket **PUT**.

2. Másolja be az URL-cím `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06`.

3. Adja meg az index definícióját (lásd alább) a kérés törzsében.

4. Kattintson a **küldése**.

![Postman-kérelem törzse][8]

### <a name="index-definition"></a>Index definíciója

A mezők gyűjteménye határozza meg a dokumentum szerkezete. Mindegyik dokumentumnak rendelkeznie kell ezeket a mezőket, és minden mezőnek kell lennie egy adattípust. A keresésekben a sztringekat tartalmazó mezők vesznek részt, ezért a numerikus adatokat is érdemes sztringként beállítani, ha azt szeretné, hogy a keresés kiterjedjen rájuk.

Az egyes mezők attribútumai határozzák meg az engedélyezett műveleteket. A REST API-k alapértelmezés szerint számos műveletet lehetővé tesznek. Például alapértelmezés szerint minden sztring kereshető, lekérdezhető, szűrhető és kategorizálható. Gyakran csak akkor attribútumainak beállítása, amikor szüksége van egy viselkedés kikapcsolásához.

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }

A kérelem elküldését követően megjelenik a 201-es HTTP-válasz, amely azt jelzi, hogy az index sikeresen létrejött. A műveletet ellenőrizheti a portálon, azonban vegye figyelembe, hogy a portáloldal frissítési időköze miatt esetleg egy-két percet várakoznia kell.

> [!TIP]
> Ha 504-es HTTP-választ kap, ellenőrizze, hogy az URL-címben a HTTPS előtag van-e megadva. Ha 400-as vagy 404-es HTTP-válasz jelenik meg, akkor ellenőrizze, hogy a kérelem törzsében nincsenek-e beillesztési hibák. A 403-as HTTP-válasz általában az API-kulccsal kapcsolatos hibát jelez (érvénytelen a kulcs vagy szintaktikai hiba van az API-kulcs meghatározásában).

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Az index létrehozása és adatokkal való feltöltése két különböző lépés. Az Azure Searchben az index tartalmazza az összes kereshető adatot, amelyeket JSON-dokumentumok formájában adhat be. A [hozzáadása, frissítése vagy törlése a dokumentumok REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) erre a célra szolgál. 

Az URL-címet kell terjeszteni a `docs` gyűjtemények és `index` műveletet.

Ehhez a Postman:

1. Cserélje a parancsot a **POST** parancsra.

2. Másolja be az URL-cím `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs/index?api-version=2019-05-06`.

3. Adja meg a JSON-dokumentumok (lásd alább) a kérés törzsében.

4. Kattintson a **küldése**.

![Postman-kérelem hasznos adatai][10]

### <a name="json-documents-to-load-into-the-index"></a>JSON-dokumentumok betöltése az indexbe

A kérelem törzse négy, a szállodák indexéhez hozzáadandó dokumentumot tartalmaz.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "description_fr": "Meilleur hôtel en ville",
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
             "description_fr": "Hôtel le moins cher en ville",
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
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

Néhány másodperc múlva megjelenik a 200-as HTTP-válasz a munkamenetlistában. Ez azt jelenti, hogy a dokumentumok sikeresen létrejöttek. 

Ha a 207-es HTTP-válasz jelenik meg, legalább egy dokumentumot nem sikerült feltölteni. Ha a 404-es válasz jelenik meg, akkor a kérelem fejlécében vagy törzsében szintaktikai hiba van: ellenőrizze, hogy valóban módosította-e a végpontot, hogy tartalmazza a `/docs/index` útvonalat.

> [!Tip]
> A kiválasztott adatforrások esetében választhatja az alternatív *indexelő* megközelítést, amely egyszerűbb, és csökkenti az indexáláshoz szükséges kódot. További információkért lásd: [Indexelőműveletek](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Most, hogy az index és a dokumentumok is betöltődtek, számára küldhet lekérdezéseket az őket használó [Search dokumentumok REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Az URL-cím lekérdezési karakterláncot, megadva a következő keresési operátor használatával terjeszteni.

Ehhez a Postman:

1. Módosítsa a kívánt műveleteket **első**.

2. Másolja be az URL-cím `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2019-05-06`.

3. Kattintson a **küldése**.

Ez a lekérdezés a „motel” kifejezést keresi, és a találatokban szereplő dokumentumok számát adja vissza. A kérések és válaszok hasonlóan kell kinéznie az alábbi képernyőképen a Postman számára kattintás után **küldése**. Az állapotkódnak 200-nak kell lennie.

 ![Postman-lekérdezés válasza][11]

## <a name="get-index-properties"></a>Index tulajdonságainak lekérése
A rendszer-információk lekérdezésével a dokumentumok számát és a tárhelyhasználatot is lekérheti: `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/stats?api-version=2019-05-06`

A Postmanben a kérelem az alábbihoz fog hasonlítani, a válaszban pedig szerepleni fog a dokumentumok száma és a felhasznált lemezterület mérete bájtban megadva.

 ![Postman-rendszerlekérdezés][12]

Figyelje meg, hogy az api-version szintaxisa eltér. Ebben a kérelemben a `?` karakterrel fűzheti hozzá az api-version paramétert. A `?` elkülöníti a lekérdezési karakterláncot az URL-címet, az & elválasztja az egyes "név = érték" párt a lekérdezési karakterláncban. Ebben a lekérdezésben az api-version az első és egyetlen lekérdezési sztring.

Az API-val kapcsolatos további információkért lásd: [első Index statisztika REST API](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha már nincs szüksége a keresési szolgáltatás, a leggyorsabb kiadási szolgáltatások módja az erőforráscsoport, amely tartalmazza az Azure Search szolgáltatás törlésével. Az erőforráscsoport törlésével véglegesen töröl Mindent, többek között a szolgáltatások és a tárolt tartalmat. A portálon az erőforráscsoport neve az egyes szolgáltatások Áttekintés lapján szerepel.

## <a name="next-steps"></a>További lépések

A REST-ügyfelek rendkívül hasznosak a rögtönzött vizsgálatokhoz, most azonban, hogy már ismeri a REST API-k működését, folytathatja a kódolást. A következő lépésekért lásd az alábbi hivatkozásokat:

+ [Rövid útmutató: -Index létrehozása .NET SDK használatával](search-create-index-dotnet.md)
+ [Rövid útmutató: Hozzon létre az indexekben (REST) PowerShell-lel](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-get-started-postman/fiddler-url.png
[2]: ./media/search-get-started-postman/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-get-started-postman/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-get-started-postman/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-get-started-postman/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-get-started-postman/postman-url.png
[7]: ./media/search-get-started-postman/fiddler-request.png
[8]: ./media/search-get-started-postman/postman-request.png
[9]: ./media/search-get-started-postman/fiddler-docs.png
[10]: ./media/search-get-started-postman/postman-docs.png
[11]: ./media/search-get-started-postman/postman-query.png
[12]: ./media/search-get-started-postman/postman-system-query.png
