---
pageTitle: Synonyms in Azure Search | Microsoft Docs
description: Bontsa ki a keresési lekérdezés hatókörét szinonimák használata
authors: mhko
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
manager: jlembicz
ms.author: nateko
ms.openlocfilehash: 03e45aae37a0c0474dbd9cc5dd5e3fddd347bd62
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="synonyms-in-azure-search"></a>Az Azure Search szinonimák

Szinonimák a keresőprogramok, amely implicit módon bontsa ki a lekérdezés hatókörét a felhasználónak nem kell ténylegesen adja meg a kifejezés nem megfelelő használati társítani. Például a kifejezés "kutya" és a szinonima rendelését "canine" és "ételadagot", "kutya" tartalmazó dokumentumokat kap, "canine" vagy "ételadagot" is tartozik a lekérdezés hatókörén belül.

Az Azure Search szinonimát bővítése időben történik. Egy szolgáltatás együtt nincs meglévő műveletek megszakadását szinonimát leképezéseket adhat hozzá. Hozzáadhat egy **synonymMaps** anélkül, hogy az index újraépítése mező definíció tulajdonságot.

## <a name="feature-availability"></a>Szolgáltatások rendelkezésre állása

A szinonimák támogatja a legújabb api-verzió (api-version = 2017-11-11). Jelenleg nincs Azure Portal-támogatás.

## <a name="how-to-use-synonyms-in-azure-search"></a>Az Azure search szinonimák használata

Az Azure Search szinonimát támogatása, amely meghatározza, és töltse fel a szolgáltatás a szinonima maps alapul. A maps képeznek egy független erőforrás (például az adatforrásokat, illetve indexek), és használhatja a keresési szolgáltatás index bármely kereshető mező.

Szinonimát képezi le, és indexek egymástól függetlenül megmaradjanak. Miután szinonimát térképre határozza meg, és töltse fel azt a szolgáltatást, a mező szinonimát funkciót nevű új tulajdonsággal hozzáadásával engedélyezheti **synonymMaps** mező definíciójában. Mindig egy teljes-dokumentum művelet, ami azt jelenti, hogy Ön nem létrehozása, frissítése vagy törlése a szinonima térkép részei Növekményesen létrehozása, frissítése és törlése szinonimát térképre. Egy Újrabetöltés még egy-egy bejegyzésnek frissítése szükséges.

Szinonimák beépítése a keresőalkalmazás két lépésből áll:

1.  Vegyen fel egy szinonima társítást az alábbi API-k segítségével a keresési szolgáltatáshoz.  

2.  Az index definícióját a szinonima térkép használandó kereshető mezők konfigurálására.

### <a name="synonymmaps-resource-apis"></a>SynonymMaps erőforrás API-k

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Adja hozzá, vagy frissítse a szolgáltatás alatt szinonimát térkép, POST használatával, vagy HELYEZZE.

Szinonimát maps feltöltötte-e a szolgáltatás POST vagy PUT keresztül. Minden egyes szabály az új sor karaktert ("\n") kell elválasztani. Legfeljebb 5000 szabálynál szinonimát térkép szabad szolgáltatásban és más termékváltozatok 10 000 szabályokat adhat meg. Minden egyes szabály legfeljebb 20 bővítések rendelkezhet.

Szinonimát maps az alábbiakban ismertetett Apache Solr formátumúnak kell lennie. Ha egy meglévő szinonimát szótár más formátumú, és közvetlenül használja, tudassa velünk, a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Létrehozhat egy új szinonimát leképezés HTTP POST használatával az alábbi példában látható módon:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

    {  
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Azt is megteheti PUT használja, és adja meg az URI a szinonima térkép nevét. Ha a szinonima leképezés nem létezik, a rendszer létrehozza.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

    {  
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Apache Solr szinonimát formátumban

A Solr formátumhoz egyenértékű és explicit szinonimát leképezéseket. A nyílt forráskódú szinonimát szűrő megadását Apache Solr, a jelen dokumentumban ismertetett igazodik leképezési szabályokat: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Az alábbiakban az egyenértékű szinonimák minta szabályt.
```
USA, United States, United States of America
```

Keresési lekérdezés a fenti a szabály az "USA" bővített "USA" vagy "Az Amerikai Egyesült Államok" vagy "Az Amerikai Egyesült Államok".

Explicit leképezési nyíl megjelölt "= >". Megadása esetén a kifejezés, amely megfelel a bal oldali keresési lekérdezés sorozatát "= >" váltja fel a alternatívák jobb oldalán. Az alábbi szabály tekintve keresőkifejezések "Washington", "Wash." vagy a "WA" összes felülíródik a "WA". Explicit leképezési csak a megadott irányban vonatkozik, és ebben az esetben nem írja a lekérdezés "Washington" a "WA".
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Lista szinonimát leképezi a szolgáltatás alatt.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>A szolgáltatás alatt szinonimát térkép beolvasása.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>A szolgáltatás alatt szinonimák térképre törlése.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Az index definícióját a szinonima térkép használandó kereshető mezők konfigurálására.

Új mező tulajdonság **synonymMaps** segítségével adja meg a szinonima térképre használandó kereshető mező. Szinonimát maps szolgáltatás szintű erőforrás, és az index a szolgáltatás alatt futó minden mező szerint lehet hivatkozni.

    POST https://[servicename].search.windows.net/indexes?api-version=2017-11-11
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymMaps** adható meg "Edm.String" vagy "Collection(Edm.String)" típusú kereshető mezőt.

> [!NOTE]
> Akkor is csak egy szinonima mezőben hozzárendelését. Ha több szinonimát maps használni kívánt, tudassa velünk, a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Egyéb keresési funkciókat a szinonimák hatása

A szinonimák szolgáltatás felülírja az eredeti lekérdezés szinonimák a vagy operátorral. Emiatt találati kiemelése és profilok pontozási kezeli az eredeti és a szinonimák egyenértékűként.

Szinonimát szolgáltatás keresési lekérdezések, és nem vonatkozik a szűrők vagy értékkorlátozást. Ehhez hasonlóan javaslatok alapuló csak az eredeti kifejezés; találat szinonimát a válaszban nem jelennek meg.

Szinonimát bővítések nem vonatkoznak a helyettesítő karakteres kifejezést; előtag, az intelligens egyeztetésű, és a regex feltételek nem bontja ki.

## <a name="tips-for-building-a-synonym-map"></a>Tippek az szinonimát térképre felépítése

- Egy rövid, tetszetős szinonimát leképezés hatékonyabb, mint a lehetséges megfeleltetésekben teljesnek. A túl nagy vagy összetett szótárak elemzése, és ezek befolyásolják a lekérdezés a lekérdezés sok szinonimák való tárolásához hosszabb ideig. Ahelyett, hogy a becslés, amellyel feltételek használhatók, a tényleges feltételeket keresztül kérhet egy [keresési forgalom elemző jelentés](search-traffic-analytics.md).

- Egy előzetes és az érvényesítés megadásával, mert engedélyezése, majd használja ez a jelentés pontosan határozza meg, hogy mely feltételeit fog hasznot húzhatnak a szinonima egyezés, és folytassa használni, hogy a szinonima térképre van előállító jobb eredményének ellenőrzése. Az előre meghatározott jelentésben a csempék "leggyakoribb keresési lekérdezések" és "nulla-eredmény keresési lekérdezések" Erre azért van szükség a szükséges információkat.

- Létrehozhat több szinonimát maps keresési alkalmazás (például úgy, hogy ha az alkalmazás támogatja a többnyelvű vásárlói bázisunk nyelv). Jelenleg a mező csak egyikét használhatja őket. A mező synonymMaps tulajdonság bármikor frissítheti.

## <a name="next-steps"></a>További lépések

- Ha egy meglévő index (nem éles) fejlesztői környezetben, hogyan szinonimák hozzáadása módosítja keresési élményt biztosít, beleértve a profilok pontozási gyakorolt megtekintéséhez kattintson a kijelölés kis dictionary és javaslatok kísérletezhet.

- [Engedélyezze a keresési forgalom analytics](search-traffic-analytics.md) és az előre definiált Power BI-jelentés segítségével megtudhatja, mely kifejezések a legtöbb, és melyeket térjen vissza a dokumentumot. A szótár kell lennie során az indexben dokumentumok rendszer lekérdezésekhez szinonimák felvenni volt képes ezek insights felvértezve, vizsgálja felül.
