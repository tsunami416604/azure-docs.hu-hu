---
title: A lekérdezések keresési indexen keresztüli bővítésének szinonimái
titleSuffix: Azure Cognitive Search
description: Hozzon létre egy szinonimaleképezést a keresési lekérdezés hatókörének bővítéséhez az Azure Cognitive Search indexen. A hatókör ki van szélesítve, hogy az ön által megadott egyenértékű kifejezéseket is tartalmazza a listában.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: aa573e84fa9fff83bd6a894f516ce5f67b3afa79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194342"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Szinonimák az Azure Cognitive Search-ben

A keresőmotorok szinonimái egyenértékű kifejezéseket társítanak, amelyek implicit módon kiterjesztik a lekérdezés hatókörét anélkül, hogy a felhasználónak ténylegesen meg kellene adnia a kifejezést. Például, meghatározott a időszak " kutyaféle" és szinonima egyesülés -ból " kutyaféle" és " kiskutya", akármi ok tartalmaz " kutya", " kutyaféle" vagy " kiskutya" akarat esel bent a hatókör -ból kérdés.

Az Azure Cognitive Search szinonimában a szinonima bővítése lekérdezési időben történik. Szinonimaleképezéseket adhat hozzá egy szolgáltatáshoz, a meglévő műveletek megszakítása nélkül. Az index újraépítése nélkül hozzáadhat **egy szinonimMaps** tulajdonságot egy meződefinícióhoz.

## <a name="create-synonyms"></a>Szinonimák létrehozása

Nincs portál támogatás szinonimák létrehozásához, de használhatja a REST API-t vagy a .NET SDK-t. A REST használatának megkezdéséhez javasoljuk a [Postman használatát](search-get-started-postman.md) és a kérelmek összeállítását ezzel az API-val: [Create Synonym Maps](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map). A C# fejlesztők számára első lépések az [Azure Cognitive Search szinonimáinak hozzáadása c# használatával.](search-synonyms-tutorial-sdk.md)

Ha [az ügyfél által felügyelt kulcsokat](search-security-manage-encryption-keys.md) használja a szolgáltatás oldali inaktív titkosításhoz, ezt a védelmet alkalmazhatja a szinonimatérkép tartalmára.

## <a name="use-synonyms"></a>Szinonimák használata

Az Azure Cognitive Search szinonimában a szinonima támogatása a szolgáltatásáltal definiált és feltöltött szinonimatérképeken alapul. Ezek a leképezések független erőforrást (például indexeket vagy adatforrásokat) alkotnak, és a keresési szolgáltatás bármely indexében bármely kereshető mező használható.

A szinonimatérképek és -indexek egymástól függetlenül karbanmaradnak. Miután definiált egy szinonimatérképet, és feltöltötte azt a szolgáltatásba, engedélyezheti a szinonimafunkciót egy mezőben egy **új, szinonimatérkép** nevű tulajdonság hozzáadásával a meződefinícióban. A szinonimatérkép létrehozása, frissítése és törlése mindig egy teljes dokumentumművelet, ami azt jelenti, hogy a szinonimatérkép egyes részeit nem lehet fokozatosan létrehozni, frissíteni vagy törölni. Akár egyetlen bejegyzés frissítése is újratöltést igényel.

A szinonimák beépítése a keresési alkalmazásba két lépésből áll:

1.  Szinonimaleképezés hozzáadása a keresési szolgáltatáshoz az alábbi API-kon keresztül.  

2.  Konfiguráljon egy kereshető mezőt az indexdefiníciószi szinonimaleképezés használatára.

Több szinonimaleképezést is létrehozhat a keresési alkalmazáshoz (például nyelv szerint, ha az alkalmazás támogatja a többnyelvű ügyfélkört). Jelenleg egy mező csak az egyiket használhatja. A mező szinonimaTérkép-tulajdonságát bármikor frissítheti.

### <a name="synonymmaps-resource-apis"></a>SynonymMaps erőforrás API-k

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Adjon hozzá vagy frissítsen egy szinonimatérképet a szolgáltatás alatt a POST vagy a PUT használatával.

A szinonimatérképeket postán vagy puton keresztül tölti fel a szolgáltatásba. Minden szabályt az új sorkarakternek ('\n') kell elhatároltnak lennie. Egy ingyenes szolgáltatásban szinonimaleképezésenként legfeljebb 5000 szabályt határozhat meg, és leképezésenként 20 000 szabályt az összes többi sk-ban. Minden szabály legfeljebb 20 bővítéssel rendelkezhet.

Szinonima térképek kell az Apache Solr formátumban, amely az alábbiakban ismertetjük. Ha van egy meglévő szinonima szótár más formátumban, és szeretné használni közvetlenül, kérjük, tudassa velünk a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Új szinonimátkészíthet a HTTP POST használatával, ahogy az alábbi példában is látható:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Másik lehetőségként használhatja a PUT-ot, és megadhatja a szinonimát az URI-n. Ha a szinonimatérkép nem létezik, akkor létrejön.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Apache Solr szinonima formátum

A Solr formátum támogatja az egyenértékű és explicit szinonimaleképezéseket. A leképezési szabályok megfelelnek az Apache Solr nyílt forráskódú szinonimaszűrő specifikációjának, amelyet a jelen dokumentum a következő dokumentumban ismertetett: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Az alábbiakban az egyenértékű szinonimákra vonatkozó mintaszabály látható.
```
USA, United States, United States of America
```

A fenti szabállyal az "USA" keresési lekérdezés "USA" vagy "Egyesült Államok" vagy "Amerikai Egyesült Államok" lesz.

Az explicit leképezést egy "=>" nyíl jelöli. Ha meg van adva, a keresési lekérdezés nek a bal oldalán az "=>" kifejezéssorozata a jobb oldalon lévő alternatívákkal lesz helyettesítve. Az alábbi szabály alapján a "Washington", "Wash" lekérdezéseket kell lekeresni. vagy a "WA" átlesz írva "WA"-ra. Az explicit leképezés csak a megadott irányban érvényes, és ebben az esetben nem írja át a "WA" lekérdezést "Washington"-ra.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Sorolja fel a szinonimatérképeket a szolgáltatása alatt.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Kap egy szinonimatérképet a szolgáltatása alatt.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Szinonimák leképezésének törlése a szolgáltatás alatt.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Konfiguráljon egy kereshető mezőt az indexdefiníciószi szinonimaleképezés használatára.

Egy új mező tulajdonság **szinonimMaps** lehet használni, hogy adja meg a szinonimaleképezés használni egy kereshető mező. A szinonimaleképezések szolgáltatási szintű erőforrások, és a szolgáltatás indexének bármely mezője hivatkozhat rá.

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

**A szinonimaTérképek** megadhatók az "Edm.String" vagy "Collection(Edm.String)" típusú kereshető mezőkhöz.

> [!NOTE]
> Mezőnként csak egy szinonimatérkép lehet. Ha több szinonimát szeretne használni, kérjük, tudassa velünk a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>A szinonimák hatása más keresési funkciókra

A szinonimák funkció újraírja az eredeti lekérdezést a VAGY operátor szinonimáival. Ezért a találati kiemelési és pontozási profilok az eredeti kifejezést és szinonimákat egyenértékűként kezelik.

A szinonima szolgáltatás a keresési lekérdezésekre vonatkozik, és nem vonatkozik a szűrőkre vagy a lapatokra. Hasonlóképpen, a javaslatok csak az eredeti kifejezésen alapulnak; szinonimaegyezések nem jelennek meg a válaszban.

A szinonimakiterjesztések nem vonatkoznak a helyettesítő karakteres keresési kifejezésekre; előtag, fuzzy és regex kifejezések nincsenek kibontva.

Ha egyetlen lekérdezést kell végeznie, amely szinonimát és helyettesítő karaktert, regexet vagy intelligens kereséseket alkalmaz, a lekérdezéseket a VAGY szintaxissal kombinálhatja. Ha például a szinonimákat helyettesítő karakterekkel kombinálja az `<query> | <query>*`egyszerű lekérdezésszintaxishoz, a kifejezés a .

Ha egy fejlesztési (nem éles) környezetben már rendelkezik indexszel, kísérletezzen egy kis szótárral, és tekintse meg, hogyan változtatja meg a szinonimák hozzáadása a keresési élményt, beleértve a pontozási profilokra, a leírás kiemelésére és a javaslatokra gyakorolt hatást.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szinonimatérkép létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)