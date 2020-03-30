---
title: Egyéni elemzők hozzáadása karakterláncmezőkhöz
titleSuffix: Azure Cognitive Search
description: Konfigurálja az Azure Cognitive Search teljes szöveges keresési lekérdezéseiben használt szövegtokenizereket és karakterszűrőket.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 9bf0fb1a33a98031a78155a3956ac6d6abe33029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113631"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Egyéni elemzők hozzáadása az Azure Cognitive Search index karakterláncmezőihez

Az *egyéni analizátor* egy adott típusú [szöveganalizátor,](search-analyzers.md) amely a meglévő tokenizer és választható szűrők felhasználó által definiált kombinációjából áll. Kombinálásával tokenizers és szűrők új módon, testre szabhatja a szövegfeldolgozás a keresőmotorban, hogy konkrét eredményeket. Létrehozhat például egy egyéni elemzőt *egy char szűrővel,* hogy a szövegbevitel tokenizedje előtt eltávolítsa a HTML-jelöléseket.

 Több egyéni elemzőt is definiálhat a szűrők kombinációjának beállításához, de minden mező csak egy elemzőt használhat az indexelés elemzéséhez és egyet a keresési elemzéshez. Az ügyfélelemző megjelenésének szemléltetése: [Custom analyzer example](search-analyzers.md#Custom-analyzer-example).

## <a name="overview"></a>Áttekintés

 A teljes [szöveges keresőmotor](search-lucene-query-architecture.md)szerepe egyszerű szavakkal, a dokumentumok feldolgozása és tárolása oly módon, amely lehetővé teszi a hatékony lekérdezést és visszakeresést. Magas szinten minden a fontos szavak dokumentumokból való kinyerésén, az indexben való elhelyezésen, majd az index használatával egy adott lekérdezés szavainak megfelelő dokumentumok keresésén múlik. A dokumentumokból és keresési lekérdezésekből származó szavak kinyerésének folyamatát *lexikai elemzésnek nevezzük.* A lexikai elemzést végző összetevőket *analizátoroknak nevezzük.*

 Az Azure Cognitive Search alkalmazásban előre definiált nyelv-független elemzők közül választhat az [Analizátorok](#AnalyzerTable) táblában, vagy a [nyelvi elemzőkben &#40;az Azure Cognitive Search szolgáltatás REST API-jában ](index-add-language-analyzers.md)&#41;felsorolt nyelvspecifikus elemzők. Lehetősége van saját egyéni elemzők meghatározására is.  

 Az egyéni analizátor lehetővé teszi, hogy átvegye az irányítást a szöveg átalakítása indexelhető és kereshető tokenek. Ez egy felhasználó által definiált konfiguráció, amely egyetlen előre definiált tokenizer, egy vagy több jogkivonat-szűrők és egy vagy több karakterszűrők. A tokenizer felelős a szöveg tokenek, és a jogkivonat-szűrők a tokenizer által kibocsátott jogkivonatok módosításáért felelős. A rendszer a szövegbeviteli szöveg előkészítéséhez a rendszer által a tokenizer feldolgozása előtt alkalmazza a karakterszűrőket. A karakterszűrő például lecserélhet bizonyos karaktereket vagy szimbólumokat.

 Az egyéni elemzők által engedélyezett népszerű forgatókönyvek a következők:  

- Fonetikus keresés. Fonetikus szűrő hozzáadásával engedélyezheti a keresést a szó hangja imázsa alapján, nem pedig a beírt szöveg alapján.  

- Tiltsa le a lexikai elemzést. A Kulcsszóelemző vel nem elemzett kereshető mezőket hozhat létre.  

- Gyors előtag/utótag keresés. Adja hozzá az Edge N-gram token szűrőt a szavak előtagainak indexeléséhez a gyors előtagegyeztetés engedélyezéséhez. Kombinálja a fordított token szűrővel az utótag-egyeztetéshez.  

- Egyéni tokenizálás. Például a szóköz tokenizer a mondatok bontása tokenek használatával szóköz, mint a határolójel  

- ASCII hajtogatás. Adja hozzá a Standard ASCII összecsukható szűrőt, hogy normalizálja a mellékjeleket, például ö vagy ê a keresési kifejezésekben.  

  Ez a lap a támogatott elemzők, tokenizers, tokenesések, jogkivonat-szűrők és char szűrők listáját tartalmazza. Az indexdefiníció módosításainak leírását egy használati példával is megtalálhatja. Az Azure Cognitive Search implementációjában használt mögöttes technológiával kapcsolatos további háttéraz [Analysis package summary (Lucene)](https://lucene.apache.org/core/6_0_0/core/org/apache/lucene/codecs/lucene60/package-summary.html)című témakörben olvashat bővebben. Az elemzőkonfigurációk példáit az [Analizátorok hozzáadása az Azure Cognitive Search szolgáltatásban című témakörben talál.](search-analyzers.md#examples)

## <a name="validation-rules"></a>Érvényesítési szabályok  
 Az elemzők, tokenizerek, tokenszűrők és char szűrők nevének egyedinek kell lennie, és nem lehet azonos az előre definiált elemzők, tokenizerek, tokenszűrők vagy char szűrők bármelyikével. A már használatban lévő neveket a [Tulajdonsághivatkozásban](#PropertyReference) láthatja.

## <a name="create-custom-analyzers"></a>Egyéni elemzők létrehozása
 Egyéni elemzők et az index létrehozási időpontjában határozhat meg. Az egyéni elemző megadásának szintaxisát ebben a szakaszban ismerteti. A szintaxist a Mintadefiníciók áttekintésével is megismerkedhet az [Azure Cognitive Search analizátorhozzáadása című csoportjában.](search-analyzers.md#examples)  

 Az analizátor definíciója tartalmaz egy nevet, egy típust, egy vagy több karakterszűrőt, legfeljebb egy tokenizert és egy vagy több tokenszűrőt a tokenizálás utáni feldolgozáshoz. A program a jogkivonat-kezelés előtt alkalmazza a char filers-t. A tokenszűrők és a karakterszűrők balról jobbra haladnak.

 A `tokenizer_name` a tokenizer neve, `token_filter_name_1` `token_filter_name_2` és a token szűrők `char_filter_name_1` nevei, és `char_filter_name_2` a karakterszűrők nevei (lásd a [Tokenizers](#Tokenizers), [Token filters](#TokenFilters) és Char filters tables for valid values).

Az analizátor definíciója a nagyobb index része. Az index többi részével kapcsolatos információkért lásd: [Index API létrehozása.](https://docs.microsoft.com/rest/api/searchservice/create-index)

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
>  A létrehozott egyéni elemzők nem vannak elérhetővé téve az Azure Portalon. Az egyéni elemző hozzáadása csak úgy érhető el, ha olyan kódot ad hozzá, amely az index meghatározásakor hívásokat kezdeményez az API-hoz.  

 Egy indexdefiníción belül ezt a szakaszt a create indexkérelem törzsében bárhol elhelyezheti, de általában a végén kerül:  

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

A karakterszűrők, tokenizerek és tokenszűrők definíciói csak akkor kerülnek az indexbe, ha egyéni beállításokat ad meg. Meglévő szűrő vagy tokenizer jelenlegi állapotában való használatához adja meg név szerint az analizátor definíciójában.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>Egyéni elemzők tesztelése

Használhatja a **Test Analyzer művelet** a REST [API-ban,](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) hogy hogyan egy analizátor szünetek adott szöveget jogkivonatok.

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

Ha egy analizátor, egy tokenizer, egy tokenszűrő vagy egy char szűrő definiálva van, nem módosítható. Újak csak akkor adhatók hozzá `allowIndexDowntime` egy meglévő indexhez, ha a jelző értéke igaz az indexfrissítési kérelemben:

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Ez a művelet legalább néhány másodpercig offline állapotba helyezi az indexet, ami az indexelési és lekérdezési kérelmek sikertelensítését okozza. Az index teljesítménye és írási elérhetősége az index frissítése után néhány percig, vagy a nagyon nagy indexek esetében hosszabb ideig csökkenthető, de ezek a hatások ideiglenesek, és végül önmagukban oldhatók fel.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>Elemző hivatkozása

Az alábbi táblázatok az indexdefiníció analizátorainak, tokenizersnek, jogkivonat-szűrőnek és char szűrőszakaszának konfigurációs tulajdonságait sorolják fel. Egy analizátor, tokenizer vagy szűrő szerkezete az indexben ezekből az attribútumokból áll. Az érték-hozzárendelési információkat a [Tulajdonsághivatkozás című témakörben talál.](#PropertyReference)

### <a name="analyzers"></a>Elemzők

Az elemzők esetében az indexattribútumok attól függően változnak, hogy előre definiált vagy egyéni elemzőket használ.For analyzeers, index attributes vary on the whether you're using predefined or custom analyzeers.

#### <a name="predefined-analyzers"></a>Előre definiált elemzők

|||  
|-|-|  
|Név|Csak betűket, számjegyeket, szóközöket, kötőjeleket vagy aláhúzásjeleket tartalmazhat, csak alfanumerikus karakterekkel kezdődhet és végződhet, és legfeljebb 128 karakter ből állhat.|  
|Típus|Analizáló típusa a támogatott elemzők listájából. Lásd **az analyzer_type** oszlopot az [Analizátorok](#AnalyzerTable) táblázatban.|  
|Beállítások|Az alábbi [Elemzők](#AnalyzerTable) táblázatban felsorolt, előre definiált elemző érvényes beállításainak kell lenniük.|  

#### <a name="custom-analyzers"></a>Egyéni elemzők

|||  
|-|-|  
|Név|Csak betűket, számjegyeket, szóközöket, kötőjeleket vagy aláhúzásjeleket tartalmazhat, csak alfanumerikus karakterekkel kezdődhet és végződhet, és legfeljebb 128 karakter ből állhat.|  
|Típus|A "#Microsoft.Azure.Search.CustomAnalyzer" (#Microsoft.Azure.Search.CustomAnalyzer" (#Microsoft.Azure.Search.CustomAnalyzer" (#Microsoft.Azure.Search.CustomAnalyzer|  
|Karakterszűrők|Állítsa a [Karakterszűrők](#char-filters-reference) táblában felsorolt előre definiált karakterszűrők egyikére vagy az indexdefinícióban megadott egyéni karakterszűrőre.|  
|Tokenizer|Kötelező. Állítsa be az alábbi [tokenizerek](#Tokenizers) táblázatban felsorolt előre definiált tokenizerek egyikére, vagy az indexdefinícióban megadott egyéni tokenizerre.|  
|TokenFilters|Állítsa be a [Token szűrők](#TokenFilters) táblában felsorolt előre definiált jogkivonatszűrők egyikére vagy egy indexdefinícióban megadott egyéni jogkivonat-szűrőre.|  

> [!NOTE]
> Az egyéni analizátort úgy kell konfigurálni, hogy ne állítson elő 300 karakternél hosszabb jogkivonatokat. Az indexelés sikertelen az ilyen jogkivonatokkal rendelkező dokumentumok esetében. Vágja le őket, vagy figyelmen kívül hagyja őket, használja a **TruncateTokenFilter** és a **LengthTokenFilter** volt.  Ellenőrizze [**a Token szűrőket**](#TokenFilters) hivatkozásként.

<a name="CharFilter"></a>

### <a name="char-filters"></a>Karakterszűrők

 A char szűrő segítségével előkészíti a bemeneti szöveget, mielőtt a tokenizer feldolgozása. Például, ők helyettesíthetik bizonyos karakterek vagy szimbólumok. Egy egyéni elemzőben több karakterszűrő is lehet. A karakterszűrők a felsorolás sorrendjében futnak.  

|||  
|-|-|  
|Név|Csak betűket, számjegyeket, szóközöket, kötőjeleket vagy aláhúzásjeleket tartalmazhat, csak alfanumerikus karakterekkel kezdődhet és végződhet, és legfeljebb 128 karakter ből állhat.|  
|Típus|A támogatott karakterszűrők listájából származó karakterszűrő-típus. Lásd **char_filter_type** oszlopot az alábbi [Karakterszűrők](#char-filters-reference) táblázatban.|  
|Beállítások|Adott Karakterszűrők típusú érvényes beállításoknak kell [lenniük.](#char-filters-reference)|  

### <a name="tokenizers"></a>Tokenizers

 A tokenizer a folyamatos szöveget tokenek sorozatára osztja, például egy mondat ot szóra bontva.  

 Megadhatja, hogy pontosan egy tokenizer egyéni analizátor. Ha egynél több tokenizerre van szüksége, létrehozhat több egyéni elemzőt, és az indexsémában mezőalapú alapon rendelheti hozzá őket.  
Az egyéni elemző használhat egy előre definiált tokenizer alapértelmezett vagy testreszabott beállításokkal.  

|||  
|-|-|  
|Név|Csak betűket, számjegyeket, szóközöket, kötőjeleket vagy aláhúzásjeleket tartalmazhat, csak alfanumerikus karakterekkel kezdődhet és végződhet, és legfeljebb 128 karakter ből állhat.|  
|Típus|Tokenizer nevét a támogatott tokenizers listájából. Lásd **tokenizer_type** oszlopot az alábbi [Tokenizers](#Tokenizers) táblázatban.|  
|Beállítások|Az alábbi [tokenizers](#Tokenizers) táblázatban felsorolt adott tokenizer-típus érvényes beállításainak kell lennie.|  

### <a name="token-filters"></a>Tokenszűrők

 A jogkivonat-szűrő egy tokenizer által létrehozott jogkivonatok kiszűrésére vagy módosítására szolgál. Megadhat például egy kisbetűs szűrőt, amely az összes karaktert kisbetűssé alakítja.   
Egy egyéni elemzőben több jogkivonat-szűrő is lehet. A tokenszűrők a felsorolás sorrendjében futnak.  

|||  
|-|-|  
|Név|Csak betűket, számjegyeket, szóközöket, kötőjeleket vagy aláhúzásjeleket tartalmazhat, csak alfanumerikus karakterekkel kezdődhet és végződhet, és legfeljebb 128 karakter ből állhat.|  
|Típus|A támogatott jogkivonatszűrők listájából származó jogkivonat-szűrő neve. Lásd **token_filter_type** oszlopot az alábbi [Token szűrők](#TokenFilters) táblázatban.|  
|Beállítások|Adott jogkivonat-szűrőtípus [tokenszűrőinek](#TokenFilters) kell lenniük.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Tulajdonság hivatkozása

Ez a szakasz az egyéni elemző, tokenizáló, char szűrő vagy tokenszűrő definíciójában megadott attribútumok érvényes értékeit tartalmazza az indexben. Az Apache Lucene használatával megvalósított elemzők, tokenizerek és szűrők a Lucene API dokumentációjára mutató hivatkozásokat tartalmaznak.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Előre definiált elemzők – hivatkozás

|**analyzer_name**|**analyzer_type**  <sup>1.</sup>|**Leírás és beállítások**|  
|-|-|-|  
|[Kulcsszó](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak) |A mező teljes tartalmát egyetlen tokenként kezeli. Ez olyan adatok esetén hasznos, mint az irányítószámok, azonosítók és néhány terméknév.|  
|[Minta](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer (PatternAnalyzer)|Rugalmasan választja el a szöveget kifejezésekre egy reguláris kifejezésmintán keresztül.<br /><br /> **Beállítások**<br /><br /> kisbetűs (típus: bool) - Meghatározza, hogy a kifejezések kisbetűsek-e. Az alapértelmezett érték igaz.<br /><br /> [minta](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (típus: karakterlánc) – Reguláris kifejezésminta a tokenelválasztók nak megfelelően. Az alapértelmezett érték a \w+.<br /><br /> [jelzők](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (típus: karakterlánc) - Reguláris kifejezés jelzők. Az alapértelmezett érték egy üres karakterlánc. Megengedett értékek: CANON_EQ, CASE_INSENSITIVE, MEGJEGYZÉSEK, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> stopwords (típus: string array) - A stopwords listája. Az alapértelmezett érték egy üres lista.|  
|[Egyszerű](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak) |A szöveget nem betűvel osztja el, és kisbetűssé alakítja. |  
|[Standard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Más néven standard.lucene)|StandardAnalyzer|Standard Lucene analizátor, amely a szabványos tokenizer, kisbetűs szűrő és stop szűrő.<br /><br /> **Beállítások**<br /><br /> maxTokenLength (típus: int) - A maximális tokenhossz. Az alapértelmezett érték 255. A maximális hossznál hosszabb tokenek felosztásra kerülnek. A maximális tokenhossz 300 karakter.<br /><br /> stopwords (típus: string array) - A stopwords listája. Az alapértelmezett érték egy üres lista.|  
|standardasciifolding.lucene|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak) |Standard analizátor Ascii összecsukható szűrővel. |  
|[állj](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer (StopAnalyzer)|A szöveget nem betűkkel osztja el, a kis- és stopword tokenszűrőket alkalmazza.<br /><br /> **Beállítások**<br /><br /> stopwords (típus: string array) - A stopwords listája. Az alapértelmezett beállítás egy előre definiált lista az angol nyelvhez. |  
|[Szóköz](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak) |Egy analizátor, amely a whitespace tokenizer. A 255 karakternél hosszabb tokenek felosztásra kerülnek.|  

 <sup>1</sup> Analizáló típusok mindig előtaggal "#Microsoft.Azure.Search" úgy, hogy a "PatternAnalyzer" ténylegesen meg"#Microsoft.Azure.Search.PatternAnalyzer". Eltávolítottuk az előtagot a rövidség kedvéért, de az előtag szükséges a kódban. 
 
A analyzer_type csak testreszabható analizátorok számára biztosított. Ha nincsenek lehetőségek, mint a kulcsszóelemző esetében, nincs társítva #Microsoft.Azure.Search típusú.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>Karakterszűrők – hivatkozás

Az alábbi táblázatban az Apache Lucene használatával megvalósított karakterszűrők a Lucene API dokumentációjához kapcsolódnak.

|**char_filter_name**|**char_filter_type** <sup>1.</sup>|**Leírás és beállítások**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |A HTML-szerkezetek kiszűrésére kísérletet megkísérelő karakterszűrő.|  
|[Hozzárendelés](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|A leképezési beállítással definiált leképezéseket alkalmazó karakterszűrő. A megfelelő kapzsi (a leghosszabb minta egyezés egy adott ponton nyer). A csere lehet az üres karakterlánc.<br /><br /> **Beállítások**<br /><br /> leképezések (típus: karakterlánctömb) - A következő formátumú leképezések listája: "a=>b" (az "a" karakter összes előfordulása helyébe "b" karakter lép). Kötelező.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|A bemeneti karakterlánc karaktereit lecserélő karakterszűrő. Egy reguláris kifejezést használ a megőrzésre kerülő karaktersorozatok azonosítására, és egy helyettesítő mintát a helyettesíthető karakterek azonosítására. Például a bemeneti szöveg = "aa bb aa bb", pattern="(aa)\\\s+(bb)" replacement="$1#$2", eredmény = "aa#bb aa#bb".<br /><br /> **Beállítások**<br /><br /> minta (típus: karakterlánc) - Kötelező.<br /><br /> csere (típus: karakterlánc) - Kötelező.|  

 <sup>1</sup> A char szűrőtípusok mindig előtaggal vannak előrögzítve a "#Microsoft.Azure.Search" kóddal, így a "MappingCharFilter" valójában "#Microsoft.Azure.Search.MappingCharFilter" néven van megadva. Eltávolítottuk az előtagot, hogy csökkentsük a táblázat szélességét, de ne felejtse el szerepelnie a kódban. Figyelje meg, hogy char_filter_type csak a testre szabható szűrőkesetében van megadva. Ha nincsenek lehetőségek, mint a html_strip esetében, nincs társítva #Microsoft.Azure.Search típusú.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Tokenizerek – hivatkozás

Az alábbi táblázatban az Apache Lucene használatával megvalósított tokenizerek a Lucene API dokumentációjához kapcsolódnak.

|**tokenizer_name**|**tokenizer_type** <sup>1.</sup>|**Leírás és beállítások**|  
|-|-|-|  
|[Klasszikus](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer (Klasszikus Tokenizer)|Nyelvtanalapú tokenizer, amely alkalmas a legtöbb európai nyelvű dokumentum feldolgozására.<br /><br /> **Beállítások**<br /><br /> maxTokenLength (típus: int) - A maximális tokenhossz. Alapértelmezett: 255, maximum: 300. A maximális hossznál hosszabb tokenek felosztásra kerülnek.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Tokenizes a bemenet egy élről n-gramm adott méret (ek).<br /><br /> **Beállítások**<br /><br /> minGram (típus: int) - Alapértelmezett: 1, maximum: 300.<br /><br /> maxGram (típus: int) - Alapértelmezett: 2, maximum: 300. Nagyobbnak kell lennie, mint a minGram.<br /><br /> tokenChars (type: string array) - A tokenekben tartandó karakterosztályok. Megengedett értékek: <br />"letter", "digit", "whitespace", "írásjelek", "szimbólum". Alapértelmezés szerint egy üres tömb - megtartja az összes karaktert. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|A teljes bemenetet egyetlen jogkivonatként bocsátja ki.<br /><br /> **Beállítások**<br /><br /> maxTokenLength (típus: int) - A maximális tokenhossz. Alapértelmezett: 256, maximum: 300. A maximális hossznál hosszabb tokenek felosztásra kerülnek.|  
|[Levél](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |A szöveget nem betűkre osztja. A 255 karakternél hosszabb tokenek felosztásra kerülnek.|  
|[Kisbetűs](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |A szöveget nem betűvel osztja el, és kisbetűssé alakítja. A 255 karakternél hosszabb tokenek felosztásra kerülnek.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Nyelvspecifikus szabályok kal osztja a szöveget.<br /><br /> **Beállítások**<br /><br /> maxTokenLength (típus: int) - A maximális tokenhossz, alapértelmezett: 255, maximum: 300. A maximális hossznál hosszabb tokenek felosztásra kerülnek. A 300 karakternél hosszabb tokenek először 300 hosszúságú tokenekre vannak felosztva, majd mindegyik token felosztása a maxTokenLength készlet alapján történik.<br /><br />isSearchTokenizer (típus: bool) - Állítsa igaz, ha a keresési tokenizer, értéke hamis, ha az indexelés tokenizer. <br /><br /> nyelv (típus: karakterlánc) - Használandó nyelv, alapértelmezett "angol". Az engedélyezett értékek a következők:<br />"bangla", "bolgár", "katalán", "kínai Egyszerűsített", "kínaiHagyományos", "horvát", "cseh", "dán", "holland", "angol", "francia", "német", "görög", "gujarati", "hindi", "izlandi", "indonéz", "olasz", "japán", "kannada", koreai", "maláj", "malajálam", "marathi", "norwegianBokmaal", "lengyel", "portugál", "portugálbrazil", "pandzsábi", "román", "orosz", "szerbcirill", "szerblatin", "szlovén", "spanyol", "svéd", "tamil", "telugu", "thai", " ukrán", "urdu", "vietnami" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Nyelvspecifikus szabályok használatával osztja fel a szöveget, és a szavakat alapűrlapjukra csökkenti<br /><br /> **Beállítások**<br /><br />maxTokenLength (típus: int) - A maximális tokenhossz, alapértelmezett: 255, maximum: 300. A maximális hossznál hosszabb tokenek felosztásra kerülnek. A 300 karakternél hosszabb tokenek először 300 hosszúságú tokenekre vannak felosztva, majd mindegyik token felosztása a maxTokenLength készlet alapján történik.<br /><br /> isSearchTokenizer (típus: bool) - Állítsa igaz, ha a keresési tokenizer, értéke hamis, ha az indexelés tokenizer.<br /><br /> nyelv (típus: karakterlánc) - Használandó nyelv, alapértelmezett "angol". Az engedélyezett értékek a következők:<br />"arab", "bangla", "bolgár", "katalán", "horvát", "cseh", "dán", "holland", "angol", "észt", "finn", "francia", "német", "görög", "gujarati", "héber", "hindi", "magyar", "izlandi", "indonéz", "olasz", "kannada", lett", "litván", "maláj", "malayalam", "marathi", "norwegianBokmaal", "polish", "portugál", "portugálbrazil", "pandzsábi", "román", "orosz", "szerbcirill", "serbianLatin", "szlovák", "szlovén", "spanyol", "svéd", " tamil", "telugu", "török", "ukrán", "urdu" |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Tokenizes a bemeneti n-gramm a megadott méret (ek).<br /><br /> **Beállítások**<br /><br /> minGram (típus: int) - Alapértelmezett: 1, maximum: 300.<br /><br /> maxGram (típus: int) - Alapértelmezett: 2, maximum: 300. Nagyobbnak kell lennie, mint a minGram. <br /><br /> tokenChars (type: string array) - A tokenekben tartandó karakterosztályok. Engedélyezett értékek: "letter", "digit", "whitespace", "írásjelek", "szimbólum". Alapértelmezés szerint egy üres tömb - megtartja az összes karaktert. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|Elérési útTokenizerV2|Tokenizer az útvonal-szerű hierarchiák.<br /><br /> **Beállítások**<br /><br /> határoló (típus: karakterlánc) - Alapértelmezett: '/.<br /><br /> csere (típus: karakterlánc) - Ha be van állítva, cserélje le a határoló karaktert. Az alapértelmezett érték megegyezik a határolóértékkel.<br /><br /> maxTokenLength (típus: int) - A maximális tokenhossz. Alapértelmezett: 300, maximum: 300. A maxTokenLength-nél hosszabb elérési utakat a rendszer figyelmen kívül hagyja.<br /><br /> reverse (type: bool) - Ha igaz, fordított sorrendben generál token. Alapértelmezett érték: false (hamis).<br /><br /> skip (type: bool) - Kezdeti tokenek kihagyva. Az alapértelmezett érték a 0.|  
|[Minta](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Ez a tokenizer regex minta egyeztetést használ a különböző jogkivonatok létrehozásához.<br /><br /> **Beállítások**<br /><br /> [minta](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (típus: karakterlánc) - Reguláris kifejezésminta. Az alapértelmezett érték a \W+. <br /><br /> [jelzők](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (típus: karakterlánc) - Reguláris kifejezés jelzők. Az alapértelmezett érték egy üres karakterlánc. Megengedett értékek: CANON_EQ, CASE_INSENSITIVE, MEGJEGYZÉSEK, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> csoport (típus: int) - Melyik csoport kivonat tokenek. Az alapértelmezett érték -1 (felosztott).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Megtöri a szöveget a [Unicode szövegszegmentálási szabályai szerint.](https://unicode.org/reports/tr29/)<br /><br /> **Beállítások**<br /><br /> maxTokenLength (típus: int) - A maximális tokenhossz. Alapértelmezett: 255, maximum: 300. A maximális hossznál hosszabb tokenek felosztásra kerülnek.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenizes URL-eket és e-maileket egy tokenként.<br /><br /> **Beállítások**<br /><br /> maxTokenLength (típus: int) - A maximális tokenhossz. Alapértelmezett: 255, maximum: 300. A maximális hossznál hosszabb tokenek felosztásra kerülnek.|  
|[Szóköz](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak) |Szöveg felosztása szóközöknél. A 255 karakternél hosszabb tokenek felosztásra kerülnek.|  

 <sup>1</sup> Tokenizer típusok mindig előtaggal a kód "#Microsoft.Azure.Search", így a "ClassicTokenizer" ténylegesen meg van adva a "#Microsoft.Azure.Search.ClassicTokenizer". Eltávolítottuk az előtagot, hogy csökkentsük a táblázat szélességét, de ne felejtse el szerepelnie a kódban. Figyelje meg, hogy tokenizer_type csak a tokenizers, amely testreszabható. Ha nincsenek lehetőségek, mint a levél tokenizer esetében, nincs társítva #Microsoft.Azure.Search típusú.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Tokenszűrők – hivatkozás

Az alábbi táblázatban az Apache Lucene használatával megvalósított jogkivonat-szűrők a Lucene API dokumentációjához kapcsolódnak.

|**token_filter_name**|**token_filter_type** <sup>1.</sup>|**Leírás és beállítások**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |Egy tokenszűrő, amely az arab normalizálót alkalmazza az ortográfia normalizálására.|  
|[Aposztróf](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |Az összes karaktert az aposztróf után csíkokra (beleértve magát az aposztrófot is) csíkokra. |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Ábécé, numerikus és szimbolikus Unicode karakterek, amelyek nem az első 127 ASCII karakter (a "Basic Latin" Unicode blokk) azok ASCII megfelelői, ha van ilyen.<br /><br /> **Beállítások**<br /><br /> preserveOriginal (típus: bool) - Ha igaz, az eredeti token megmarad. Az alapértelmezett érték hamis.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenSzűrő|CJK-kifejezések bigramjait képezi, amelyek a StandardTokenizer-ből jönnek létre.<br /><br /> **Beállítások**<br /><br /> ignoreScripts (típus: karakterlánc tömb) - Figyelmen kívül hagyandó parancsfájlok. Az engedélyezett értékek a következők: "han", "hiragana", "katakana", "hangul". Az alapértelmezett érték egy üres lista.<br /><br /> outputUnigrams (típus: bool) - Állítsa igaz, ha mindig szeretné kiadni mind unigramm és bigrams. Az alapértelmezett érték hamis.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |Normalizálja a CJK szélességbeli különbségeket. A teljes szélességű ASCII változatokat az egyenértékű latin és félszélességű Katakana változatokba hajtogatja az egyenértékű kana-ba. |  
|[Klasszikus](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |Eltávolítja az angol birtokosokat, és a pislákat a betűszavakból. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Hozhat bigramokat a gyakran előforduló kifejezésekhez indexelés közben. Az egyes kifejezések még mindig indexelt is, a bigrams betakarva.<br /><br /> **Beállítások**<br /><br /> commonWords (típus: karakterlánc tömb) - A közös szavak készlete. Az alapértelmezett érték egy üres lista. Kötelező.<br /><br /> ignoreCase (típus: bool) - Ha igaz, az egyeztetés nem imitoter. Az alapértelmezett érték hamis.<br /><br /> queryMode (típus: bool) - Bigram-ot hoz létre, majd eltávolítja a gyakori szavakat és az egyetlen kifejezéseket, majd egy közös szót. Az alapértelmezett érték hamis.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Sok germán nyelven megtalálható összetett szavakat bomlik fel.<br /><br /> **Beállítások**<br /><br /> wordList (típus: karakterlánc tömb) - A szavak listája, amelynek ellen ez a mérkőzés. Az alapértelmezett érték egy üres lista. Kötelező.<br /><br /> minWordSize (típus: int) - Csak a szavak hosszabbak, mint ez kap feldolgozott. Az alapértelmezett érték 5.<br /><br /> minSubwordSize (típus: int) - Csak az ennél hosszabb alszavak kerülnek kimenetre. Az alapértelmezett érték 2.<br /><br /> maxSubwordSize (típus: int) - Csak ennél rövidebb alszó kerül kimenetre. Az alapértelmezett érték 15.<br /><br /> onlyLongestMatch (típus: bool) - Csak a leghosszabb egyező alszót adja hozzá a kimenethez. Az alapértelmezett érték hamis.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|A megadott méret(ek) n-grammját hozza létre a bemeneti jogkivonat elejéről vagy hátuljáról történő indításkor.<br /><br /> **Beállítások**<br /><br /> minGram (típus: int) - Alapértelmezett: 1, maximum: 300.<br /><br /> maxGram (típus: int) - Alapértelmezett: 2, maximum 300. Nagyobbnak kell lennie, mint a minGram.<br /><br /> side (type: string) - Itt adható meg, hogy az n-gram melyik oldalából kell generálni a bemenetet. Megengedett értékek: "front", "back" |  
|[elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Eltávolítja az elisionokat. Például a "l'avion" (a sík) "avion" (sík) lesz.<br /><br /> **Beállítások**<br /><br /> cikkek (típus: karakterlánctömb) - Eltávolítandó cikkek csoportja. Az alapértelmezett érték egy üres lista. Ha nincs készlet cikklista, alapértelmezés szerint az összes francia cikk törlődik.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |Normalizálja a német karaktereket a [German2 hógolyó algoritmus](https://snowballstem.org/algorithms/german2/stemmer.html) heurisztika szerint.|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |A szöveg normalizálása hindi nyelven a helyesírási változatok bizonyos különbségeinek eltávolításához. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normalizálja a szöveg Unicode ábrázolását indiai nyelveken.
|[Tartsa](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenSzűrő|Olyan jogkivonatszűrő, amely csak a megadott szólistában szereplő szöveget tartalmazó tokeneket tartja.<br /><br /> **Beállítások**<br /><br /> keepWords (típus: karakterlánc tömb) - A szavak listáját kell tartani. Az alapértelmezett érték egy üres lista. Kötelező.<br /><br /> keepWordsCase (típus: bool) - Ha igaz, kisbetűs minden szót először. Az alapértelmezett érték hamis.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|A kifejezéseket kulcsszavakként jelöli meg.<br /><br /> **Beállítások**<br /><br /> kulcsszavak (típus: string array) - Kulcsszavakként megjelölt szavak listája. Az alapértelmezett érték egy üres lista. Kötelező.<br /><br /> ignoreCase (típus: bool) - Ha igaz, kisbetűs minden szót először. Az alapértelmezett érték hamis.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |Minden bejövő tokent kétszer bocsát ki kulcsszóként, egyszer pedig nem kulcsszóként. |  
|[kszár](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |Nagy teljesítményű kstem szűrő angolnyelvre. |  
|[Hossza](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenSzűrő|Eltávolítja a túl hosszú vagy túl rövid szavakat.<br /><br /> **Beállítások**<br /><br /> min (típus: int) - A minimális szám. Alapértelmezett: 0, maximum: 300.<br /><br /> max (típus: int) - A maximális szám. Alapértelmezett: 300, maximum: 300.|  
|[Korlátoz](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Korlátozza a tokenek indexelés közben.<br /><br /> **Beállítások**<br /><br /> maxTokenCount (típus: int) - A létrehozandó tokenek maximális száma. Az alapértelmezett érték 1.<br /><br /> consumeAllTokens (típus: bool) – Függetlenül attól, hogy a bemenetösszes tokenkell-e használni akkor is, ha maxTokenCount elérte. Az alapértelmezett érték hamis.|  
|[Kisbetűs](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |A tokenszöveget kisbetűsé normalizálja. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|N-grammot hoz létre a megadott méret(ek)ből.<br /><br /> **Beállítások**<br /><br /> minGram (típus: int) - Alapértelmezett: 1, maximum: 300.<br /><br /> maxGram (típus: int) - Alapértelmezett: 2, maximum 300. Nagyobbnak kell lennie, mint a minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenSzűrő|Java regexes segítségével több tokenek, egy-egy rögzítési csoport egy vagy több minták.<br /><br /> **Beállítások**<br /><br /> minták (típus: karakterlánc tömb) - Az egyes jogkivonatokkal egyeztetendő minták listája. Kötelező.<br /><br /> preserveOriginal (típus: bool) - Állítsa igaz értékre az eredeti token visszaadására, még akkor is, ha az egyik minta megegyezik, alapértelmezett: true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Egy jogkivonat-szűrő, amely az adatfolyam minden egyes jogkivonatára alkalmazza a mintát, és lecseréli az egyezési eseményeket a megadott cserekarakterláncra.<br /><br /> **Beállítások**<br /><br /> minta (típus: karakterlánc) - Kötelező.<br /><br /> csere (típus: karakterlánc) - Kötelező.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak) |A perzsa normalizálást alkalmazza. |  
|[Fonetikus](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|FonetikusTokenFilter|Hozzon létre tokeneket fonetikus egyezéshez.<br /><br /> **Beállítások**<br /><br /> kódoló (típus: karakterlánc) - Fonetikus kódoló használni. Az engedélyezett értékek a következők: "metafon", "doubleMetaphone", "soundex", "sovábbfejlesztettSoundex", "caverphone1", "caverphone2", "kölni", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Alapértelmezett: "metafon". Az alapértelmezett a metafon.<br /><br /> További információt a [kódolóban](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) talál.<br /><br /> replace (type: bool) - Igaz, ha a kódolt tokenek kell cserélni az eredeti tokenek, hamis, ha hozzá kell adni a szinonimák. Az alapértelmezett érték igaz.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |Átalakítja a token streamet a [Porter-szemlék algoritmus szerint.](https://tartarus.org/~martin/PorterStemmer/) |  
|[Fordított](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |Megfordítja a token karakterláncát. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |Normalizálja a felcserélhető skandináv karakterek használatát. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |Összehajtja a skandináv karaktereket åÅäæÄÆ->és öÖøø->o. Azt is diszkriminálja használata kettős magánhangzók aa, ae, ao, oe és oo, így csak az első. |  
|[Zsindely](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenSzűrő|Tokenek kombinációit hozza létre egyetlen jogkivonatként.<br /><br /> **Beállítások**<br /><br /> maxShingleSize (típus: int) - Alapértelmezés szerint 2.<br /><br /> minShingleSize (típus: int) - Alapértelmezés szerint 2.<br /><br /> outputUnigrams (típus: bool) - ha igaz, a kimeneti adatfolyam tartalmazza a bemeneti tokeneket (unigramm) és a zsindelyeket. Az alapértelmezett érték igaz.<br /><br /> outputUnigramsIfNoShingles (típus: bool) - Ha igaz, felülbírálja a outputUnigrams==false viselkedését azokban az időkben, amikor nem áll rendelkezésre zsindely. Az alapértelmezett érték hamis.<br /><br /> tokenSeparator (type: string) - A karakterlánc, amelyet a szomszédos tokenek zsindely egyesítésekor használ. Az alapértelmezett érték " ".<br /><br /> filterToken (type: string) - A karakterlánc, amelyet minden olyan pozícióhoz be kell szúrni, ahol nincs token. Az alapértelmezett érték a "_".|  
|[Hógolyó](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenSzűrő|Hógolyó token szűrő.<br /><br /> **Beállítások**<br /><br /> nyelv (típus: string) - Megengedett értékek: "armenian", "baszk", "katalán", "dán", "holland", "angol", "finn", "francia", "német", "german2", "magyar", "olasz", "kp", "lovins", "norvég", "porter", "portugál", "román", " orosz", "spanyol", "svéd", "török"|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normalizálja a Sorani szöveg Unicode ábrázolását.<br /><br /> **Beállítások**<br /><br /> Nincs.|  
|szőni|StemmerTokenSzűrő|Nyelvspecifikus szisztaszűrő.<br /><br /> **Beállítások**<br /><br /> nyelv (típus: karakterlánc) - Az engedélyezett értékek a következők: <br /> -   ["arab"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["örmény"](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["baszk"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["Brazil"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />- "bolgár"<br />-   ["katalán"](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["cseh"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["dán"](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["Holland"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["Angol"](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["Könnyű Angol"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalAngol"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["birtokos angol"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["finn"](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "lightFinn"<br />-   ["Francia"](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["Könnyű Francia"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrancia"](https://dl.acm.org/citation.cfm?id=318984)<br />- "galíciai"<br />- "minimalGalician"<br />-   ["német"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["könnyűnémet"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "minimalGerman"<br />-   ["görög"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />- "hindi"<br />-   ["magyar"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightMagyar"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["indonéz"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["ír"](https://snowballstem.org/otherapps/oregan/)<br />-   ["olasz"](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["Könnyű Olasz"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["Sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["lett"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norvég"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimálnorvég"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["portugál"](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortugál"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortugál"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugálRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["román"](https://snowballstem.org/otherapps/romanian/)<br />-   ["orosz"](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["KönnyűOrosz"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["spanyol"](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanyol"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["svéd"](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "lightSwedish"<br />-   ["török"](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|A szótár-szárú kifejezések kulcsszavakként vannak megjelölve, ami megakadályozza, hogy a láncon belül meghátráljon. Minden tárolószűrő előtt el kell helyezni.<br /><br /> **Beállítások**<br /><br /> szabályok (típus: karakterlánctömb) - Eredő szabályok a következő formátumban "word => szár" például "futott => run". Az alapértelmezett érték egy üres lista.  Kötelező.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenSzűrő|Eltávolítja a leállítási szavakat a tokenstreamből. Alapértelmezés szerint a szűrő egy előre definiált stop szólistát használ az angol nyelvhez.<br /><br /> **Beállítások**<br /><br /> stopwords (típus: string array) - A stopwords listája. StopwordsList nincs megadva, ha stopwordslist van megadva.<br /><br /> stopwordsList (típus: karakterlánc) - A stopwords előre meghatározott listája. Nem adható meg, ha stopwords van megadva. Az engedélyezett értékek a következők: "arab", "örmény", "baszk", "brazil", "bolgár", "katalán", "cseh", "dán", "holland", "angol", "finn", "francia", "galíciai", "német", "görög", "hindi", "magyar", "indonéz", "ír", "olasz", "lett", "lett", "lett". ", "norvég", "perzsa", "portugál", "román", "orosz", "sorani", "spanyol", "svéd", "thai", "török", alapértelmezett: "angol". Nem adható meg, ha stopwords van megadva. <br /><br /> ignoreCase (típus: bool) - Ha igaz, akkor először minden szó kisbetűs. Az alapértelmezett érték hamis.<br /><br /> removeTrailing (típus: bool) - Ha igaz, figyelmen kívül hagyja az utolsó keresési kifejezést, ha ez egy stop szó. Az alapértelmezett érték igaz.
|[Szinonimája](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Egy vagy több szó szinonimáinak felel meg egy jogkivonat-adatfolyamban.<br /><br /> **Beállítások**<br /><br /> szinonimák (típus: karakterlánctömb) - Kötelező. Szinonimák listája az alábbi két formátum egyikében:<br /><br /> -hihetetlen, hihetetlen, mesés => csodálatos - minden szempontból a bal oldalon => szimbólum helyébe minden szempontból a jobb oldalon.<br /><br /> -hihetetlen, hihetetlen, mesés, csodálatos - Egy vesszővel elválasztott lista az egyenértékű szavakról. A lista értelmezésének módosításához állítsa be a kibontási beállítást.<br /><br /> ignoreCase (típus: bool) - Case-redők bemenet megfelelő. Az alapértelmezett érték hamis.<br /><br /> kibontás (típus: bool) - Ha igaz, a szinonimák listájában szereplő összes szó (ha => jelölés nem használatos) leképezést egymásikhoz. <br />A következő lista: hihetetlen, hihetetlen, mesés, csodálatos egyenértékű: hihetetlen, hihetetlen, mesés, csodálatos => hihetetlen, hihetetlen, mesés, csodálatos, csodálatos, csodálatos, csodálatos, csodálatos, csodálatos, csodálatos<br /><br />- Ha hamis, a következő lista: hihetetlen, hihetetlen, mesés, csodálatos egyenértékű: hihetetlen, hihetetlen, mesés, csodálatos => hihetetlen.|  
|[Berendezés](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |Levágja a kezdő és záró szóközt a tokenekből. |  
|[Megcsonkít](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|CsonkaTokenFilter|A kifejezések csonkolása egy adott hosszra.<br /><br /> **Beállítások**<br /><br /> hossz (típus: int) - Alapértelmezett: 300, maximum: 300. Kötelező.|  
|[Egyedi](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenSzűrő|Kiszűri a tokeneket az előző jogkivonattal megegyező szöveggel.<br /><br /> **Beállítások**<br /><br /> onlyOnSamePosition (típus: bool) - Ha be van állítva, távolítsa el az ismétlődéseket csak ugyanabban a pozícióban. Az alapértelmezett érték igaz.|  
|[Nagybetűs](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(a típus csak akkor érvényes, ha a lehetőségek rendelkezésre állnak)  |A tokenszöveg et nagybetűsre normalizálja. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|A szavakat alszavakra osztja, és nem kötelező átalakításokat hajt végre a alszócsoportokon.<br /><br /> **Beállítások**<br /><br /> generateWordParts (típus: bool) – Szóegyes részeit hozza létre, például az "AzureSearch" lesz az "Azure" "Keresés". Az alapértelmezett érték igaz.<br /><br /> generateNumberParts (típus: bool) - Számalszót generál. Az alapértelmezett érték igaz.<br /><br /> catenateWords (típus: bool) – A szórészek maximális futtatása eltágul, például az "Azure-Search" "AzureSearch" lesz. Az alapértelmezett érték hamis.<br /><br /> catenateNumbers (típus: bool) - A kontrolálandó alkatrészek maximális futtatása, például az "1-2" "12" lesz. Az alapértelmezett érték hamis.<br /><br /> catenateAll (típus: bool) – Az összes alszórész catenated, például az "Azure-Search-1" lesz "AzureSearch1". Az alapértelmezett érték hamis.<br /><br /> splitOnCaseChange (típus: bool) - Ha igaz, osztja a szavakat caseChange, például "AzureSearch" lesz "Azure" "Keresés". Az alapértelmezett érték igaz.<br /><br /> preserveOriginal - Az eredeti szavak megőrzését és a segédszólistához való hozzáadását eredményezi. Az alapértelmezett érték hamis.<br /><br /> splitOnNumerics (típus: bool) - Ha igaz, osztja a számok, például "Azure1Search" lesz "Azure" "1" "Keresés". Az alapértelmezett érték igaz.<br /><br /> stemEnglishPossessive (típus: bool) - A követő ""-k eltávolítása minden alszóhoz eltávolításra kerül. Az alapértelmezett érték igaz.<br /><br /> protectedWords (type: string array) - Jogkivonatok, amelyek védelmet nyújtanak a tagolt ságok tól. Az alapértelmezett érték egy üres lista.|  

 <sup>1</sup> Jogkivonat-szűrőtípusok mindig előtaggal "#Microsoft.Azure.Search" kód, így a "ArabicNormalizationTokenFilter" ténylegesen "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter" lesz megadva.  Eltávolítottuk az előtagot, hogy csökkentsük a táblázat szélességét, de ne felejtse el szerepelnie a kódban.  


## <a name="see-also"></a>Lásd még  
 [Azure Cognitive Search REST API-k](https://docs.microsoft.com/rest/api/searchservice/)   
 [Elemzők az Azure Cognitive Search > példák](search-analyzers.md#examples)    
 [Index létrehozása &#40;Azure Cognitive Search REST API-&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
