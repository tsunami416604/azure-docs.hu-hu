---
title: Teljes szöveges lekérdezés és indexelésmotor-architektúra (Lucene)
titleSuffix: Azure Cognitive Search
description: Megvizsgálja a Lucene lekérdezésfeldolgozásés a dokumentum-visszakeresés fogalmak teljes szöveges keresés, az Azure Cognitive Search kapcsolódó.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d46d0309b3d2ffb638016e88ba022e49009eedf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282938"
---
# <a name="how-full-text-search-works-in-azure-cognitive-search"></a>A teljes szöveges keresés működése az Azure Cognitive Searchben

Ez a cikk azoknak a fejlesztőknek szól, akiknek mélyebb ismeretekre van szükségük aRról, hogyan működik a Lucene teljes szöveges keresése az Azure Cognitive Search szolgáltatásban. Szöveges lekérdezéseknél az Azure Cognitive Search a legtöbb esetben zökkenőmentesen visszaadja a várt eredményeket, de időnként előfordulhat, hogy az eredmény „helytelennek” tűnik. Ilyenkor a Lucene-lekérdezés végrehajtásának négy fázisában (lekérdezéselemzés, lexikális analízis, dokumentumok egyeztetése, pontozás) szerzett jártasság segíthet azonosítani a lekérdezési paraméterek vagy az indexelési konfiguráció adott módosításait, a kívánt eredmény elérése érdekében. 

> [!Note] 
> Az Azure Cognitive Search lucene-t használ a teljes szöveges kereséshez, de a Lucene-integráció nem teljes. Szelektíven elérhetővé tesszük és bővítjük a Lucene-funkciókat az Azure Cognitive Search számára fontos forgatókönyvek engedélyezéséhez. 

## <a name="architecture-overview-and-diagram"></a>Építészet áttekintése és diagramja

A teljes szöveges keresési lekérdezés feldolgozása a lekérdezés szövegének elemzésével kezdődik a keresési kifejezések kinyeréséhez. A keresőmotor indexet használ a megfelelő kifejezésekkel rendelkező dokumentumok lekéréséhez. Az egyes lekérdezési kifejezéseket néha lebontják és új formákká alakítják, hogy szélesebb hálót vethessünk le arra, ami potenciális egyezésnek tekinthető. Az eredményhalmazt ezután az egyes egyező dokumentumokhoz rendelt relevancia-pontszám szerint rendezi a program. A rangsorolt lista élén lévők visszakerülnek a hívó alkalmazásba.

Újramegadott, lekérdezés végrehajtása négy szakaszból áll: 

1. Lekérdezéselemzés 
2. Lexikális elemzés 
3. Dokumentum beolvasása 
4. Pontozás 

Az alábbi ábra a keresési kérelmek feldolgozásához használt összetevőket mutatja be. 

 ![Lucene-lekérdezési architektúradiagram az Azure Cognitive Search szolgáltatásban][1]


| A legfontosabb összetevők | Funkcionális leírás | 
|----------------|------------------------|
|**Lekérdezéselemzők** | A lekérdezési kifejezések elkülönítése a lekérdezési operátoroktól, és hozza létre a keresőmotornak küldendő lekérdezési struktúrát (lekérdezési fát). |
|**Elemzők** | Lexikális elemzés végrehajtása lekérdezési kifejezések szerint. Ez a folyamat magában foglalhatja a lekérdezési kifejezések átalakítását, eltávolítását vagy bővítését. |
|**Index** | Az indexelt dokumentumokból kinyert kereshető kifejezések tárolására és rendszerezésére használt hatékony adatstruktúra. |
|**Kereső** | A fordított index tartalma alapján lekéri és pontszámok az egyező dokumentumokat. |

## <a name="anatomy-of-a-search-request"></a>A keresési kérelmek működése

A keresési kérelem az eredményhalmazban visszaadandó adatok teljes specifikációja. A legegyszerűbb formában, ez egy üres lekérdezés feltétel nélkül. Egy reálisabb példa paramétereket, több lekérdezési kifejezést tartalmaz, esetleg bizonyos mezőkre, esetleg egy szűrőkifejezéssel és rendezési szabályokkal.  

A következő példa egy keresési kérelem, amelyet a REST API használatával küldhet az Azure Cognitive Search [szolgáltatásnak.](https://docs.microsoft.com/rest/api/searchservice/search-documents)  

~~~~
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
~~~~

Ehhez a kéréshez a keresőmotor a következőket teszi:

1. Kiszűri azokat a dokumentumokat, ahol az ár legalább 60 dollár, és kevesebb, mint 300 dollár.
2. Végrehajtja a lekérdezést. Ebben a példában a keresési lekérdezés kifejezésekből `"Spacious, air-condition* +\"Ocean view\""` és kifejezésekből áll: (a felhasználók általában nem adnak meg írásjeleket, de a példában való belefoglalása lehetővé teszi számunkra, hogy elmagyarázzuk, hogyan kezelik az elemzők). Ehhez a lekérdezéshez a keresőmotor megvizsgálja az `searchFields` "Ocean view" nézetet tartalmazó dokumentumok leírását és címmezőit, valamint a "tágas" kifejezéssel vagy a "légkondicionálás" előtaggal kezdődő kifejezésekkel. A `searchMode` paraméter bármely kifejezés (alapértelmezett) vagy mindegyik egyezésére szolgál olyan esetekben,`+`amikor a kifejezés kifejezetten nem kötelező ( ).
3. Megrendeli az eredményül kapott szállodakészletet egy adott földrajzi hely közelsége alapján, majd visszatér a hívó alkalmazáshoz. 

A cikk többsége a keresési *lekérdezés*feldolgozásáról szól: `"Spacious, air-condition* +\"Ocean view\""`. A szűrés és a rendezés hatókörén kívül esik. További információt a [Search API referenciadokumentációjában](https://docs.microsoft.com/rest/api/searchservice/search-documents)talál.

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>1. szakasz: Lekérdezéselemzés 

Mint megjegyeztük, a lekérdezési karakterlánc a kérelem első sora: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

A lekérdezéselemző elválasztja az operátorokat (például `*` a példában és `+` a példában) a keresési kifejezésektől, és a keresési lekérdezést támogatott típusú *segédlekérdezésekké* bontja: 

+ *kifejezés lekérdezés* önálló kifejezések (mint a tágas)
+ *kifejezéslekérdezés* idézett kifejezésekhez (például óceánnézet)
+ *előtaglekérdezés* kifejezésekhez, amelyeket `*` egy előtag operátor követ (például légkondicionáló)

A támogatott lekérdezéstípusok teljes listáját a [Lucene-lekérdezés szintaxisa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) című témakörben találja.

A segédlekérdezéshez társított operátorok határozzák meg, hogy a lekérdezés "kell" vagy "kell" teljesülnie ahhoz, hogy egy dokumentum egyezésnek minősüljön. Például `+"Ocean view"` a "must" az `+` operátor miatt. 

A lekérdezéselemző átszervezi az allekérdezéseket egy *lekérdezési fába* (a lekérdezést reprezentáló belső struktúrába), amelyet továbbad a keresőmotornak. A lekérdezéselemzés első szakaszában a lekérdezési fa így néz ki.  

 ![Logikai lekérdezés keresési módja bármely][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Támogatott elemzők: Egyszerű és teljes Lucene 

 Az Azure Cognitive Search két `simple` különböző lekérdezési `full`nyelvet (alapértelmezett) és a .Azure Cognitive Search(default) és a . Ha a `queryType` paramétert a keresési kérelemmel együtt állítja be, megmondja a lekérdezéselemzőnek, hogy melyik lekérdezési nyelvet válassza, hogy tudja, hogyan kell értelmezni az operátorokat és a szintaxist. Az [egyszerű lekérdezési nyelv](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) intuitív és robusztus, gyakran alkalmas a felhasználói bevitel ügyféloldali feldolgozás nélküli értelmezésére. Támogatja a webes keresőmotorokból ismerős lekérdezési operátorokat. A [Teljes Lucene lekérdezési nyelv](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) `queryType=full`, amelyet a beállítással kap, kiterjeszti az alapértelmezett egyszerű lekérdezési nyelvet azáltal, hogy további operátorokat és lekérdezéstípusokat , például helyettesítő, intelligens, regex és mezőhatókör-lekérdezéseket támogat. Az Egyszerű lekérdezés szintaxisában küldött reguláris kifejezés például lekérdezési karakterláncként, nem pedig kifejezésként lesz értelmezve. A jelen cikkben szereplő példakérelem a Teljes Lucene lekérdezési nyelvet használja.

### <a name="impact-of-searchmode-on-the-parser"></a>A searchMode hatása az elemzőre 

Egy másik keresési kérelem paraméter, amely `searchMode` hatással van az elemzésre, a paraméter. Ez határozza meg a logikai lekérdezések alapértelmezett operátorát: bármely (alapértelmezett) vagy az összes.  

Ha `searchMode=any`az alapértelmezett beállítás esetén a tágas és a légkondicionáló közötti`||`térhatároló OR ( ), így a mintalekérdezés szövege egyenértékű a következőképpen: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Explicit operátorok, `+` például a `+"Ocean view"`, egyértelműek a logikai lekérdezés eksztázisában (a *kifejezésnek* meg kell egyeznie). Kevésbé nyilvánvaló, hogyan kell értelmezni a fennmaradó kifejezések: tágas és légkondicionáló. Ha a keresőmotor találni mérkőzések óceánra néző *és* tágas *és* légkondicionáló? Vagy meg kell találni óceán kilátás plusz *az egyik* a fennmaradó feltételeket? 

Alapértelmezés szerint`searchMode=any`( ), a keresőmotor feltételezi a tágabb értelmezést. Mindkét mezőt egyeztetni *kell,* tükrözve a "vagy" szemantikát. A korábban bemutatott kezdeti lekérdezési fa a két "kell" művelettel az alapértelmezett értéket mutatja.  

Tegyük fel, `searchMode=all`hogy most meg . Ebben az esetben a szóköz "és" műveletként lesz értelmezve. A fennmaradó feltételek mindegyikének jelen kell lennie a dokumentumban ahhoz, hogy egyezésnek minősüljön. Az eredményül kapott mintalekérdezés a következőképpen módosul: 

~~~~
+Spacious,+air-condition*+"Ocean view"
~~~~

A lekérdezés módosított lekérdezési fája a következő lenne, ahol az egyező dokumentum mindhárom allekérdezés metszéspontja: 

 ![Logikai lekérdezés keresési módja az összes][3]

> [!Note] 
> A `searchMode=any` `searchMode=all` választás egy olyan döntés, amelyet a legjobb, ha reprezentatív lekérdezéseket futtat. Azok a felhasználók, akik valószínűleg operátorokat vesznek fel (gyakoriak a dokumentumtárolókban való kereséskor), intuitívabb eredményeket találhatnak, ha `searchMode=all` tájékoztatják a logikai lekérdezésszerkezeteket. Az operátorok közötti `searchMode` kölcsönhatásról az [Egyszerű lekérdezés szintaxisa](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)című témakörben van szó.

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>2. szakasz: Lexikális elemzés 

A lexikális elemzők a lekérdezési fa felépítése után dolgozza fel a *kifejezéslekérdezéseket* és a *kifejezéslekérdezéseket.* Az analizátor elfogadja az elemző által megadott szövegbemeneteket, feldolgozza a szöveget, majd visszaküldi a tokenizált kifejezéseket, amelyeket be kell építeni a lekérdezési fába. 

A lexikális elemzés leggyakoribb formája a *nyelvi elemzés,* amely egy adott nyelvre vonatkozó szabályok alapján alakítja át a lekérdezési kifejezéseket: 

* Lekérdezési kifejezés csökkentése egy szó gyökérformájára 
* A nem alapvető fontosságú szavak eltávolítása (stopwords, például "a" vagy "és" angol nyelven) 
* Összetett szó törése alkatrészekre 
* Kisburkolat egy nagybetűs szó 

Ezek a műveletek általában törli konklúdi a felhasználó által megadott szövegbevitel és az indexben tárolt kifejezések közötti különbségeket. Az ilyen műveletek túlmutatnak a szövegfeldolgozáson, és magának a nyelvnek a mélyreható ismeretét igénylik. A nyelvi tudatosság ezen rétegének hozzáadásához az Azure Cognitive Search támogatja a Lucene és a Microsoft [nyelvi elemzőinek](https://docs.microsoft.com/rest/api/searchservice/language-support) hosszú listáját.

> [!Note]
> Az elemzési követelmények a forgatókönyvtől függően a minimálistól a bonyolultig terjedhetnek. A lexikai elemzés összetettségét az előre definiált elemzők kiválasztásával vagy saját [egyéni analizátor](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search)létrehozásával szabályozhatja. Az elemzők hatóköre kereshető mezők, és egy meződefiníció részeként vannak megadva. Ez lehetővé teszi a lexikális elemzés mezőnkénti módosítására. Meghatározatlan, a *szabványos* Lucene analizátor t használja.

A példánkban az elemzés előtt a kezdeti lekérdezési fa a "Tágas" kifejezéssel rendelkezik, nagy "S" betűvel és vesszővel, amelyet a lekérdezéselemző a lekérdezési kifejezés részeként értelmez (a vessző nem minősül lekérdezési nyelv operátorának).  

Amikor az alapértelmezett analizátor feldolgozza a kifejezést, akkor kisbetűs "óceán nézet" és a "tágas", és távolítsa el a vessző karakter. A módosított lekérdezési fa a következőképpen fog kinézni: 

 ![Logikai lekérdezés elemzett kifejezésekkel][4]

### <a name="testing-analyzer-behaviors"></a>Az analizátor viselkedésének tesztelése 

Az analizátor viselkedése az [Analyze API segítségével](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)tesztelhető. Adja meg az elemezni kívánt szöveget, hogy lássa, milyen kifejezéseket hoz létre az adott elemző. Ha például meg szeretné tekinteni, hogy a standard analizátor hogyan dolgozza fel a "légkondicionáló" szöveget, a következő kérést teheti:

~~~~
{
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

A szabványos analizátor a következő két tokenre bontja a bemeneti szöveget, olyan attribútumokkal jelölve meg őket, mint a kezdő és záró eltolások (a találat kiemeléséhez használt), valamint azok helyzete (a kifejezésegyeztetéshez):

~~~~
{
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
~~~~

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Kivételek a lexikai elemzés alól 

A lexikális elemzés csak a teljes kifejezéseket igénylő lekérdezéstípusokra vonatkozik – akár kifejezéslekérdezésre, akár kifejezéslekérdezésre. Nem vonatkozik a hiányos kifejezésekkel rendelkező lekérdezéstípusokra – előtaglekérdezésre, helyettesítő lekérdezésre, regex lekérdezésre –, illetve egy homályos lekérdezésre. Ezek a lekérdezéstípusok, beleértve az `air-condition*` előtag lekérdezés kifejezés a példában, közvetlenül hozzáadódik a lekérdezési fa, megkerülve az elemzési szakaszban. Az ilyen típusú lekérdezési kifejezéseken végrehajtott egyetlen átalakítás a lowercasing.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>3. szakasz: Dokumentum-visszakeresés 

A dokumentumlekérés olyan dokumentumok keresésére utal, amelyek az indexben megfelelő kifejezésekkel vannak elhatárolódnak. Ezt a szakaszt egy példa alapján lehet a legjobban megérteni. Kezdjük egy szállodai index, amelynek a következő egyszerű séma: 

~~~~
{
    "name": "hotels",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "title", "type": "Edm.String", "searchable": true },
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
~~~~

Tételezzük fel továbbá, hogy ez az index a következő négy dokumentumot tartalmazza: 

~~~~
{
    "value": [
        {
            "id": "1",
            "title": "Hotel Atman",
            "description": "Spacious rooms, ocean view, walking distance to the beach."
        },
        {
            "id": "2",
            "title": "Beach Resort",
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."
        },
        {
            "id": "3",
            "title": "Playa Hotel",
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },
        {
            "id": "4",
            "title": "Ocean Retreat",
            "description": "Quiet and secluded"
        }
    ]
}
~~~~

**A kifejezések indexelése**

A lekérés megértéséhez segít néhány alapvető tudni az indexelést. A tárolási egység egy fordított index, minden kereshető mezőhöz egy. A fordított indexen belül az összes dokumentum összes kifejezésének rendezett listája található. Minden kifejezés leképezi azon dokumentumok listáját, amelyekben előfordul, amint az az alábbi példában is látható.

A fordított indexben lévő kifejezések létrehozásához a keresőmotor lexikális elemzést végez a dokumentumok tartalmán keresztül, hasonlóan ahhoz, ami a lekérdezés feldolgozása során történik:

1. *A szövegbemenetek* az analizátor konfigurációjától függően egy analizátornak, kisbetűsek, írásjelektől megfosztottak stb. 
2. *A tokenek* a szövegelemzés kimenetei.
3. *A kifejezések* hozzáadódnak az indexhez.

Gyakori, de nem szükséges, hogy ugyanazokat az elemzőket használja a keresési és indexelési műveletekhez, hogy a lekérdezési kifejezések jobban hasonlítsanak az indexen belüli kifejezésekre.

> [!Note]
> Az Azure Cognitive Search segítségével különböző elemzőket adhat `indexAnalyzer` `searchAnalyzer` meg az indexeléshez és a kereséshez további és mezőparamétereken keresztül. Ha nincs megadva, a `analyzer` tulajdonsággal beállított elemző az indexeléshez és a kereséshez is használható.  

**Fordított index például dokumentumok**

Visszatérve a példánkhoz, a **címmezőhöz,** a fordított index így néz ki:

| Időtartam | Dokumentumlista |
|------|---------------|
| Atman | 1 |
| Beach | 2 |
| Hotel | 1, 3 |
| Óceán | 4  |
| Playa | 3 |
| Resort | 3 |
| Visszavonulás | 4 |

A címmezőben csak a *szálloda* jelenik meg két dokumentumban: 1, 3.

A **megnevezés** mező esetében az index a következő:

| Időtartam | Dokumentumlista |
|------|---------------|
| Levegő | 3
| és | 4
| Beach | 1
| Kondicionált | 3
| Kényelmes | 3
| Távolság | 1
| Sziget | 2
| kaua啦i | 2
| található | 2
| north (észak) | 2
| Óceán | 1, 2, 3
| / | 2
| itt: |2
| Csendes | 4
| Szobák  | 1, 3
| Félreeső | 4
| Shore | 2
| Tágas | 1
| műveletnek a(z) | 1, 2
| erre: | 1
| Nézd | 1, 2, 3
| Gyaloglás | 1
| a | 3


**Lekérdezési kifejezések egyeztetése az indexelt kifejezésekkel**

A fenti fordított indexek alapján térjünk vissza a mintalekérdezéshez, és nézzük meg, hogyan találhatók egyező dokumentumok a példalekérdezéshez. Emlékezzünk vissza, hogy a végső lekérdezési fa így néz ki: 

 ![Logikai lekérdezés elemzett kifejezésekkel][4]

A lekérdezés végrehajtása során az egyes lekérdezések egymástól függetlenül hajthatók végre a kereshető mezőkkel. 

+ A TermQuery, "tágas", megfelel az 1- es dokumentumnak (Hotel Atman). 

+ A "légkondicionáló*" előtaglekérdezés nem egyezik egyetlen dokumentummal sem. 

  Ez a viselkedés néha összezavarja a fejlesztőket. Bár a dokumentumban szerepel a légkondicionált kifejezés, az alapértelmezett elemző két kifejezésre osztja. Emlékezzünk vissza, hogy a részleges kifejezéseket tartalmazó előtag-lekérdezéseket a program nem elemzi. Ezért a "légkondicionáló" előtaggal rendelkező kifejezéseket a fordított index ben keresik, és nem találhatók.

+ A PhraseQuery, "ocean view", megkeresi az "óceán" és a "nézet" kifejezéseket, és ellenőrzi a kifejezések közelségét az eredeti dokumentumban. Az 1., 2. Közlemény dokumentum 4 a kifejezés óceán a címben, de nem tekinthető egyezésnek, mint keresünk az "óceán nézet" kifejezés helyett az egyes szavakat. 

> [!Note]
> A keresési lekérdezés az Azure Cognitive Search indexében lévő összes kereshető mezővel függetlenül történik, kivéve, ha korlátozza a `searchFields` paraméterrel beállított mezőket, amint azt a példa keresési kérelem szemlélteti. A kijelölt mezők bármelyikének megfelelő dokumentumokat ad vissza. 

Összességében a kérdéses lekérdezés esetében az 1, 2, 3 dokumentumok egyeznek. 

## <a name="stage-4-scoring"></a>4. szakasz: Pontozás  

A keresési eredményhalmaz minden dokumentumához relevancia-pontszám van rendelve. A relevancia-pontszám funkciója az, hogy magasabb rangú dokumentumokat, amelyek a legjobbválaszt a felhasználói kérdésre, ahogy azt a keresési lekérdezés. A pontszám kiszámítása az egyező kifejezések statisztikai tulajdonságai alapján történik. A pontozási képlet középpontjában a [TF/IDF (kifejezés frekvencia-inverz dokumentum gyakorisága)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)áll. A ritka és gyakori kifejezéseket tartalmazó lekérdezésekben a TF/IDF a ritka kifejezést tartalmazó eredményeket támogatja. Például egy hipotetikus indexben az összes Wikipedia-cikkel, *az elnök*lekérdezésének megfelelő dokumentumokból származó dokumentumokban az *elnöknek* megfelelő dokumentumok relevánsabbak, mint *a*.


### <a name="scoring-example"></a>Példa pontozási példa

A példalekérdezésnek megfelelő három dokumentum visszahívása:
~~~~
search=Spacious, air-condition* +"Ocean view"  
~~~~
~~~~
{
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
~~~~

Az 1. *spacious* *ocean view* A következő két dokumentum csak az óceánnézet kifejezésnek felel *meg.* Meglepő lehet, hogy a 2- es és 3-as dokumentum relevancia-pontszáma eltérő, még akkor is, ha ugyanúgy egyeztetett a lekérdezéssel. Ez azért van, mert a pontozási képlet több összetevőt tartalmaz, mint a TF/IDF. Ebben az esetben a 3. Ismerje meg [Lucene gyakorlati pontozási képletét,](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) hogy megértse, hogyan befolyásolhatja a mező hossza és más tényezők a relevancia-pontszámot.

Egyes lekérdezéstípusok (helyettesítő karakter, előtag, regex) mindig állandó pontszámot adnak a teljes dokumentumpontszámhoz. Ez lehetővé teszi, hogy a lekérdezésbővítés során talált egyezések szerepeljenek az eredményekközött, de a rangsorolás befolyásolása nélkül. 

Egy példa szemlélteti, hogy ez miért fontos. A helyettesítő karakteres keresések, beleértve az előtag-kereséseket is, definíció szerint nem egyértelműek, mivel a bemenet egy részleges karakterlánc, amely nagyon sok különböző kifejezéssel rendelkezik potenciális egyezésekkel (fontolja meg a "körutazások*" bevitelét, a "túrák", a "tourettes" és a " turmaline"). Tekintettel ezen eredmények jellegére, nincs mód arra, hogy ésszerűen következtetni, hogy mely kifejezések értékesebbek, mint mások. Emiatt figyelmen kívül hagyjuk a kifejezés gyakorisága, amikor pontozási eredmények típusú helyettesítő karakteres, előtag és regex típusú lekérdezések. A részleges és teljes kifejezéseket tartalmazó többrészes keresési kérelmekben a részleges bemeneti eredmények állandó pontszámmal vannak beépítve, hogy elkerüljék a potenciálisan váratlan egyezések irányábamutató elfogultságot.

### <a name="score-tuning"></a>Pontszám hangolása

Az Azure Cognitive Search szolgáltatásban kétféleképpen hangolhatja be a relevanciapontszámokat:

1. **A pontozási profilok** szabályok alapján népszerűsítik a rangsorolt eredménylistában szereplő dokumentumokat. A példánkban a címmezőben egyező dokumentumokat relevánsabbá tehetjük, mint a leírás mezőben egyeztetett dokumentumokat. Továbbá, ha az indexünknek minden egyes szállodára lenne ármezője, akkor alacsonyabb árú dokumentumokat reklámozhatnánk. További információ a [pontozási profilok keresési indexhez való hozzáadásáról.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **A kifejezéskiemelés** (csak a Teljes lucene lekérdezés `^` szintaxisában érhető el) olyan kiemelési operátort biztosít, amely a lekérdezési fa bármely részére alkalmazható. A példánkban a *légkondicionáló*\*előtag keresése helyett rá lehet keresni a *pontos légkondicionálás* vagy az előtag kifejezésre, de a pontos kifejezéssel megegyező dokumentumok magasabbak a lekérdezés kifejezésre való kiemelés alkalmazásával: *légkondicionáló^2|| légkondicionáló**. További információ a [kifejezéskiemelésről.](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost)


### <a name="scoring-in-a-distributed-index"></a>Pontozás elosztott indexben

Az Azure Cognitive Search összes indexe automatikusan több szegmensre oszlik, így gyorsan terjesztheti az indexet több csomópont között a szolgáltatás skálázása vagy leskálázása során. Keresési kérelem kiadásakor, az egyes szegmensek egymástól függetlenül. Az egyes szegmensek eredményeit ezután egyesíti, és pontszám szerint rendezi (ha nincs más rendelés definiálva). Fontos tudni, hogy a pontozási függvény súlyok lekérdezési kifejezés gyakorisága ellen inverz dokumentum gyakorisága az összes dokumentumot a szegmensen belül, nem az összes szilánkok!

Ez azt jelenti, hogy a relevancia-pontszám eltérő *lehet* az azonos dokumentumok esetében, ha különböző szegmenseken vannak. Szerencsére az ilyen különbségek általában eltűnnek, mivel az indexben lévő dokumentumok száma az egyenletesebb távú eloszlás miatt növekszik. Nem lehet feltételezni, hogy melyik szegmensen kerül elhelyezésre egy adott dokumentum. Azonban feltételezve, hogy egy dokumentumkulcs nem változik, mindig ugyanahhoz a szegmenshez lesz rendelve.

Általánosságban elmondható, hogy a dokumentumpontszám nem a legjobb tulajdonság a dokumentumok rendeléséhez, ha a rendelés stabilitása fontos. Ha például két azonos pontszámmal rendelkező dokumentum van, nincs garancia arra, hogy melyik jelenik meg először ugyanazon lekérdezés későbbi futtatásakor. A dokumentum pontszáma csak az eredmények ben található többi dokumentumhoz képest adhat általános képet a dokumentum relevanciájához.

## <a name="conclusion"></a>Összegzés

A siker az internetes keresőmotorok emelte elvárásait a teljes szöveges keresés a személyes adatokat. Szinte bármilyen keresési élmény, most elvárjuk, hogy a motor megérteni a szándékunkat, akkor is, ha a feltételek hibásan írt vagy hiányos. Lehet, hogy még arra is számíthatunk, hogy a mérkőzések közel azonos feltételek vagy szinonimák alapján, amelyeket valójában soha nem határoztunk meg.

Technikai szempontból a teljes szöveges keresés rendkívül összetett, kifinomult nyelvi elemzést és a feldolgozás szisztematikus megközelítését igényli oly módon, hogy a lekérdezési kifejezések et desztillálja, bővíti és átalakítja a releváns eredmény érdekében. Tekintettel a benne rejlő összetettségre, számos olyan tényező van, amely befolyásolhatja a lekérdezés kimenetelét. Ebből az okból, befektetés az idő, hogy megértsék a mechanika a teljes szöveges keresés kínál kézzelfogható előnyöket, amikor megpróbálja a munka révén váratlan eredményeket.  

Ez a cikk az Azure Cognitive Search környezetében végzett teljes szöveges keresést tárt fel. Reméljük, hogy elegendő hátteret biztosít a gyakori lekérdezési problémák kezeléséhez szükséges lehetséges okok és megoldások felismeréséhez. 

## <a name="next-steps"></a>További lépések

+ Készítse el a mintaindexet, próbálja ki a különböző lekérdezéseket, és tekintse át az eredményeket. További információt az [Index létrehozása és lekérdezése a portálon című témakörben talál.](search-get-started-portal.md#query-index)

+ Próbálkozzon további lekérdezési szintaxissal a [Keresési dokumentumok](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) példa szakaszból, vagy az Egyszerű [lekérdezés szintaxisából](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) a kereséskezelőben a portálon.

+ Tekintse át [a pontozási profilokat,](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) ha be szeretné hangolni a rangsorolást a keresési alkalmazásban.

+ További információ a [nyelvspecifikus lexikális elemzők alkalmazásáról.](https://docs.microsoft.com/rest/api/searchservice/language-support)

+ [Egyéni elemzők konfigurálása](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) minimális feldolgozáshoz vagy speciális feldolgozáshoz adott mezőkön.

## <a name="see-also"></a>Lásd még

[Dokumentumok keresése – REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Teljes Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[A keresési eredmények kezelése](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
