---
title: Egyéni elemzők – Azure Search hozzáadása
description: Szöveg tokenizers és az Azure Search teljes szöveges keresési lekérdezésekben használt karakter szűrők módosítása.
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 74c47bcb4097f4db896a778bc4855c0815b967bf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436468"
---
# <a name="add-custom-analyzers-to-an-azure-search-index"></a>Egyéni elemzők az Azure Search-index hozzáadása

A *egyéni elemző* egy adott típusú [szöveg analyzer](search-analyzers.md) , hogy egy felhasználó által definiált kombinációja meglévő jogkivonatokat létrehozó és a választható szűrők áll. Új módokon tokenizers és a szűrők kombinálásával testre szabhatja az adott eredmények elérése érdekében a keresőmotor szövegfeldolgozást. Például létrehozhatja az egyéni elemző egy *char szűrő* HTML-kód eltávolítása előtt a bemeneti szöveg tokenekre vannak.

 Megadhat több egyéni elemzőket szűrők kombinációit eltérő, de minden mező csak használhat egy elemző indexelés, elemzési és a egy a search-elemzést. Egy ügyfél analyzer néz ki olyan bemutatásáért, lásd: [egyéni elemző példa](search-analyzers.md#Example1).

## <a name="overview"></a>Áttekintés

 Szerepe a [teljes szöveges keresés motor](search-lucene-query-architecture.md), egyszerűen fogalmazva a rendszer tárolás dokumentumok oly módon, amely lehetővé teszi a hatékony lekérdezésben és lekéréséhez. Magas szinten megkönnyíti fontos szavak kinyerését dokumentumok, helyezné azokat egy index és az index használatával, amelyek megfelelnek egy adott lekérdezésre vonatkozó szavakat dokumentumok keresése. Az a folyamat a szavak beolvasása a dokumentumok és a keresési lekérdezések *lexikai elemzés*. Hajtsa végre a lexikális elemzés összetevőket az úgynevezett *elemzők*.

 Az Azure Search szolgáltatásban közül választhat az előre meghatározott nyelvtől elemzők készletét a [elemzők](#AnalyzerTable) tábla vagy a felsorolt nyelvspecifikus elemzők [nyelvi elemzők &#40;Azure Search szolgáltatás REST API&#41;](index-add-language-analyzers.md). Lehetősége is van a saját egyéni elemzőket meghatározásához.  

 Egyéni elemző lehetővé teszi, hogy a szöveg átalakítása indexelhető és kereshető jogkivonatok folyamata felett. Egy egyetlen előre meghatározott tokenizer, egy vagy több jogkivonat szűrők és a egy vagy több karakter szűrő egy felhasználó által definiált konfigurációs. A jogkivonatokat létrehozó felelős szöveg ossza tokeneket, és a token szűrők a jogkivonatokat létrehozó által kibocsátott jogkivonatok módosítására. CHAR alkalmazza a rendszer a bemeneti szöveg előkészítése, mielőtt dolgoz fel a jogkivonatokat létrehozó. Char szűrő lecserélheti például bizonyos karakterek és szimbólumok.

 Egyéni elemzők által engedélyezett népszerű forgatókönyvek a következők:  

-   Fonetikus keresés. Ahhoz, hogy a keresés alapján hogyan szó úgy érzi, nem hogyan van beírva fonetikus szűrőt ad meg.  

-   Tiltsa le a lexikális elemzés. A kulcsszó analyzer használatával, amely nem lesz elemezve kereshető mezőket hozhat létre.  

-   Gyors előtag/utótag-keresési. Adja hozzá az Edge-N-Gram típusú jogkivonat szűrő index előtagok ahhoz, hogy gyors előtag megfelelő szó. Való összevonásához utótag az egyező fordított token szűrő.  

-   A jogkivonatok egyéni. Például használja a szóköz tokenizer mondatokat felosztása elválasztó karakterek használatával egy elválasztó jogkivonatok  

-   ASCII-megfeleltetés. Adja hozzá a Standard ASCII modellrészeket a szűrőt például lumen vagy ê mellékjeleket optimalizálására a keresési feltételeket.  

 Ezen a lapon támogatott elemzők, tokenizers, tokent, és char szűrőket listáját tartalmazza. Az index definícióját egy példa a használatra a módosítások leírását is megtalálhatja. További ismereteket a az alapul szolgáló technológiát kihasználhatók a az Azure Search végrehajtására, lásd: [elemzés összegzése csomag (Lucene)](https://lucene.apache.org/core/4_10_0/core/org/apache/lucene/codecs/lucene410/package-summary.html). Az elemző konfigurációjának példák: [elemzők hozzáadása az Azure Search](search-analyzers.md#examples).

## <a name="validation-rules"></a>Érvényesítési szabályok  
 Elemzők, tokenizers, jogkivonat-szűrők és char szűrők nevét kell egyedinek lennie, és nem lehet ugyanaz, mint bármelyik, az előre meghatározott elemzők, tokenizers, jogkivonat szűrők vagy char szűrőket. Tekintse meg a [tulajdonsághivatkozás](#PropertyReference) nevek már használatban van.

## <a name="create-custom-analyzers"></a>Hozzon létre egyéni elemzők
 Index létrehozáskor egyéni elemzőket adhatja meg. Ebben a szakaszban leírt egyéni elemző megadása esetén. Akkor is is Ismerkedjen meg a szintaxist a minta-definíciók áttekintésével [elemzők hozzáadása az Azure Search](search-analyzers.md#examples).  

 Egy elemző-definíció nevét, a típus, egy vagy több karakter szűrő, egy jogkivonatokat létrehozó legfeljebb és a jogkivonatok utáni feldolgozási jogkivonat egy vagy több szűrő tartalmazza. CHAR kiemelik előtt a jogkivonatok érvényesek. Token és char szűrőket lépnek balról jobbra.

 A `tokenizer_name` neve, a jogkivonatokat létrehozó `token_filter_name_1` és `token_filter_name_2` token szűrők nevei és `char_filter_name_1` és `char_filter_name_2` char nevei szűrők vannak (lásd: a [Tokenizers](#Tokenizers), [ Jogkivonat-szűrők](#TokenFilters) és Char szűri az érvényes értékek a táblák).

Az elemző definíciója a nagyobb index része. Lásd: [Index API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) az index a többi kapcsolatos információkat.

```  
"analyzers":(optional)[  
   {  
      "name":"name of analyzer",  
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",  
      "charFilters":[  
         "char_filter_name_1",  
         "char_filter_name_2"  
      ],  
      "tokenizer":"tokenizer_name",  
      "tokenFilters":[  
         "token_filter_name_1",  
         "token_filter_name_2"  
      ]  
   },  
   {  
      "name":"name of analyzer",  
      "@odata.type":"#analyzer_type",  
      "option1":value1,  
      "option2":value2,  
      ...  
   }  
],  
"charFilters":(optional)[  
   {  
      "name":"char_filter_name",  
      "@odata.type":"#char_filter_type",  
      "option1":value1,  
      "option2":value2,  
      ...  
   }  
],  
"tokenizers":(optional)[  
   {  
      "name":"tokenizer_name",  
      "@odata.type":"#tokenizer_type",  
      "option1":value1,  
      "option2":value2,  
      ...  
   }  
],  
"tokenFilters":(optional)[  
   {  
      "name":"token_filter_name",  
      "@odata.type":"#token_filter_type",  
      "option1":value1,  
      "option2":value2,  
      ...  
   }  
]  
```  

> [!NOTE]  
>  Ön által létrehozott egyéni elemzőket nem érhetők el az Azure Portalon. Adjon hozzá egy egyéni elemző eszköz egyetlen módja a kódot, amely az API-hívást hajt végre, egy index meghatározásakor keresztül történik.  

 Ebben a szakaszban helyezheti bárhol a létrehozás index kérelem törzse, de általában kerül a végén belül egy indexdefiníciót:  

```  
{  
  "name": "name_of_index",  
  "fields": [ ],  
  "suggesters": [ ],  
  "scoringProfiles": [ ],  
  "defaultScoringProfile": (optional) "...",  
  "corsOptions": (optional) { },  
  "analyzers":(optional)[ ],  
  "charFilters":(optional)[ ],  
  "tokenizers":(optional)[ ],  
  "tokenFilters":(optional)[ ]  
}  
```  

Char, tokenizers, és token szűrőket definícióit kerülnek az index csak akkor, ha egyéni beállításokat határozza meg. Egy meglévő szűrő vagy jogkivonatokat létrehozó, mint-akkor adja meg azt az elemző definícióban található nevének.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>Egyéni elemzők tesztelése

Használhatja a **teszt Analyzer művelet** a a [REST API-t](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) megtekintéséhez, hogyan egy elemző bontja a megadott szöveg jogkivonatokat.

**Kérés**
```
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
  Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```
**Válasz**
```
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>Egyéni elemzők frissítése

Miután egy elemző, egy tokenizer, egy jogkivonat szűrő vagy char szűrő van megadva, nem lehet módosítani. Új lehet hozzáadni egy meglévő index csak akkor, ha a `allowIndexDowntime` jelző értéke igaz található az indexben frissítésére vonatkozó kérelemben:

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Ez a művelet az index offline legalább néhány másodpercet, így az indexelést és a lekérdezési kérelem sikertelen vesz igénybe. Az index teljesítmény és az írás rendelkezésre állásának néhány percen belül az index frissített, vagy hosszabb ideig nagyon nagy méretű indexek, de ezek gyakorolt ideiglenes, és végül a saját megoldásához korlátozott lehet.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>Elemző referencia

Az alábbi táblázat az elemzők, tokenizers, jogkivonat szűrők konfiguráció tulajdonságainak listában, és a egy indexdefiníciót szűrő szakaszában char. Ezek az attribútumok egy elemző, tokenizer vagy szűrő az index felépítését tevődik össze. Érték hozzárendelése információkért lásd: a [tulajdonsághivatkozás](#PropertyReference).

### <a name="analyzers"></a>Elemzők

Az elemzők, indexattribútumokat eltérőek lehetnek attól függően, hogy az előre definiált vagy egyéni elemzőt használja.

#### <a name="predefined-analyzers"></a>Előre definiált elemzők

|||  
|-|-|  
|Name (Név)|Csak tartalmazhat betűket, számokat, szóközöket, kötőjeleket vagy aláhúzásjeleket tartalmazhat, azonban csak kezdő és záró betűket és számokat tartalmazhatnak, és legfeljebb 128 karakter hosszúságú lehet.|  
|Typo|Elemző listájából támogatott elemzőket. Tekintse meg a **analyzer_type** oszlopa a [elemzők](#AnalyzerTable) az alábbi táblázat.|  
|Beállítások|Kell egy előre meghatározott elemző eszköz szerepel az érvényes beállítások a [elemzők](#AnalyzerTable) az alábbi táblázat.|  

#### <a name="custom-analyzers"></a>Egyéni elemzők

|||  
|-|-|  
|Name (Név)|Csak tartalmazhat betűket, számokat, szóközöket, kötőjeleket vagy aláhúzásjeleket tartalmazhat, azonban csak kezdő és záró betűket és számokat tartalmazhatnak, és legfeljebb 128 karakter hosszúságú lehet.|  
|Typo|Must be "#Microsoft.Azure.Search.CustomAnalyzer".|  
|CharFilters|Állítsa a felsorolt előre meghatározott char szűrők egyikét a [Char szűrők](#CharFilter) tábla vagy az index definícióját a megadott egyéni char szűrőt.|  
|Jogkivonatokat létrehozó|Kötelező. Vagy az előre meghatározott tokenizers szereplő egyik beállítása a [Tokenizers](#Tokenizers) az alábbi táblázat vagy egy egyéni tokenizer, az index definícióját megadott.|  
|TokenFilters|Állítsa a felsorolt előre meghatározott token szűrők egyikét a [jogkivonat-szűrők](#TokenFilters) tábla vagy az index definícióját a megadott egyéni token szűrő.|  

<a name="CharFilter"></a>

### <a name="char-filters"></a>CHAR szűrők

 Char szűrő segítségével dolgoz fel a jogkivonatokat létrehozó előtt készítse elő a bemeneti szöveg. Például ezek lecserélheti bizonyos karakterek és szimbólumok. Az egyéni elemző rendelkezhet több char szűrőt. A listán sorrendben futnak a CHAR szűrőket.  

|||  
|-|-|  
|Name (Név)|Csak tartalmazhat betűket, számokat, szóközöket, kötőjeleket vagy aláhúzásjeleket tartalmazhat, azonban csak kezdő és záró betűket és számokat tartalmazhatnak, és legfeljebb 128 karakter hosszúságú lehet.|  
|Typo|Támogatott char szűrőt a listából Char szűrőtípust. Lásd: **char_filter_type** oszlopa a [Char szűrők](#CharFilter) az alábbi táblázat.|  
|Beállítások|Az érvényes beállítások kell lennie egy adott [Char szűrők](#CharFilter) típusa.|  

### <a name="tokenizers"></a>Tokenizers

 A jogkivonatokat létrehozó folyamatos szöveg jogkivonatok, például egy mondatnál ossza szavak sorozatát osztja fel.  

 Megadhat egy egyéni elemző pontosan egy jogkivonatokat létrehozó. Ha egynél több jogkivonatokat létrehozó van szüksége, hozzon létre több egyéni elemzőket, és rendelje a mező szerint történik az indexsémában.  
Egyéni elemző használható egy előre meghatározott jogkivonatokat létrehozó vagy alapértelmezett, vagy testre szabott beállítások.  

|||  
|-|-|  
|Name (Név)|Csak tartalmazhat betűket, számokat, szóközöket, kötőjeleket vagy aláhúzásjeleket tartalmazhat, azonban csak kezdő és záró betűket és számokat tartalmazhatnak, és legfeljebb 128 karakter hosszúságú lehet.|  
|Typo|A támogatott tokenizers listájából jogkivonatokat létrehozó neve. Lásd: **tokenizer_type** oszlopa a [Tokenizers](#Tokenizers) az alábbi táblázat.|  
|Beállítások|Kell egy adott jogkivonatokat létrehozó típusú szerepel az érvényes beállítások a [Tokenizers](#Tokenizers) az alábbi táblázat.|  

### <a name="token-filters"></a>Token szűrők

 Szűrje ki, vagy módosítsa a tokeneket a jogkivonatokat létrehozó által generált jogkivonat szűrő segítségével. Például az összes karakter kisbetűssé alakítja egy kis szűrőt is megadhat.   
Több jogkivonat-szűrőt használhat egyéni elemző. A listán sorrendben futnak a token szűrőket.  

|||  
|-|-|  
|Name (Név)|Csak tartalmazhat betűket, számokat, szóközöket, kötőjeleket vagy aláhúzásjeleket tartalmazhat, azonban csak kezdő és záró betűket és számokat tartalmazhatnak, és legfeljebb 128 karakter hosszúságú lehet.|  
|Typo|Támogatott token-szűrők listája token szűrő nevét. Lásd: **token_filter_type** oszlopa a [jogkivonat-szűrők](#TokenFilters) az alábbi táblázat.|  
|Beállítások|Meg kell [jogkivonat-szűrők](#TokenFilters) egy adott token szűrő típusa.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Tulajdonsághivatkozás

Ez a témakör az érvényes értékek egy egyéni elemző, tokenizer, char szűrő vagy az index token szűrő definíciójában megadott attribútumok esetén. Elemzők tokenizers és szűrők használatával az Apache Lucene megvalósított rendelkezik a Lucene-API dokumentációja mutató hivatkozásokat.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Előre definiált elemzők referencia

|**analyzer_name**|**analyzer_type**<sup>1  </sup>|**Leírás és beállítások**|  
|-|-|-|  
|[keyword](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (írja be vonatkozik, csak ha lehetőségek érhetők el) |Az egyes mezők a teljes tartalom kezeli. Ez akkor hasznos, adatok, például a zip-kódok, azonosítók és bizonyos termékek neveit.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Rugalmasan elkülöníti a szöveget be egy Reguláriskifejezés-mintának keresztül feltételek.<br /><br /> **Beállítások**<br /><br /> kis (típus: logikai) – azt határozza meg, hogy kisbetűvel vannak-e a használati. Az alapértelmezett érték true.<br /><br /> [a minta](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (típus: karakterlánc) – egy Reguláriskifejezés-mintának megfelelő jogkivonat elválasztók. Az alapértelmezett érték \w+.<br /><br /> [jelölők](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (típus: karakterlánc) – reguláris kifejezés jelzők. Az alapértelmezett érték egy üres karakterlánc. Megengedett értékek: CANON_EQ CASE_INSENSITIVE, A MEGJEGYZÉSEKET, DOTALL, LITERAL, TÖBBSOROS, UNICODE_CASE, UNIX_LINES<br /><br /> áll (típus: karakterlánc-tömbben)-áll listáját. Az alapértelmezett üres lista lesz.|  
|[Egyszerű](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el) |Szöveges értékekből nem a levelek osztja fel, és kisbetűsre konvertálja azokat. |  
|[standard](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Más néven standard.lucene)|StandardAnalyzer|Standard Lucene-elemzőt, a standard tokenizer, kis szűrő és stop-szűrő állnak.<br /><br /> **Beállítások**<br /><br /> maxTokenLength (típus: int) – a token hosszabb. Az alapértelmezett érték 255. A jogkivonatok hosszabb a maximálisan megengedett el vannak osztva. Token használható hossza legfeljebb 300 karakternél.<br /><br /> áll (típus: karakterlánc-tömbben)-áll listáját. Az alapértelmezett üres lista lesz.|  
|standardasciifolding.lucene|(írja be vonatkozik, csak ha lehetőségek érhetők el) |Standard analyzer ASCII-megfeleltetés szűrőt. |  
|[állj](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Szöveges értékekből nem a levelek osztja fel, a kisbetűk és a nem indexelendő szót token szűrőt alkalmazza.<br /><br /> **Beállítások**<br /><br /> áll (típus: karakterlánc-tömbben)-áll listáját. Az alapértelmezett érték az angol nyelvű tájékoztatáshoz egy előre meghatározott listában. |  
|[elválasztó karakter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el) |Egy elemző, amely az elválasztó karakterek jogkivonatokat létrehozó használja. Jogkivonatok, amelyek legfeljebb 255 karakter hosszúságú el vannak osztva.|  

 <sup>1</sup> analyzer típusok előtagja mindig a következő kódban a "#Microsoft.Azure.Search" úgy, hogy a "PatternAnalyzer" ténylegesen ezt kell megadni, mint "#Microsoft.Azure.Search.PatternAnalyzer". Az előtag kivonatosan eltávolítottuk, de az előtag azért van szükség a kód. 
 
A analyzer_type csak testre szabható elemzők biztosítunk. Ha nem tartoznak, beállítások, hasonlóan az a kulcsszó-elemző, nincs társított #Microsoft.Azure.Search típus.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>CHAR szűrők referencia

Az alábbi táblázatban az Apache Lucene segítségével megvalósított karakter szűrők kapcsolódnak a Lucene-API dokumentációja.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Leírás és beállítások**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Próbálja ki a HTML char szűrő hoz létre.|  
|[Leképezés](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|A leképezések beállítással meghatározott leképezések alkalmazó char szűrő. Egyeztetésekor a rendszer mohó (leghosszabb minta egy adott pont WINS-kiszolgáló megfelelő). Csere engedélyezett üres karakterlánc lehet.<br /><br /> **Beállítások**<br /><br /> leképezések (típus: karakterlánc-tömbben) – egy, a következő formátumú hozzárendelések listáját: "a = > b" (összes előfordulását a "b" karaktert "a" helyén karakter). Kötelező.|  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Lecseréli a bemeneti karakterlánc karaktereinek char szűrő. Cserélje le egy reguláris kifejezéssel azonosíthatja azokat a karaktersorozatok megőrzése érdekében és a helyettesítő karakterek azonosításához mintát használ. Például szövegbevitel = "aa-bb aa-bb" pattern="(aa)\\\s+(bb)" helyettesítő = "1 # 2 USD$" eredmény = "aa #bb aa #bb".<br /><br /> **Beállítások**<br /><br /> minta (típus: karakterlánc) – kötelező.<br /><br /> Csere (típus: karakterlánc) – kötelező.|  

 <sup>1</sup> szűrőtípusok char előtagja mindig a következő kódban a "#Microsoft.Azure.Search" úgy, hogy a "MappingCharFilter" ténylegesen ezt kell megadni, mint "#Microsoft.Azure.Search.MappingCharFilter. Eltávolítottuk az előtagot a csökkenti a szélességét, a tábla, de ne feledje, hogy adja hozzá a kódot. Vegye figyelembe, hogy char_filter_type csak előírt szűrőket testre szabható. Ha nem tartoznak, beállítások, hasonlóan a html_strip, nincs társított #Microsoft.Azure.Search típus.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Tokenizers referencia

Az alábbi táblázatban az Apache Lucene segítségével megvalósított tokenizers kapcsolódnak a Lucene-API dokumentációja.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Leírás és beállítások**|  
|-|-|-|  
|[classic](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Szintaxis-alapú tokenizer, amely lehetővé teszi a legtöbb európai nyelvű dokumentumok feldolgozására.<br /><br /> **Beállítások**<br /><br /> maxTokenLength (típus: int) – a token hosszabb. Alapértelmezett: legfeljebb 255: 300. A jogkivonatok hosszabb a maximálisan megengedett el vannak osztva.|  
|[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Az n-gramokat, a bemenet egy edge-ből tokenizes size(s) megadott.<br /><br /> **Beállítások**<br /><br /> minGram (típus: int) – alapértelmezett: 1, maximális: 300.<br /><br /> maxGram (típus: int) – alapértelmezett: legfeljebb 2: 300. MinGram nagyobbnak kell lennie.<br /><br /> tokenChars (típus: karakterlánc-tömbben)-osztályokat, a tokeneket ne karakter. Megengedett értékek: <br />"levél", "számjegy", "elválasztó karakter", "írásjelek", "szimbólum". Alapértelmezés szerint a tömb üres,-tartja az összes karaktert. |  
|[keyword_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Bocsát ki a teljes bemeneti egyetlen tokenként.<br /><br /> **Beállítások**<br /><br /> maxTokenLength (típus: int) – a token hosszabb. Alapértelmezett: 256 maximálisan: 300. A jogkivonatok hosszabb a maximálisan megengedett el vannak osztva.|  
|[letter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Szöveges értékekből nem a levelek osztja fel. Jogkivonatok, amelyek legfeljebb 255 karakter hosszúságú el vannak osztva.|  
|[lowercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Szöveges értékekből nem a levelek osztja fel, és kisbetűsre konvertálja azokat. Jogkivonatok, amelyek legfeljebb 255 karakter hosszúságú el vannak osztva.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| A szöveg nyelvét szabályok használatával osztja fel.<br /><br /> **Beállítások**<br /><br /> maxTokenLength (típus: int) – a token hosszabb, alapértelmezett: legfeljebb 255: 300. A jogkivonatok hosszabb a maximálisan megengedett el vannak osztva. A jogkivonatok hosszabb, mint 300 karakternél. először felosztása hosszúságú 300 jogkivonatokat, majd az egyes jogkivonatok van felosztva az maxTokenLength-készlet alapján.<br /><br />isSearchTokenizer (típus: logikai) – állítsa az igaz értékre, ha a keresés jogkivonatokat létrehozó használják, ha hamis értékre állítja az indexelési jogkivonatokat létrehozó használja. <br /><br /> nyelv (típus: karakterlánc) –, használt nyelv alapértelmezett "angol". Megengedett értékek a következők:<br />"bangla", "bolgár", "katalán", "chineseSimplified", "chineseTraditional", "Horvát", "Magyar", "dán", "holland", "Magyar", "francia", "német", "görög", "gudzsaráti", "hindi", "izlandi", "indonéz", "olasz", "japán nyelvű", "kannada" " koreai","malay","malajálam","maráthi","norwegianBokmaal","lengyel","portugál","portugueseBrazilian","punjabi","román","spanyol","serbianCyrillic","serbianLatin","Szlovén","spanyol","svéd","tamil","telugu","thai"" ukrán","urdu","vietnami" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Elosztja a szöveg nyelvét szabályok segítségével, és csökkenti a szavakat az alap űrlapokhoz<br /><br /> **Beállítások**<br /><br />maxTokenLength (típus: int) – a token hosszabb, alapértelmezett: legfeljebb 255: 300. A jogkivonatok hosszabb a maximálisan megengedett el vannak osztva. A jogkivonatok hosszabb, mint 300 karakternél. először felosztása hosszúságú 300 jogkivonatokat, majd az egyes jogkivonatok van felosztva az maxTokenLength-készlet alapján.<br /><br /> isSearchTokenizer (típus: logikai) – állítsa az igaz értékre, ha a keresés jogkivonatokat létrehozó használják, ha hamis értékre állítja az indexelési jogkivonatokat létrehozó használja.<br /><br /> nyelv (típus: karakterlánc) –, használt nyelv alapértelmezett "angol". Megengedett értékek a következők:<br />"arab", "bangla", "bolgár", "katalán", "Horvát", "Magyar", "dán", "holland", "Magyar", "észt", "finn", "francia", "német", "görög", "gudzsaráti", "héber", "hindi", "Magyar", "izlandi", "indonéz", "olasz", az "kannada" " lett","litván","malay","malajálam","maráthi","norwegianBokmaal","lengyel","portugál","portugueseBrazilian","punjabi","román","spanyol","serbianCyrillic","serbianLatin","Szlovák","Szlovén","spanyol","svéd"" tamil","telugu","török","ukrán","urdu" |
|[nGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|A bemeneti az n-gramokat, az adott size(s) tokenizes.<br /><br /> **Beállítások**<br /><br /> minGram (típus: int) – alapértelmezett: 1, maximális: 300.<br /><br /> maxGram (típus: int) – alapértelmezett: legfeljebb 2: 300. MinGram nagyobbnak kell lennie. <br /><br /> tokenChars (típus: karakterlánc-tömbben)-osztályokat, a tokeneket ne karakter. Megengedett értékek: "levél", "számjegy", "elválasztó karakter", "írásjelek", "szimbólum". Alapértelmezés szerint a tömb üres,-tartja az összes karaktert. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Elérési út-szerű hierarchiákhoz tokenizer.<br /><br /> **Beállítások**<br /><br /> elválasztó karaktert (típus: karakterlánc) – alapértelmezett: "/.<br /><br /> Csere (típus: karakterlánc) – Ha értéke, felülírja az elválasztó karakter. Elválasztó karakter értéke alapértelmezés szerint ugyanaz.<br /><br /> maxTokenLength (típus: int) – a token hosszabb. Alapértelmezett: legfeljebb 300: 300. MaxTokenLength hosszabb elérési utak a rendszer figyelmen kívül hagyja.<br /><br /> fordított (típus: logikai) – értéke igaz, ha létrehoz tokent fordított sorrendben. Alapértelmezett: False (hamis).<br /><br /> hagyja ki (típus: logikai)-jogkivonatok kihagyása a kezdeti. Az alapértelmezett érték a 0.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|A jogkivonatokat létrehozó használja a regex megadhatók különböző jogkivonatok létrehozásához.<br /><br /> **Beállítások**<br /><br /> minta (típus: karakterlánc) – Reguláriskifejezés-mintának. Az alapértelmezett érték \w+.<br /><br /> [jelölők](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (típus: karakterlánc) – reguláris kifejezés jelzők. Az alapértelmezett érték egy üres karakterlánc. Megengedett értékek: CANON_EQ CASE_INSENSITIVE, A MEGJEGYZÉSEKET, DOTALL, LITERAL, TÖBBSOROS, UNICODE_CASE, UNIX_LINES<br /><br /> csoport (típus: int) – jogkivonatokba csomagolja ki, melyik csoporthoz. Az alapértelmezett érték -1 (split).|  
|[standard_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Megszakítja a következő szöveg a [Unicode szöveg Szegmentálás szabályok](https://unicode.org/reports/tr29/).<br /><br /> **Beállítások**<br /><br /> maxTokenLength (típus: int) – a token hosszabb. Alapértelmezett: legfeljebb 255: 300. A jogkivonatok hosszabb a maximálisan megengedett el vannak osztva.|  
|[uax_url_email](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|URL-címek és e-mailek tokenizes egy tokenként.<br /><br /> **Beállítások**<br /><br /> maxTokenLength (típus: int) – a token hosszabb. Alapértelmezett: legfeljebb 255: 300. A jogkivonatok hosszabb a maximálisan megengedett el vannak osztva.|  
|[elválasztó karakter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el) |Elválasztó karakterek a szöveg osztja fel. Jogkivonatok, amelyek legfeljebb 255 karakter hosszúságú el vannak osztva.|  

 <sup>1</sup> jogkivonatokat létrehozó típusok előtagja mindig a következő kódban a "#Microsoft.Azure.Search" úgy, hogy a "ClassicTokenizer" ténylegesen ezt kell megadni, mint "#Microsoft.Azure.Search.ClassicTokenizer". Eltávolítottuk az előtagot a csökkenti a szélességét, a tábla, de ne feledje, hogy adja hozzá a kódot. Vegye figyelembe, hogy tokenizer_type csak testre szabható tokenizers biztosítunk. Ha nem tartoznak, beállítások, hasonlóan az a betűvel tokenizer, nincs társított #Microsoft.Azure.Search típus.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Szűrők jogkivonat-referencia

Az alábbi táblázat a token szűrők, amelyek Apache Lucene használata a Lucene-API dokumentációja vannak csatolva.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Leírás és beállítások**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Token szűrő, amely az arab normalizáló a orthography optimalizálására a következőhöz vonatkozik.|  
|[apostrophe](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Törli az összes karakter után az aposztróf (beleértve a saját maga aposztróf). |  
|[asciifolding](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Alakítja, amelyek nem részei az első 127 ASCII-karaktereket (a "Alapszintű Latin" Unicode-blokk) az ASCII ezzel egyenértékű, ha van ilyen betűk, számok és szimbolikus Unicode-karaktert.<br /><br /> **Beállítások**<br /><br /> preserveOriginal (típus: logikai) – Ha igaz értéke esetén az eredeti token marad. Az alapértelmezett érték FALSE (hamis).|  
|[cjk_bigram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Űrlapok bigrams StandardTokenizer előállított CJK feltételek.<br /><br /> **Beállítások**<br /><br /> ignoreScripts (típus: karakterlánc-tömbben)-parancsfájlok figyelmen kívül hagyja. Megengedett értékek: "han", "hiragana", "katakana", "hangul". Az alapértelmezett üres lista lesz.<br /><br /> outputUnigrams (típus: logikai) – true (igaz), ha azt szeretné, a kimenetben unigrams és bigrams is. Az alapértelmezett érték FALSE (hamis).|  
|[cjk_width](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Normalizálja CJK szélesség különbségeket. Kevesebb modellrészt teljes szélesség ASCII változat be a megfelelő alapvető latin és azokat a megfelelő kana fél szélességű Katakana variantní hodnoty. |  
|[classic](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Az angol nyelvű possessives távolítja el, és a mozaikszavak pontra. |  
|[common_grams](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Milyen gyakran jelentkezik, miközben az indexelés a bigrams hozhatnak létre. Önálló kifejezések továbbra is indexelt is, az átfedett bigrams.<br /><br /> **Beállítások**<br /><br /> commonWords (típus: karakterlánc-tömbben) – a gyakori szavakat készletét. Az alapértelmezett üres lista lesz. Kötelező.<br /><br /> ignoreCase (típus: logikai) – Ha igaz értéke esetén egyeztetésekor a rendszer megkülönbözteti a kis-és nagybetű nincs megkülönböztetve. Az alapértelmezett érték FALSE (hamis).<br /><br /> queryMode (típus: logikai) – bigrams állít elő, majd eltávolítja a gyakori szavakat és a egy gyakori szót követ ugyanazon használati feltételek. Az alapértelmezett érték FALSE (hamis).|  
|[dictionary_decompounder](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Bontja a különböző Germanic nyelvű található összetett szavakat.<br /><br /> **Beállítások**<br /><br /> a szólista (típus: karakterlánc-tömbben) – az egyeztetéshez szavak listáját. Az alapértelmezett üres lista lesz. Kötelező.<br /><br /> minWordSize (típus: int) – csak ennél hosszabb szavakat feldolgozni. Az alapértelmezett érték 5.<br /><br /> minSubwordSize (típus: int) – csak subwords ennél hosszabb a rendszer használt kimeneti adattípus. Az alapértelmezett érték a 2.<br /><br /> maxSubwordSize (típus: int) – csak subwords rövidebb, mint ez a rendszer használt kimeneti adattípus. Az alapértelmezett érték 15.<br /><br /> onlyLongestMatch (típus: logikai) – csak a leghosszabb egyező kimeneti subword hozzáadása. Az alapértelmezett érték FALSE (hamis).|  
|[edgeNGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|A megadott size(s) az n-gramokat kezdve az előtérben vagy egy bemeneti jogkivonat hátuljára hoz létre.<br /><br /> **Beállítások**<br /><br /> minGram (típus: int) – alapértelmezett: 1, maximális: 300.<br /><br /> maxGram (típus: int) – alapértelmezett: 2, legfeljebb 300. MinGram nagyobbnak kell lennie.<br /><br /> ügyféloldali (típus: karakterlánc) – Itt adhatja meg, mely a bemeneti oldalán az n-gram elő kell állítani a. Megengedett értékek: "előtér", "újra" |  
|[elision](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Eltávolítja a elisions. Például "l'avion" (az adatsík) "avion" (adatsík) alakítja át.<br /><br /> **Beállítások**<br /><br /> cikkek (típus: karakterlánc-tömbben) – egy számú cikknél eltávolítása. Az alapértelmezett üres lista lesz. Cikkek beállított felsorolása nem szerepel, alapértelmezés szerint minden francia cikkek eltávolítja.|  
|[german_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Megfelelően a heurisztika a német karakterek normalizálja az [German2 snowball algoritmus](http://snowballstem.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Hindi nyelvi változata létezik néhány eltérés eltávolítása a szöveges normalizálja. |  
|[indic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normalizálja indiai nyelveinek szövegére Unicode ábrázolása.
|[keep](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Egy jogkivonat szűrőt, amely csak tartja a jogkivonatok szereplő szöveg a megadott listán szereplő szavakat.<br /><br /> **Beállítások**<br /><br /> keepWords (típus: karakterlánc-tömbben) –, így szavak listáját. Az alapértelmezett üres lista lesz. Kötelező.<br /><br /> keepWordsCase (típus: logikai) – Ha igaz értéke esetén az alsó esetben minden szó első. Az alapértelmezett érték FALSE (hamis).|  
|[keyword_marker](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Jelöli meg a kulcsszavak feltételek vonatkoznak.<br /><br /> **Beállítások**<br /><br /> a kulcsszavak (típus: karakterlánc-tömbben)-kulcsszavak megjelölése szavak listáját. Az alapértelmezett üres lista lesz. Kötelező.<br /><br /> ignoreCase (típus: logikai) – Ha igaz értéke esetén az alsó esetben minden szó első. Az alapértelmezett érték FALSE (hamis).|  
|[keyword_repeat](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Minden bejövő jogkivonatot többször kétszer kulcsszó és többször nem kulcsszót bocsát ki. |  
|[kstem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Nagy teljesítményű kstem szűrő angol nyelven. |  
|[Hossza](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Eltávolítja a szavak, amelyek túl hosszú, túl rövid.<br /><br /> **Beállítások**<br /><br /> minimális (típus: int) – a minimális számát. Alapértelmezett: 0, maximális: 300.<br /><br /> Max (típus: int) – a maximális számát. Alapértelmezett: legfeljebb 300: 300.|  
|[limit](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|A jogkivonatok számát korlátozza az indexelés során.<br /><br /> **Beállítások**<br /><br /> maxTokenCount (típus: int)-jogkivonatok létrehozásához maximális száma. Az alapértelmezett érték 1.<br /><br /> consumeAllTokens (típus: logikai) – a bemeneti összes jogkivonatok kell használni, akkor is, ha maxTokenCount eléri-e. Az alapértelmezett érték FALSE (hamis).|  
|[lowercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Jogkivonat szöveg kisbetűsre normalizálja. |  
|[nGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|A megadott size(s) az n-gramokat állít elő.<br /><br /> **Beállítások**<br /><br /> minGram (típus: int) – alapértelmezett: 1, maximális: 300.<br /><br /> maxGram (típus: int) – alapértelmezett: 2, legfeljebb 300. MinGram nagyobbnak kell lennie.|  
|[pattern_capture](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Használja a Java regexes több tokent, minden egyes rögzítési csoport egy vagy több mintákat egy küldik.<br /><br /> **Beállítások**<br /><br /> minták (típus: karakterlánc-tömbben) – elleni tokenek-minták listáját. Kötelező.<br /><br /> preserveOriginal (típus: logikai) – true (igaz), az eredeti token vissza, akkor is, ha egy mintát megegyezik-e alapértelmezett: igaz |  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Token szűrő, amely egy mintát alkalmaz a Stream minden tokenhez egyezés előfordulások cserélje le a megadott behelyettesítendő karakterlánc.<br /><br /> **Beállítások**<br /><br /> minta (típus: karakterlánc) – kötelező.<br /><br /> Csere (típus: karakterlánc) – kötelező.|  
|[persian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el) |Perzsa a normalizálási vonatkozik. |  
|[phonetic](https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|-Jogkivonat készítése esetleges fonetikus egyezések.<br /><br /> **Beállítások**<br /><br /> kódoló (típus: karakterlánc) – fonetikus kódoló használatára. Megengedett értékek: "metaphone", "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "Köln", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Alapértelmezett érték: "metaphone". Alapértelmezés szerint metaphone.<br /><br /> Lásd: [kódoló](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) további információt.<br /><br /> cserélje le (típus: logikai) – igaz, ha a kódolt jogkivonatok kell lecserélnie eredeti jogkivonatok, false (hamis), ha azok szinonimáit szerint kell kiosztani. Az alapértelmezett érték true.|  
|[porter_stem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |A token adatfolyam a megfelelően alakítja át a [Porter, amely szerint algoritmus](https://tartarus.org/~martin/PorterStemmer/). |  
|[fordított](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Megfordítja a jogkivonat-karakterláncot. |  
|[scandinavian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Normalizálja a cserélhető karakterkészletet használatát. |  
|[scandinavian_folding](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Kevesebb modellrészt karakterkészletet åÅäæÄÆ -> egy és öÖøØ -> o. Azt is megkülönböztetést tartalmaz két magánhangzót felhasználása ellen aa, ae, ao, Outlook Express és fájlmegosztásain, így csak az elsőt. |  
|[Nagy rács](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Az egyes létrehozza a jogkivonatokat kombinációi.<br /><br /> **Beállítások**<br /><br /> maxShingleSize (típus: int) – alapértelmezés szerint a 2.<br /><br /> minShingleSize (típus: int) – alapértelmezés szerint a 2.<br /><br /> outputUnigrams (típus: logikai): Ha igaz értéke esetén a kimeneti adatfolyamba tartalmaz Tetőfedő lapok és a bemeneti jogkivonatok (unigrams). Az alapértelmezett érték true.<br /><br /> outputUnigramsIfNoShingles (típus: logikai) – értéke igaz, ha outputUnigrams viselkedését felülbírálása == false, mikor érhetők el nem Tetőfedő lapok idő. Az alapértelmezett érték FALSE (hamis).<br /><br /> tokenSeparator (típus: karakterlánc) – a szomszédos jogkivonatok csatlakoztatáskor egy nagy rács kialakításához használandó karakterlánc. Az alapértelmezett érték a "".<br /><br /> filterToken (típus: karakterlánc) – a karakterlánc minden helyzetben, amikor nem nem jogkivonat beszúrásához. Az alapértelmezett érték "_".|  
|[Snowball](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Snowball Token szűrő.<br /><br /> **Beállítások**<br /><br /> nyelv (típus: karakterlánc) – engedélyezett értékek közé tartozik: "Örmény", "baszk", "katalán", "dán", "holland", "Magyar", "finn", "francia", "német", "german2", "Magyar", "olasz", "kp", "lovins", "norvég", "porter", "portugál", "román", " Orosz","spanyol","svéd","török"|  
|[sorani_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|A Unicode Sorani szöveges alakot normalizálja.<br /><br /> **Beállítások**<br /><br /> Nincs.|  
|Szótőkereső|StemmerTokenFilter|Nyelvspecifikus, amely szerint szűrőt.<br /><br /> **Beállítások**<br /><br /> nyelv (típus: karakterlánc) – engedélyezett értékek közé tartozik: <br /> -   ["arab"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["Örmény"](http://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["basque"](http://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["brazíliai"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />– "bolgár"<br />-   ["katalán"](http://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["czech"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["dán"](http://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["holland"](http://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](http://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["angol"](http://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](http://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](http://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["finn"](http://snowballstem.org/algorithms/finnish/stemmer.html)<br />– "lightFinnish"<br />-   ["Franciaország"](http://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />– "Galíciai"<br />– "minimalGalician"<br />-   ["német"](http://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](http://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />– "minimalGerman"<br />-   ["görög"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />– "hindi"<br />-   ["Magyar"](http://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["indonéz"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["ír"](http://snowballstem.org/otherapps/oregan/)<br />-   ["olasz"](http://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["lett"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norwegian"](http://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["portugál"](http://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["román"](http://snowballstem.org/otherapps/romanian/)<br />-   ["spanyol"](http://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["spanyol"](http://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["swedish"](http://snowballstem.org/algorithms/swedish/stemmer.html)<br />– "lightSwedish"<br />-   ["török"](http://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Szótár kocsány vonatkozó használati feltételek vannak megjelölve kulcsszavakat, amely megakadályozza a láncban, amely szerint. Mielőtt stemming szűrők kell elhelyezni.<br /><br /> **Beállítások**<br /><br /> szabályok (típus: karakterlánc-tömbben)-szabályok a következő formátumban, amely szerint "szó = > típusazonosító" például "futott = > futtatása". Az alapértelmezett üres lista lesz.  Kötelező.|  
|[áll](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Eltávolítja a token streamből stopszavak. Alapértelmezés szerint a szűrő egy előre meghatározott stop listát használja az angol nyelvű tájékoztatáshoz.<br /><br /> **Beállítások**<br /><br /> áll (típus: karakterlánc-tömbben)-áll listáját. Nem lehet megadni, ha meg van adva egy stopwordsList.<br /><br /> stopwordsList (típus: karakterlánc) – egy előre meghatározott értéklistából áll. Nem lehet megadni, ha meg van adva áll. Megengedett értékek: "arab", "Örmény", "baszk", "brazíliai", "bolgár", "katalán", "Magyar", "dán", "holland", "Magyar", "finn", "francia", "Gallego", "német", "görög", "hindi", "Magyar", "indonéz", "ír", "olasz", "lett ","norvég","Perzsa","portugál","román","spanyol","sorani","spanyol","svéd","thai","török", alapértelmezett:"angol". Nem lehet megadni, ha meg van adva áll. <br /><br /> ignoreCase (típus: logikai) – Ha értéke igaz, minden szót alacsonyabb kisbetűsek első. Az alapértelmezett érték FALSE (hamis).<br /><br /> removeTrailing (típus: logikai) – értéke igaz, ha figyelmen kívül hagyja a legutolsó keresési kifejezést, ha egy leállítási szót. Az alapértelmezett érték true.
|[synonym](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Egyezések egyetlen vagy többszörös word szinonimák a token streamben.<br /><br /> **Beállítások**<br /><br /> Szinonimák (típus: karakterlánc-tömbben) – kötelező. A következő két formátumok egyikében szinonimák listája:<br /><br /> -hihetetlen, unbelievable, ünnepi = > lenyűgöző - = bal oldalán található minden használati > szimbólum cserélése összes kifejezést a jobb oldalon.<br /><br /> -hihetetlen, unbelievable, ünnepi, lenyűgöző - egyenértékű szavak vesszővel tagolt listája. A kibontás beállítás módosításához a lista értelmezését.<br /><br /> ignoreCase (típus: logikai)-eset – modellrész bemeneti egyeztetéséhez. Az alapértelmezett érték FALSE (hamis).<br /><br /> Bontsa ki (típus: logikai) – Ha igaz értéke esetén az összes szavak szinonimák listájában (ha = > nem használ) leképezése egy másik. <br />Az alábbi lista: hihetetlen, unbelievable, ünnepi, lenyűgöző 03T00: hihetetlen, unbelievable, ünnepi, lenyűgöző = > hihetetlen, unbelievable, ünnepi, lenyűgöző<br /><br />-Ha hamis, a következők: hihetetlen, unbelievable, ünnepi, lenyűgöző egyenértékűek: hihetetlen, unbelievable, ünnepi, lenyűgöző = > hihetetlen.|  
|[trim](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Kezdő és záró szóközök érkező jogkivonatok levágja. |  
|[TRUNCATE](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|A használati csonkolja az adott.<br /><br /> **Beállítások**<br /><br /> hossz (típus: int) – alapértelmezett: legfeljebb 300: 300. Kötelező.|  
|[Egyedi](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Kiszűri a megegyező szöveget az előző token a jogkivonatok.<br /><br /> **Beállítások**<br /><br /> onlyOnSamePosition (típus: logikai) – Ha állítsa be, csak az azonos helyen ismétlődések eltávolítása. Az alapértelmezett érték true.|  
|[nagybetűk](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(írja be vonatkozik, csak ha lehetőségek érhetők el)  |Jogkivonat szöveg nagybetűsre normalizálja. |  
|[word_delimiter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Subwords felosztja a szavakat, és nem kötelező átalakításokat végez subword csoportok.<br /><br /> **Beállítások**<br /><br /> generateWordParts (típus: logikai)-hatására létrejön, például "AzureSearch" válik az "Azure" "Search" szavak részeit. Az alapértelmezett érték true.<br /><br /> generateNumberParts (típus: logikai)-szám subwords generáló okoz. Az alapértelmezett érték true.<br /><br /> catenateWords (típus: logikai) – hatására a legmagasabb fut a részek egészhez való catenated, a word, az "Azure-Search" példa "AzureSearch" lesz. Az alapértelmezett érték FALSE (hamis).<br /><br /> catenateNumbers (típus: logikai) – okok maximális száma részek és kell catenated futtatja, például a "1-2" "12" lesz. Az alapértelmezett érték FALSE (hamis).<br /><br /> catenateAll (típus: logikai) – okok összes subword részek pontos catenated lehet, például: "Az Azure-Search-1" "AzureSearch1" lesz. Az alapértelmezett érték FALSE (hamis).<br /><br /> splitOnCaseChange (típus: logikai) – Ha igaz értéke esetén a elágazást caseChange, például az "AzureSearch" szavak "Azure" "Search" lesz. Az alapértelmezett érték true.<br /><br /> preserveOriginal - okok eredeti szavak megőrzi, és hozzáadja a subword listájához. Az alapértelmezett érték FALSE (hamis).<br /><br /> splitOnNumerics (típus: logikai) – Ha igaz értéke esetén a számokat, például "Azure1Search" válik az "Azure" "1" elágazást "Search". Az alapértelmezett érték true.<br /><br /> stemEnglishPossessive (típus: logikai) – záró "a" el kell távolítani az egyes subword okait. Az alapértelmezett érték true.<br /><br /> protectedWords (típus: karakterlánc-tömbben)-jogkivonatok szembeni tagolt folyamatban van. Az alapértelmezett üres lista lesz.|  

 <sup>1</sup> Tokentípusokat szűrő előtagja mindig a következő kódban a "#Microsoft.Azure.Search" úgy, hogy a "ArabicNormalizationTokenFilter" ténylegesen ezt kell megadni, mint "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter".  Eltávolítottuk az előtagot a csökkenti a szélességét, a tábla, de ne feledje, hogy adja hozzá a kódot.  

> [!NOTE]
> Ez szükséges, hogy konfigurálja-e az egyéni elemző, nem a hosszabb 300 karakternél jogkivonatok létrehozására. Az indexelő esetében nem sikerül a dokumentumok ilyen jogkivonatokkal. Trim őket, vagy figyelmen kívül hagyja azokat, használja a **TruncateTokenFilter** és a **LengthTokenFilter** jelölik.


## <a name="see-also"></a>Lásd még  
 [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Az Azure Search szolgáltatásban elemzők > Példák](search-analyzers.md#examples)    
 [Index létrehozása &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
