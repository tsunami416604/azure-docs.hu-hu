---
title: Biztonsági szűrők az eredmények vágásához
titleSuffix: Azure Cognitive Search
description: Hozzáférés-vezérlés az Azure Cognitive Search-tartalmakon biztonsági szűrők és felhasználói identitások használatával.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24f168f68a60ebb0408b7f1c367039ea5caea6d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794281"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Biztonsági szűrők az eredmények vágásához az Azure Cognitive Search szolgáltatásban

A keresési eredmények lefaragása az Azure Cognitive Search felhasználói identitás alapján biztonsági szűrőket alkalmazhat. Ez a keresési élmény általában megköveteli a keresést kérő konkretincia azonosítójának összehasonlítását egy olyan mezővel, amely tartalmazza a dokumentumhoz engedéllyel rendelkező alapelveket. Ha egyezést talál, a felhasználó vagy az egyszerű felhasználó (például egy csoport vagy szerepkör) hozzáfér a dokumentumhoz.

A biztonsági szűrés elérésének egyik módja az egyenlőségi kifejezések bonyolult eltiltása: például `Id eq 'id1' or Id eq 'id2'`, és így tovább. Ez a megközelítés hibaérzékeny, nehezen karbantartható, és azokban az esetekben, ahol a lista több száz vagy több ezer értéket tartalmaz, sok másodperccel lassítja a lekérdezés válaszidejét. 

Egy egyszerűbb és gyorsabb megközelítés `search.in` révén a funkciót. Ha egyenlőségi kifejezés helyett használ, `search.in(Id, 'id1, id2, ...')` másodperc alatti válaszidőkre számíthat.

Ez a cikk bemutatja, hogyan végezheti el a biztonsági szűrést az alábbi lépésekkel:
> [!div class="checklist"]
> * Egyszerű azonosítókat tartalmazó mező létrehozása 
> * Meglévő dokumentumok leküldése vagy frissítése a megfelelő fő azonosítókkal
> * Keresési kérelem `search.in` kiadása`filter`

>[!NOTE]
> A fő azonosítók beolvasásának folyamatára ez a dokumentum nem terjed ki. Meg kell kapnia a személyazonosságát szolgáltató.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik egy [Azure-előfizetéssel](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [az Azure Cognitive Search szolgáltatással](https://docs.microsoft.com/azure/search/search-create-service-portal)és az [Azure Cognitive Search Indexszel.](https://docs.microsoft.com/azure/search/search-create-index-portal)  

## <a name="create-security-field"></a>Biztonsági mező létrehozása

A dokumentumoknak tartalmazniuk kell egy mezőt, amely meghatározza, hogy mely csoportok nak van hozzáférése. Ez az információ lesz az a szűrőfeltétel, amely alapján a rendszer a kiállítónak visszaadott eredményhalmazból kivan jelölve vagy elutasítja a dokumentumokat.
Tegyük fel, hogy rendelkezünk a biztonságos fájlok indexével, és minden fájl elérhető a felhasználók különböző készletei számára.
1. Adja `group_ids` hozzá a mezőt (itt `Collection(Edm.String)`bármilyen nevet megválaszthat) a részeként. Győződjön meg arról, `filterable` hogy `true` a mezőattribútuma van beállítva, hogy a keresési eredmények a felhasználó hozzáférése alapján legyenek szűrve. Ha például a `group_ids` "secured_file_b" `["group_id1, group_id2"]` felirattal `file_name` rendelkező dokumentum mezőjét állítja be, csak a "group_id1" vagy "group_id2" csoportazonosítókhoz tartozó felhasználók férhetnek hozzá a fájlhoz.
   Győződjön meg arról, hogy `retrievable` `false` a mező attribútuma úgy van beállítva, hogy a keresési kérelem részeként ne kerüljön vissza.
2. Is `file_id` hozzá `file_name` és mezők kedvéért ezt a példát.  

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

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Adatok lenyomása az indexbe a REST API használatával
  
HTTP POST-kérelem kiadása az index URL-végpontjára. A HTTP-kérelem törzse egy JSON-objektum, amely a hozzáadandó dokumentumokat tartalmazza:

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

Ha egy meglévő dokumentumot a csoportok listájával kell `merge` frissítenie, a következő `mergeOrUpload` műveletet használhatja:

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

A dokumentumok hozzáadásának és frissítésének részleteiért olvassa el [a Dokumentumok szerkesztése](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>A biztonsági szűrő alkalmazása

A dokumentumok hozzáférés alapján `group_ids` való vágásához ki kell `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` adnia egy keresési lekérdezést egy szűrővel, ahol a "group_id1, group_id2,..." azok a csoportok, amelyekhez a keresési kérelem kibocsátója tartozik.
Ez a szűrő minden `group_ids` olyan dokumentumnak megfelel, amelynek a mezője az adott azonosítók egyikét tartalmazza.
A dokumentumok Azure Cognitive Search használatával történő kereséssel kapcsolatos részletekért olvassa el a [Keresési dokumentumok](https://docs.microsoft.com/rest/api/searchservice/search-documents)szolgáltatást.
Vegye figyelembe, hogy ez a minta bemutatja, hogyan kereshet dokumentumokat POST-kérelem használatával.

A HTTP POST-kérelem kiadása:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

Adja meg a szűrőt a kérelem törzsében:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Vissza kell kapnia `group_ids` a dokumentumokat oda, ahol a "group_id1" vagy a "group_id2" tartalmazza. Más szóval megkapja azokat a dokumentumokat, amelyekhez a kérelem kibocsátója olvasási hozzáféréssel rendelkezik.

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

Így szűrheti az eredményeket a felhasználói identitás `search.in()` és az Azure Cognitive Search funkció alapján. Ezzel a funkcióval elvi azonosítókat adhat át a kérelmező felhasználónak, hogy egyezzen az egyes céldokumentumokhoz társított fő azonosítókkal. A keresési kérelem kezelése kor `search.in` a függvény kiszűri azokat a keresési eredményeket, amelyekhez a felhasználó egyik alaptagja sem rendelkezik olvasási hozzáféréssel. A fő azonosítók képviselhetik például a biztonsági csoportokat, szerepköröket vagy akár a felhasználó saját identitását.
 
## <a name="see-also"></a>Lásd még

+ [Active Directory identitásalapú hozzáférés-vezérlés az Azure Cognitive Search szűrőivel](search-security-trimming-for-azure-search-with-aad.md)
+ [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
+ [Adatbiztonság és hozzáférés-vezérlés az Azure Cognitive Search műveleteiben](search-security-overview.md)