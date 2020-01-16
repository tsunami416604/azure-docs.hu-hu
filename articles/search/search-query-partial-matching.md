---
title: Minták és speciális karakterek egyeztetése
titleSuffix: Azure Cognitive Search
description: A helyettesítő karakterek és az előtag-lekérdezések használata az Azure Cognitive Search lekérdezési kérelmében szereplő teljes vagy részleges kifejezéseknek megfelelően. A speciális karaktereket tartalmazó, nehezen egyeztethető minták teljes lekérdezési szintaxissal és egyéni elemzővel oldhatók fel.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ec1422d03cce78bdd8206f6687a78b63ddf989dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989617"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Mintázatok és speciális karakterek (kötőjelek) egyeztetése

A speciális karaktereket (`-, *, (, ), /, \, =`) tartalmazó lekérdezések, illetve a részleges feltételeken alapuló lekérdezési minták esetében a további konfigurációs lépések általában szükségesek ahhoz, hogy az index a várt tartalmat a megfelelő formátumban tartalmazza. 

Alapértelmezés szerint egy telefonszám, például az `+1 (425) 703-6214` `"1"`, `"425"`, `"703"`, `"6214"`. Elképzelhető, hogy a `"3-62"`, a kötőjelet tartalmazó részleges kifejezésekre való keresés sikertelen lesz, mert a tartalom valójában nem létezik az indexben. 

Ha részleges karakterláncokat vagy speciális karaktereket kell keresnie, felülbírálhatja az alapértelmezett elemzőt egy olyan egyéni elemzővel, amely egyszerűbb jogkivonatok létrehozása-szabályok alatt működik, a teljes feltételek megőrzéséhez szükséges, ha a lekérdezési karakterláncok egy kifejezés vagy egy speciális alkatrészt tartalmaznak. karaktereket. A lépés a következőképpen néz ki:

+ Válasszon egy előre definiált elemzőt, vagy határozzon meg egy egyéni elemzőt, amely a kívánt kimenetet hozza létre
+ Az analizátor kiosztása a mezőhöz
+ Az index és a teszt létrehozása

Ez a cikk végigvezeti ezeket a feladatokat. Az itt leírt módszer más forgatókönyvekben hasznos: a helyettesítő karakteres és reguláris kifejezéses lekérdezésekhez a mintázat megfeleltetésének alapjaként is szükség van a teljes feltételekre. 

> [!TIP]
> A analyers kiértékelése olyan iterációs folyamat, amely gyakori index-újraépítést igényel. Ezt a lépést a Poster, a REST API-k [létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index), az indexek [törlése](https://docs.microsoft.com/rest/api/searchservice/delete-index), a[dokumentumok betöltése](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)és a dokumentumok [keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents)során egyszerűbbé teheti. A betöltési dokumentumok esetében a kérelem törzsének tartalmaznia kell egy kis reprezentatív adatkészletet, amelyet szeretne tesztelni (például egy telefonszámot vagy Termékkód számot tartalmazó mező). Ha ezekkel az API-kkal ugyanabban a Poster-gyűjteményben vannak, gyorsan elvégezheti ezeket a lépéseket.

## <a name="choosing-an-analyzer"></a>Analizátor kiválasztása

Ha olyan elemzőt választ ki, amely teljes körű jogkivonatokat állít elő, a következő elemzők gyakoriak:

| Analyzer | Viselkedés |
|----------|-----------|
| [kulcsszó](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | A teljes mező tartalma egyetlen kifejezésként van jogkivonat. |
| [szóköz](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Csak szóközöket választ ki. A kötőjeleket vagy más karaktereket tartalmazó kifejezéseket egyetlen jogkivonatként kezeli a rendszer. |
| [Egyéni analizátor](index-add-custom-analyzers.md) | ajánlott Az egyéni elemző létrehozása lehetővé teszi a tokenizer és a jogkivonat-szűrő megadását is. A korábbi elemzőket a következőképpen kell használni:. Az egyéni elemző lehetővé teszi, hogy kiválassza a használni kívánt tokenizers és jogkivonat-szűrőket. <br><br>Az ajánlott kombináció a [tokenizer kulcsszó](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) , amely egy [kisbetűs jogkivonat-szűrővel](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)rendelkezik. Önmagában az előre definiált [kulcsszó-elemző](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) nem kisbetűs, így a lekérdezések sikertelenek lehetnek. Az egyéni elemző egy mechanizmust biztosít az alsó szintű jogkivonat-szűrő hozzáadásához. |

Ha webes API-teszt eszközt (például Poster) használ, hozzáadhatja a [test Analyzer-Rest-hívást](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) a jogkivonat-kimenet vizsgálatához. A meglévő indexek és a kötőjeleket vagy a részleges kifejezéseket tartalmazó mezők esetében különböző elemzőket lehet kipróbálni a megadott feltételek alapján, hogy megtudja, milyen jogkivonatok vannak kibocsátva.  

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
1. A `whitespace` vagy a `keyword` Analyzer használatára vonatkozó kérelem módosítása:

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
> Ügyeljen arra, hogy a lekérdezés-elemzők a lekérdezési fa létrehozásakor gyakran alacsonyabb eseti kifejezéseket adjanak a keresési kifejezésekben. Ha olyan elemzőt használ, amely nem a kis-és nagybetűket használja, és nem várt eredményeket kap, ez az oka lehet. A megoldás egy lwower token-szűrő hozzáadása.

## <a name="analyzer-definitions"></a>Elemzői definíciók
 
Függetlenül attól, hogy az elemzőket kiértékeli, vagy egy adott konfigurációval halad előre, meg kell adnia az analizátort a mező definíciójában, és saját maga is konfigurálnia kell, ha nem beépített elemzőt használ. Az elemzők cseréjekor általában újra kell építeni az indexet (eldobás, újbóli létrehozás és újratöltés). 

### <a name="use-built-in-analyzers"></a>Beépített elemzők használata

A beépített vagy előre definiált elemzők név szerint adhatók meg egy mező definíciójának `analyzer` tulajdonságában, és az indexben nem szükséges további konfiguráció. Az alábbi példa azt szemlélteti, hogyan állíthatja be a `whitespace` Analyzert egy mezőre.

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
További információ az összes elérhető beépített elemzőről: [előre definiált elemzők listája](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

### <a name="use-custom-analyzers"></a>Egyéni elemzők használata

Ha [Egyéni elemzőt](index-add-custom-analyzers.md)használ, definiálja az indexben a tokenizer, a tokenfilter és a lehetséges konfigurációs beállításokkal rendelkező felhasználó által definiált kombinációval. Ezután hivatkozzon egy mező-definícióra, ugyanúgy, mint a beépített elemző.

Ha a cél a teljes távú jogkivonatok létrehozása, a **kulcsszó tokenizer** és **kisbetűs jogkivonat-szűrőből** álló egyéni analizátor ajánlott.

+ A tokenizer kulcsszó egyetlen jogkivonatot hoz létre egy mező teljes tartalmához.
+ A kisbetűs jogkivonat szűrője a nagybetűket kis méretű szöveggé alakítja át. A lekérdezés-elemzők általában kisbetűsek a nagybetűs szöveg bemenetei. A Lowercasing homogenizálja a bemeneteket a jogkivonatos feltételekkel.

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
]

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
> A rendszer az `keyword_v2` tokenizer és `lowercase` jogkivonat-szűrőt ismeri a rendszeren, és az alapértelmezett konfigurációját használja, ezért a név alapján hivatkozhat rájuk, anélkül, hogy először kellene megadnia őket.

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
]

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

Az adatelemzők az indexelés során és a lekérdezés végrehajtása során hívhatók. Gyakran ugyanazt az elemzőt használja mindkettőhöz, de az egyes munkaterhelésekhez egyéni elemzőket is beállíthat. Az analizátor-felülbírálások a `analyzers` szakasz [index definíciójában](https://docs.microsoft.com/rest/api/searchservice/create-index) vannak megadva, majd adott mezőkre hivatkoznak. 

Ha az indexelés során csak egyéni elemzésre van szükség, az egyéni elemzőt alkalmazhatja az indexelésre, és továbbra is használhatja a standard Lucene Analyzert (vagy más elemzőt) a lekérdezésekhez.

A szerepkör-specifikus elemzések megadásához megadhatja a mező tulajdonságait az alapértelmezett `analyzer` tulajdonság helyett a `indexAnalyzer` és az `searchAnalyzer` beállításával.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>Ismétlődő mezők különböző forgatókönyvekhez

Egy másik lehetőség kihasználja a különböző forgatókönyvek optimalizálására szolgáló felhasználónkénti elemző-hozzárendelést. Pontosabban megadhatja a "featureCode" és a "featureCodeRegex" karakterláncot, hogy támogassa a normál teljes szöveges keresést az első, és a speciális minta egyeztetését a másodikban.

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
  "analyzer": "my_customanalyzer"
},
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk azt ismerteti, hogyan járulnak hozzá az elemzők a problémák lekérdezéséhez és a lekérdezési problémák megoldásához. A következő lépésként tekintse meg az analizátorra gyakorolt hatást az indexeléssel és a lekérdezések feldolgozásával kapcsolatban. Különösen érdemes lehet a Retext API-t használni a jogkivonat-kimenet visszaküldéséhez, hogy pontosan lássuk, mit hoz létre az elemző az indexhez.

+ [Nyelvi elemzők](search-language-support.md)
+ [Az Azure Cognitive Searchban való szövegszerkesztés elemzői](search-analyzers.md)
+ [Szöveges API elemzése (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [A teljes szöveges keresés működése (lekérdezési architektúra)](search-lucene-query-architecture.md)