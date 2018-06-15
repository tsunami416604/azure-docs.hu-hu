---
title: Teljes szöveges keresés (Lucene) motor architektúra az Azure Search |} Microsoft Docs
description: Teljes szöveges keresés, mint a kapcsolódó Azure Search Lucene lekérdezés feldolgozása és dokumentum beolvasása fogalmakat ismerteti.
manager: jlembicz
author: yahnoosh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: jlembicz
ms.openlocfilehash: 4382c3001f6b0a9227407beccb483347bccb387c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195012"
---
# <a name="how-full-text-search-works-in-azure-search"></a>Hogyan teljes szöveges keresés az Azure Search működik

Ez a cikk a fejlesztők számára, akik bemutatják, hogyan Lucene teljes szöveges keresés működik-e az Azure Search van. A szöveges lekérdezések Azure Search problémamentesen fog továbbítani a kívánt eredmény elérése érdekében a legtöbb esetben azonban úgy tűnik, "off" valamilyen módon eredményt időnként előfordulhat, hogy. Ezekben a helyzetekben, hogy a háttérben Lucene lekérdezés-végrehajtás négy szakaszaiban (elemzési, lexikális elemzés lekérdezéséhez dokumentálja a megfelelő pontozási) segítenek azonosítani azokat a lekérdezés-paraméterek vagy az index konfigurációja, hogy a rendszer a kívánt eredmény adott módosításait. 

> [!Note] 
> Az Azure Search Lucene használ a teljes szöveges keresés, de Lucene integrációs nem teljes. A Microsoft szelektív módon teszi közzé, és ahhoz, hogy az Azure Search fontos forgatókönyvek Lucene bővíthetők. 

## <a name="architecture-overview-and-diagram"></a>Architektúra áttekintése és diagramja

Teljes szöveges keresési lekérdezés feldolgozása kezdődik-e a lekérdezés szövegének keresőkifejezéseket kibontásához elemzése. A keresőmotor index használja a megfelelő adatokkal dokumentumok beolvasása. Egyéni lekérdezési kifejezések néha lebontva, és az új űrlapok szélesebb körű nettó konvertálni keresztül mi tekinthető, amely lehetséges elkészített. Egy eredménykészlet majd minden egyes megfelelő dokumentum rendelt relevanciájának pontszámot szerint van rendezve. Azok a rangsorolt lista tetején a rendszer visszairányítja a hívó alkalmazás.

Lekérdezés-végrehajtás rendelkezik állapítani, négy fázisból áll: 

1. A lekérdezés elemzése 
2. Lexikális elemzés 
3. A dokumentum beolvasása 
4. Pontozó 

Az alábbi ábra szemlélteti a search kérelmek feldolgozásához használt összetevőket. 

 ![Az Azure Search Lucene lekérdezés-architektúra ábrája][1]


| A legfontosabb összetevők | Funkcionális leírása | 
|----------------|------------------------|
|**Lekérdezés elemzők** | A lekérdezési operátorok lekérdezési kifejezések külön, és a keresőmotor küldendő lekérdezési struktúra (a lekérdezés-fa) létrehozása. |
|**Lekérdezések** | Lexikális elemzést lekérdezés igényei szerint. Ez a folyamat magába foglaló átalakítása, eltávolítása vagy lekérdezési kifejezések bővíteni. |
|**Index** | Egy hatékony tárolására és kinyert kereshető feltételek szervezésére szolgáló adatstruktúra indexelt dokumentumok. |
|**Keresőmotor** | Olvassa be, és a fordított index tartalma alapján egyező dokumentumok pontszámaihoz. |

## <a name="anatomy-of-a-search-request"></a>Keresési kérelem szerkezete

A keresési kérelme, mert egy teljes megadását mi vissza kell adni egy eredményhalmazban szerepel. A legegyszerűbb esetben bármilyen feltételt nem tartalmazó egy üres lekérdezést esetében. A modell példa paramétereket tartalmaz, több lekérdezés feltételeket, lehet, hogy bizonyos mezők, valószínűleg egy kifejezést, és a rendezés szabályok hatókörébe.  

A következő példa az Azure Search használatával el tudja küldeni keresési kérelem a [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

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

A kérelem a keresőmotor a következőket hajtja végre:

1. Az ár esetén legalább $60 és kisebb, mint 300 $ dokumentumok szűrők.
2. A lekérdezés végrehajtása. Ebben a példában a keresési lekérdezés áll kifejezések és a feltételek: `"Spacious, air-condition* +\"Ocean view\""` (felhasználók általában nem adhatja meg absztrakt azonban, beleértve a következő példában lehetővé teszi azt ismertetik, hogyan elemzőkkel kezelnie). Ehhez a lekérdezéshez a keresőmotor megvizsgálja a leírást, és címmezők megadott `searchFields` "Óceáni nézet", tartalmazó dokumentumok és továbbá "ahhoz," kifejezést vagy a feltételeket, amelyek a előtaggal kezdődik "air-condition". A `searchMode` paraméterrel felel meg a kifejezés (alapértelmezett), vagy azokat, olyan esetekben, ahol a kifejezés nincs explicit módon szükséges (`+`).
3. Rendelések a létrejövő által egy adott földrajzi hely közelében a szállodák készletét, és ezután visszaérkezik a hívó alkalmazás. 

Ez a cikk a legtöbb tárgya feldolgozását a *keresési lekérdezés*: `"Spacious, air-condition* +\"Ocean view\""`. Szűrési és rendezési kívül esnek a hatókörön. További információkért lásd: a [keresési API-referenciadokumentáció](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>1. fázis: Lekérdezés elemzése 

Amint azt a lekérdezési karakterláncban a kérelem első sorának: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

A lekérdezéselemzőben elválasztja az operátorok (például `*` és `+` a példában) keresési feltételeket, és a keresési lekérdezést deconstructs *segédlekérdezések* támogatott típusú: 

+ *kifejezés lekérdezés* önálló feltételek (például ahhoz)
+ *kifejezés lekérdezés* idézőjelek közé zárt feltételek (például óceáni megtekintése)
+ *előtag-lekérdezés* követ egy előtag operátor feltételek `*` (például air-condition)

A támogatott lekérdezéstípusok teljes listáját lásd: [Lucene lekérdezés szintaxisa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Operátor segédlekérdezésben társított határozza meg, hogy a lekérdezés "" vagy "kell lennie" ahhoz, hogy a dokumentum akkor, ha egyezés teljesül. Például `+"Ocean view"` "kell" van miatt a `+` operátor. 

Lekérdezéselemzőben átszervezése a segédlekérdezések egy *lekérdezés fa* (egyik belső struktúra, a lekérdezés képviselő) továbbítja a keresőmotort. A lekérdezés elemzése első szakaszában a lekérdezés fa néz ki.  

 ![Logikai érték searchmode minden lekérdezése][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Támogatott elemzők: egyszerű és a teljes Lucene 

 Az Azure Search mutatja meg két különböző lekérdezési nyelv `simple` (alapértelmezett) és `full`. Úgy, hogy a `queryType` paraméter, a search kérelemmel, közli a lekérdezéselemzőben mely lekérdezési nyelv úgy dönt, így az tudni fogja, a kezelők és értelmezése. A [egyszerű lekérdezési nyelv](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) intuitív és robusztus, akkor gyakran megfelelő szerint felhasználói adatbevitelt értelmezése-ügyféloldali feldolgozás nélkül van. Támogatja a lekérdezési operátorok ismerős webes keresőmotorokból. A [teljes Lucene lekérdezési nyelv](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), amely úgy, hogy elérhetővé `queryType=full`, az alapértelmezett egyszerű lekérdezési nyelv bővíti a további operátorok és a helyettesítő karakter, az intelligens egyeztetésű például lekérdezéstípusok, a reguláris kifejezéssel és a mező hatókörű lekérdezések támogatásának hozzáadásával. Például egy egyszerű lekérdezés szintaxisát küldött reguláris kifejezés értelmezését egy lekérdezési karakterlánc és a kifejezés nem. A példa egy kérelem a cikkben a teljes Lucene lekérdezés nyelvét használja.

### <a name="impact-of-searchmode-on-the-parser"></a>Az elemző a searchMode hatása 

Keresési kérelmet egy másik paraméter, amely befolyásolja a elemzése a `searchMode` paraméter. Azt szabályozza, hogy az alapértelmezett operátor logikai lekérdezések: vagy az összes bármely (alapértelmezett).  

Ha `searchMode=any`, amely az alapértelmezett, a hely elválasztó között ahhoz, és a air-condition vagy (`||`), így a minta lekérdezésszöveg egyenértékű: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Explicit operátorok, például a `+` a `+"Ocean view"`, amelyek egyértelműen logikai lekérdezés kialakításában (kifejezés *kell* felel meg). Kevésbé nyilvánvaló van a fennmaradó feltételek értelmezése: ahhoz, és air-condition. Érdemes a keresőmotor találatokat óceáni nézeten *és* ahhoz, *és* air-condition? Vagy kell keresés óceáni nézet plus *egy* fennmaradó feltételét? 

Alapértelmezés szerint (`searchMode=any`), a keresőmotor azt feltételezi, hogy a szélesebb körű értelmezése. Vagy mező *kell* egyeztetni, amely tükrözi "vagy" szemantikáját. A kezdeti lekérdezést fa korábban bemutatott, a két, "kell" műveletek, az alapértelmezett.  

Tegyük fel, hogy most hivatott `searchMode=all`. Ebben az esetben a hely a "és" művelet kerül értelmezésre. A fennmaradó feltételek mindegyikének is jelen kell lennie ahhoz, hogy egyezés a dokumentumban. Az eredményül kapott mintalekérdezés értelmezését az alábbiak szerint: 

~~~~
+Spacious,+air-condition*+"Ocean view"  
~~~~

Ehhez a lekérdezéshez módosított lekérdezés fát következőképpen nézne ki, ahol a megfelelő dokumentumokra az összes három segédlekérdezések metszetét: 

 ![Az összes logikai lekérdezés searchmode][3]

> [!Note] 
> Kiválasztása `searchMode=any` keresztül `searchMode=all` legjobb döntést jutott reprezentatív lekérdezések futtatásával. Felhasználók, akik valószínűleg tartalmaz az operátorok (Ha a Keresés a dokumentum tárolja közös) található eredmények intuitívabb Ha `searchMode=all` arról tájékoztatja a logikai lekérdezési szerkezeteket. További információk az közötti együttműködés `searchMode` és operátorok, lásd: [egyszerű lekérdezés szintaxisát](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>2. fázis: Lexikális elemzés 

Lexikális elemzőkkel folyamat *lekérdezések távon* és *lekérdezések kifejezés* a lekérdezés fa felépítése után. Egy elemző eszköz az az elemző által megadott szöveges adatokat fogad, dolgozza fel a szöveg, és majd küld vissza a tokenekre feltételeket a lekérdezés fa szóló. 

Az általános űrlap lexikális elemzési *nyelvi elemzés* átalakítások lekérdezése alapján a szabályok egy adott nyelvre vonatkozó feltételeket, amelyek: 

* A lekérdezés kifejezés csökkentése a legfelső szintű űrlap szó 
* Nélkülözhető szavak eltávolítása (szavak, például az "a" vagy "és" angol nyelven) 
* Egy összetett a word ossza összetevői 
* Alsó körül, egy nagybetű word 

Ezeket a műveleteket az egyes törlése a felhasználó által megadott szöveg bemenetet és a feltételeket az indexben tárolt közötti különbséget. Az ilyen műveletek szöveg feldolgozási túlmutató, és magát a nyelv alapos ismerete szükséges. Nyelvi tájékoztatási réteg hozzáadásához az Azure Search támogatja listája túl hosszú [nyelvi elemzőkkel](https://docs.microsoft.com/rest/api/searchservice/language-support) Lucene és a Microsoft.

> [!Note]
> Elemzés követelmények között lehet minimális fejlesztett ki attól függően, a forgatókönyv számára. Az előre definiált elemzőkkel kiválasztásával közül, vagy hozzon létre egy saját lexikális elemzés összetettsége szabályozhatja [egyéni analyzer](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Lekérdezések hatóköre kereshető mezőt, és egy mező definíciójának részeként vannak megadva. Ez lehetővé teszi, hogy eltérő lexikális elemzés, mező alapon. Nincs megadva, a *szabványos* Lucene analyzer szolgál.

A példa kedvéért elemzés, mielőtt a kezdeti lekérdezést fa van kifejezés "Spacious," nagybetűs "S" és a lekérdezési kifejezésre (vesszővel nem tekinthető a lekérdezési nyelv operátor) részeként értelmezi lekérdezéselemzőben vesszővel válassza el.  

Az alapértelmezett analyzer dolgozza fel a kifejezés, emellett kisbetűs "óceáni nézet" és "ahhoz,", és a vessző karakter eltávolítása. A módosított lekérdezés fa a következőképpen fog kinézni: 

 ![Logikai lekérdezés elemzett adatokkal][4]

### <a name="testing-analyzer-behaviors"></a>Tesztelési analyzer viselkedések 

Egy elemző viselkedését használatával kell tesztelni a [elemzése API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Adja meg a analyzer adott kifejezések létrehoz megjelenítéséhez elemezni kívánt szöveg. Például hogy módját a szabványos analyzer szeretné feldolgozni a szöveges "air-condition", kiadhatja a következő kérelmet:

~~~~
{ 
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

A szabványos analyzer bontja a következő két jogkivonatok ellátása megjegyzésekkel őket a tulajdonságai, például a kezdő és záró eltolások (találati konzolban használt), valamint pozíciójuk (használt kifejezés a megfelelő) a bemeneti szöveg:

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

### <a name="exceptions-to-lexical-analysis"></a>Kivételeket lexikális elemzés 

Lexikális elemző csak a teljes feltételek – kifejezés lekérdezés vagy egy kifejezés lekérdezés igénylő lekérdezéstípusok vonatkozik. Lekérdezéstípusok – előtag lekérdezés, helyettesítő karaktereknek, regex lekérdezés – hiányos adatokkal, vagy egy intelligens lekérdezés nem vonatkozik. Azok lekérdezése típusok, beleértve a kifejezés előtag lekérdezés *air-condition\**  a fenti példában kerülnek közvetlenül a lekérdezés fa, az elemzési fázis kihagyásával. Az adott típusú lekérdezési kifejezések végre csak átalakítás lowercasing van.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>3. fázis: A dokumentum beolvasása 

Az index feltételeit egyeztetésével dokumentumok keresése dokumentum beolvasása hivatkozik. Ez a szakasz egy példán keresztül legjobb értendő. Kezdjük a szállodák index a következő egyszerű séma rendelkezik: 

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

További azt feltételezik, hogy ezt az indexet tartalmaz a következő négy dokumentumokat: 

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

**Hogyan feltételek indexelt**

Szeretné megtudni, lekérés, áttekinteni tudni, hogy néhány alapvető indexelése. Tárolási egységének értéke egy fordított index, egy az összes kereshető mezőt. Fordított index belül az összes dokumentumot az összes kifejezést a rendezett listáját. Minden kifejezéshez van leképezve, amelyben előfordul, az alábbi példa szerint egyértelmű dokumentumok listáját.

A feltételek fordított index létrehozásához a keresőmotor lexikális analysis a dokumentumok tartalmát, mi történik, a lekérdezés feldolgozása során hasonló keresztül hajtja végre:

1. *Szöveg bemenetek* átadni egy analyzer alsó cased absztrakt, és így tovább, a analyzer konfigurációjától függően az újrafoglalások. 
2. *Jogkivonatok* szöveg elemzési kimenete.
3. *Feltételek* hozzáadódik az indexhez.

Általános, de nem szükséges, az azonos elemzőkkel használandó keresési és indexelési műveletek, hogy lekérdezési kifejezések mint belül az index feltételeket is.

> [!Note]
> Az Azure Search lehetővé teszi a különböző elemzőkkel indexeléshez adja meg, és keressen további keresztül `indexAnalyzer` és `searchAnalyzer` paraméterek mezőben. Ha nincs megadva, a analyzer beállított a `analyzer` a tulajdonságot használja az indexelés és a keresést.  

**Példa dokumentumok fordított indexe**

A jelen példában visszatér a **cím** mezőt, a fordított index néz ki:

| Időtartam | Dokumentumok listájához |
|------|---------------|
| atman | 1 |
| kínál | 2 |
| Szálloda | 1, 3 |
| óceáni | 4  |
| PlayA | 3 |
| végső esetben | 3 |
| Retreat | 4 |

A cím mezőben csak *Szálloda* mutatja két dokumentumot: 1, 3.

Az a **leírás** mezőben az index a következőképpen történik:

| Időtartam | Dokumentumok listájához |
|------|---------------|
| vezeték nélkül | 3
| és | 4
| kínál | 1
| annak | 3
| Feladatkezelő | 3
| távolság | 1
| sziget | 2
| kauaʻi | 2
| található | 2
| északi régiója | 2
| óceáni | 1, 2, 3
| / | 2
| bekapcsolva |2
| Csendes | 4
| helyiségekben  | 1, 3
| secluded | 4
| parti | 2
| Ahhoz | 1
| a | 1, 2
| erre: | 1
| megtekintés | 1, 2, 3
| érdekében | 1
| együtt | 3


**Egyező lekérdezési kifejezések indexelt feltételek ellen**

A fenti fordított indexek, most térjen vissza a mintalekérdezés, és hogyan egyező dokumentumok tekintse meg a példalekérdezés talált. Visszahívása, hogy az utolsó lekérdezési fa néz ki: 

 ![Logikai lekérdezés elemzett adatokkal][4]

Lekérdezés-végrehajtás során egyes lekérdezések végrehajtása a kereshető mezők elleni egymástól függetlenül. 

+ A TermQuery, "ahhoz,", megegyezik a dokumentum 1 (szálloda Atman). 

+ A PrefixQuery "air-condition *", nem felel meg a dokumentumokat. 

  Ez az, hogy a fejlesztők néha confuses. Bár klimatizált kifejezés szerepel a dokumentum, azt oszlik két feltételek által az alapértelmezett elemző eszközt. Visszahívása előtag a lekérdezések részleges feltételeket tartalmaz, amelyek nem került sor. Ezért a kifejezéseket "air-condition" előtag a fordított index keresni, és nem található.

+ A PhraseQuery "óceáni nézet", a feltételek "óceáni" és "view" keresése, és ellenőrzi a közelében, az eredeti dokumentumhoz kifejezések. Dokumentumok, 1, 2, 3 felel meg a lekérdezés a Leírás mezőben. Figyelje meg a dokumentum 4 a kifejezés óceáni a címben rendelkezik, de nem tekinthető egyezés, azt keres egyes szavak helyett a "óceáni nézet" kifejezés helyett szerepel. 

> [!Note]
> Keresési lekérdezés végrehajtása egymástól függetlenül szemben az összes kereshető mezőt az Azure Search-index kivéve korlátozhatja a mezőket, állítsa a `searchFields` paraméter, a keresési kérelem (példa) ismertetett módon. A kijelölt mezőket egyik megfelelő dokumentumok adja vissza. 

A teljes az adott lekérdezéshez megfelelő dokumentumok 1, 2, 3. 

## <a name="stage-4-scoring"></a>4. szakasz: pontozási  

Minden egyes dokumentum egy keresési eredménykészletben hozzá van rendelve egy relevanciájának pontszám. A relevanciájának pontszám feladata a nagyobb a dimenziószáma ezeket a dokumentumokat, amely a legjobb felhasználói kérdések megválaszolása, ahogy a keresési lekérdezés. A pontszám egyező kifejezések statisztikai tulajdonságok alapján számítja ki. A pontozási képlet középpontjában van [TF/IDF (kifejezés gyakoriság-inverz dokumentum gyakoriság)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). Ritka és gyakori feltételeket tartalmazó lekérdezésekben TF/IDF elősegíti a ritka kifejezés, amely a. Például egy elméleti index összes Wikipedia cikkekkel, a dokumentumok, amely egyezik a lekérdezés *elnökét*, a megfelelő dokumentumok *elnök* relevánsnak több mint a megfelelő dokumentumok *a*.


### <a name="scoring-example"></a>A pontozási – példa

A három dokumentumok a példalekérdezés egyező visszahívása:
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

1 felelt meg a lekérdezés legjobb mert mindkét kifejezés *ahhoz,* és a szükséges kódot *óceáni nézet* fordulhat elő, a Leírás mezőben. A következő két dokumentumok felel meg a kifejezés csak *óceáni nézet*. Előfordulhat, hogy meglepő, hogy a dokumentum 2 és 3 relevanciájának pontszám eltér, annak ellenére, hogy azok a lekérdezés egyező azonos módon. Ennek az oka a pontozási képlet csak TF/IDF-nál több részből áll. Ebben az esetben dokumentum 3 van hozzárendelve valamivel nagyobb pontszám, mert annak leírását rövidebb. További tudnivalók [Lucene tartozó gyakorlati pontozási képlet](https://lucene.apache.org/core/4_0_0/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) megértése, hogyan mező maximális hossza és más tényezők befolyásolhatják a a relevanciájának pontszámot.

Néhány lekérdezés (helyettesítő, előtag, regex) típusú mindig hozzájárulhatnak a a teljes dokumentum pontszám állandó pontszámot. Ez lehetővé teszi, hogy a találat keresztül lekérdezés bővítése foglalandó az eredményeket, de az nem befolyásolja a rangsorolási. 

Egy példa azt mutatja be, ezért ez számít. Helyettesítő karakteres kereséssel előtag keresések, beleértve egyértelműek definition, mert a bemeneti érték egy részleges karakterlánc lehetséges megegyezik (vel, fontolja meg egy bemeneti "bemutató *", "bemutatók", "tourettes" és "tourmaline" találat) különböző feltételeket nagyon nagy számú. Ezekkel az eredményekkel jellegéből, nincs mód ésszerűen következtetési forrásaként, mely feltételek értékesebb, mint a többire. Ezért azt figyelmen kívül hagyása kifejezés gyakoriságot amikor pontozási eredményei típusok helyettesítő, előtag és regex lekérdezésekben. A többrészes keresési kérelmet, amely részleges és teljes feltételeket tartalmaz, a részleges bemeneti eredmények elkerülése érdekében a potenciálisan váratlan egyezések felé időeltérés állandó pontszám feldolgozásába beépített.

### <a name="score-tuning"></a>Pontszám hangolása

Az Azure Search relevanciájának pontszámok hangolására két módja van:

1. **A pontozási profil** lépteti elő a dokumentumokat a meghatározott szabályok alapján eredmények rangsorolt listáját. A fenti példában a dokumentumok, a Leírás mezőben egyező-nál több megfelelő cím mezőben egyező dokumentumok volt javasolt. Emellett az indexnek minden Szálloda ár mező volna, ha azt sikerült előléptetni alsó ár dokumentumok. További tudnivalók a [pontozási profil hozzáadása egy keresési indexszel.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Távon kiemelési** (csak a teljes Lucene lekérdezés szintaxisát elérhető) biztosít a kiemelési operátor `^` , amelyek alkalmazhatók a lekérdezés fa bármely részét. A fenti példában az előtag alapján keres helyett *air-condition*\*, egy megkeresése, amelyek vagy a pontos kifejezés *air-condition* vagy az előtag, de a pontos kifejezés a megfelelő dokumentumok előrébb a kifejezés lekérdezés program alkalmazásával: * vezeték nélkül-feltétel ^ 2. Air-condition **. További információ [távon kiemelési](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Egy elosztott index pontozási

Az indexek, az Azure Search automatikusan felosztása több szilánkok gyors terjesztése során szolgáltatás méretezési az index több csomópont között, így akár vagy csökkentheti. Keresési kérelem elküldésekor megjelenítés elleni minden shard egymástól függetlenül. Minden shard eredményeinek majd egyesített és (Ha nincs más rendezés van meghatározva) pontszám szerint rendezve. Fontos tudni, hogy a pontozó függvény súlyok összes szilánkok között nem kifejezés gyakoriság szemben a más néven inverz dokumentum gyakoriságot adja meg a shard minden dokumentumot lekérdezése!

Ez azt jelenti, hogy egy relevanciájának pontszám *sikerült* térhet azonos dokumentumokhoz, ha ezek különböző szilánkok legyen elhelyezve. Szerencsére ezek az eltérések általában az indexelt dokumentumok száma miatt több még kifejezés terjesztési növekedésével eltűnik. Nincs lehetőség számára, hogy mely shard az adott dokumentumtípus kerülnek. Azonban ha egy dokumentum kulcs nem változik, az mindig rendeli hozzá ugyanazt a shard.

A dokumentum pontszám általában nem a legjobb attribútum dokumentumok rendezéshez, ha fontos a sorrend stabilitását. Például egy azonos pontszám két dokumentumok megadott, nincs garancia melyik jelenik meg először a következő frissítési kísérletei során ugyanabban a lekérdezésben. A dokumentum pontszám csak adjon más dokumentumok viszonyítva dokumentum fontos általános értelemben eredmények készletében.

## <a name="conclusion"></a>Összegzés

Internetes keresők sikeres titkos adatok a teljes szöveges keresés elvárásainak idézett elő. Szinte bármilyen keresési élményt biztosít terveink most már a megérteni a leképezést, akkor is, ha feltételek hibás vagy hiányos a motor. Akkor is előfordulhat, hogy várhatóan közelében kifejezések vagy soha nem ténylegesen megadott szinonimák alapján.

Technikai szempontból teljes szöveges keresés nagyon összetett, nyelvi kifinomult elemzést és, hogy az eszközöket, bontsa ki és lekérdezési kifejezések képes biztosítani a megfelelő eredmény átalakítási feldolgozási rendszeres megközelítése. Adott a rejlő összetett szolgáltatásokkal, nagy mennyiségű tényezőket, amelyek hatással lehetnek a lekérdezés eredményeit vannak. Emiatt beállítás esetén a teljes szöveges keresés megismerése időt befektetés előnyökkel anyagi közben váratlan eredményekhez keresztül működnek.  

Ez a cikk felfedezte az Azure Search kontextusában a teljes szöveges keresés. Reméljük, biztosít elegendő háttér ismeri fel a lehetséges okokért és megoldásokért lekérdezés kapcsolatos gyakori problémák címzéshez. 

## <a name="next-steps"></a>További lépések

+ Az minta index létrehozása, próbálja ki a különböző lekérdezéseket, és tekintse át az eredményeket. Útmutatásért lásd: [hozza létre, és a portál egy index lekérdezése](search-get-started-portal.md#query-index).

+ Próbálja meg a további lekérdezés szintaxisa a [dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) példa szakasz vagy [egyszerű lekérdezés szintaxisát](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) a keresési ablak a portálon.

+ Felülvizsgálati [profilok pontozási](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) Ha azt szeretné, hogy az alkalmazás rangsorolási finomhangolásához.

+ Megtudhatja, hogyan alkalmazza [nyelvspecifikus lexikális elemzőkkel](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Konfigurálja az egyéni lekérdezések](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) minimális feldolgozási vagy adott mezők speciális terhelése.

+ [Hasonlítsa össze a szabványos és az angol nyelvű elemzőkkel](http://alice.unearth.ai/)) egymás mellett a bemutató webhelyen. 

## <a name="see-also"></a>Lásd még

[REST API-t dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Teljes Lucene lekérdezés szintaxisa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[A keresési eredmények kezelése](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
