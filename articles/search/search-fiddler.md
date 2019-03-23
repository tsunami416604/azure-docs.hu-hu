---
title: Ismerje meg a Postman vagy a Fiddler - az Azure Search REST API-k
description: Az Azure Search Postman vagy a Fiddler használatával HTTP-kérések és REST API-hívás.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c99380faee8fd1bc42922f7f0e367edde1154a9b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368901"
---
# <a name="quickstart-explore-azure-search-rest-apis-using-postman-or-fiddler"></a>Gyors útmutató: Ismerkedés az Azure Search REST API-k Postman vagy a Fiddler

Az egyik legegyszerűbb Fedezze fel a [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice) állítson össze a HTTP-kérelmekre, és vizsgálja meg a válaszok Postman vagy a Fiddler használatával. A megfelelő eszközökkel, ezeket az útmutatásokat követve már azelőtt küldhet kéréseket, és megtekintheti a válaszokat, hogy akár egyetlen sornyi kódot is írna.

> [!div class="checklist"]
> * Webes API tesztelési eszköz letöltése
> * A keresési szolgáltatás API-kulcsának és végpontjának beszerzése
> * Kérelemfejlécek konfigurálása
> * Index létrehozása
> * Index betöltése
> * Keresés az indexekben

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a munka megkezdése előtt, majd [regisztrálhat az Azure Searchre](search-create-service-portal.md).

## <a name="download-tools"></a>Eszközök letöltése

A következő eszközöket széles körben használják a webes fejlesztéshez, de ha másik eszközt szokott használni, a cikkben lévő útmutatások arra is érvényesek.

+ [Postman asztali alkalmazás](https://www.getpostman.com/)
+ [Telerik Fiddler](https://www.telerik.com/fiddler)

## <a name="get-the-api-key-and-endpoint"></a>Az API-kulcs és a végpont beszerzése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. Az Azure Portalon, a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://my-service-name.search.windows.net`.

2. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-fiddler/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.


## <a name="configure-headers"></a>A fejlécek konfigurálása

Mindegyik eszköz megőrzi a munkamenet fejlécadatait, ami azt jelenti, hogy az URL-végpontot, az API-verziót, az API-kulcsot és a tartalomtípust csak egyszer kell megadni.

A teljes URL-címnek az alábbi példához kell hasonlítania, azzal az eltéréssel, hogy a **`my-app`** helyőrző helyére egy érvényes nevet kell írni: `https://my-app.search.windows.net/indexes/hotels?api-version=2017-11-11`

A szolgáltatás URL-címének szerkezete az alábbi elemeket tartalmazza:

+ HTTPS-előtag.
+ A szolgáltatás URL-címe, amely a portálról kérhető le.
+ Egy erőforrás, azaz egy művelet, amely egy objektumot hoz létre a szolgáltatáson. Ebben a lépésben egy index nevű *hotels*.
+ Az api-version, amely egy kötelező kisbetűs a karakterlánc a „?api-version=2017-11-11” formátumban az aktuális verzió esetében. Az [API-verziókat](search-api-versions.md) a rendszer rendszeresen frissíti. Az API-verziót minden kérelemnél adja meg, hogy teljes mértékben szabályozhassa, a rendszer mikor melyik verziót használja.  

A kérelemfejléc szerkezete két elemből tevődik össze, a tartalomtípusból és az előző szakaszban leírt API-kulcsból:

    api-key: <placeholder>
    Content-Type: application/json


### <a name="postman"></a>Postman

Állítson össze egy kérelmet, amely a következő képernyőképhez hasonlóan néz ki. Ebben használja a **PUT** parancsot. 

![Postman-kérelem fejléce][6]

### <a name="fiddler"></a>Fiddler

Állítson össze egy kérelmet, amely a következő képernyőképhez hasonlóan néz ki. Ebben használja a **PUT** parancsot. A Fiddler beszúrja a `User-Agent=Fiddler` paramétert. A két további kérelemfejléc ez alá új sorokba illeszthető be. A szolgáltatás tartalomtípusát és API-kulcsát illessze be a szolgáltatás rendszergazdai hozzáférési kulcsának használatával.

![Fiddler-kérelem fejléce][1]

> [!Tip]
> Kapcsolja ki a webes forgalom felesleges, nem kapcsolódó HTTP-tevékenység elrejtéséhez. A fiddler esetében a **fájl** menüben kapcsolja ki a **forgalom rögzítése**. 

## <a name="1---create-an-index"></a>1 – Index létrehozása

A kérelem törzse tartalmazza az indexdefiníciót. A kérelemtörzs hozzáadása teszi teljessé az indexet létrehozó kérelmet.

Az index neve mellett a mezők gyűjteménye a kérelem legfontosabb összetevője. A mezők gyűjteménye határozza meg az indexsémát. Mindegyik mező esetében határozza meg annak típusát. A keresésekben a sztringekat tartalmazó mezők vesznek részt, ezért a numerikus adatokat is érdemes sztringként beállítani, ha azt szeretné, hogy a keresés kiterjedjen rájuk.

Az egyes mezők attribútumai határozzák meg az engedélyezett műveleteket. A REST API-k alapértelmezés szerint számos műveletet lehetővé tesznek. Például alapértelmezés szerint minden sztring kereshető, lekérdezhető, szűrhető és kategorizálható. Gyakran csak akkor kell attribútumokat beállítania, ha valamely viselkedést ki szeretné kapcsolni. Az attribútumokkal kapcsolatos további információk: [Index létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
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

Ha 504-es HTTP-választ kap, ellenőrizze, hogy az URL-címben a HTTPS előtag van-e megadva. Ha 400-as vagy 404-es HTTP-válasz jelenik meg, akkor ellenőrizze, hogy a kérelem törzsében nincsenek-e beillesztési hibák. A 403-as HTTP-válasz általában az API-kulccsal kapcsolatos hibát jelez (érvénytelen a kulcs vagy szintaktikai hiba van az API-kulcs meghatározásában).


### <a name="postman"></a>Postman

Másolja az indexdefiníciót a kérelemtörzsbe az alábbi képernyőfelvételhez hasonlóan, és kattintson a **küldése** felső jobb a befejezett kérelem elküldéséhez.

![Postman-kérelem törzse][8]

### <a name="fiddler"></a>Fiddler

Másolja az indexdefiníciót a kérelemtörzsbe az alábbi képernyőfelvételhez hasonlóan, és kattintson a **Execute** felső jobb a befejezett kérelem elküldéséhez.

![Fiddler-kérelem törzse][7]

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Az index létrehozása és adatokkal való feltöltése két különböző lépés. Az Azure Searchben az index tartalmazza az összes kereshető adatot, amelyeket JSON-dokumentumok formájában adhat be. Az ehhez a tevékenységhez tartozó API áttekintéséhez lásd: [Dokumentumok hozzáadása, frissítése vagy törlése (REST)](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

+ Ebben a lépésben a parancsot cserélje a **POST** parancsra.
+ Módosítsa a végpontot, hogy tartalmazza a `/docs/index` útvonalat. A teljes URL-címnek a következőképp kell kinéznie: `https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11`
+ A kérelemfejléceket ne módosítsa. 

A kérelem törzse négy, a szállodák indexéhez hozzáadandó dokumentumot tartalmaz.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "description_fr": "Meilleur hôtel en ville"
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


### <a name="postman"></a>Postman

Cserélje a parancsot a **POST** parancsra. Módosítsa az URL-címet, hogy tartalmazza a `/docs/index` útvonalat. Másolja a dokumentumokat a kérelemtörzsbe az alábbi képernyőfelvételhez hasonlóan, és ezután hajtsa végre a kérést.

![Postman-kérelem hasznos adatai][10]

### <a name="fiddler"></a>Fiddler

Cserélje a parancsot a **POST** parancsra. Módosítsa az URL-címet, hogy tartalmazza a `/docs/index` útvonalat. Másolja a dokumentumokat a kérelemtörzsbe az alábbi képernyőfelvételhez hasonlóan, és ezután hajtsa végre a kérést.

![Fiddler-kérelem hasznos adatai][9]

## <a name="3---search-an-index"></a>3 – Keresés az indexekben
Most, hogy az index és a dokumentumok is betöltődtek, számára küldhet lekérdezéseket az őket használó [dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents) REST API-t.

+ Ebben a lépésben a parancsot cserélje a **GET** parancsra.
+ Módosítsa a végpontot, hogy tartalmazza a lekérdezési paramétereket, köztük a keresett sztringekat is. A lekérdezési URL például a következőképp nézhet ki: `https://my-app.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2017-11-11`
+ A kérelemfejléceket ne módosítsa

Ez a lekérdezés a „motel” kifejezést keresi, és a találatokban szereplő dokumentumok számát adja vissza. A kérések és válaszok hasonlóan kell kinéznie az alábbi képernyőképen a Postman számára kattintás után **küldése**. Az állapotkódnak 200-nak kell lennie.

 ![Postman-lekérdezés válasza][11]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Tippek a mintalekérdezések Fiddlerben való futtatásához

A következő példalekérdezés az [A keresési index működése (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) cikkből származik. A cikkben szereplő számos példalekérdezés szóközt is tartalmaz, amely a Fiddler esetében nem engedélyezett. Minden szóközt cseréljen le + karakterre, mielőtt beillesztené a lekérdezési sztringet a lekérdezés Fiddler használatával történő megkísérléséhez.

**A szóközök cseréje előtt (a „lastRenovationDate desc” részben):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11

**A szóközök + jelre cserélése után (a „lastRenovationDate+desc” részben):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2017-11-11

## <a name="get-index-properties"></a>Index tulajdonságainak lekérése
A rendszer-információk lekérdezésével a dokumentumok számát és a tárhelyhasználatot is lekérheti: `https://my-app.search.windows.net/indexes/hotels/stats?api-version=2017-11-11`

A Postmanben a kérelem az alábbihoz fog hasonlítani, a válaszban pedig szerepleni fog a dokumentumok száma és a felhasznált lemezterület mérete bájtban megadva.

 ![Postman-rendszerlekérdezés][12]

Figyelje meg, hogy az api-version szintaxisa eltér. Ebben a kérelemben a `?` karakterrel fűzheti hozzá az api-version paramétert. A ? karakter választja el az URL-címet a lekérdezési sztringtől, az &amp; karakter pedig az egyes „név=érték” párokat a lekérdezési sztringen belül. Ebben a lekérdezésben az api-version az első és egyetlen lekérdezési sztring.

További információk erről az API-ról: [Indexstatisztikák lekérdezése (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).


### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Tippek az indexstatisztikák Fiddlerben történő megtekintéséhez

A Fiddlerben kattintson az **Inspectors** (Vizsgálók), majd a **Headers** (Fejlécek) fülre, végül válassza ki a JSON formátumot. Ekkor megjelenik a dokumentumok száma és a tárhely mérete (KB).

## <a name="next-steps"></a>További lépések

A REST-ügyfelek rendkívül hasznosak a rögtönzött vizsgálatokhoz, most azonban, hogy már ismeri a REST API-k működését, folytathatja a kódolást. A következő lépésekért lásd az alábbi hivatkozásokat:

+ [Rövid útmutató: -Index létrehozása .NET SDK használatával](search-create-index-dotnet.md)
+ [Rövid útmutató: Hozzon létre az indexekben (REST) PowerShell-lel](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png