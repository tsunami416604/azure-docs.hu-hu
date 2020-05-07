---
title: Részleges feltételek, mintázatok és speciális karakterek
titleSuffix: Azure Cognitive Search
description: Használjon helyettesítő karaktereket, regexeket és előtag-lekérdezéseket az Azure Cognitive Search lekérdezési kérelemben szereplő teljes vagy részleges feltételek egyeztetéséhez. A speciális karaktereket tartalmazó, nehezen egyeztethető minták teljes lekérdezési szintaxissal és egyéni elemzővel oldhatók fel.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 05ff56c904fc48a1041ad40f00110a8ff0fd01f1
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592043"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>Részleges kifejezéses keresés és minták speciális karakterekkel (helyettesítő karakterek, regex, mintázatok)

A *részleges kifejezéses keresés* olyan kifejezésekre hivatkozik, amelyek a kifejezés töredékeit foglalják magukban, ahol a teljes kifejezés helyett csak a kezdő, a középső vagy a záró kifejezés (más néven előtag, Infix vagy utótag típusú lekérdezések) lehet. A *minták* töredékek kombinációját is tartalmazhatják, gyakran olyan speciális karakterekkel, mint a lekérdezési karakterlánc részét képező kötőjelek vagy perjelek. Gyakori használati esetek közé tartozik például egy telefonszám, URL-cím, személy vagy termékkód, vagy összetett szó részeinek lekérdezése.

A részleges és a minta keresés problémát okozhat, ha az index nem rendelkezik a várt formátumú feltételekkel. Az indexelés [lexikális elemzési fázisában](search-lucene-query-architecture.md#stage-2-lexical-analysis) (feltéve, hogy az alapértelmezett standard elemző) a speciális karaktereket elveti, az összetett és az összetett karakterláncok fel vannak osztva, és a szóközök törlődnek; a minta-lekérdezések sikertelenek lehetnek, ha nem található egyezés. `+1 (425) 703-6214` Például egy telefonszám ( `"1"` `"425"`például:,, `"703"`, `"6214"`) nem fog megjelenni a `"3-62"` lekérdezésben, mert a tartalom valójában nem létezik az indexben. 

A megoldás egy olyan elemző meghívása, amely megőrzi a teljes karakterláncot, beleértve a szóközöket és a speciális karaktereket is, ha szükséges, a részleges feltételek és minták egyeztetéséhez. Ha egy további mezőt hoz létre egy ép karakterlánchoz, és egy Content-Serving Analyzert használ, a megoldás alapja.

> [!TIP]
> Ismeri a Poster és a REST API-kat? [Töltse le a lekérdezési példák gyűjteményét](https://github.com/Azure-Samples/azure-search-postman-samples/) , amely a jelen cikkben ismertetett részleges kifejezéseket és speciális karaktereket kérdezi le.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Mi a részleges keresés az Azure-ban Cognitive Search

Az Azure Cognitive Searchban a részleges keresés és a minta az alábbi űrlapokon érhető el:

+ [Előtag-keresés](query-simple-syntax.md#prefix-search), például `search=cap*`a "kapitány Jack Waterfront Inn" vagy a "Gacc Capital" egyeztetése. Az egyszerű lekérdezési szintaxist vagy a teljes Lucene lekérdezési szintaxist használhatja az előtag-kereséshez.

+ [Helyettesítő karakteres keresés](query-lucene-syntax.md#bkmk_wildcard) vagy [reguláris kifejezés](query-lucene-syntax.md#bkmk_regex) , amely egy beágyazott sztring mintázatát vagy részeit keresi. A helyettesítő karaktereknek és a reguláris kifejezéseknek a teljes Lucene szintaxissal kell rendelkezniük. Az utótag és az index lekérdezése reguláris kifejezésként van kialakítva.

  Néhány példa a részleges kifejezéses keresésre: az alábbiakat tartalmazza. Az "alfanumerikus" kifejezéssel megadott utótag-lekérdezés esetében helyettesítő karakteres keresést (`search=/.*numeric.*/`) kell használni a találatok kereséséhez. A belső karaktereket (például egy URL-töredéket) tartalmazó részleges kifejezéshez szükség lehet escape-karakterek hozzáadására. A JSON-ban a továbbítási perjelek `/` egy visszafelé perjelet `\`eredményeznek. `search=/.*microsoft.com\/azure\/.*/` Ennek megfelelően a "Microsoft.com/Azure/" URL-töredék szintaxisa.

Ahogy azt a fentiekben is említettük, a fentiek mindegyike megköveteli, hogy az index a minta egyeztetését eredményező sztringeket tartalmazzon, amelyeket a standard Analyzer nem biztosít. A cikkben ismertetett lépéseket követve gondoskodhat arról, hogy a szükséges tartalom létezik az ilyen forgatókönyvek támogatásához.

## <a name="solving-partialpattern-search-problems"></a>Részleges/minta keresési problémák megoldása

Ha töredékekre vagy mintázatokra vagy speciális karakterekre kell keresnie, felülbírálhatja az alapértelmezett elemzőt egy olyan egyéni elemzővel, amely egyszerűbb jogkivonatok létrehozása-szabályok alatt működik, megőrizve a teljes karakterláncot. A lépés a következőképpen néz ki:

+ Adjon meg egy mezőt, amely a sztring érintetlen verzióját tárolja (feltéve, hogy elemezni és nem elemzett szöveget kíván használni)
+ Válasszon egy előre definiált analizátort, vagy Definiáljon egy egyéni elemzőt egy nem elemzett inép karakterlánc kimenetéhez
+ Az egyéni elemző kiosztása a mezőhöz
+ Az index létrehozása és tesztelése

> [!TIP]
> Az elemzők kiértékelése olyan iterációs folyamat, amely gyakori index-újraépítést igényel. Ezt a lépést a Poster, a REST API-k [létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index), az indexek [törlése](https://docs.microsoft.com/rest/api/searchservice/delete-index), a[dokumentumok betöltése](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)és a dokumentumok [keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents)során egyszerűbbé teheti. A betöltési dokumentumok esetében a kérelem törzsének tartalmaznia kell egy kis reprezentatív adatkészletet, amelyet szeretne tesztelni (például egy telefonszámot vagy Termékkód számot tartalmazó mező). Ha ezekkel az API-kkal ugyanabban a Poster-gyűjteményben vannak, gyorsan elvégezheti ezeket a lépéseket.

## <a name="duplicate-fields-for-different-scenarios"></a>Ismétlődő mezők különböző forgatókönyvekhez

Az elemzők kiosztása egy mező alapján történik, ami azt jelenti, hogy az indexben mezőket hozhat létre a különböző forgatókönyvek optimalizálásához. Pontosabban megadhatja a "featureCode" és a "featureCodeRegex" karakterláncot, hogy támogassa a normál teljes szöveges keresést az első, és a speciális minta egyeztetését a másodikban.

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>Elemző kiválasztása

Ha olyan elemzőt választ ki, amely teljes körű jogkivonatokat állít elő, a következő elemzők gyakoriak:

| Analyzer | Viselkedés |
|----------|-----------|
| [nyelvi elemzők](index-add-language-analyzers.md) | A megőrzi a kötőjeleket összetett szavakban vagy sztringekben, magánhangzó-mutációkban és művelet-űrlapokban. Ha a lekérdezési minták kötőjeleket tartalmaznak, a nyelvi elemző használata elegendő lehet. |
| [kulcsszó](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | A teljes mező tartalma egyetlen kifejezésként van jogkivonat. |
| [szóköz](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Csak szóközöket választ ki. A kötőjeleket vagy más karaktereket tartalmazó kifejezéseket egyetlen jogkivonatként kezeli a rendszer. |
| [Egyéni analizátor](index-add-custom-analyzers.md) | ajánlott Az egyéni elemző létrehozása lehetővé teszi a tokenizer és a jogkivonat-szűrő megadását is. A korábbi elemzőket a következőképpen kell használni:. Az egyéni elemző lehetővé teszi, hogy kiválassza a használni kívánt tokenizers és jogkivonat-szűrőket. <br><br>Az ajánlott kombináció a [tokenizer kulcsszó](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) , amely egy [kisbetűs jogkivonat-szűrővel](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)rendelkezik. Önmagában az előre definiált [kulcsszó-elemző](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) nem kisbetűs, így a lekérdezések sikertelenek lehetnek. Az egyéni elemző egy mechanizmust biztosít az alsó szintű jogkivonat-szűrő hozzáadásához. |

Ha webes API-teszt eszközt (például Poster) használ, hozzáadhatja a [test Analyzer-Rest-hívást](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) a jogkivonat-kimenet vizsgálatához.

A használatához rendelkeznie kell egy meglévő indexszel. A meglévő indexek és a kötőjeleket vagy a részleges kifejezéseket tartalmazó mezők esetében különböző elemzőket lehet kipróbálni a megadott feltételek alapján, hogy megtudja, milyen jogkivonatok vannak kibocsátva.  

1. Tekintse meg a standard Analyzert, és tekintse meg, hogy alapértelmezés szerint a rendszer milyen feltételekkel rendelkezik.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Értékelje ki a választ, és tekintse meg, hogy a szöveg hogyan legyen jogkivonatban az indexen belül. Figyelje meg, hogy az egyes kifejezések kisebbek és feltörtek.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Módosítsa a kérelmet a vagy `whitespace` `keyword` a Analyzer használatára:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. A válasz mostantól egyetlen tokenből áll, amely a karakterlánc részeként megőrzött kötőjelekkel van ellátva. Ha egy mintázatra vagy egy részleges kifejezésre kell keresnie, a lekérdezési motor mostantól a találatok megtalálásának alapja.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Ügyeljen arra, hogy a lekérdezés-elemzők a lekérdezési fa létrehozásakor gyakran alacsonyabb eseti kifejezéseket adjanak a keresési kifejezésekben. Ha olyan elemzőt használ, amely nem a kis-és nagybetűket használja, és nem várt eredményeket kap, ez az oka lehet. A megoldás egy kisméretű jogkivonat-szűrő hozzáadása az alábbi, "egyéni elemzők használata" című szakaszban leírtak szerint.

## <a name="configure-an-analyzer"></a>Analizátor konfigurálása
 
Függetlenül attól, hogy az elemzőket kiértékeli, vagy egy adott konfigurációval halad előre, meg kell adnia az analizátort a mező definíciójában, és saját maga is konfigurálnia kell, ha nem beépített elemzőt használ. Az elemzők cseréjekor általában újra kell építeni az indexet (eldobás, újbóli létrehozás és újratöltés). 

### <a name="use-built-in-analyzers"></a>Beépített elemzők használata

A beépített vagy előre definiált elemzők név szerint adhatók meg egy `analyzer` mező definíciójának tulajdonságában, és az indexben nem szükséges további konfiguráció. Az alábbi példa bemutatja, hogyan állíthatja be az `whitespace` elemzőt egy mezőre. 

További forgatókönyvek és további információ a beépített elemzők használatáról: [előre definiált elemzők listája](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>Egyéni elemzők használata

Ha [Egyéni elemzőt](index-add-custom-analyzers.md)használ, definiálja az indexben a tokenizer felhasználó által definiált kombinációjában, amely a lehetséges konfigurációs beállításokkal rendelkezik. Ezután hivatkozzon egy mező-definícióra, ugyanúgy, mint a beépített elemző.

Ha a cél a teljes távú jogkivonatok létrehozása, a **kulcsszó tokenizer** és **kisbetűs jogkivonat-szűrőből** álló egyéni analizátor ajánlott.

+ A tokenizer kulcsszó egyetlen jogkivonatot hoz létre egy mező teljes tartalmához.
+ A kisbetűs jogkivonat szűrője a nagybetűket kis méretű szöveggé alakítja át. A lekérdezés-elemzők általában kisbetűsek a nagybetűs szöveg bemenetei. Az alsó szintű ház homogenizálja a bemeneteket a jogkivonatos feltételekkel.

Az alábbi példa egy egyéni elemzőt mutat be, amely a kulcsszó tokenizer és egy kisbetűs jogkivonat-szűrőt biztosít.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> A `keyword_v2` rendszer a `lowercase` tokenizer és a jogkivonat-szűrőt ismeri a rendszeren, és az alapértelmezett konfigurációját használja, ezért a név alapján hivatkozhat rájuk, anélkül, hogy először kellene megadnia őket.

## <a name="build-and-test"></a>Buildelés és tesztelés

Miután meghatározta a forgatókönyvét támogató elemzőket és mezőértékeket tartalmazó indexet, betöltheti a reprezentatív karakterláncokat tartalmazó dokumentumokat, hogy tesztelje a részleges karakterlánc-lekérdezéseket. 

Az előző szakaszban a logikát ismertetjük. Ez a szakasz végigvezeti az egyes API-k lépésein, amelyeket a megoldás tesztelésekor kell meghívnia. Ahogy korábban már említettük, ha interaktív webes tesztelési eszközt (például Poster) használ, gyorsan elvégezheti ezeket a feladatokat.

+ Az [index törlése](https://docs.microsoft.com/rest/api/searchservice/delete-index) eltávolítja az azonos nevű meglévő indexet, hogy újra létre lehessen hozni.

+ A [create index](https://docs.microsoft.com/rest/api/searchservice/create-index) létrehozza az index struktúrát a keresési szolgáltatásban, beleértve az analizátor-definíciókat és a Analyzer-specifikációval rendelkező mezőket.

+ A [betöltési dokumentumok](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) a dokumentumokat a tárgymutatóval megegyező struktúrával, valamint kereshető tartalommal importálják. A lépés után az index készen áll a lekérdezésre vagy a tesztelésre.

+ A [test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) bemutatkozott a [válasszon egy elemzőt](#choose-an-analyzer). Tesztelje az index egyes karakterláncait különböző elemzők használatával, hogy megtudja, hogyan történik a feltételek tokenje.

+ A [dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents) során megtudhatja, hogyan hozhat létre egy lekérdezési kérést [egyszerű szintaxissal](query-simple-syntax.md) vagy [teljes Lucene szintaxissal](query-lucene-syntax.md) helyettesítő karakterekkel és reguláris kifejezésekkel.

  A részleges lejáratú lekérdezéseknél, például a "3-6214" lekérdezésben a "+ 1 (425) 703-6214" kifejezésre való kereséshez használhatja az egyszerű szintaxist: `search=3-6214&queryType=simple`.

  A Infix és az utótag lekérdezéséhez, például a "NUM" vagy a "numerikus" lekérdezéshez az "alfanumerikus" kifejezéshez használja a teljes Lucene szintaxist és egy reguláris kifejezést:`search=/.*num.*/&queryType=full`

## <a name="tips-and-best-practices"></a>Tippek és ajánlott eljárások

### <a name="tune-query-performance"></a>Lekérdezési teljesítmény hangolása

Ha a keyword_v2 tokenizer és a kisbetűs jogkivonat-szűrőt tartalmazó ajánlott konfigurációt alkalmazza, a lekérdezés teljesítményének csökkenése miatt előfordulhat, hogy az indexben már meglévő jogkivonatok esetében a további jogkivonat-szűrő feldolgozása történik. 

Az alábbi példa egy [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) hozzáadásával teszi lehetővé, hogy az előtag gyorsabban illeszkedjen. További tokenek jönnek létre a 2-25 karakteres kombinációkban, amelyek karaktereket tartalmaznak: (nem csak MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). Az is elképzelhető, hogy a további jogkivonatok létrehozása nagyobb indexet eredményeznek.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Különböző elemzők használata az indexeléshez és a lekérdezések feldolgozásához

Az adatelemzők az indexelés során és a lekérdezés végrehajtása során hívhatók. Gyakran ugyanazt az elemzőt használja mindkettőhöz, de az egyes munkaterhelésekhez egyéni elemzőket is beállíthat. Az elemző felülbírálások egy `analyzers` szakasz [index definíciójában](https://docs.microsoft.com/rest/api/searchservice/create-index) vannak megadva, majd adott mezőkre hivatkoznak. 

Ha az indexelés során csak egyéni elemzésre van szükség, az egyéni elemzőt alkalmazhatja az indexelésre, és továbbra is használhatja a standard Lucene Analyzert (vagy más elemzőt) a lekérdezésekhez.

A szerepkör-specifikus elemzés megadásához megadhatja a mező tulajdonságait az alapértelmezett `indexAnalyzer` `searchAnalyzer` `analyzer` tulajdonság helyett az egyes beállításokhoz.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>További lépések

Ez a cikk azt ismerteti, hogyan járulnak hozzá az elemzők a problémák lekérdezéséhez és a lekérdezési problémák megoldásához. A következő lépésként tekintse meg az analizátorra gyakorolt hatást az indexeléssel és a lekérdezések feldolgozásával kapcsolatban. Különösen érdemes lehet a Retext API-t használni a jogkivonat-kimenet visszaküldéséhez, hogy pontosan lássuk, mit hoz létre az elemző az indexhez.

+ [Nyelvi elemzők](search-language-support.md)
+ [Az Azure Cognitive Searchban való szövegszerkesztés elemzői](search-analyzers.md)
+ [Szöveges API elemzése (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [A teljes szöveges keresés működése (lekérdezési architektúra)](search-lucene-query-architecture.md)