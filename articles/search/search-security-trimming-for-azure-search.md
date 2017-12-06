---
title: "Az Azure Search biztonsági tisztítás"
description: "Alkalmazzon biztonsági elrejtés Azure Search-szűrők használata."
ms.custom: 
ms.date: 08/07/2017
ms.service: search
ms.reviewer: 
ms.suite: 
ms.tgt_pltfrm: 
caps.latest.revision: "26"
author: revitalbarletz
ms.author: revitalb
manager: jlembicz
ms.openlocfilehash: f49004b68f95ae796196009e3cf879e3503ecf91
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="security-trimming-with-azure-search"></a>Az Azure Search biztonsági tisztítás

A felhasználó identitása alapján dokumentum hozzáférés korlátozása a keresési eredmények biztonsági szűrőket is alkalmazhat. A keresési funkciók általában megköveteli a személy, aki a keresés elleni ezeket az alapelveket, akik a dokumentum tartalmazó mező kérelmek identitásának összehasonlítása. A program egyezést talál, a felhasználó vagy az egyszerű (például egy csoport vagy szerepkör) dokumentum hozzáférése van.

Biztonsági eléréséhez a egyirányú szűrési keresztül történik egy bonyolult vagy műveletet egyenlőség kifejezések: például `Id eq 'id1' or Id eq 'id2'`, és így tovább. Erre akkor hibákhoz vezethet, nehéz a kezelése, és azokban az esetekben, ahol a lista tartalmazza-e a több száz vagy ezer értékek lassítja a lekérdezési válaszidőt hány másodpercig által. 

Egy egyszerűbb és gyorsabb megoldás keresztül, a `search.in` függvény. Ha `search.in(Id, 'id1, id2, ...')` helyett egy egyenlőség kifejezés alárendelt második válasz számíthat alkalommal.

Ez a cikk bemutatja, hogyan hajthat végre olyan biztonsági szűrést, az alábbi lépéseket követve:
> [!div class="checklist"]
> * A fő azonosítókat tartalmazó mező létrehozása 
> * Leküldéses vagy meglévő dokumentumokat a megfelelő egyszerű azonosítók frissítése
> * A keresési foglalhatnak `search.in``filter`

>[!NOTE]
> A jelen dokumentum nem ismerteti a fő azonosítók lekérésének folyamatát. Az identitás-szolgáltatótól szerezheti be.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik egy [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [Azure Search szolgáltatás](https://docs.microsoft.com/azure/search/search-create-service-portal), és [Azure Search-Index](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Hozzon létre biztonsági mező

A dokumentumok tartalmaznia kell egy mező megadása, hogy mely csoportok rendelkezzenek hozzáféréssel. Ez a szűrési feltételt, amely dokumentumok kiválasztott vagy a kibocsátó számára visszaadott eredménykészlet fogja elutasítani válik.
Tegyük fel, hogy a védett fájlok index van, és minden fájl különböző felhasználók által elérhető.
1. A mező hozzáadása `group_ids` (dönthet úgy, ide bármilyen nevét), egy `Collection(Edm.String)`. Győződjön meg arról, hogy a mezőben egy `filterable` attribútum értékének beállítása `true` , hogy a keresési eredményeket a rendszer a hozzáférést a felhasználó rendelkezik-e alapján. Például, ha beállította a `group_ids` mezőről `["group_id1, group_id2"]` a dokumentum `file_name` "secured_file_b", csak a csoport azonosítók "group_id1" vagy "group_id2" olvasási hozzáféréssel rendelkezik a fájlhoz tartozó felhasználókat.
   Győződjön meg arról, hogy a mező `retrievable` attribútum van beállítva `false` , hogy a rendszer nem adott vissza a keresési kérelem részeként.
2. Is hozzáadhat `file_id` és `file_name` ebben a példában az mezőket.  

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

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Adatok küldése az indexbe, a REST API használatával
  
Az index URL-végpontjának HTTP POST-kérelmet ad ki. A HTTP-kérelem törzse lehet hozzáadni a dokumentumokat tartalmazó JSON-objektum:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=[api-version]  
Content-Type: application/json
api-key: [admin key]
```

A kérelem törzsében szereplő a dokumentumok tartalmának megadása:

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

Ha módosítania meglévő dokumentum a csoportok listáját, használhatja a `merge` vagy `mergeOrUpload` művelet:

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

Létrehozásától vagy frissítésétől egészen a dokumentumok a teljes részletekért elolvashatja [szerkeszthetik a dokumentumokat a](https://docs.microsoft.com/en-us/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>A biztonsági szűrés

Ahhoz, hogy a dokumentumok alapján trim `group_ids` hozzáférés kell ki egy keresési lekérdezés egy `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` szűrő, ahol "group_id1, group_id2,..." csoportokra, amelyhez a keresési kérelmet kibocsátó tartozik.
Ez a szűrő megfelelő összes dokumentumot, amelynek a `group_ids` mező egy adott azonosítót tartalmaz.
Az Azure Search használatával dokumentumok keresése részletes olvasható [dokumentumok keresése](https://docs.microsoft.com/en-us/rest/api/searchservice/search-documents).
Vegye figyelembe, hogy ez a minta bemutatja, hogyan kereshet egy POST kérést használó dokumentumokat.

A HTTP POST-kérelmet ad ki:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=[api-version]  
Content-Type: application/json  
api-key: [admin or query key]
```

Adja meg a szűrő a kérelem törzsében szereplő:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

A dokumentumok szerezheti vissza, ha `group_ids` "group_id1" vagy "group_id2" tartalmaz. Más szóval kap a dokumentumok, amelyhez a kérés kibocsátó olvasási hozzáféréssel rendelkezik.

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

Ez a hogyan szűrheti a felhasználói azonosító és az Azure keresési eredmény `search.in()` függvény. Ez a funkció használatával adjon át az egyes céldokumentumban társított egyszerű azonosítók egyeztetéshez a kérelmező felhasználó egyszerű azonosítót. Keresési kérelem kezelésekor a `search.in` függvény kiszűri a keresési eredmények között, amelyekhez nincs a felhasználó rendszerbiztonsági tagok olvasási hozzáféréssel rendelkezik. Az egyszerű azonosítók jelenthet többek között a biztonsági csoportok, szerepkörök vagy akár a felhasználó saját identitását.
 
