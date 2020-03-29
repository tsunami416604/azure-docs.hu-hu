---
title: Minták és speciális karakterek egyeztetése
titleSuffix: Azure Cognitive Search
description: Helyettesítő karakteres és előtag-lekérdezések használatával egyezik az Azure Cognitive Search lekérdezési kérelem teljes vagy részleges kifejezésekkel. A speciális karaktereket tartalmazó nehezen egyező minták teljes lekérdezésszintaxissal és egyéni elemzőkkel oldhatók meg.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: f78ba5b351a3da46d7b8b3780cf00772c4f3b2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289311"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Minták és speciális karakterek egyeztetése (kötőjelek)

A speciális karaktereket`-, *, (, ), /, \, =`( ), vagy egy nagyobb kifejezésen belüli részleges kifejezéseken alapuló lekérdezési minták esetében általában további konfigurációs lépésekre van szükség annak biztosításához, hogy az index a várt tartalmat tartalmazza a megfelelő formátumban. 

Alapértelmezés szerint egy telefonszám `+1 (425) 703-6214` tokenized `"1"`je: `"703"` `"6214"`, `"425"`, , . Képzelheti, hogy a `"3-62"`kötőjelet tartalmazó részleges kifejezésekre való keresés sikertelen lesz, mert az adott tartalom valójában nem létezik az indexben. 

Ha részleges karakterláncokon vagy speciális karaktereken kell keresnie, felülírhatja az alapértelmezett elemzőt egy egyszerűbb tokenizálási szabályok szerint működő egyéni analizátorral, megőrizve a teljes kifejezéseket, amelyek szükségesek, ha a lekérdezési karakterláncok egy kifejezés vagy speciális részrészeit tartalmazzák. Karakterek. Egy lépést hátra, a megközelítés így néz ki:

+ Válasszon egy előre definiált elemzőt, vagy definiáljon egy egyéni elemzőt, amely a kívánt kimenetet állítja elő
+ Az analizátor hozzárendelése a mezőhöz
+ Az index összeállítása és tesztelése

Ez a cikk végigvezeti ezeket a feladatokat. Az itt leírt megközelítés más esetekben is hasznos: helyettesítő karakteres és reguláris kifejezés lekérdezések is szükség van a teljes kifejezések, mint az alapja a minta egyeztetés. 

> [!TIP]
> Az analyerek kiértékelése olyan iteratív folyamat, amely gyakori index-újraépítést igényel. Ezt a lépést megkönnyítheti a Postman, a [Create Index,](https://docs.microsoft.com/rest/api/searchservice/create-index) [Delete Index,](https://docs.microsoft.com/rest/api/searchservice/delete-index)[Load Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)és [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents)REST API-k használatával. A Dokumentumok betöltése mezőben a kérelemtörzsnek tartalmaznia kell egy kis reprezentatív adatkészletet, amelyet tesztelni szeretne (például egy telefonszámokkal vagy termékkódokkal rendelkező mezővel). Ezekkel az API-kkal ugyanabban a Postman-gyűjteményben gyorsan végighaladhat ezeken a lépéseken.

## <a name="choosing-an-analyzer"></a>Analizátor kiválasztása

Amikor olyan elemzőt választ, amely teljes időtartamú jogkivonatokat hoz létre, a következő elemzők gyakori a következők:

| Elemző | Viselkedésmódok |
|----------|-----------|
| [Kulcsszó](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | A teljes mező tartalma egyetlen kifejezésként tokenálódik. |
| [Szóköz](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Csak a fehér tereken válik el. A kötőjeleket vagy más karaktereket tartalmazó kifejezéseket a csomag egyetlen jogkivonatként kezeli. |
| [egyéni elemző](index-add-custom-analyzers.md) | (ajánlott) Egyéni analizátor létrehozása lehetővé teszi, hogy adja meg a tokenizer és a token szűrő. Az előző analizátorokat a hogy-is kell használni. Az egyéni analizátor lehetővé teszi, hogy válassza ki, mely tokenizers és token szűrők használata. <br><br>Az ajánlott kombináció a [kisméretű tokenszűrővel](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)rendelkező [tokenizer kulcsszó.](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) Önmagában az előre definiált [kulcsszóelemző](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) nem kisbetűs kisbetűs szöveget, ami a lekérdezések sikertelensítéséhez vezethet. Az egyéni analizátor egy mechanizmust biztosít a kisbetűs jogkivonat-szűrő hozzáadásához. |

Ha egy webes API-teszteszközt használ, például postmant, hozzáadhatja a [Test Analyzer REST-hívást](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) a tokenizált kimenet vizsgálatához. Adott egy meglévő index és egy mező, amely kötőjelek vagy részleges kifejezések, kipróbálhatja a különböző elemzők több mint konkrét kifejezéseket, hogy milyen jogkivonatok bocsátanak ki.  

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
> Ne feledje, hogy a lekérdezéselemzők gyakran kisbetűs kifejezéseket a keresési kifejezésben a lekérdezési fa létrehozásakor. Ha olyan elemzőt használ, amely nem kisbetűs szövegbevitelt használ, és nem kapja meg a várt eredményeket, ez lehet az oka. A megoldás egy lwower-esettoken szűrő hozzáadása.

## <a name="analyzer-definitions"></a>Elemző definíciói
 
Akár analizátorokat értékel, akár egy adott konfigurációval halad előre, meg kell adnia az analizátort a meződefiníción, és esetleg magát az analizátort kell konfigurálnia, ha nem beépített analizátort használ. Az elemzők cseréjekor általában újra kell építenie az indexet (drop, recreate és reload). 

### <a name="use-built-in-analyzers"></a>Beépített analizátorok használata

A beépített vagy előre definiált elemzők név szerint `analyzer` megadhatók egy meződefiníció tulajdonságán, és nincs szükség további konfigurációra az indexben. A következő példa bemutatja, `whitespace` hogyan kell beállítani az analizátor egy mezőben.

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
Az összes elérhető beépített elemzőről az [Előre definiált elemzők listájában](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)talál további információt. 

### <a name="use-custom-analyzers"></a>Egyéni elemzők használata

Ha [egyéni analizátort](index-add-custom-analyzers.md)használ, definiálja azt az indexben a tokenizer, tokenszűrő felhasználó által definiált kombinációjával, a lehetséges konfigurációs beállításokkal. Ezután hivatkozzon egy meződefinícióra, ugyanúgy, mint egy beépített elemzőre.

Ha a cél a teljes távú tokenizálás, egy egyéni analizátor, amely egy **kulcsszó tokenizer** és **kis-nagybetűs token szűrő** ajánlott.

+ A tokenizer kulcsszó egyetlen jogkivonatot hoz létre a mező teljes tartalmához.
+ A kisbetűs tokenszűrő a kisbetűket kisbetűs szöveggé alakítja át. A lekérdezéselemzők általában kisbetűs, nagybetűs szövegbevitelt tartalmaznak. Csökkenti homogenizálja a bemenetek a tokenizált kifejezéseket.

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

### <a name="duplicate-fields-for-different-scenarios"></a>Ismétlődő mezők különböző forgatókönyvekhez

Egy másik lehetőség a mezőelemzői hozzárendelést használja a különböző forgatókönyvekhez való optimalizáláshoz. Pontosabban, lehet, hogy meghatározza a "featureCode" és a "featureCodeRegex", hogy támogassa a rendszeres teljes szöveges keresés az első, és a speciális minta megfelelő a második.

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

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatja, hogy az elemzők hogyan járulnak hozzá a lekérdezési problémákhoz és a lekérdezési problémák megoldásához. Következő lépésként tekintse meg közelebbről az indexelésre és a lekérdezésfeldolgozásra gyakorolt analizátorhatást. Különösen fontolja meg a Szöveg elemzése API-t a tokenizált kimenet visszaadására, hogy pontosan láthassa, mit hoz létre egy elemző az indexhez.

+ [Nyelvi elemzők](search-language-support.md)
+ [Elemzőelemzők szövegfeldolgozáshoz az Azure Cognitive Search szolgáltatásban](search-analyzers.md)
+ [Szöveg API elemzése (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [A teljes szöveges keresés működése (lekérdezési architektúra)](search-lucene-query-architecture.md)
