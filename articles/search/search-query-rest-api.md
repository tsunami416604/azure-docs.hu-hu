---
title: Index lekérdezése (REST API – Azure Search) | Microsoft Docs
description: Létrehozhat keresési lekérdezést az Azure Search szolgáltatásban, a keresési eredmények szűrését és rendezését pedig keresési paraméterek használatával végezheti el.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.topic: quickstart
ms.date: 04/20/2018
ms.openlocfilehash: 035dc4ac349513867253e5593e01fab4fec62f6b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186511"
---
# <a name="query-your-azure-search-index-using-the-rest-api"></a>Az Azure Search-index lekérdezése a REST API használatával
> [!div class="op_single_selector"]
>
> * [Áttekintés](search-query-overview.md)
> * [Portál](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
>
>

Ebből a cikkből megtudhatja, hogyan történik egy index lekérdezése az [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/) használatával.

A bemutató elindítása előtt [létre kell hoznia egy Azure Search-indexet](search-what-is-an-index.md), majd [fel kell töltenie azt adatokkal](search-what-is-data-import.md). Háttér-információkért lásd: [A teljes szöveges keresés működése az Azure Search szolgáltatásban](search-lucene-query-architecture.md).

## <a name="identify-your-azure-search-services-query-api-key"></a>Azonosítsa az Azure Search szolgáltatás lekérdezési API-kulcsát
Az Azure Search REST API-ján végrehajtott keresési műveletek egyik fontos eleme az Ön által üzembe helyezett szolgáltatás számára létrehozott *API-kulcs*. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

1. A szolgáltatás API-kulcsainak megkereséséhez bejelentkezhet az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg az Azure Search szolgáltatáspaneljét
3. Kattintson a „Kulcsok” ikonra

A szolgáltatás *rendszergazdai kulcsokkal* és *lekérdezési kulcsokkal* rendelkezik.

* Az elsődleges és másodlagos *rendszergazdai kulcsok* teljes jogosultságot biztosítanak az összes művelethez, beleértve a szolgáltatás felügyeletének, valamint az indexek, indexelők és adatforrások létrehozásának és törlésének képességét. Két kulcs létezi, tehát ha az elsődleges kulcs újbóli létrehozása mellett dönt, a másodlagos kulcsot továbbra is használhatja (ez fordítva is igaz).
* A *lekérdezési kulcsok* csak olvasási hozzáférést biztosítanak az indexekhez és dokumentumokhoz, és általában a keresési kérelmeket kibocsátó ügyfélalkalmazások kapják meg őket.

Indexlekérdezéshez a lekérdezési kulcsok egyikét használhatja. A rendszergazdai kulcsok szintén használhatók a lekérdezésekhez, az alkalmazáskódban azonban inkább lekérdezési kulcsot használjon, mivel ez a módszer jobban követi a [legalacsonyabb jogosultsági szint elvét](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="formulate-your-query"></a>A lekérdezés meghatározása
Kétféleképpen [keresheti meg az indexet a REST API használatával](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Az egyik lehetőség egy HTTP POST kérés kiadása azon a helyen, ahol a lekérdezési paraméterek vannak meghatározva a kéréstörzs JSON-objektumában. A másik lehetőség egy HTTP GET kérés kiadása azon a helyen, ahol a lekérdezési paraméterek vannak meghatározva a kérés URL-címén belül. A lekérdezési paraméterek méretének tekintetében a POST több [enyhe korlátozással](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) rendelkezik, mint a GET. Éppen ezért a POST használatát javasoljuk, hacsak nem állnak fenn olyan speciális körülmények, amelyek a GET használatát kényelmesebbé tennék.

A POST és a GET esetében egyaránt meg kell majd adnia a *szolgáltatás nevét*, az *index nevét*, valamint a megfelelő *API-verziót* (a jelen dokumentum kiadásakor érvényes API-verzió: `2017-11-11`) a kérés URL-címében. A GET esetében a lekérdezési paramétereket az URL-cím végén található *lekérdezési karakterláncban* kell megadni. Az URL-cím formátuma alább látható:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2017-11-11

A POST esetében a formátum ugyanez, azzal a kiegészítéssel, hogy a lekérdezési karakterlánc paraméterei között csak az API-verzió szerepel.

#### <a name="example-queries"></a>Példa a lekérdezésekre
Alább néhány példa látható a „hotels” nevű index lekérdezéseire. Ezek a lekérdezések GET- és POST-formátumban is megtekinthetők.

A teljes indexben keres a „budget” kifejezésre, és csak a `hotelName` mezőt adja vissza:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2017-11-11

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2017-11-11
{
    "search": "budget",
    "select": "hotelName"
}
```

Egy olyan szűrőt alkalmaz az indexen, amely az éjszakánkénti 150 dollárnál olcsóbb szállodákra keres rá, majd visszaadja a `hotelId` és `description` mezőket:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2017-11-11

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2017-11-11
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

A teljes indexen végrehajtja a keresést, azt egy adott mező (`lastRenovationDate`) szerint csökkenő sorrendbe rendezi, veszi az első két találatot, majd kizárólag a `hotelName` és `lastRenovationDate` mezőket jeleníti meg:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2017-11-11

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2017-11-11
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="submit-your-http-request"></a>A HTTP-kérés elküldése
Azt követően, hogy meghatározta a lekérdezést a HTTP-kérés (GET esetében) URL-címének vagy (POST esetében) törzsének részeként, megadhatja a kérésfejléceket, majd elküldheti a lekérdezést.

#### <a name="request-and-request-headers"></a>Kérés és kérésfejlécek
A GET esetében kettő, a POST esetében három kérésfejlécet kell meghatároznia:

1. Az `api-key` fejléc beállításának a fenti I. lépésben található lekérdezési kulcsnak kell lennie. Az `api-key` fejléceként rendszergazdai kulcsot is használhat, javasolt azonban a lekérdezési kulcs használata, mivel az kizárólag csak olvasási hozzáférést biztosít az indexekhez és a dokumentumokhoz.
2. Az `Accept` fejléc beállítása a következő legyen: `application/json`.
3. Kizárólag a POST kérelem esetében, a `Content-Type` fejléc beállítása szintén a következő legyen: `application/json`.

Az alábbiakban megtekintheti a „hotels” index Azure Search REST API használatával történő kereséséhez tartozó HTTP GET kérést, amely a „motel” kifejezésre egy egyszerű lekérdezéssel keres rá:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2017-11-11
Accept: application/json
api-key: [query key]
```

Az alábbiakban ugyanazt a példalekérdezést láthatja a HTTP POST használatával:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2017-11-11
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

A sikeres lekérdezési kérés `200 OK` állapotkódot eredményez, a keresési eredmények pedig a kéréstörzsben, JSON-objektum formájában lesznek visszaadva. A fenti lekérdezés eredménye a következőképpen fog megjelenni, feltételezve, hogy a „hotels” index az [Adatok importálása az Azure Search szolgáltatásban REST API használatával](search-import-data-rest-api.md) rész mintaadataival van feltöltve (vegye figyelembe, hogy a JSON a jobb áttekinthetőség érdekében formázva van).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

További segítségért tekintse meg a [Dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) rész „Válasz” szakaszát. További információk a meghiúsult műveletek esetében visszaadható HTTP-állapotkódokról: [HTTP-állapotkódok (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).
