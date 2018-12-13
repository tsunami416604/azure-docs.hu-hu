---
title: A search szolgáltatás REST API – Azure Search használatával az adatok feltöltése
description: Ismerje meg, hogyan tölthet fel adatokat egy teljes szöveges kereshető indexet az Azure Search HTTP-kérések és a REST API használatával.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/20/2018
ms.custom: seodec2018
ms.openlocfilehash: b3044ec3fb21e77c5174ebd5a6b2dabd2282240f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312855"
---
# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Adatfeltöltés az Azure Search szolgáltatásba a REST API használatával
> [!div class="op_single_selector"]
>
> * [Áttekintés](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
>
>

Jelen cikk az Azure Search-indexbe történő adatimportálást mutatja be az [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/) használatával.

A bemutató elindítása előtt [létre kell hoznia egy Azure Search-indexet](search-what-is-an-index.md).

A dokumentumok REST API használatával az indexbe történő küldéséhez egy HTTP POST kérést fog kiadni az index URL-címének végpontján. A HTTP-kérés törzse egy olyan JSON-objektum, amely tartalmazza a hozzáadni, módosítani vagy törölni kívánt dokumentumokat.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Azonosítsa az Azure Search szolgáltatás rendszergazdai API-kulcsát
Ha a REST API használatával HTTP-kérések kiadását végzi a szolgáltatáson, *mindegyik* API-kérésnek tartalmaznia kell az Ön által üzembe helyezett Search szolgáltatáshoz létrehozott API-kulcsot. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

1. A szolgáltatás API-kulcsainak megkereséséhez bejelentkezhet az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg az Azure Search szolgáltatáspaneljét
3. Kattintson a „Kulcsok” ikonra

A szolgáltatás *rendszergazdai kulcsokkal* és *lekérdezési kulcsokkal* fog rendelkezni.

* Az elsődleges és másodlagos *rendszergazdai kulcsok* teljes jogosultságot biztosítanak az összes művelethez, beleértve a szolgáltatás felügyeletének, valamint az indexek, indexelők és adatforrások létrehozásának és törlésének képességét. Két kulcs létezi, tehát ha az elsődleges kulcs újbóli létrehozása mellett dönt, a másodlagos kulcsot továbbra is használhatja (ez fordítva is igaz).
* A *lekérdezési kulcsok* csak olvasási hozzáférést biztosítanak az indexekhez és a dokumentumokhoz, és általában a keresési kéréseket kibocsátó ügyfélalkalmazások számára vannak kiosztva.

Egy indexbe történő adatimportáláshoz az elsődleges vagy a másodlagos rendszergazdai kulcsok bármelyikét használhatja.

## <a name="decide-which-indexing-action-to-use"></a>A használni kívánt indexelési művelet megadása
A REST API használatakor JSON-kéréstörzsekkel ellátott HTTP POST kéréseket fog kiadni az Azure Search-index végponti URL-címére. A HTTP-kéréstörzsben lévő JSON-objektum egyetlen „value” nevű egyedi JSON-tömböt fog tartalmazni. A tömbben lévő JSON-objektumok az indexhez hozzáadni, abban frissíteni vagy abból törölni kívánt dokumentumokat képviselik.

A „value” tömbben található minden JSON-objektum egy-egy indexelendő dokumentumot képvisel. Ezen objektumok mindegyike tartalmazza a dokumentum kulcsát, valamint megadja a végrehajtani kívánt indexelési műveletet (például feltöltés, egyesítés vagy törlés). Attól függően, hogy az alábbi műveletek közül melyiket választja ki, az egyes dokumentumok esetében csak bizonyos mezők lesznek kötelezően megjelenítendők:

| @search.action | Leírás | Az egyes dokumentumok kötelező mezői | Megjegyzések |
| --- | --- | --- | --- |
| `upload` |Az `upload` művelet működése hasonló az „upsert” (frissítés/beszúrás) műveletéhez, ahol a rendszer az új dokumentumot beilleszti, ha pedig már létező dokumentumról van szó, akkor frissíti/kicseréli azt. |billentyű, továbbá a meghatározni kívánt egyéb mezők |Létező dokumentum frissítése/cseréje esetén a kérésben nem megadott mezők beállítása a következő lesz: `null`. Ez történik abban az esetben is, ha a mező korábban nem null értékre lett beállítva. |
| `merge` |Egy meglévő dokumentumot frissít a megadott mezőkkel. Ha a dokumentum nem található az indexben, az egyesítés meg fog hiúsulni. |billentyű, továbbá a meghatározni kívánt egyéb mezők |A rendszer az egyesítési művelet során megadott mezőkre cseréli a dokumentum meglévő mezőit. Ez `Collection(Edm.String)` típusú mezőket tartalmaz. Ha például a dokumentum egy `["budget"]` értékű `tags` mezőt tartalmaz, és egyesítést hajt végre a `tags` mező `["economy", "pool"]` értékével, a `tags` mező végső értéke `["economy", "pool"]` lesz. Nem pedig a következő lesz: `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Ha az indexben már létezik az adott kulccsal ellátott dokumentum, ezen művelet viselkedése hasonló lesz a `merge` műveletéhez. Ha nem létezik ilyen dokumentum, a művelet viselkedése az `upload` új dokumentum esetében mutatott viselkedésének fog megfelelni. |billentyű, továbbá a meghatározni kívánt egyéb mezők |- |
| `delete` |Eltávolítja a megadott dokumentumot az indexből. |csak billentyű |A rendszer figyelmen kívül hagyja a kulcsmezőn kívül megadott mezőket. Ha egyetlen mezőt kíván eltávolítani a dokumentumból, e helyett használja a `merge` műveletet, és a mező számára explicit módon adja meg a null értéket. |

## <a name="construct-your-http-request-and-request-body"></a>A HTTP-kérés és a kérés törzsének létrehozása
Most, hogy összegyűjtötte az indexelési műveletekhez szükséges mezők értékeit, készen áll a tulajdonképpeni HTTP-kérés és a JSON-kérés törzsének létrehozására az adatok importálásához.

#### <a name="request-and-request-headers"></a>Kérés és kérésfejlécek
Az URL-címben meg kell majd adnia a szolgáltatás nevét, az index nevét (ami ebben az esetben „hotels”), valamint a megfelelő API-verziót (a jelen dokumentum kiadásakor érvényes API-verzió: `2017-11-11`). Meg kell határoznia a `Content-Type` és `api-key` kérésfejléceket is. Az utóbbi esetében használja a szolgáltatás rendszergazdai kulcsainak egyikét.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>A kérelem törzse
```JSON
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
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

Ebben az esetben a következő keresési műveleteket használjuk: `upload`, `mergeOrUpload` és `delete`.

Jelen példában feltételezzük, hogy a „hotels” index már fel van töltve dokumentumokkal. Figyelje meg, hogy az `mergeOrUpload` használatakor nem volt szükséges megadni a dokumentumban szereplő összes mezőt, illetve hogy a `delete` használatakor kizárólag a dokumentumkulcsot (`hotelId`) adtuk meg.

Vegye figyelembe azt is, hogy egyetlen indexelési kérésbe legfeljebb 1000 dokumentumot (vagy 16 MB adatmennyiséget) foglalhat.

## <a name="understand-your-http-response-code"></a>A HTTP válaszkód ismertetése
#### <a name="200"></a>200
Az indexelési kérés sikeres elküldését követően HTTP-választ fog kapni a következő állapotkóddal: `200 OK`. A HTTP-válasz JSON-törzse a következő lesz:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Ha az indexelés legalább egy elem esetében meghiúsult, a rendszer a következő állapotkódot fogja visszaadni: `207`. A HTTP-válasz JSON-törzse fogja tartalmazni a sikertelen indexelésű dokumentum(ok) adatait.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [!NOTE]
> Ez leggyakrabban azt jelenti, hogy a Search szolgáltatás terhelése elérte azt a pontot, amelytől kezdve az indexelési kérések `503` válaszokat adnak vissza. Ebben az esetben határozottan javasoljuk az ügyfélkód visszahívását és az újrapróbálkozás előtt egy kis várakozást. Ezzel a rendszer számára időt ad a helyreállításra, így a jövőbeni kérések nagyobb eséllyel lesznek sikeresek. A gyors újrapróbálkozásokkal csupán az adott szituációt állandósítja.
>
>

#### <a name="429"></a>429
Az indexenkénti dokumentumszám-kvóta túllépésekor a rendszer a következő állapotkódot fogja visszaadni: `429`.

#### <a name="503"></a>503
Ha az indexelés a kérésben szereplő összes elem esetében meghiúsult, a rendszer a következő állapotkódot fogja visszaadni: `503`. Ez a hibaüzenet azt jelzi, hogy a rendszer terhelése nagy, és a kérés jelenleg nem dolgozható fel.

> [!NOTE]
> Ebben az esetben határozottan javasoljuk az ügyfélkód visszahívását és az újrapróbálkozás előtt egy kis várakozást. Ezzel a rendszer számára időt ad a helyreállításra, így a jövőbeni kérések nagyobb eséllyel lesznek sikeresek. A gyors újrapróbálkozásokkal csupán az adott szituációt állandósítja.
>
>

További információk a dokumentumokkal végzett műveletekről, illetve a sikeres/meghiúsult műveletekre adott rendszerválaszokról: [Dokumentumok hozzáadása, frissítése vagy törlése](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents). További információk a meghiúsult műveletek esetében visszaadható HTTP-állapotkódokról: [HTTP-állapotkódok (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

## <a name="next-steps"></a>További lépések
Az Azure Search-index feltöltését követően készen áll a dokumentumkeresési lekérdezések kiadásának elindítására. Részletes információk: [Az Azure Search-index lekérdezése](search-query-overview.md).
