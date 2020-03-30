---
title: Rangsorolási hasonlóság algoritmusa
titleSuffix: Azure Cognitive Search
description: Hogyan kell beállítani a hasonlóság algoritmus kipróbálni az új hasonlóság algoritmus rangsor
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: c327440649300533c94c2a1956e3c45f433c9780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409972"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Rangsorolási algoritmus az Azure Cognitive Search szolgáltatásban

> [!IMPORTANT]
> 2020. július 15-től az újonnan létrehozott keresési szolgáltatások a BM25 rangsorolási funkciót fogják használni, amely a legtöbb esetben bizonyítottan olyan keresési rangsorokat biztosít, amelyek jobban igazodnak a felhasználói elvárásokhoz, mint a jelenlegi alapértelmezett rangsor.  A kiváló rangsoroláson túl a BM25 lehetővé teszi az eredmények finomhangolásához rendelkezésre álló konfigurációs beállításokat olyan tényezők alapján, mint a dokumentum mérete.  
>
> Ezzel a változással valószínűleg kisebb változásokat fog látni a keresési eredmények sorrendjében.   Azok számára, akik szeretnék tesztelni a hatását ez a változás, tettünk elérhetővé a 2019-05-06-Preview API-t, hogy lehetővé tegye a BM25 pontozás az új indexek.  

Ez a cikk azt ismerteti, hogyan frissítheti a 2020.

Az Azure Cognitive Search az Okapi BM25 algoritmus, a *BM25Similarity*hivatalos Lucene implementációját fogja használni, amely felváltja a korábban használt *ClassicSimilarity* implementációt. A régebbi ClassicSimilarity algoritmushoz hasonlóan a BM25Similarity egy TF-IDF-szerű visszakeresési függvény, amely a kifejezés gyakoriságát (TF) és az inverz dokumentumgyakoriságot (IDF) használja változóként az egyes dokumentum-lekérdezéspárok relevanciapontszámainak kiszámításához, amely használt rangsor. Míg fogalmilag hasonló a régebbi Classic Similarity algoritmus, BM25 veszi a gyökere a valószínűségi információ-visszakeresés, hogy javítsa rajta. A BM25 speciális testreszabási lehetőségeket is kínál, például lehetővé teszi a felhasználó számára annak eldöntését, hogy a relevanciapontszám hogyan méreteződik az egyező kifejezések kifejezésgyakoriságával.

## <a name="how-to-test-bm25-today"></a>Hogyan teszteljük BM25 ma

Új index létrehozásakor beállíthat egy "hasonlóság" tulajdonságot. A *2019-05-06-Preview* verziót kell használnia, az alábbiak szerint.

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

2020. július 15.-e előtt létrehozott szolgáltatások esetén: Ha a hasonlóság kimarad, vagy null értékre van állítva, az index a régi klasszikus hasonlósági algoritmust fogja használni.

2020. július 15.))

A hasonlósági értéket a következő két érték egyikére is állíthatja be: *"#Microsoft.Azure.Search.ClassicSimilarity"* vagy *"#Microsoft.Azure.Search.BM25Similarity"*.


## <a name="bm25-similarity-parameters"></a>BM25 hasonlósági paraméterek

A BM25 hasonlóság két felhasználó által testreszabható paraméterrel szabályozza a számított relevancia-pontszámot:

### <a name="k1"></a>k1

A *k1* paraméter szabályozza a skálázási funkciót az egyes egyező kifejezések kifejezésgyakorisága és a dokumentum-lekérdezéspár végső relevancia-pontszáma között.

A nulla érték egy "bináris modellt" jelent, ahol az egyező kifejezés hozzájárulása megegyezik az összes egyező dokumentumesetében, függetlenül attól, hogy ez a kifejezés hányszor jelenik meg a szövegben, míg a nagyobb k1 érték lehetővé teszi, hogy a pontszám tovább növekedjen, ahogy egyre több azonos kifejezés példányai találhatók a dokumentumban. Alapértelmezés szerint az Azure Cognitive Search 1.2-es értéket használ a k1 paraméterhez. Nagyobb k1 érték használata fontos lehet azokban az esetekben, amikor azt várjuk, hogy több kifejezés is része legyen a keresési lekérdezésnek. Ezekben az esetekben érdemes előnyben részesíteni azokat a dokumentumokat, amelyek megfelelnek a különböző lekérdezési kifejezések nek, amelyeket olyan dokumentumokon keresnek, amelyek csak egyetlen egyezésnek felelnek meg. Például, amikor az "Apollo Spaceflight" kifejezéseket tartalmazó dokumentumok indexét kérdezzük, érdemes csökkenteni a görög mitológia című cikk pontszámát, amely néhány tucatszor tartalmazza az "Apollo" kifejezést, anélkül, hogy megemlítené az "Űrrepülést", szemben az "Űrrepülés" kifejezéssel, mint az "Űrrepülés" egy másik cikk, amely kifejezetten megemlíti mind az "Apollo" és a "Spaceflight" egy maroknyi alkalommal csak. 
 
### <a name="b"></a>b

A *b* paraméter azt szabályozza, hogy a dokumentum hossza hogyan befolyásolja a relevancia-pontszámot.

A 0,0 érték azt jelenti, hogy a dokumentum hossza nem befolyásolja a pontszámot, míg az 1,0 érték azt jelenti, hogy a kifejezés gyakoriságának a relevanciapontszámra gyakorolt hatása a dokumentum hossza szerint normalizálódik. A b paraméterhez használt alapértelmezett érték az Azure Cognitive Search függvényében 0,75. A kifejezés gyakoriságának a dokumentum hossza szerinti normalizálása akkor hasznos, ha hosszabb dokumentumokat szeretnénk büntetni. Bizonyos esetekben a hosszabb dokumentumok (például egy teljes regény) nagyobb valószínűséggel tartalmaznak sok irreleváns kifejezést, mint a sokkal rövidebb dokumentumok.

### <a name="setting-k1-and-b-parameters"></a>K1 és b paraméterek beállítása

A b vagy k1 értékek testreszabásához egyszerűen adja hozzá őket tulajdonságként a hasonlóság objektumhoz a BM25 használatakor:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

A hasonlósági algoritmus csak indexlétrehozási időpontban állítható be. Ez azt jelenti, hogy a használt hasonlósági algoritmus nem módosítható a meglévő indexek esetében. A *"b"* és *"k1"* paraméterek módosíthatók egy BM25-öt használó meglévő indexdefiníció frissítésekor. Ha ezeket az értékeket egy meglévő indexen módosítja, az index legalább néhány másodpercig offline állapotba kerül, ami az indexelési és lekérdezési kérelmek sikertelensítését eredményezi. Emiatt be kell állítania az "allowIndexDowntime=true" paramétert a frissítési kérelem lekérdezési karakterláncában:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```


## <a name="see-also"></a>Lásd még  

 [Azure Cognitive Search REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Pontozási profilok hozzáadása az indexhez](index-add-scoring-profiles.md)    
 [Index létrehozása &#40;Azure Cognitive Search REST API-&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
  [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
