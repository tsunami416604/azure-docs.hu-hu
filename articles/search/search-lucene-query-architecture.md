---
title: Teljes szöveges keresés (Lucene) motor architektúra – Azure Search
description: Teljes szöveges kereséshez, mint az Azure Search kapcsolódó Lucene lekérdezési feldolgozására és dokumentum beolvasása fogalmak ismertetése.
manager: jlembicz
author: yahnoosh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: dedfc7db6aef6d55fd50c94a217bdc489b9615f3
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633861"
---
# <a name="how-full-text-search-works-in-azure-search"></a>Teljes szöveges keresés működése az Azure Search szolgáltatásban

Ez a cikk olyan fejlesztőknek szól, akik az Azure Search Lucene teljes szöveges keresés működése jobban megértheti kell. Szöveges lekérdezések esetén Azure Search zökkenőmentesen fog nyújtani a várt eredményt a legtöbb helyzetben, de alkalmanként kaphat "off" valahogy úgy tűnik, hogy eredményt. Ebben az esetben Lucene lekérdezési végrehajtási négy fázisát háttérrel rendelkező (lekérdezése elemzési, lexikai elemzés, dokumentum-egyeztetéséhez, pontozási) segítségével azonosíthatja a lekérdezési paraméterek és az index konfigurációja, amely magában foglal a kívánt adott módosítása serkenti az eredményt. 

> [!Note] 
> Az Azure Search Lucene használja a teljes szöveges kereséshez, de a Lucene-integráció tehát nem tekinthető teljesnek. Azt szelektíven teszi közzé, és a Lucene funkciójának engedélyezése az Azure Search fontos a forgatókönyvek bővítése. 

## <a name="architecture-overview-and-diagram"></a>Az architektúra áttekintése és diagramja

Egy teljes szöveges keresési lekérdezés feldolgozása kezdődik, elemzés, bontsa ki a keresési feltételeket a lekérdezés szövege. A keresőmotor az egyező feltételeinek dokumentumok lekérdezésének index használja. Az egyes lekérdezési kifejezések néha lebontva, és új űrlapok, egy szélesebb körű net között mi lehetséges egyezést tekinthető át az elkészített. Az eredményhalmaz majd egy relevanciapontszám rendelt minden egyes egyező dokumentum szerint van rendezve. Ezek a rangsorolt listát tetején visszakerülnek a hívó alkalmazás.

Állapítani, lekérdezés-végrehajtás négy fázisból áll: 

1. Elemzési lekérdezés 
2. Lexikai elemzés 
3. A dokumentum beolvasása 
4. A pontozás 

Az alábbi ábra szemlélteti a keresési kérelmek feldolgozásához használt összetevőket. 

 ![Lucene lekérdezési architektúra diagramja az Azure Search szolgáltatásban][1]


| A legfontosabb összetevők | Funkcionális leírása | 
|----------------|------------------------|
|**Lekérdezés elemzők** | Lekérdezési kifejezések elkülönítése a lekérdezési operátorokkal és a keresőmotor küldendő lekérdezési struktúra (lekérdezés fa) létrehozása. |
|**Elemzők** | Hajtsa végre a lexikális elemzése a lekérdezési kifejezéseket. Ezt a folyamatot is magában foglalhat, átalakítja, eltávolítása vagy a lekérdezési feltételek kiterjesztése. |
|**Index** | Egy hatékony adatszerkezet, tárolására és rendszerezheti a kinyert indexelt dokumentumok kereshető feltételek használható. |
|**Keresőmotor** | Olvassa be, és a fordított index tartalma alapján egyező dokumentumok pontszámmodell. |

## <a name="anatomy-of-a-search-request"></a>Egy keresési kérelmet szerkezete

Egy keresési kérelmet egy teljes specifikace mi vissza kell adni egy eredményhalmazban szerepel. A legegyszerűbb formájában az üres lekérdezés bármiféle feltétellel. Realisztikusabb példában paramétereket tartalmazza, több lekérdezés feltételeket, bizonyos mezők, valószínűleg egy kifejezést, és a rendezés szabályok esetleg hatóköre.  

Az alábbi példában egy keresési kérelmet küldünk előfordulhat, hogy az Azure Search használatával az [REST API-val](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

~~~~
POST /indexes/hotels/docs/search?api-version=2017-11-11 
{  
    "search": "Spacious, air-condition* +\"Ocean view\"",  
    "searchFields": "description, title",  
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",  
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
 } 
~~~~

A kérelem a keresőmotor a következőket teszi:

1. Kiszűri a dokumentumok az ár esetén legalább $60 és kevesebb mint 300 USD.
2. A lekérdezés végrehajtása. Ebben a példában a keresési lekérdezés mondatokat és áll feltételek: `"Spacious, air-condition* +\"Ocean view\""` (felhasználók általában nem ad meg absztrakt, de azt ismertetik, hogyan elemzők kezelni azt, beleértve a példa segítségével). Ehhez a lekérdezéshez, a keresőmotor megvizsgálja a leírást, és cím mezőkben megadott `searchFields` dokumentumok, amelyek tartalmazzák a "View óceán", és emellett az "ahhoz," kifejezés vagy a feltételeket, amelyeket előtaggal kezdődik "air-condition". A `searchMode` paraméter használható bármilyen kifejezés (alapértelmezett), vagy azokat, az esetekben, ahol egy kifejezés nem explicit módon szükséges megfelelő (`+`).
3. A rendelések a létrejövő közel kerülhet egy adott földrajzi helyen a szállodák közül állította, és a hívó alkalmazás majd vissza. 

Ez a cikk a legtöbb tárgya feldolgozását a *keresési lekérdezés*: `"Spacious, air-condition* +\"Ocean view\""`. Szűrés és rendezés esnek a hatókörön. További információkért lásd: a [Search API dokumentációja](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>1. fázis: Elemzési lekérdezés 

Feljegyzett, a lekérdezési karakterláncban a kérés az első sort: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

A lekérdezéselemző elválasztja az operátorok (például `*` és `+` a példában) keresési feltételeit, és a keresési lekérdezést deconstructs *segédlekérdezések* támogatott típusú: 

+ *lekérdezési kifejezés* önálló kifejezésekre (például ahhoz)
+ *lekérdezési kifejezés* határolójeles feltételek (például óceán megtekintése)
+ *előtag lekérdezés* követ egy előtagot operátor feltételek `*` (például air-condition)

Támogatott lekérdezési típusok teljes listáját lásd: [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Kapcsolódó allekérdezés operátorok határozza meg, hogy a lekérdezés "kell" vagy "kell" elégedett az eredménnyel, ahhoz, hogy egy dokumentumot, figyelembe kell venni az egyezést. Ha például `+"Ocean view"` "kell" van oka az, hogy a `+` operátor. 

A lekérdezéselemző átszervezése a segédlekérdezések be egy *lekérdezés fa* (egy belső szerkezetét, a lekérdezés képviselő) továbbítja az keresőmotort. Elemzési lekérdezés első szakaszában a lekérdezés fa néz ki.  

 ![Logikai érték searchmode minden lekérdezése][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Támogatott elemzők: Egyszerű és teljes Lucene 

 Az Azure Search mutatja a két különböző lekérdezési nyelvet, `simple` (alapértelmezett), és `full`. Beállításával a `queryType` paramétert a keresési kéréssel, akkor mondja el a lekérdezéselemző mely lekérdezési nyelvet választja, hogy tudja, a kezelők és a szintaxis értelmezése. A [egyszerű lekérdezési nyelv](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) intuitív és robusztus, gyakran megfelelő értelmezéséhez, a felhasználói bevitel – ügyféloldali feldolgozás nélkül van. Támogatja a jól ismert webes keresőmotorokból lekérdezési operátor. A [teljes Lucene lekérdezési nyelv](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), amelyek beállításával kap `queryType=full`, terjeszti ki az alapértelmezett egyszerű lekérdezési nyelv támogatásával további operátorok és a lekérdezés típusok, pl. helyettesítő, intelligens, a regex és a mező-hatáskörű lekérdezések. Például egy reguláris kifejezést az egyszerű lekérdezési szintaxis küldött lenne értelmezni egy lekérdezési karakterláncot, és nem egy kifejezés. A kérelem (példa) ebben a cikkben a teljes Lucene lekérdezési nyelvet használ.

### <a name="impact-of-searchmode-on-the-parser"></a>Az elemző a searchMode hatása 

Keresési kérelmet egy másik paraméter, amely befolyásolja a elemzés a `searchMode` paraméter. Azt szabályozza, hogy az alapértelmezett üzemeltető logikai lekérdezések: vagy az összes bármely (alapértelmezett).  

Amikor `searchMode=any`, amely az alapértelmezett beállítás, a helyet elválasztó közötti ahhoz, és a air-condition vagy (`||`), ami egyenértékű a mintául szolgáló lekérdezés szövege: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Explicit operátorok, például `+` a `+"Ocean view"`, egyértelmű a logikai lekérdezési konstrukció (az előfizetési időszak *kell* felel meg). A fennmaradó használati értelmezése kevésbé nyilvánvaló van: ahhoz, és air-condition. A keresőmotor találni egyezések óceán nézethez *és* ahhoz, *és* air-condition? Vagy kell keresés óceán nézet plusz *egy* fennmaradó feltételeket? 

Alapértelmezés szerint (`searchMode=any`), a keresőmotor feltételezi, hogy a szélesebb körű értelmezését. Bármely mező *kell* vehet, existovat shoda. "vagy" szemantika. A kezdeti lekérdezés fa korábban bemutatott, a kettő a "kell" operations, az alapértelmezett jeleníti meg.  

Tegyük fel, hogy most már beállított `searchMode=all`. Ebben az esetben a terület kerül értelmezésre "és" művelet. A fennmaradó feltételek mindegyike is jelen kell lennie ahhoz, hogy egyezés a dokumentumban. Az eredményül kapott mintalekérdezés értelmezését módon: 

~~~~
+Spacious,+air-condition*+"Ocean view"  
~~~~

A módosított lekérdezés fa ehhez a lekérdezéshez a következők lennének, ahol a megfelelő dokumentum az összes három segédlekérdezések metszetét: 

 ![Az összes logikai lekérdezés searchmode][3]

> [!Note] 
> Választás `searchMode=any` keresztül `searchMode=all` legjobb döntést jutott reprezentatív lekérdezések futtatásával. Felhasználók, akik valószínűleg tartalmaz az operátorok (keresését dokumentumban tárolja a közös) találhatja eredmények intuitívabb Ha `searchMode=all` tájékoztatja a logikai lekérdezési szerkezeteket. További információ a kölcsönhatások közötti `searchMode` operátorok, tekintse meg és [egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>2. fázis: Lexikai elemzés 

Lexikai elemzőket folyamat *lekérdezések távú* és *lekérdezések kifejezést* után a lekérdezési fa felépítése. Egy elemző fogadja el az elemző által szolgáltatott bemeneti szöveg, dolgozza fel a szöveget, és be kell építeni a lekérdezés fa majd küld vissza a tokenekre bontott feltételeket. 

A leggyakoribb formája, lexikai elemzés *nyelvi elemzés* melyik átalakításokat a lekérdezés a megadott nyelvre a meghatározott szabályok alapján: 

* A legfelső képernyőre szó csökkenti a lekérdezési kifejezés 
* Eltávolítás, melyek nem nélkülözhetetlenek szavakat (áll, például az "a" vagy "és" angol nyelven) 
* Egy összetett a word kompatibilitástörő összetevő részre 
* Kis-és nagybetűhasználatának egy nagybetűs word alacsonyabb 

Mindezen műveleteket általában az a felhasználó által megadott szöveges bevitel és a használati az indexben tárolt közötti különbségek törlése. Ezeket a műveleteket nem szöveg feldolgozása, és igényelnek a nyelvet, maga átfogó ismeretére. Azure Search nyelvi figyelése a réteg hozzáadása, támogatja a hosszú [nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support) a lucene-től és a Microsoft.

> [!Note]
> Elemzés követelmények között lehet minimális, a forgatókönyvtől függően alkotóelemeknek a működését. Szabályozhatja lexikai elemzés összetettségétől, a választ az előre meghatározott elemzők az egyik, vagy hozzon létre saját [egyéni elemző](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Elemzők hatóköre kereshető mezőket, és a egy mező definíciójának részeként vannak megadva. Ez lehetővé teszi, hogy eltérő lexikai elemzés, mező alapon. Nincs megadva, a *standard* Lucene-elemzőt használja.

Ebben a példában az elemzést követően előtt a kezdeti lekérdezés fa "Spacious," kis "S" és a egy vesszőt (vesszővel nem tekinthető a lekérdezési nyelv operátor) lekérdezési kifejezés részeként értelmezi a lekérdezéselemző rendelkezik kifejezés.  

Az alapértelmezett elemző feldolgozza az az időszak, amikor azt fogja kisbetűs "óceán view" és "ahhoz,", és távolítsa el a vessző karakter. A módosított lekérdezés fa a következőképpen fog kinézni: 

 ![Az elemzett feltételeinek logikai lekérdezés][4]

### <a name="testing-analyzer-behaviors"></a>Tesztelési analyzer viselkedések 

Egy elemző viselkedését használatával kell vizsgálni a [elemezheti API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Adja meg a fogalmakkal elemző eszköz generál megtekintéséhez elemezni kívánt szöveget. Például ha szeretné látni, hogyan a standard szintű analyzer szeretné feldolgozni a szöveg "air-condition", adhat ki a következő kérelmet:

~~~~
{ 
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

A standard szintű analyzer a bemeneti szöveg bontja a következő két tokenek jegyzetkészítés azokat az attribútumokat, például a kezdő és záró eltolás (használt találatok kiemelése), valamint a pozíció (a kifejezés egyeztetéséhez használt):

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

### <a name="exceptions-to-lexical-analysis"></a>Kivételek lexikai elemzés 

Lexikai elemzés csak teljes használati – kifejezés lekérdezés vagy egy kifejezés lekérdezést igénylő lekérdezési típusokra vonatkozik. Ez nem vonatkozik a lekérdezések típusai – előtag lekérdezés, helyettesítő karaktert tartalmazó lekérdezés, regex lekérdezés – hiányos adatokkal, vagy egy intelligens lekérdezés. Ezek lekérdezése típusok, többek között az előtag lekérdezési kifejezés a `air-condition*` ebben a példában kerülnek közvetlenül a lekérdezés fában, az elemzési fázis kihagyásával. Az adott típusú lekérdezési kifejezések végrehajtott csak átalakítás lowercasing van.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>3. fázis: A dokumentum beolvasása 

A dokumentum lekéréséhez hivatkozik az egyeztetési feltételek az indexben dokumentumok keresése. Ebben a szakaszban egy példán keresztül legjobb értendő. Kezdjük egy "Hotels" index a következő egyszerű séma kellene: 

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

További azt feltételezik, hogy ez az index tartalmazza a következő négy dokumentumokat: 

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

**Hogyan indexeli ugyan, feltételek**

Szeretné megtudni, lekérés, megismeréséről néhány alapvető tudnivalók az indexelés. A tárolási egység fordított index, egy az egyes kereshető mezőjében. Fordított index belül az összes feltétel összes dokumentum a rendezett listája. Minden időszak, amelyben azok előfordulásakor, nyilvánvaló, az alábbi példa a dokumentumok listájának rendeli hozzá.

A feltételek fordított index létrehozásához, a keresőmotor a tartalomhoz, a dokumentumok, hasonlóan mi történik a lekérdezés feldolgozása során lexikai elemzés hajtja végre:

1. *Bemeneti szöveg* átadni egy elemző, alacsonyabb kisbetűsek eltávolítani az absztrakt, és így tovább, analyzer konfigurációjától függően. 
2. *Jogkivonatok* szöveg elemzési eredményeket.
3. *Feltételek* hozzáadódnak az index.

Gyakori, de nem szükséges, a Keresés és a műveletek indexelő, így a lekérdezési kifejezések több mint belül az index feltételek keresse meg az azonos elemzők használandó.

> [!Note]
> Az Azure Search lehetővé teszi, hogy adja meg a különböző elemzők az indexelés és keresés használatával további `indexAnalyzer` és `searchAnalyzer` paraméterek mezőben. Ha nincs megadva, az elemző beállított a `analyzer` tulajdonság szolgál az indexelés és keresés.  

**Példa dokumentumok fordított indexe**

Térjen vissza a jelen példában a **cím** mező, a fordított index néz ki:

| Időtartam | Dokumentumok listájához |
|------|---------------|
| atman | 1 |
| Beach | 2 |
| Szálloda | 1, 3 |
| óceán | 4  |
| PlayA | 3 |
| végső megoldásként | 3 |
| Retreat | 4 |

A cím mező csak a *Szálloda* megjelenik-e két dokumentumot: 1., 3.

Az a **leírás** mező, az index a következőképpen történik:

| Időtartam | Dokumentumok listájához |
|------|---------------|
| vezeték nélkül | 3
| és | 4
| Beach | 1
| megfelel | 3
| kényelmes | 3
| távolságskála | 1
| sziget | 2
| kauaʻi | 2
| található | 2
| északi régiója | 2
| óceán | 1, 2, 3
| / | 2
| be |2
| csendes | 4
| termek  | 1, 3
| secluded | 4
| part | 2
| Ahhoz | 1
| a | 1, 2
| erre: | 1
| megtekintés | 1, 2, 3
| walking | 1
| a következővel: | 3


**Egyező lekérdezési kifejezések indexelt feltételek ellen**

Most adja meg a fenti fordított indexek, térjen vissza a mintalekérdezés és hogyan egyező dokumentumok lásd a példa lekérdezés találhatók. Idézze, hogy az utolsó lekérdezési fa néz ki: 

 ![Az elemzett feltételeinek logikai lekérdezés][4]

Lekérdezés-végrehajtás során egyes lekérdezések végrehajtásakor a kereshető mező alapján egymástól függetlenül. 

+ A TermQuery, "ahhoz,", egyezések dokumentum-1 (szálloda Atman). 

+ A PrefixQuery "air-condition *", nem felel meg a dokumentumokat. 

  Ez az a probléma, amely időnként confuses fejlesztők számára. Klimatizált kifejezés szerepel a dokumentumot, bár van felosztva két kifejezés az alapértelmezett elemző által. Ne felejtse el, hogy az előtag-lekérdezések részleges feltételeket tartalmaz, nem lesz elemezve. Ezért a kifejezéseket "air-condition" előtagot az fordított index kulcskeresési, és nem található.

+ A PhraseQuery, "óceán view", a feltételek "óceán" és "megtekintése" kikeresi, és ellenőrzi a közelében, az eredeti dokumentum feltételek. Dokumentumok, 1, 2 és 3 felel meg a lekérdezés a Leírás mezőben. Figyelje meg a dokumentum 4 a cím kifejezés óceán rendelkezik, de egyezést, nem számít, hogy keres az egyes szavak helyett a "óceán view" kifejezés helyett szerepel. 

> [!Note]
> Egy keresési hajtja végre a lekérdezést egymástól függetlenül az Azure Search-indexben kereshető mező alapján, ha rendelkező mezők korlátozza a `searchFields` paraméterrel, ahogyan az a keresési kérelem (példa). A kiválasztott mezők megfelelő dokumentumok adja vissza. 

A teljes a szóban forgó lekérdezés, a dokumentumok, amelyek megfelelnek 1, 2, 3. 

## <a name="stage-4-scoring"></a>4. fázis: A pontozás  

Minden dokumentumnak egy keresési eredményhalmaz egy relevanciapontszám van hozzárendelve. A relevanciapontszám feladata magasabb rank, ezeket a dokumentumokat, amely a legjobban a keresési lekérdezés által kifejezett egy felhasználó kapcsolatos kérdésére választ kaphat. A pontszám, amely megfelel a feltételek statisztikai tulajdonságok alapján számítja ki. A pontozó függvény lényege [TF/IDF (kifejezés gyakorisága – inverz dokumentum frequency)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). A ritka és gyakori használati tartalmazó lekérdezéseket TF/IDF elősegíti a ritka kifejezést tartalmazó eredmények. Például az összes Wikipedia-cikk elméleti index, dokumentumokból, amely egyezik a lekérdezés *elnöke*, a megfelelő dokumentumok *elnöke* relevánsabb, mint a dokumentumok minősülnek a megfelelő *a*.


### <a name="scoring-example"></a>Pontozó példa

A három dokumentum, amely megfelel a példalekérdezés visszahívása:
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

1 felelt meg a lekérdezés legjobb mert mindkét kifejezés *ahhoz,* és a szükséges kifejezés *óceán nézet* fordulhat elő, a Leírás mezőben. A következő két dokumentumok egyeznie a kifejezés csak *óceán nézet*. Meglepő, hogy a dokumentum a 2. és 3 relevanciapontszám eltér, annak ellenére, hogy azok a lekérdezés matched ugyanúgy lehet. Mivel a pontozási képlet további összetevők, mint az imént TF/IDF van. Ebben az esetben a dokumentum 3 van hozzárendelve némileg magasabb pontszámot, mert rövidebb a leírását. Ismerje meg [Lucene a gyakorlati pontozási képlet](https://lucene.apache.org/core/4_0_0/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) megérteni, hogyan mező hossza és egyéb tényezők befolyásolhatják a a relevanciapontszám.

Néhány lekérdezés (altartományokra is kibővített, előtag, regex) típusú mindig közreműködés egy állandó pontszám a teljes dokumentum pontszámot. Ez lehetővé teszi, hogy a foglalandó eredményeit, a lekérdezés bővítése keresztül található egyezés, de a rangsorolás befolyásolása nélkül. 

Egy példa azt mutatja be, hogy miért ez számít. Nem jelenik meg a helyettesítő karakteres kereséssel előtag keresések esetén, beleértve a definíció szerint egyértelmű, mivel a bemeneti részleges karakterlánc-lehetséges egyezések a különböző használati nagyon nagy számú (az egyezés található a "bemutatók", "tourettes" bemenetének "bemutató *", érdemes, és a " tourmaline"). Ezekkel az eredményekkel természetéből, nincs lehetőség ésszerűen célszámítógéppel a mely feltételek, mint a többi értékét. Ezért azt figyelmen kívül kifejezés gyakoriságok amikor eredmények pontozási lekérdezések típusok helyettesítő, az előtag és a reguláris kifejezés. Részleges és teljes körű feltételeit tartalmazó több keresési kérelem, az eredményeket a részleges bemeneti elkerülése érdekében eltérés potenciálisan váratlan egyezések felé állandó pontszámot részét képezik.

### <a name="score-tuning"></a>Pontszám hangolása

Az Azure Search szolgáltatásban a relevancia alapján végzett pontszámok finomhangolása két módja van:

1. **Pontozási profilok** előléptetni a dokumentumok az eredményeket, szabályai alapján rangsorolt listáját. Ebben a példában a cím mező több megfelelő, a Leírás mezőben az egyező dokumentumok, mint az egyező dokumentumok volt javasolt. Ezenkívül ha az index minden egyes Szálloda ár kitölteni, hogy előmozdíthatja a dokumentumok az alacsonyabb díj ellenében. Ismerje meg, hogyan további [pontozási profilok hozzáadása a search-index.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Kiemelési távú** (csak a teljes Lucene lekérdezési szintaxis elérhető) biztosít egy gyorsított operátor `^` , amelyek alkalmazhatók a lekérdezés fa bármelyik részét. Ebben a példában az előtag a helyett *air-condition*\*, egy sikerült keresési a pontos kifejezés *air-condition* vagy az előtag, de a pontos kifejezés a megfelelő dokumentumok előrébb kifejezés lekérdezéshez boost alkalmazásával: * a feltétel vezeték nélkül ^ 2 || Air-condition **. Tudjon meg többet [távú kiemelési](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>A pontozás elosztott indexbe

Az Azure Search szolgáltatásban az összes index automatikusan felosztása több szegmens, így gyors terjesztése az index több csomópont között szolgáltatásskálázás során fel- vagy leskálázás. Egy keresési kérelmet ad ki, amikor azt minden egyes szegmens egymástól függetlenül van állították. Minden egyes szegmens eredményeinek ezután egyesíti, és (Ha nincs más rendezés van megadva), a pontszám alapján rendezve. Fontos tudni, hogy a pontozó függvény súlyok kifejezés gyakorisága ellen a szegmensben lévő összes dokumentumot más néven inverz dokumentum gyakorisága lekérdezése nem minden szegmensben!

Ez azt jelenti, hogy egy relevanciapontszám *sikerült* azonos dokumentumok eltérőek lehetnek, ha azokat a különböző szegmensben találhatók. Ezek a különbségek Szerencsére általában az indexelt dokumentumok számát növekedésével további még kifejezés terjesztési miatt eltűnnek. Már nem azt feltételezik, hogy melyik szegmensben lévő bármely adott dokumentum kerül. Azonban ha egy dokumentum kulcs nem változik, akkor mindig rendeli hozzá ugyanabban a szegmensben.

Általában a dokumentum pontszám nem áll a dokumentumok rendezéshez, ha fontos a sorrend stabilitását ajánlott attribútum. Például adja meg egy azonos pontszám a két dokumentumot, nincs garancia arra, melyiket jelenik meg először az ugyanabból a lekérdezés a későbbi futtatások. Az eredménykészletet a dokumentum pontszám csak adjon általános megismerje a dokumentum relevancia alapján végzett egyéb dokumentumokat viszonyítva.

## <a name="conclusion"></a>Összegzés

Sikeresen megtörtént az internetes keresés keresőmotor szerint teljes szöveges kereséshez elvárások következik be személyes adatok. Szinte bármilyen keresési funkciót most várhatóan a motor megérteni a leképezést, akkor is, ha feltételek hibás vagy hiányos. Előfordulhat, hogy még várhatóan egyenértékű kifejezéseket és szinonimákat valójában soha nem meghatározott közelében alapján.

Technikai szempontból teljes szöveges keresés nagy mértékben összetett, fejlett nyelvi elemzési és feldolgozási, hogy nyerhet ki, bontsa ki, és átalakíthatja a lekérdezési kifejezések, hogy a megfelelő eredmény rendszeres megközelítése. Adja meg a rejlő hagyhatják, sok olyan tényezőt, amelyek hatással lehetnek a lekérdezés eredményének vannak. Emiatt az időt a teljes szöveges keresés mechanics megértéséhez előnyökkel képzés résztvevői hasznos képességekkel munka – nem várt eredmények tett kísérlet során.  

Ebben a cikkben bemutattuk az Azure Search kontextusában a teljes szöveges keresés. Reméljük, hogy biztosítja a lehetséges okokért és megoldásokért címzéshez lekérdezés kapcsolatos gyakori problémák felismerése elegendő tudással. 

## <a name="next-steps"></a>További lépések

+ A mintakód-index létrehozása, próbálja ki a különböző lekérdezéseket, és tekintse át az eredményeket. Útmutatásért lásd: [hozhat létre, és a portálon egy index lekérdezése](search-get-started-portal.md#query-index).

+ Próbálja ki a további lekérdezési szintaxis a [dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) példa szakaszban vagy [egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) a keresési ablakban a portálon.

+ Felülvizsgálat [pontozási profilok](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) Ha azt szeretné, ennek a területnek a keresési alkalmazásban finomhangolása.

+ Ismerje meg, hogyan alkalmazhatja a [nyelvspecifikus lexikai elemzőket](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Egyéni elemzők konfigurálása](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) minimális feldolgozás vagy adott mezők speciális terhelése.

+ [Hasonlítsa össze a standard és az angol nyelvű elemzők](https://alice.unearth.ai/)) egymás mellett a bemutató webhelyről. 

## <a name="see-also"></a>Lásd még

[REST API-val dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Teljes Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[A keresési eredmények kezelése](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
