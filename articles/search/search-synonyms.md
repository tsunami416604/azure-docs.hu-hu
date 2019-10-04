---
title: A lekérdezés kiterjesztésének szinonimái a keresési indexnél – Azure Search
description: Hozzon létre egy szinonimát a keresési lekérdezés hatókörének kibontásához egy Azure Search indexen. A hatókör ki van bővítve, hogy belefoglalja a listában megadott egyenértékű feltételeket.
author: brjohnstmsft
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
manager: nitinme
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d9ddb5af42c538558a69ce68e7ea90161c947b12
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186457"
---
# <a name="synonyms-in-azure-search"></a>Szinonimák a Azure Searchban

A keresőmotorok szinonimái olyan egyenértékű kifejezésekkel rendelkeznek, amelyek implicit módon bővítik a lekérdezés hatókörét anélkül, hogy a felhasználónak ténylegesen meg kellene adnia a kifejezést. Például a "Dog" kifejezés és a "kutya" és "kiskutya" szinonimái társítása esetén a "Dog", "kutya" vagy "Puppy" tartalmú dokumentumok a lekérdezés hatókörén belül maradnak.

Azure Search a szinonimák bővítése a lekérdezés időpontjában történik. A meglévő műveletek megszakítása nélkül is hozzáadhat szinonimákat a szolgáltatásokhoz. **SynonymMaps** tulajdonságot adhat hozzá egy mező definícióhoz anélkül, hogy újra kellene építenie az indexet.

## <a name="create-synonyms"></a>Szinonimák létrehozása

A rendszer nem támogatja a szinonimák létrehozására szolgáló portál használatát, de használhatja a REST API vagy a .NET SDK-t is. A REST használatának megkezdéséhez javasoljuk, hogy a Poster és az API-t használó kérelmek megfogalmazását [használja](search-get-started-postman.md) : [Szinonima leképezések létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map). A C# fejlesztők számára megkezdheti a [szinonimák hozzáadását az C#Azure Search szolgáltatásban ](search-synonyms-tutorial-sdk.md).

Ha az [ügyfél által felügyelt kulcsokat](search-security-manage-encryption-keys.md) használja a kiszolgálóoldali titkosításhoz, akkor ezt a védelmet a szinonima Térkép tartalmára is alkalmazhatja.

## <a name="use-synonyms"></a>A szinonimák használata

Azure Search a szinonimák támogatása az Ön által definiált és a szolgáltatásba feltöltött szinonimás térképeken alapul. Ezek a térképek egy független erőforrásnak (például indexeknek vagy adatforrásoknak) minősülnek, és a keresési szolgáltatás bármely indexének bármely kereshető mezője használhatja.

A szinonimák és az indexek egymástól függetlenül vannak karbantartva. Miután meghatározta a szinonimákat, és feltölti a szolgáltatásba, engedélyezheti a szinonima funkciót egy mezőhöz egy új, **synonymMaps** nevű tulajdonság hozzáadásával a mező definíciójában. A szinonimák leképezésének létrehozása, frissítése és törlése mindig egy teljes dokumentumból álló művelet, ami azt jelenti, hogy a szinonima-Térkép részeit nem lehet Növekményesen létrehozni, frissíteni vagy törölni. A frissítéshez még egy bejegyzést is újra kell tölteni.

A szinonimák a keresési alkalmazásba való beépítése egy kétlépéses folyamat:

1.  Vegyen fel egy szinonimát a keresési szolgáltatáshoz az alábbi API-kon keresztül.  

2.  Egy kereshető mező beállítása a szinonimák leképezésének használatára az index definíciójában.

### <a name="synonymmaps-resource-apis"></a>SynonymMaps Resource API-k

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Vegyen fel vagy frissítsen egy szinonima-leképezést a szolgáltatásban a POST vagy a PUT paranccsal.

A szinonima térképek a szolgáltatásba POST vagy PUT használatával lesznek feltöltve. Minden szabályt az új vonal karakterének ("\n") kell elválasztani. Egy ingyenes szolgáltatásban és a 10 000-szabályokban az összes többi SKU esetében legfeljebb 5 000 szabályt adhat meg. Minden szabály legfeljebb 20 kiterjesztéssel rendelkezhet.

A szinonima Maps-nek Apache Solr formátumban kell lennie, amely alább látható. Ha egy meglévő szinonimát tartalmazó szótár eltérő formátumú, és közvetlenül szeretné használni, kérjük, tudassa velünk a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

A HTTP POST használatával új szinonimákat hozhat létre, ahogy az alábbi példában is látható:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Azt is megteheti, hogy a PUT elemre kattint, és megadja a szinonimák megfeleltetésének nevét az URI-n. Ha a szinonimák leképezése nem létezik, a rendszer létrehozza.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Apache Solr szinonimájának formátuma

A Solr formátuma támogatja az egyenértékű és a explicit szinonimák leképezését. A leképezési szabályok betartják az Apache Solr nyílt forráskódú szinonimáit, amelyek a jelen dokumentumban olvashatók: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Az alábbi minta szabály egyenértékű szinonimákat mutat be.
```
USA, United States, United States of America
```

A fenti szabály szerint az "USA" keresési lekérdezés az "USA" vagy a "Egyesült Államok" vagy a "Egyesült Államok of America" kifejezésre lesz kiterjesztve.

Az explicit leképezést a "= >" nyíl jelöli. Ha meg van adva, a "= >" bal oldalán található keresési lekérdezés lejárati sorozata a jobb oldalon található alternatívákkal lesz lecserélve. Az alábbi szabály miatt a "Washington", "Wash" lekérdezési lekérdezések. vagy a "WA" kifejezés minden újraírásra kerül a "WA"-be. A explicit leképezés csak a megadott irányra vonatkozik, és a "WA" lekérdezés nem írható át "Washington" értékre ebben az esetben.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>A szolgáltatáshoz tartozó szinonimák listázása.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>A szolgáltatáshoz tartozó szinonima-Térkép beszerzése.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>A szolgáltatáshoz tartozó szinonimák leképezésének törlése.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Egy kereshető mező beállítása a szinonimák leképezésének használatára az index definíciójában.

A **synonymMaps** új mező tulajdonsága egy kereshető mezőhöz használandó szinonima-leképezés megadására használható. A szinonima-térképek szolgáltatási szintű erőforrások, és a szolgáltatásban lévő index bármely mezője hivatkozhat rá.

    POST https://[servicename].search.windows.net/indexes?api-version=2019-05-06
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

a **synonymMaps** megadható a "EDM. String" vagy "Collection (EDM. String)" típusú kereshető mezőkhöz.

> [!NOTE]
> Mezőhöz csak egy szinonimát lehet rendelni. Ha több szinonima-térképet szeretne használni, kérjük, tudassa velünk a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Szinonimák hatása más keresési funkciókra

A szinonima funkció átírja az eredeti lekérdezést szinonimákkal a vagy operátorral. Emiatt a kiemelési és pontozási profilok az eredeti kifejezést és a szinonimákat egyenértékűként kezelik.

A szinonima funkció a keresési lekérdezésekre vonatkozik, és nem vonatkozik a szűrőkre vagy az aspektusokra. Hasonlóképpen, a javaslatok kizárólag az eredeti feltételeken alapulnak; a válaszban nem jelennek meg szinonima egyezések.

A szinonimák bővítései nem érvényesek a helyettesítő karakteres keresési kifejezésekre; az előtag, a fuzzy és a regex kifejezések nincsenek kibontva.

Ha egyetlen lekérdezést kell végrehajtania, amely szinonimákat, valamint helyettesítő karaktereket, regexeket vagy fuzzy kereséseket alkalmaz, a lekérdezéseket a vagy a szintaxis használatával kombinálhatja. Ha például a szinonimákat az egyszerű lekérdezési szintaxishoz helyettesítő karakterekkel kombinálja, a kifejezés `<query> | <query>*`a következő lesz:.

## <a name="tips-for-building-a-synonym-map"></a>Tippek szinonimás Térkép létrehozásához

- Egy rövid, jól megtervezett szinonimás Térkép hatékonyabb, mint a lehetséges egyezések teljes listája. A túlzottan nagy vagy összetett szótárak hosszabb időt is igénybe vehetik a lekérdezés késését, ha a lekérdezés sok szinonimára bővül. Nem hiszem, hogy milyen használati feltételek használhatók, a tényleges kifejezéseket a [keresési forgalom elemzése jelentés](search-traffic-analytics.md)segítségével érheti el.

- Az előzetes és az érvényesítési gyakorlat is lehetővé teszi, hogy a jelentés használatával pontosan meghatározza, hogy mely feltételek részesülnek a szinonimák között, majd továbbra is használja azt érvényesítésként, hogy a szinonimák térképe jobb eredményt ad. Az előre definiált jelentésben a "leggyakoribb keresési lekérdezések" és a "nulla eredményű keresési lekérdezések" a szükséges információkat biztosítja.

- A keresési alkalmazáshoz több szinonimás térképet is létrehozhat (például ha az alkalmazás támogatja a többnyelvű ügyfélkört). Jelenleg egy mező csak az egyiket használhatja. A mezők synonymMaps tulajdonságát bármikor frissítheti.

## <a name="next-steps"></a>További lépések

- Ha meglévő indexe van egy fejlesztési (nem éles) környezetben, kísérletezzen egy kisméretű szótárral, és nézze meg, hogy a szinonimák hozzáadása hogyan módosítja a keresési élményt, beleértve a pontozási profilok, a kiemelések és a javaslatok hatását.

- [Engedélyezze a Search Traffic Analytics szolgáltatást](search-traffic-analytics.md) , és az előre definiált Power bi jelentés használatával megtudhatja, hogy mely feltételek használják a legtöbbet, és melyek nulla dokumentumokat adnak vissza. Ezeket az információkat felhasználva áttekintheti a szótárt, hogy tartalmazza azokat a szinonimákat, amelyekkel az indexben lévő dokumentumokra feloldható nem termékre vonatkozó lekérdezések.
