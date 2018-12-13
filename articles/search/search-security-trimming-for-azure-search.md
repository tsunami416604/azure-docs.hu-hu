---
title: Az eredmények – Azure Search-csonkolás biztonsági szűrők
description: Hozzáférés-vezérlést a biztonsági szűrők és a felhasználói identitások Azure Search-tartalom.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 08/07/2017
author: brjohnstmsft
ms.author: brjohnst
manager: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: 84147b250ea17df9af67cc8a9025cdf6ec59a705
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314227"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Biztonsági szűrők a tisztítás eredmények elérése érdekében az Azure Search szolgáltatásban

Biztonsági szűrők szűkítheti a keresési eredmények az Azure Search szolgáltatásban felhasználói identitás alapján is alkalmazhat. A keresési funkciót általában megköveteli a személy, aki a keresés szemben az elvek, akik számára engedélyezett a dokumentumot tartalmazó kérelmek identitásának összehasonlítása. Ha van egyezés, a felhasználó vagy az egyszerű (például egy csoport vagy szerepkör) rendelkezik a dokumentum elérését.

Egyirányú értékre a biztonsági szűrés keresztül történik egy bonyolult vagy műveletet egyenlőség kifejezések: például `Id eq 'id1' or Id eq 'id2'`, és így tovább. Ez a megközelítés akkor hibalehetőséget magában rejtő, nehezen fenntartható, és azokban az esetekben, ahol a lista tartalmazza-e a több száz vagy akár több ezer olyan értékek, a lekérdezések válaszideje szerint hány másodpercig lelassul. 

Egy egyszerűbb és gyorsabb megoldás keresztül, a `search.in` függvény. Ha `search.in(Id, 'id1, id2, ...')` helyett egyenlőségi kifejezés, másodperc törtrésze várható alkalommal.

Ez a cikk bemutatja, hogyan végezheti el a biztonsági szűrés, az alábbi lépéseket követve:
> [!div class="checklist"]
> * Az egyszerű azonosítókat tartalmazó mező létrehozása 
> * Leküldéses vagy a megfelelő egyszerű azonosítókkal meglévő dokumentumok frissítéséhez
> * Az egy keresési kérelmet `search.in` `filter`

>[!NOTE]
> Ez a dokumentum nem terjed ki az egyszerű azonosítók lekérését jelenti. Ez a szolgáltatás identitásszolgáltatótól szerezheti be.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy egy [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [Azure Search szolgáltatás](https://docs.microsoft.com/azure/search/search-create-service-portal), és [Azure Search-Index](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Biztonsági mező létrehozása

A dokumentumok tartalmaznia kell egy mezőben adja meg, hogy mely csoportok kapjanak hozzáférést. Ez a szűrési feltételeket, amelyek dokumentumok kiválasztása vagy utasítani a kibocsátó számára visszaadott eredménykészlet válik.
Tegyük fel, hogy rendelkezünk a védett fájlok index, és minden egyes fájl által a felhasználók egy másik csoportja érhető el.
1. Mező hozzáadása `group_ids` (választhat Itt bármilyen nevet), egy `Collection(Edm.String)`. Ellenőrizze, hogy a mező egy `filterable` attribútum beállítása `true` úgy, hogy a keresési eredmények szűr a rendszer a hozzáférést a felhasználó rendelkezik alapján. Például, ha beállította a `group_ids` mezőt `["group_id1, group_id2"]` a dokumentum a `file_name` "secured_file_b", csak a csoport azonosítók "group_id1" vagy "group_id2" olvasási hozzáférést a fájlhoz tartozó felhasználók.
   Győződjön meg arról, hogy a mező `retrievable` attribútum `false` úgy, hogy ezt nem adja vissza a keresési kérelem részeként.
2. Is hozzáadhat `file_id` és `file_name` feltételezünk mezőket.  

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

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>A REST API használatával az indexbe történő adatleküldéshez
  
Egy HTTP POST kérés kiadása az index URL-cím végponthoz. A HTTP-kérelem törzse hozzáadandó dokumentumot tartalmazó JSON-objektum:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=[api-version]  
Content-Type: application/json
api-key: [admin key]
```

A kérelem törzsében a dokumentumok tartalmának megadása:

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

Ha egy meglévő dokumentumot frissítse azokat a csoportokat van szüksége, használhatja a `merge` vagy `mergeOrUpload` művelet:

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

Hozzáadása vagy frissítése a dokumentumok teljes részleteiért olvassa [szerkeszthetik a dokumentumokat a](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>A biztonsági szűrők alkalmazása

Annak érdekében, hogy a tároló visszaigénylésének dokumentumok alapján `group_ids` hozzáférést, meg kell keresési lekérdezés küldése az egy `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` szűrőt, ahol "group_id1 group_id2...", amely a keresési kérelmet kibocsátó tartozik csoportjai.
Ez a szűrő megfelel összes dokumentumot, amelynek a `group_ids` mező tartalmaz egy adott azonosítók.
További részletek az Azure Search-dokumentumok keresése, beolvashatja [dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Vegye figyelembe, hogy ez a minta bemutatja, hogyan kereshet egy POST kérést dokumentumok.

A HTTP POST kérés kiadása:

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

A dokumentumok szerezheti vissza, ahol `group_ids` "group_id1" vagy "group_id2" tartalmaz. Más szóval kap a dokumentumokat, amelyhez a kérés kibocsátó olvasási hozzáféréssel rendelkezik.

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

Ez a hogyan felhasználói identitás és az Azure Search az eredményeket szűrheti `search.in()` függvény. Ez a funkció használatával adja át az egyes céldokumentumban társított egyszerű azonosítók egyeztetéshez a kérelmező felhasználó egyszerű azonosítók. Egy keresési kérelmet történik, ha a `search.in` függvény kiszűri a keresési eredmények, amelyekhez a felhasználó rendszerbiztonsági tagok közül egyik sem rendelkezik olvasási hozzáféréssel. Az egyszerű azonosítók hozhat létre például a biztonsági csoportok, szerepkörök vagy akár a felhasználó saját identitását.
 
## <a name="see-also"></a>Lásd még

+ [Az Active Directory azonosító-alapú hozzáférés-vezérlés az Azure Search-szűrők használata](search-security-trimming-for-azure-search-with-aad.md)
+ [Szűrők az Azure Search szolgáltatásban](search-filters.md)
+ [Adatok biztonsági és hozzáférés-vezérlés az Azure Search-műveletek](search-security-overview.md)