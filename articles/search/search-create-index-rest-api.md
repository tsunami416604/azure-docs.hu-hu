---
title: "Azure Search-index létrehozása REST API használatával | Microsoft Docs"
description: "Index létrehozása kódból az Azure Search HTTP REST API használatával."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: ac6c5fba-ad59-492d-b715-d25a7a7ae051
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: fc2f30569acc49dd383ba230271989eca8a14423
ms.openlocfilehash: 107186c6d77550948169caa6f0f89589dd3bddd9

---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>Azure Search-index létrehozása REST API használatával
> [!div class="op_single_selector"]
>
> * [Áttekintés](search-what-is-an-index.md)
> * [Portál](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
>
>

Ez a cikk végigvezeti az Azure Search-[index](https://msdn.microsoft.com/library/azure/dn798941.aspx) Azure Search REST API használatával történő létrehozásának folyamatán.

Már az útmutató követése és az index létrehozása előtt [létre kell hoznia egy Azure Search szolgáltatást](search-create-service-portal.md).

Az Azure Search-index REST API használatával történő létrehozásához egyetlen HTTP POST-kérelmet fog küldeni az Azure Search szolgáltatás URL-végpontjának. Az index definícióját a kérés törzse fogja tartalmazni megfelelően formázott JSON-tartalomként.

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>I. Az Azure Search szolgáltatás adminisztrációs API-kulcsának azonosítása
Most, hogy létrehozta az Azure Search szolgáltatást, HTTP-kérelmeket küldhet a szolgáltatás URL-végpontjának a REST API használatával. Azonban az *összes* API-kérelemnek tartalmaznia kell a létesített Search szolgáltatás számára előállított API-kulcsot. Érvényes kulcs birtokában kérelmenként bizalom hozható létre a kérelmet küldő alkalmazás és a kérelmet kezelő szolgáltatás között.

1. A szolgáltatás API-kulcsainak megkereséséhez be kell jelentkeznie az [Azure portálra](https://portal.azure.com/)
2. Nyissa meg az Azure Search szolgáltatáspaneljét
3. Kattintson a „Kulcsok” ikonra

A szolgáltatás *rendszergazdai kulcsokkal* és *lekérdezési kulcsokkal* fog rendelkezni.

* Az elsődleges és másodlagos *rendszergazdai kulcsok* teljes jogosultságot biztosítanak az összes művelethez, beleértve a szolgáltatás felügyeletének, valamint az indexek, indexelők és adatforrások létrehozásának és törlésének képességét. Két kulcs létezi, tehát ha az elsődleges kulcs újbóli létrehozása mellett dönt, a másodlagos kulcsot továbbra is használhatja (ez fordítva is igaz).
* A *lekérdezési kulcsok* csak olvasási hozzáférést biztosítanak az indexekhez és dokumentumokhoz, és általában a keresési kérelmeket kibocsátó ügyfélalkalmazások kapják meg őket.

Index létrehozása céljából az elsődleges és a másodlagos adminisztrációs kulcsot is használhatja.

## <a name="ii-define-your-azure-search-index-using-well-formed-json"></a>II. Az Azure Search-index meghatározása megfelelően formázott JSON-tartalommal
A szolgáltatásnak küldött egyetlen HTTP POST-kérelem létrehozza az indexet. A HTTP POST-kérelem törzse egyetlen, az Azure Search-indexet meghatározó JSON-objektumot fog tartalmazni.

1. A JSON-objektum első tulajdonsága az index neve.
2. A JSON-objektum második tulajdonsága egy `fields` nevű JSON-tömb, amely külön JSON-objektumokat tartalmaz az index egyes mezőihez. A JSON-objektumok mindegyike több név-érték párt tartalmaz a mezők minden attribútumához (például a „name”, „type” stb. attribútumokhoz).

Az index tervezésekor nagyon fontos figyelembe venni a keresés során tapasztalt felhasználói élményt és az üzleti igényeket, mivel minden egyes mezőt a [megfelelő attribútumokhoz](https://msdn.microsoft.com/library/azure/dn798941.aspx) kell hozzárendelni. Ezek az attribútumok határozzák meg, hogy melyik mezőkre melyik keresési funkciók (szűrés, értékkorlátozás, rendezés, teljes szöveges keresés stb.) vonatkoznak. A meg nem adott attribútumok esetén az alapértelmezett beállítás a vonatkozó keresési funkció engedélyezése, hacsak kifejezetten le nem tiltja a funkciót.

A fenti példában az indexnek a „hotels” nevet adtuk, és a mezőket az alábbiak szerint definiáltuk:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Minden mező esetében annak alapján választottuk ki az indexattribútumokat, ahogyan szerintünk az alkalmazások használni fogják őket. A `hotelId` például egy olyan egyedi kulcs, amelyet a szállodát kereső emberek valószínűleg nem fognak ismerni, ezért a mező esetén a `searchable` `false` értékre állításával letiltjuk a teljes szöveges keresést, így helyet takarítunk meg az indexben.

Vegye figyelembe, hogy az indexben csakis egy `Edm.String` típusú mező lehet kijelölve „kulcsmezőként”.

A fenti indexdefiníció egyéni nyelvi elemzőt használ a `description_fr` mezőhöz, mert a mező francia szöveg tárolására szolgál. A nyelvi elemzőkkel kapcsolatos további információkért tekintse meg [az MSDN Nyelvi támogatás című témakörét](https://msdn.microsoft.com/library/azure/dn879793.aspx), valamint a vonatkozó [blogbejegyzést](https://azure.microsoft.com/blog/language-support-in-azure-search/).

## <a name="iii-issue-the-http-request"></a>III. A HTTP-kérelem küldése
1. Az indexdefiníció kérelemtörzsként való használatához küldjön egy HTTP POST-kérelmet az Azure Search szolgáltatásvégpontjának URL-címére. Ügyeljen arra, hogy az URL-címben a szolgáltatásnevet használja állomásnévként, és hogy a megfelelő `api-version` szerepeljen a lekérdezési karakterlánc paraméterként (a dokumentum közzétételének időpontjában az aktuális API-verzió a `2016-09-01`).
2. A kérelem fejlécében a `Content-Type` számára adja meg az `application/json` beállítást. Az `api-key` fejlécben is meg kell adni a szolgáltatás I. lépésben azonosított adminisztrációs kulcsát.

Az alábbi kérelem küldéséhez meg kell adnia a saját szolgáltatásnevét és API-kulcsát:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [api-key]


Ha a kérelem sikeres, a 201-es állapotkód (Létrehozva) jelenik meg. Az indexek REST API-n keresztüli létrehozásával kapcsolatos további információkért tekintse meg az API-referenciát az [MSDN webhelyén](https://msdn.microsoft.com/library/azure/dn798941.aspx). A hiba esetén megjelenő egyéb HTTP-állapotkódokkal kapcsolatos további információkért tekintse meg a [HTTP-állapotkódok (Azure Search)](https://msdn.microsoft.com/library/azure/dn798925.aspx) című cikket.

Miután végzett az index használatával, és törölni szeretné, csak küldjön egy HTTP DELETE kérelmet. A „hotels” nevű index például a következőképpen törölhető:

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2016-09-01
    api-key: [api-key]


## <a name="next"></a>Tovább
Az Azure Search-index létrehozása után készen áll arra, hogy [feltöltse a tartalmát az indexbe](search-what-is-data-import.md), és megkezdje az adatok keresését.



<!--HONumber=Nov16_HO3-->


