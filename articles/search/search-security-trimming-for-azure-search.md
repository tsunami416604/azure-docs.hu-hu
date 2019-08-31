---
title: Az eredmények kivágására szolgáló biztonsági szűrők – Azure Search
description: Azure Search tartalmak hozzáférés-vezérlése biztonsági szűrők és felhasználói identitások használatával.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 05/02/2019
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
ms.custom: seodec2018
ms.openlocfilehash: 4d1ffa5b29a56d32a4f6a8ccf40f5bafd27795e6
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186490"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Az eredmények kivágására szolgáló biztonsági szűrők Azure Search

Biztonsági szűrőket alkalmazhat a keresési eredmények Azure Search a felhasználói identitás alapján. Ez a keresési élmény általában ahhoz szükséges, hogy össze lehessen hasonlítani azt az identitást, aki egy olyan mezőre kéri a keresést, amely tartalmazza a dokumentum engedélyeivel rendelkező alapelveket. Ha egyezést talál, a felhasználó vagy a rendszerbiztonsági tag (például egy csoport vagy szerepkör) hozzáfér ehhez a dokumentumhoz.

A biztonsági szűrés elérésének egyik módja az egyenlőségi kifejezések összetett kiválasztásán keresztül történik: például `Id eq 'id1' or Id eq 'id2'`, és így tovább. Ez a megközelítés a hibákra hajlamos, nehezen karbantartható, és olyan esetekben, amikor a lista több száz vagy több ezer értéket tartalmaz, lelassítja a lekérdezés válaszideje több másodpercen belül. 

Az egyszerűbb és gyorsabb megközelítés a `search.in` függvényen keresztül történik. Ha egyenlőségi `search.in(Id, 'id1, id2, ...')` kifejezés helyett használja, akkor várható, hogy a rendszer a második másodpercben válaszol.

Ez a cikk bemutatja, hogyan hajthatja végre a biztonsági szűrést a következő lépések végrehajtásával:
> [!div class="checklist"]
> * A résztvevő azonosítóit tartalmazó mező létrehozása 
> * Meglévő dokumentumok leküldése vagy frissítése a vonatkozó elsődleges azonosítókkal
> * Keresési kérelem `search.in` kiadása`filter`

>[!NOTE]
> A résztvevő azonosítók lekérésének folyamata nem szerepel ebben a dokumentumban. Szerezze be az azonosítót a személyazonossági szolgáltatótól.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik [Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)-előfizetéssel, [Azure Search szolgáltatással](https://docs.microsoft.com/azure/search/search-create-service-portal)és [Azure Search indexszel](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Biztonsági mező létrehozása

A dokumentumoknak tartalmazniuk kell egy mezőt, amely meghatározza, hogy mely csoportok férhetnek hozzá. Ezek az információk azokra a szűrési feltételekre vonatkoznak, amelyek alapján a rendszer kijelöli vagy elutasítja a kiállítói eredményhalmaz által visszaadott dokumentumokat.
Tegyük fel, hogy a védett fájlok indexét használjuk, és minden fájlhoz egy másik felhasználó férhet hozzá.
1. Mező `group_ids` hozzáadása (itt választhat nevet) `Collection(Edm.String)`. Győződjön meg arról, hogy a `filterable` mezőhöz egy `true` attribútum van beállítva, hogy a keresési eredmények szűrve legyenek a felhasználó hozzáférése alapján. Ha például a "secured_file_b" azonosítójú `group_ids` `file_name` dokumentumra `["group_id1, group_id2"]` állítja be a mezőt, akkor csak a "group_id1" vagy "group_id2" csoportba tartozó felhasználók rendelkeznek olvasási hozzáféréssel a fájlhoz.
   `retrievable` Győződjön`false` meg arról, hogy a mező attribútuma úgy van beállítva, hogy a rendszer ne adja vissza a keresési kérelem részeként.
2. A példa `file_id` kedvéért `file_name` adja hozzá a és a mezőket is.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Az adatküldés az indexbe az REST API használatával
  
Adjon ki egy HTTP POST-kérelmet az index URL-címének végpontjának. A HTTP-kérelem törzse egy JSON-objektum, amely tartalmazza a hozzáadandó dokumentumokat:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

A kérelem törzsében adja meg a dokumentumok tartalmát:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Ha egy meglévő dokumentumot kell frissítenie a csoportok listájával, a vagy `merge` `mergeOrUpload` a műveletet használhatja:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

A dokumentumok hozzáadásával vagy frissítésével kapcsolatos részletes információkért olvassa el a [dokumentumok szerkesztése című dokumentumot](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>A biztonsági szűrő alkalmazása

A dokumentumok `group_ids` hozzáférés alapján történő kivágásához egy `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` szűrővel rendelkező keresési lekérdezést kell kiadnia, ahol "group_id1, group_id2,..." azok a csoportok, amelyekhez a keresési kérelem kiállítója tartozik.
Ez a szűrő minden olyan dokumentumra illeszkedik `group_ids` , amelynek a mezője tartalmazza a megadott azonosítók egyikét.
A dokumentumok Azure Search használatával történő keresésével kapcsolatos részletes információkért olvassa el a [keresési dokumentumokat](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Vegye figyelembe, hogy ez a minta bemutatja, hogyan kereshet dokumentumokat a POST-kérések használatával.

A HTTP POST-kérelem kiadása:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

Határozza meg a szűrőt a kérelem törzsében:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

A dokumentumok visszaállításához `group_ids` a "group_id1" vagy a "group_id2" kifejezést kell visszakapnia. Más szóval azokat a dokumentumokat kapja meg, amelyekre a kérelem kiállítójának olvasási hozzáférése van.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>Összegzés

Így szűrheti az eredményeket a felhasználói identitás és a Azure Search `search.in()` függvény alapján. Ezzel a függvénnyel az egyes dokumentumokhoz társított elsődleges azonosítókkal egyező azonosítókat adhat meg a kérelmező felhasználó számára. Keresési kérelem kezelésekor a `search.in` függvény kiszűri azokat a keresési eredményeket, amelyekhez a felhasználó egyetlen résztvevője sem rendelkezik olvasási hozzáféréssel. A résztvevő azonosítói a biztonsági csoportok, szerepkörök vagy akár a felhasználó saját identitása is lehetnek.
 
## <a name="see-also"></a>Lásd még

+ [Identitás-alapú hozzáférés-vezérlés Active Directory Azure Search szűrők használatával](search-security-trimming-for-azure-search-with-aad.md)
+ [Szűrők a Azure Searchban](search-filters.md)
+ [Adatbiztonság és hozzáférés-vezérlés Azure Search műveletekben](search-security-overview.md)