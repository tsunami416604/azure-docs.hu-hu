---
title: Részleges kifejezések, minták és speciális karakterek
titleSuffix: Azure Cognitive Search
description: Helyettesítő karakteres, regex és előtag-lekérdezések használatával egyezés teljes vagy részleges kifejezések et az Azure Cognitive Search lekérdezési kérelem. A speciális karaktereket tartalmazó nehezen egyező minták teljes lekérdezésszintaxissal és egyéni elemzőkkel oldhatók meg.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: db60a864ff29ff9eccdcfbdc0bd63587375d4bbd
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114969"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>Részleges kifejezésű keresés és különleges karakterekkel rendelkező minták (helyettesítő karakter, regex, minták)

A *részleges kifejezésű keresés* olyan lekérdezésekre utal, amelyek részlettöredékekből állnak, ahol a teljes kifejezés helyett előfordulhat, hogy csak a kifejezés kezdete, közepe vagy vége (más néven előtag, infix vagy utótag lekérdezés). A *minta* töredékek kombinációját eredményezheti, gyakran speciális karakterekkel, például kötőjelekkel vagy perjelekkel, amelyek a lekérdezési karakterlánc részét képezik. A gyakori használati esetek közé tartozik a telefonszám, URL, személyek vagy termékkódok, illetve összetett szavak részeinek lekérdezése.

A részleges és a mintakeresés akkor lehet problémás, ha az index nem rendelkezik a várt formátumú kifejezésekkel. Az indexelés [lexikális elemzési fázisában](search-lucene-query-architecture.md#stage-2-lexical-analysis) (feltéve, hogy az alapértelmezett szabványos analizátor) a speciális karakterek elvesznek, az összetett és összetett karakterláncok feloszlanak, és a szóköz törlődik; amelyek mindegyike a mintalekérdezések sikertelensedéséhez vezethet, ha nem található egyezés. Például egy telefonszám `+1 (425) 703-6214` (tokenizált `"1"`, `"425"` `"703"`, `"6214"`, , ) nem `"3-62"` jelenik meg a lekérdezésben, mert az adott tartalom valójában nem létezik az indexben. 

A megoldás egy olyan elemző meghívása, amely megőrzi a teljes karakterláncot, beleértve a szóközöket és szükség esetén speciális karaktereket, hogy részleges kifejezéseket és mintákat egyezhessen. A megoldás alapja egy ép karakterlánc kiegészítő mezőjének létrehozása, valamint tartalommegőrző elemző használata.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Mi a részleges keresés az Azure Cognitive Search-ben?

Az Azure Cognitive Search alkalmazásban a részleges keresés és a minta a következő űrlapokon érhető el:

+ [Előtag keresés](query-simple-syntax.md#prefix-search), `search=cap*`például , egyezés a "Cap'n Jack's Waterfront Inn" vagy a "Gacc Capital". Használhatja az egyszerű lekérdezés szintaxisát vagy a teljes Lucene lekérdezés szintaxisát az előtag kereséshez.

+ [Helyettesítő karakteres keresés](query-lucene-syntax.md#bkmk_wildcard) vagy [reguláris kifejezések,](query-lucene-syntax.md#bkmk_regex) amelyek egy beágyazott karakterlánc mintáját vagy részeit keresik. A helyettesítő karakter és a reguláris kifejezések teljes Lucene szintaxist igényelnek. Az utótagok és az indexlekérdezések reguláris kifejezésként vannak megfogalmazva.

  Néhány példa a részleges kifejezéskeresésre: a következők: Utótag-lekérdezés esetén az "alfanumerikus" kifejezés sel kapcsolatban helyettesítő`search=/.*numeric.*/`keresési ( ) használatával kereshet egyezést. Belső karaktereket, például URL-töredékeket tartalmazó részleges kifejezés esetén előfordulhat, hogy escape karaktereket kell hozzáadnia. A JSON-ban `/` az előremutató per `\`eltávozik egy hátrafelé irányuló perjellel. Így `search=/.*microsoft.com\/azure\/.*/` a "microsoft.com/azure/" URL-töredék szintaxisa.

Mint megjegyezte, a fentiek mindegyike megköveteli, hogy az index olyan formátumban tartalmazkarakterláncokat, amelyek elősegítik a mintaegyeztetést, amelyet a standard analizátor nem biztosít. A cikkben ismertetett lépéseket követve biztosíthatja, hogy a forgatókönyvek támogatásához szükséges tartalom létezik.

## <a name="solving-partialpattern-search-problems"></a>Részleges/mintakeresési problémák megoldása

Ha töredékeken vagy mintákon vagy speciális karaktereken kell keresnie, felülbírálhatja az alapértelmezett elemzőt egy egyszerűbb tokenizálási szabályok szerint működő egyéni analizátorral, megtartva az egész karakterláncot. Egy lépést hátra, a megközelítés így néz ki:

+ Mező definiálása a karakterlánc ép verziójának tárolásához (feltéve, hogy elemezni és nem elemzett szöveget szeretne)
+ Válasszon előre definiált analizátort, vagy definiáljon egyéni analizátort egy nem elemzett ép karakterlánc kimenetéhez
+ Egyéni elemző hozzárendelése a mezőhöz
+ Az index létrehozása és tesztelése

> [!TIP]
> Az analizátorok kiértékelése egy iteratív folyamat, amely gyakori index-újraépítést igényel. Ezt a lépést megkönnyítheti a Postman, a [Create Index,](https://docs.microsoft.com/rest/api/searchservice/create-index) [Delete Index,](https://docs.microsoft.com/rest/api/searchservice/delete-index)[Load Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)és [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents)REST API-k használatával. A Dokumentumok betöltése mezőben a kérelemtörzsnek tartalmaznia kell egy kis reprezentatív adatkészletet, amelyet tesztelni szeretne (például egy telefonszámokkal vagy termékkódokkal rendelkező mezővel). Ezekkel az API-kkal ugyanabban a Postman-gyűjteményben gyorsan végighaladhat ezeken a lépéseken.

## <a name="duplicate-fields-for-different-scenarios"></a>Ismétlődő mezők különböző forgatókönyvekhez

Az elemzők mezőnként vannak hozzárendelve, ami azt jelenti, hogy mezőket hozhat létre az indexben a különböző forgatókönyvekhez való optimalizáláshoz. Pontosabban, lehet, hogy meghatározza a "featureCode" és a "featureCodeRegex", hogy támogassa a rendszeres teljes szöveges keresés az első, és a speciális minta megfelelő a második.

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

## <a name="choose-an-analyzer"></a>Válasszon egy elemzőt

Amikor olyan elemzőt választ, amely teljes időtartamú jogkivonatokat hoz létre, a következő elemzők gyakori a következők:

| Elemző | Viselkedésmódok |
|----------|-----------|
| [Kulcsszó](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | A teljes mező tartalma egyetlen kifejezésként tokenálódik. |
| [Szóköz](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Csak a fehér tereken válik el. A kötőjeleket vagy más karaktereket tartalmazó kifejezéseket a csomag egyetlen jogkivonatként kezeli. |
| [egyéni elemző](index-add-custom-analyzers.md) | (ajánlott) Egyéni analizátor létrehozása lehetővé teszi, hogy adja meg a tokenizer és a token szűrő. Az előző analizátorokat a hogy-is kell használni. Az egyéni analizátor lehetővé teszi, hogy válassza ki, mely tokenizers és token szűrők használata. <br><br>Az ajánlott kombináció a [kisméretű tokenszűrővel](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)rendelkező [tokenizer kulcsszó.](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) Önmagában az előre definiált [kulcsszóelemző](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) nem kisbetűs kisbetűs szöveget, ami a lekérdezések sikertelensítéséhez vezethet. Az egyéni analizátor egy mechanizmust biztosít a kisbetűs jogkivonat-szűrő hozzáadásához. |

Ha egy webes API-teszteszközt használ, például postmant, hozzáadhatja a [Test Analyzer REST-hívást](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) a tokenizált kimenet vizsgálatához.

A munkához rendelkeznie kell egy meglévő indexszel. Adott egy meglévő index és egy mező, amely kötőjelek vagy részleges kifejezések, kipróbálhatja a különböző elemzők több mint konkrét kifejezéseket, hogy milyen jogkivonatok bocsátanak ki.  

1. Ellenőrizze a Standard analizátor, hogy hogyan kifejezések tokenized alapértelmezés szerint.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Értékelje ki a választ, hogy lássa, hogyan tokenized a szöveg et az indexen belül. Figyelje meg, hogy az egyes kifejezéseket hogyan lehet kisbetűsen felbontani és felbontani.

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
1. Módosítsa a kérelmet `whitespace` `keyword` a vagy analizátor használatára:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Most a válasz egyetlen jogkivonatból áll, nagybetűs, a kötőjelek a karakterlánc részeként megmaradnak. Ha egy mintában vagy részleges kifejezésben kell keresnie, a lekérdezési motor nak megvan az alapja az egyezés megtalálásához.


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
> Ne feledje, hogy a lekérdezéselemzők gyakran kisbetűs kifejezéseket a keresési kifejezésben a lekérdezési fa létrehozásakor. Ha olyan elemzőt használ, amely nem kisbetűs szövegbevitelt használ, és nem kapja meg a várt eredményeket, ez lehet az oka. A megoldás egy kisméretű jogkivonat-szűrő hozzáadása, az alábbi "Egyéni elemzők használata" című szakaszban leírtak szerint.

## <a name="configure-an-analyzer"></a>Analizátor konfigurálása
 
Akár analizátorokat értékel, akár egy adott konfigurációval halad előre, meg kell adnia az analizátort a meződefiníción, és esetleg magát az analizátort kell konfigurálnia, ha nem beépített analizátort használ. Az elemzők cseréjekor általában újra kell építenie az indexet (drop, recreate és reload). 

### <a name="use-built-in-analyzers"></a>Beépített analizátorok használata

A beépített vagy előre definiált elemzők név szerint `analyzer` megadhatók egy meződefiníció tulajdonságán, és nincs szükség további konfigurációra az indexben. A következő példa bemutatja, `whitespace` hogyan kell beállítani az analizátor egy mezőben. Az elérhető beépített elemzőkről az [Előre definiált elemzők listájában](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)talál további információt. 

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

Ha [egyéni analizátort](index-add-custom-analyzers.md)használ, definiálja azt az indexben a tokenizer, token szűrő, a lehetséges konfigurációs beállítások felhasználó által definiált kombinációjával. Ezután hivatkozzon egy meződefinícióra, ugyanúgy, mint egy beépített elemzőre.

Ha a cél a teljes távú tokenizálás, egy egyéni analizátor, amely egy **kulcsszó tokenizer** és **kis-nagybetűs token szűrő** ajánlott.

+ A tokenizer kulcsszó egyetlen jogkivonatot hoz létre a mező teljes tartalmához.
+ A kisbetűs tokenszűrő a kisbetűket kisbetűs szöveggé alakítja át. A lekérdezéselemzők általában kisbetűs, nagybetűs szövegbevitelt tartalmaznak. Az alsó burkolat homogenizálja a bemeneteket a tokenizált kifejezésekkel.

A következő példa egy egyéni elemzőt mutat be, amely a kulcsjogkivonat-szűrőt és egy kisbetűs jogkivonat-szűrőt biztosít.

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
> A `keyword_v2` tokenizer `lowercase` és a jogkivonat-szűrő ismert a rendszer, és az alapértelmezett konfigurációk használatával, ezért hivatkozhat rájuk név szerint anélkül, hogy először meg kell határoznia őket.

## <a name="build-and-test"></a>Buildelés és tesztelés

Miután definiált egy indexet a forgatókönyvet támogató elemzőkkel és meződefiníciókkal, töltsön be olyan dokumentumokat, amelyek reprezentatív karakterláncokkal rendelkeznek, így tesztelheti a részleges karakterlánc-lekérdezéseket. 

Az előző szakaszok magyarázták a logikát. Ez a szakasz végighalad az egyes API-kon, amelyeket meg kell hívnia a megoldás tesztelésekén. Mint korábban említettük, ha egy interaktív webes teszteszköz, mint a Postman, akkor végre ezeket a feladatokat gyorsan.

+ [Az Index törlése](https://docs.microsoft.com/rest/api/searchservice/delete-index) eltávolítja az azonos nevű meglévő indexet, így újra létrehozhatja azt.

+ [Az Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) létrehozza a keresési szolgáltatás indexstruktúráját, beleértve az analizátor definícióit és az analizátor specifikációval rendelkező mezőket.

+ [A Dokumentumok betöltése](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) importálja az indexével megegyező szerkezetű dokumentumokat, valamint a kereshető tartalmat. Ezt a lépést követően az index készen áll a lekérdezésre vagy tesztelésre.

+ [Test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) vezették be [Válasszon egy elemző](#choose-an-analyzer). Tesztelje az index néhány karakterláncát különböző elemzők használatával, hogy megértse, hogyan tokenálodtak a kifejezések.

+ [A Keresési dokumentumok dokumentum a](https://docs.microsoft.com/rest/api/searchservice/search-documents) lekérdezési kérelmek létrehozásának módját ismerteti egyszerű [szintaxissal](query-simple-syntax.md) vagy [teljes Lucene szintaxissal](query-lucene-syntax.md) helyettesítő és reguláris kifejezésekhez.

  Részleges lekérdezések esetén, például a "3-6214" lekérdezésével a "+1 (425) 703-6214" egyezésének megkereséséhez használhatja az egyszerű szintaxist: `search=3-6214&queryType=simple`.

  Az infix- és utótag-lekérdezések, például a "numerikus" lekérdezése az "alfanumerikus" egyezés kereséséhez használja a teljes Lucene szintaxist és egy reguláris kifejezést:`search=/.*num.*/&queryType=full`

## <a name="tips-and-best-practices"></a>Tippek és ajánlott eljárások

### <a name="tune-query-performance"></a>Lekérdezési teljesítmény hangolása

Ha az ajánlott konfiguráció, amely tartalmazza a keyword_v2 jogkivonatoló és a kis-tokentszűrő, előfordulhat, hogy a lekérdezési teljesítmény csökkenése miatt a további jogkivonat-szűrő feldolgozása a meglévő jogkivonatok az indexben. 

A következő példa egy [EdgeNGramTokenFilter-t](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) ad hozzá, hogy az előtag gyorsabb legyen. További tokenek jönnek létre a 2-25 karakter kombinációk, amelyek magukban foglalják a karaktereket: (nem csak MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/S, MSFT/SQ, MSFT/SQL). Képzelheti el, hogy a további tokenizálás nagyobb indexet eredményez.

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

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Különböző elemzők használata indexeléshez és lekérdezésfeldolgozáshoz

Az elemzők et indexelés és a lekérdezés végrehajtása során hívják meg. Gyakori, hogy ugyanazt az analizátort használja mindkét, de konfigurálhatja az egyéni elemzők minden számítási feladatokhoz. Az analizátor-felülírások egy `analyzers` szakasz [indexdefiníciójában](https://docs.microsoft.com/rest/api/searchservice/create-index) vannak megadva, majd adott mezőkre hivatkoznak. 

Ha az egyéni elemzés csak az indexelés során szükséges, alkalmazhatja az egyéni elemző csak indexelés, és továbbra is a szabványos Lucene-elemző (vagy más elemző) a lekérdezések.

A szerepkör-specifikus elemzés megadásához mindegyikhez beállíthat tulajdonságokat `indexAnalyzer` `searchAnalyzer` a mezőben, az alapértelmezett `analyzer` tulajdonságot és nem azt.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatja, hogy az elemzők hogyan járulnak hozzá a lekérdezési problémákhoz és a lekérdezési problémák megoldásához. Következő lépésként tekintse meg közelebbről az indexelésre és a lekérdezésfeldolgozásra gyakorolt analizátorhatást. Különösen fontolja meg a Szöveg elemzése API-t a tokenizált kimenet visszaadására, hogy pontosan láthassa, mit hoz létre egy elemző az indexhez.

+ [Nyelvi elemzők](search-language-support.md)
+ [Elemzőelemzők szövegfeldolgozáshoz az Azure Cognitive Search szolgáltatásban](search-analyzers.md)
+ [Szöveg API elemzése (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [A teljes szöveges keresés működése (lekérdezési architektúra)](search-lucene-query-architecture.md)