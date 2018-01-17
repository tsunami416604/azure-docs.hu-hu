---
title: "REST API-k vizsgálata a Fiddlerben vagy a Postmanben (Azure Search REST) | Microsoft Docs"
description: "HTTP-kérések és REST API-hívások küldése az Azure Searchbe a Fiddler vagy Postman használatával."
services: search
documentationcenter: 
author: HeidiSteen
manager: cgronlun
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/04/2018
ms.author: heidist
ms.openlocfilehash: a9a6bc035ba9f02057e3086a9682b54282da19f3
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2018
---
# <a name="explore-azure-search-rest-apis-using-fiddler-or-postman"></a>Az Azure Search REST API-k vizsgálata a Fiddlerrel vagy a Postmannel

Az [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice) vizsgálatának egyik legegyszerűbb módja a HTTP-kérések összeállítása a Fiddler vagy a Postman használatával, majd a válaszok megvizsgálása. A megfelelő eszközökkel, ezeket az útmutatásokat követve már azelőtt küldhet kéréseket, és megtekintheti a válaszokat, hogy akár egyetlen sornyi kódot is írna.

> [!div class="checklist"]
> * Webes API tesztelési eszköz letöltése
> * A keresési szolgáltatás API-kulcsának és végpontjának beszerzése
> * Kérelemfejlécek konfigurálása
> * Index létrehozása
> * Index betöltése
> * Keresés az indexekben

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a munka megkezdése előtt, majd [regisztrálhat az Azure Searchre](search-create-service-portal.md).

## <a name="download-and-install-tools"></a>Eszközök letöltése és telepítése

A következő eszközöket széles körben használják a webes fejlesztéshez, de ha másik eszközt szokott használni, a cikkben lévő útmutatások arra is érvényesek.

+ [Postman (Google Chrome-bővítmény)](https://www.getpostman.com/)
+ [Telerik Fiddler](http://www.telerik.com/fiddler)

## <a name="get-the-api-key-and-endpoint"></a>Az API-kulcs és a végpont beszerzése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. Az Azure Portalon nyissa meg a Search szolgáltatás oldalát az irányítópultról, vagy [keresse meg a szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a szolgáltatások listájában.
2. A végpontot az **Áttekintés** > **Alapvető szolgáltatások** > **Url** területen szerezheti be. A végpontok például a következőképpen nézhetnek ki: `https://my-service-name.search.windows.net`.
3. Az API-kulcsot a **Beállítások** > **Kulcsok** felületen kérheti le. Redundancia érdekében a rendszer két adminisztrációs kulcsot kínál, ha esetleg cserélni szeretné a kulcsokat. Az adminisztrációs kulcsok biztosítják a szolgáltatásra vonatkozó írási jogosultságot, amely az indexek létrehozásához és betöltéséhez szükséges. Az írási műveletekhez az elsődleges és a másodlagos kulcsot egyaránt használhatja.

## <a name="configure-request-headers"></a>Kérelemfejlécek konfigurálása

Mindegyik eszköz megőrzi a munkamenet fejlécadatait, ami azt jelenti, hogy az URL-végpontot, az API-verziót, az API-kulcsot és a tartalomtípust csak egyszer kell megadni.

A teljes URL-címnek az alábbi példához kell hasonlítania, azzal az eltéréssel, hogy a **`my-app`** helyőrző helyére egy érvényes nevet kell írni: `https://my-app.search.windows.net/indexes/hotels?api-version=2016-09-01`

A szolgáltatás URL-címének szerkezete az alábbi elemeket tartalmazza:

+ HTTPS-előtag.
+ A szolgáltatás URL-címe, amely a portálról kérhető le.
+ Egy erőforrás, azaz egy művelet, amely egy objektumot hoz létre a szolgáltatáson. A jelen lépés esetében ez egy „hotels” nevű index.
+ Az api-version, amely egy kötelező kisbetűs a karakterlánc a „?api-version=2016-09-01” formátumban az aktuális verzió esetében. Az [API-verziókat](search-api-versions.md) a rendszer rendszeresen frissíti. Az API-verziót minden kérelemnél adja meg, hogy teljes mértékben szabályozhassa, a rendszer mikor melyik verziót használja.  

A kérelemfejléc szerkezete két elemből tevődik össze, a tartalomtípusból és az előző szakaszban leírt API-kulcsból:

         content-type: application/json
         api-key: <placeholder>

### <a name="fiddler"></a>Fiddler

Állítson össze egy, az alábbi képernyőfelvételen láthatóhoz hasonló kérelmet. Ebben használja a **PUT** parancsot. A Fiddler beszúrja a `User-Agent=Fiddler` paramétert. A két további kérelemfejléc ez alá új sorokba illeszthető be. A szolgáltatás tartalomtípusát és API-kulcsát illessze be a szolgáltatás rendszergazdai hozzáférési kulcsának használatával.

![Fiddler-kérelem fejléce][1]

> [!Tip]
> A webes forgalom kikapcsolásával elrejtheti a folyamatban lévő feladathoz nem kapcsolódó külső HTTP-tevékenységet. A Fiddlerben lépjen a **File** (Fájl) menüre, és kapcsolja ki a **Capture Traffic** (Forgalom rögzítése) beállítást. 

### <a name="postman"></a>Postman

Állítson össze egy, az alábbi képernyőfelvételen láthatóhoz hasonló kérelmet. Ebben használja a **PUT** parancsot. 

![Postman-kérelem fejléce][6]

## <a name="create-the-index"></a>Az index létrehozása

A kérelem törzse tartalmazza az indexdefiníciót. A kérelemtörzs hozzáadása teszi teljessé az indexet létrehozó kérelmet.

Az index neve mellett a mezők gyűjteménye a kérelem legfontosabb összetevője. A mezők gyűjteménye határozza meg az indexsémát. Mindegyik mező esetében határozza meg annak típusát. A keresésekben a karakterláncokat tartalmazó mezők vesznek részt, ezért a numerikus adatokat is érdemes karakterláncként beállítani, ha azt szeretné, hogy a keresés kiterjedjen rájuk.

Az egyes mezők attribútumai határozzák meg az engedélyezett műveleteket. A REST API-k alapértelmezés szerint számos műveletet lehetővé tesznek. Például alapértelmezés szerint minden karakterlánc kereshető, lekérdezhető, szűrhető és kategorizálható. Gyakran csak akkor kell attribútumokat beállítania, ha valamely viselkedést ki szeretné kapcsolni. Az attribútumokkal kapcsolatos további információk: [Index létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).

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

### <a name="fiddler"></a>Fiddler

Másolja az indexdefiníciót a kérelemtörzsbe az alábbi képernyőfelvételhez hasonlóan, majd kattintson a jobb felső sarokban az **Execute** (Végrehajtás) parancsra a befejezett kérelem elküldéséhez.

![Fiddler-kérelem törzse][7]

### <a name="postman"></a>Postman

Másolja az indexdefiníciót a kérelemtörzsbe az alábbi képernyőfelvételhez hasonlóan, majd kattintson a jobb felső sarokban a **Send** (Küldés) parancsra a befejezett kérelem elküldéséhez.

![Postman-kérelem törzse][8]

## <a name="load-documents"></a>Dokumentumok betöltése

Az index létrehozása és adatokkal való feltöltése két különböző lépés. Az Azure Searchben az index tartalmazza az összes kereshető adatot, amelyeket JSON-dokumentumok formájában adhat be. Az ehhez a tevékenységhez tartozó API áttekintéséhez lásd: [Dokumentumok hozzáadása, frissítése vagy törlése (REST)](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

+ Ebben a lépésben a parancsot cserélje a **POST** parancsra.
+ Módosítsa a végpontot, hogy tartalmazza a `/docs/index` útvonalat. A teljes URL-címnek a következőképp kell kinéznie: `https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01`
+ A kérelemfejléceket ne módosítsa. 

A kérelem törzse négy, a szállodák indexéhez hozzáadandó dokumentumot tartalmaz.

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

Néhány másodperc múlva megjelenik a 200-as HTTP-válasz a munkamenetlistában. Ez azt jelenti, hogy a dokumentumok sikeresen létrejöttek. 

Ha a 207-es HTTP-válasz jelenik meg, legalább egy dokumentumot nem sikerült feltölteni. Ha a 404-es válasz jelenik meg, akkor a kérelem fejlécében vagy törzsében szintaktikai hiba van: ellenőrizze, hogy valóban módosította-e a végpontot, hogy tartalmazza a `/docs/index` útvonalat.

> [!Tip]
> A kiválasztott adatforrások esetében választhatja az alternatív *indexelő* megközelítést, amely egyszerűbb, és csökkenti az indexáláshoz szükséges kódot. További információkért lásd: [Indexelőműveletek](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).

### <a name="fiddler"></a>Fiddler

Cserélje a parancsot a **POST** parancsra. Módosítsa az URL-címet, hogy tartalmazza a `/docs/index` útvonalat. Másolja a dokumentumokat a kérelemtörzsbe az alábbi képernyőfelvételhez hasonlóan, majd hajtsa végre a kérelmet.

![Fiddler-kérelem hasznos adatai][9]

### <a name="postman"></a>Postman

Cserélje a parancsot a **POST** parancsra. Módosítsa az URL-címet, hogy tartalmazza a `/docs/index` útvonalat. Másolja a dokumentumokat a kérelemtörzsbe az alábbi képernyőfelvételhez hasonlóan, majd hajtsa végre a kérelmet.

![Postman-kérelem hasznos adatai][10]

## <a name="query-the-index"></a>Az index lekérdezése
Most, hogy az index és a dokumentumok is betöltődtek, lekérdezheti őket. További információ erről az API-ról: [Dokumentumok keresése (REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents)  

+ Ebben a lépésben a parancsot cserélje a **GET** parancsra.
+ Módosítsa a végpontot, hogy tartalmazza a lekérdezési paramétereket, köztük a keresett karakterláncokat is. A lekérdezési URL például a következőképp nézhet ki: `https://my-app.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2016-09-01`
+ A kérelemfejléceket ne módosítsa

Ez a lekérdezés a „motel” kifejezést keresi, és a találatokban szereplő dokumentumok számát adja vissza. A **Send** (Küldés) gombra kattintva a Postman-kérelem és -válasz az alábbi képernyőképhez hasonló lesz. Az állapotkódnak 200-nak kell lennie.

 ![Postman-lekérdezés válasza][11]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Tippek a mintalekérdezések Fiddlerben való futtatásához

A következő példalekérdezés az [A keresési index működése (Azure Search API)](http://msdn.microsoft.com/library/dn798927.aspx) cikkből származik. A cikkben szereplő számos példalekérdezés szóközt is tartalmaz, amely a Fiddler esetében nem engedélyezett. Minden szóközt cseréljen le + karakterre, mielőtt beillesztené a lekérdezési karakterláncot a lekérdezés Fiddler használatával történő megkísérléséhez.

**A szóközök cseréje előtt (a „lastRenovationDate desc” részben):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01

**A szóközök + jelre cserélése után (a „lastRenovationDate+desc” részben):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2016-09-01

## <a name="query-index-properties"></a>Indextulajdonságok lekérdezése
A rendszer-információk lekérdezésével a dokumentumok számát és a tárhelyhasználatot is lekérheti: `https://my-app.search.windows.net/indexes/hotels/stats?api-version=2016-09-01`

A Postmanben a kérelem az alábbihoz fog hasonlítani, a válaszban pedig szerepleni fog a dokumentumok száma és a felhasznált lemezterület mérete bájtban megadva.

 ![Postman-rendszerlekérdezés][12]

Figyelje meg, hogy az api-version szintaxisa eltér. Ebben a kérelemben a `?` karakterrel fűzheti hozzá az api-version paramétert. A ? karakter választja el az URL-címet a lekérdezési karakterlánctól, az & karakter pedig az egyes „név=érték” párokat a lekérdezési karakterláncon belül. Ebben a lekérdezésben az api-version az első és egyetlen lekérdezési karakterlánc.

További információk erről az API-ról: [Indexstatisztikák lekérdezése (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).


### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Tippek az indexstatisztikák Fiddlerben történő megtekintéséhez

A Fiddlerben kattintson az **Inspectors** (Vizsgálók), majd a **Headers** (Fejlécek) fülre, végül válassza ki a JSON formátumot. Ekkor megjelenik a dokumentumok száma és a tárhely mérete (KB).

## <a name="next-steps"></a>További lépések

A REST-ügyfelek rendkívül hasznosak a rögtönzött vizsgálatokhoz, most azonban, hogy már ismeri a REST API-k működését, folytathatja a kódolást. A következő lépésekért lásd az alábbi hivatkozásokat:

+ [Index létrehozása (REST)](search-create-index-rest-api.md)
+ [Adatok importálása (REST)](search-import-data-rest-api.md)
+ [Keresés az indexekben (REST)](search-query-rest-api.md)

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