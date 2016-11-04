---
title: Az Azure Search REST API-k értékelése és tesztelése a Fiddler használatával | Microsoft Docs
description: A Fiddler használatával kódolás nélkül ellenőrizheti az Azure Search rendelkezésre állását, illetve próbálhatja ki a REST API-kat.
services: search
documentationcenter: ''
author: HeidiSteen
manager: mblythe
editor: ''

ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/08/2016
ms.author: heidist

---
# A Fiddler használata az Azure Search REST API-k értékeléséhez és teszteléséhez
> [!div class="op_single_selector"]
> * [Áttekintés](search-query-overview.md)
> * [Keresési ablak](search-explorer.md)
> * [Fiddler](search-fiddler.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Ez a cikk azt ismerteti, hogyan használható a [Telerik által ingyenesen letölthetőként kínált](http://www.telerik.com/fiddler) Fiddler a HTTP-kérelmek Azure Search REST API számára történő küldéséhez, valamint a válaszok API használatával történő megtekintéséhez kód írása nélkül. Az Azure Search teljes körűen felügyelt, üzemeltetett felhőalapú keresőszolgáltatás, amely egyszerűen programozható .NET és REST API-kon keresztül. Az Azure Search szolgáltatás REST API-jainak dokumentációja az [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx) webhelyén tekinthető meg.

Az alábbi lépéseket követve létre fog hozni egy indexet, dokumentumokat fog feltölteni, lekérdezi az indexet, majd szolgáltatási adatokat kérdez le a rendszertől.

Az alábbi lépések elvégzéséhez szüksége lesz egy Azure Search szolgáltatásra és egy `api-key` elemre. A kezdéssel kapcsolatos útmutatásért tekintse meg az [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) című cikket.

## Index létrehozása
1. Indítsa el a Fiddlert. A **Fájl** menüben kapcsolja ki a **Forgalom rögzítése** beállítást az aktuális feladathoz nem kapcsolódó HTTP-tevékenység elrejtéséhez.
2. A **Szerkesztő** lapon állítson össze egy, az alábbi képernyőfelvételen láthatóhoz hasonló kérelmet.
   
    ![][1]
3. Válassza a **PUT** lehetőséget.
4. Adja meg a szolgáltatás URL-címét, a kérelem attribútumait, valamint az API-verziót meghatározó URL-címet. Ügyeljen az alábbiakra:
   
   * A HTTPS előtagot használja.
   * A kérelemattribútum az „/indexes/hotels”. Ez arra utasítja a Search szolgáltatást, hogy létrehozzon egy „hotels” nevű indexet.
   * Az API-verziót csupa kisbetűvel, a következő formában kell megadni: „?api-version=2015-02-28” Az API-verziók azért fontosak, mert az Azure Search rendszeresen telepíti a frissítéseket. Ritka esetekben a szolgáltatás frissítése az API-t használhatatlanná tévő változást vezethet be. Emiatt az Azure Search számára minden kérelemnél meg kell adni az API-verziót, hogy Ön mindig teljes mértékben kézben tudja tartani, hogy melyik verzió van használatban.
     
     A teljes URL-címnek az alábbi példához kell hasonlítania.
     
            https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28
5. Adja meg a kérelem fejlécét, a „host” és az „api-key” értékét az Ön szolgáltatásában érvényes értékekre cserélve.
   
           User-Agent: Fiddler
           host: my-app.search.windows.net
           content-type: application/json
           api-key: 1111222233334444
6. A kérelem törzse területre illessze be az index definícióját alkotó mezőket.
   
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
7. Kattintson a **Végrehajtás** parancsra.

Néhány másodperc múlva a munkamenetlistában megjelenik a 201-es HTTP-válasz, amely azt jelzi, hogy az index sikeresen létrejött.

Ha 504-es HTTP-választ kap, ellenőrizze, hogy az URL-címben a HTTPS előtag van-e megadva. Ha 400-as vagy 404-es HTTP-válasz jelenik meg, akkor ellenőrizze, hogy a kérelem törzsében nincsenek-e beillesztési hibák. A 403-as HTTP-válasz általában az API-kulccsal kapcsolatos hibát jelez (érvénytelen a kulcs vagy szintaktikai hiba van az API-kulcs meghatározásában).

## Dokumentumok betöltése
A **Szerkesztő** lapon a dokumentumok küldésére szolgáló kérelem az alábbihoz hasonlóan jelenik meg. A kérelem törzse négy szálloda keresési adatait tartalmazza.

   ![][2]

1. Válassza a **POST** lehetőséget.
2. Adjon meg egy olyan URL-címet, amely a HTTPS előtaggal kezdődik, amelyet a szolgáltatási URL-cím, majd az „/indexes/<'indexname'>/docs/index?api-version=2015-02-28” karakterlánc követ. A teljes URL-címnek az alábbi példához kell hasonlítania.
   
           https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
3. A kérelem fejléce azonos az előzővel. Ne feledje, hogy a „host” és az „api-key” értékét az Ön szolgáltatásában érvényes értékekre cserélte.
   
           User-Agent: Fiddler
           host: my-app.search.windows.net
           content-type: application/json
           api-key: 1111222233334444
4. A kérelem törzse négy, a szállodák indexéhez hozzáadandó dokumentumot tartalmaz.
   
           {
           "value": [
           {
               "@search.action": "upload",
               "hotelId": "1",
               "baseRate": 199.0,
               "description": "Best hotel in town",
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
5. Kattintson a **Végrehajtás** parancsra.

Néhány másodperc múlva megjelenik a 200-as HTTP-válasz a munkamenetlistában. Ez azt jelenti, hogy a dokumentumok sikeresen létrejöttek. Ha a 207-es HTTP-válasz jelenik meg, legalább egy dokumentumot nem sikerült feltölteni. Ha a 404-es válasz jelenik meg, akkor a kérelem fejlécében vagy törzsében szintaktikai hiba van.

## Az index lekérdezése
Most, hogy az index és a dokumentumok is betöltődtek, lekérdezheti őket.  A **Szerkesztő** lapon a szolgáltatást lekérdező **GET** parancs az alábbi képernyőfelvételhez hasonló lesz.

   ![][3]

1. Válassza a **GET** lehetőséget.
2. Adjon meg egy olyan URL-címet, amely a HTTPS előtaggal kezdődik, amelyet a szolgáltatási URL, majd az „/indexes/<'indexname'>/docs?” karakterlánc, végül a lekérdezési paraméterek követnek. Példaképpen használja a következő URL-címet, és cserélje le benne a mintaállomásnevet egy, az Ön szolgáltatásában érvényes állomásnévvel.
   
           https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28
   
   Ez a lekérdezés a „motel” kifejezést keresi, és értékkorlátozó kategóriákat ad vissza az értékelésekhez.
3. A kérelem fejléce azonos az előzővel. Ne feledje, hogy a „host” és az „api-key” értékét az Ön szolgáltatásában érvényes értékekre cserélte.
   
           User-Agent: Fiddler
           host: my-app.search.windows.net
           content-type: application/json
           api-key: 1111222233334444

200-as válaszkódnak kell érkeznie, a válasz kimenetének pedig az alábbi képernyőfelvételhez kell hasonlítania.

   ![][4]

A következő példalekérdezés az MSDN webhelyén megtekinthető és a [Search Index operation (Azure Search API) ismertető](http://msdn.microsoft.com/library/dn798927.aspx) (Keresési index működését (Azure Search API)) cikkből származik. Az ebben a témakörben szereplő számos példalekérdezés szóközt tartalmaz, amely a Fiddler esetében nem engedélyezett. Minden szóközt cseréljen le + karakterre, mielőtt beillesztené a lekérdezési karakterláncot a lekérdezés Fiddler használatával történő megkísérléséhez.

**A szóközök cseréje előtt:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**A szóközök + jelre cserélése után:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## A rendszer lekérdezése
A rendszertől a dokumentumok számát és a tárhelyhasználatot is lekérdezheti. A **Szerkesztő** lapon a kérelem az alábbihoz fog hasonlítani, a válaszban pedig szerepleni fog a dokumentumok száma és a felhasznált lemezterület mérete.

 ![][5]

1. Válassza a **GET** lehetőséget.
2. Adjon meg egy olyan URL-címet, amelyben a szolgáltatási URL-címet az „/indexes/hotels/stats?api-version=2015-02-28” karakterlánc követi.
   
           https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28
3. Adja meg a kérelem fejlécét, a „host” és az „api-key” értékét az Ön szolgáltatásában érvényes értékekre cserélve.
   
           User-Agent: Fiddler
           host: my-app.search.windows.net
           content-type: application/json
           api-key: 1111222233334444
4. Hagyja üresen a kérés törzsét.
5. Kattintson a **Végrehajtás** parancsra. A munkamenetlistában a 200-as HTTP-állapotkódnak kell megjelennie. Válassza ki a parancshoz közzétett bejegyzést.
6. Kattintson a **Vizsgálók**, majd a **Fejlécek** fülre, végül válassza ki a JSON-formátumot. Ekkor megjelenik a dokumentumok száma és a tárhely mérete (KB).

## Következő lépések
Tekintse meg [A Search szolgáltatás kezelése az Azure rendszerben](search-manage.md) című cikket az Azure Search kód nélküli kezelésével és használatával kapcsolatban.

<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png



<!--HONumber=Sep16_HO4-->


