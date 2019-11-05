---
title: Nyelvi és szövegszerkesztés-elemzők
titleSuffix: Azure Cognitive Search
description: Az alapértelmezett standard Lucene egyéni, előre definiált vagy nyelvspecifikus alternatívákkal való helyettesítéséhez rendeljen elemzőket a kereshető szöveges mezőkhöz egy indexben.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 32ac91df042eb29c39cc54b738dbb96aff3104f3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496498"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Az Azure Cognitive Searchban való szövegszerkesztés elemzői

Az *elemző* a [teljes szöveges keresőmotor](search-lucene-query-architecture.md) összetevője, amely a lekérdezési karakterláncokban és az indexelt dokumentumokban lévő szövegek feldolgozásához felelős. A különböző elemzők a forgatókönyvtől függően különböző módokon kezelhetik a szöveget. A nyelvi elemzők a nyelvi szabályok segítségével dolgozzák fel a minőségi keresési minőséget, míg más elemzők több alapvető feladatot hajtanak végre, például a karakterek kis-és nagybetűkre való konvertálásakor. 

A nyelvi elemzők a leggyakrabban használatos, és az Azure Cognitive Search indexben található minden kereshető mezőhöz alapértelmezett nyelvi elemző van rendelve. A szöveg elemzése során a következő nyelvi átalakítások jellemzőek:

+ A nem lényeges szavakat (indexelendő) és a központozást a rendszer eltávolítja.
+ A kifejezések és az elválasztott szavak összetevőinek bontása.
+ A nagybetűs szavak kisebbek.
+ A szavakat a rendszer a legfelső szintű űrlapokra csökkenti, hogy a találatok a feszült időtől függetlenül is megtalálhatók legyenek.

A nyelvi elemzők olyan egyszerű vagy legfelső szintű űrlapokra alakítják át a szövegbeviteli adatokat, amelyek az információk tárolásához és lekéréséhez hatékonyak. A konverzió az indexelés során következik be, amikor a rendszer létrehoz egy indexet, majd a keresés során újra beolvassa az indexet. Nagyobb valószínűséggel kapja meg a várt keresési eredményeket, ha ugyanazt az elemzőt használja mindkét művelethez.

## <a name="default-analyzer"></a>Alapértelmezett elemző  

Az Azure Cognitive Search az [Apache Lucene standard Analyzert (standard Lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) használja alapértelmezettként, amely a ["Unicode Text szegmentálás"](https://unicode.org/reports/tr29/) szabályait követő elemekre bontja a szöveget. Emellett a standard Analyzer az összes karaktert a kisbetűs űrlapra konvertálja. Az indexelt dokumentumok és a keresési kifejezések az indexelés és a lekérdezés feldolgozása során is meghaladják az elemzést.  

Minden kereshető mező esetében automatikusan használatos. Az alapértelmezett mező felülbírálható a mezők alapján. Az alternatív elemzők lehetnek [nyelvi analizátorok](index-add-language-analyzers.md), [Egyéni analizátorok](index-add-custom-analyzers.md)vagy egy előre definiált elemzők az [elérhető elemzők listájából](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Elemzők típusai

Az alábbi lista ismerteti, hogy mely elemzők érhetők el az Azure Cognitive Searchban.

| Kategória | Leírás |
|----------|-------------|
| [Standard Lucene analizátor](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Default (Alapértelmezett): Nincs szükség specifikációra vagy konfigurációra. Ez az általános célú elemző a legtöbb nyelv és forgatókönyv esetében jól teljesít.|
| Előre definiált elemzők | A szolgáltatásként való használatra szánt késztermékként kínált termék. <br/>Két típus létezik: speciális és nyelvi. Mi teszi őket "előre definiált" értékre, ha név szerint hivatkozik rájuk, konfiguráció vagy testreszabás nélkül. <br/><br/>A [speciális (nyelv – agnosztikus) elemzők](index-add-custom-analyzers.md#AnalyzerTable) akkor használatosak, ha a szöveges bemenetek speciális feldolgozást vagy minimális feldolgozást igényelnek. A nem nyelvi előre definiált elemzők közé tartozik a **Asciifolding**, a **kulcsszó**, a **minta**, az **egyszerű**, a **Leállítás**és a **szóközök**.<br/><br/>[Nyelvi elemzőket](index-add-language-analyzers.md) akkor kell használni, ha az egyes nyelvekhez széles körű nyelvi támogatásra van szükség. Az Azure Cognitive Search támogatja a 35 Lucene Language Analyzers és a 50 Microsoft Natural Language Processing-elemzőt. |
|[Egyéni elemzők](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | A meglévő elemek kombinációjának felhasználó által definiált konfigurációját jelöli, amely egy tokenizer (kötelező) és opcionális szűrőket (char vagy token) tartalmaz.|

Néhány előre definiált elemző, mint például a **minta** vagy a **Leállítás**, a konfigurációs beállítások korlátozott készletét támogatja. Ezen beállítások megadásához hatékonyan hozzon létre egy egyéni elemzőt, amely az előre definiált analizátorból és az [előre definiált Analyzer-referenciában](index-add-custom-analyzers.md#AnalyzerTable)dokumentált alternatív beállításokból áll. Ahogy az egyéni konfigurációk esetében is, adja meg az új konfigurációt egy névvel, például *myPatternAnalyzer* , hogy megkülönböztesse azt a Lucene Pattern analyzerből.

## <a name="how-to-specify-analyzers"></a>Elemzők meghatározása

1. (csak egyéni elemzők esetén) Hozzon létre egy nevesített **analizátor** szakaszt az index definíciójában. További információ: [index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) és [Egyéni elemzők hozzáadása](index-add-custom-analyzers.md).

2. Az index egyik [mezőjében](https://docs.microsoft.com/rest/api/searchservice/create-index) állítsa be a mező **Analyzer** tulajdonságát egy cél-elemző nevére (például `"analyzer" = "keyword"`. Az érvényes értékek közé tartozik az előre definiált analizátor, a Language Analyzer vagy az egyéni elemző neve is az index sémában. Tervezze meg az analizátor hozzárendelését az index definíciós fázisában, mielőtt létrehozza az indexet a szolgáltatásban.

3. A **indexAnalyzer** és a **searchAnalyzer** mező paramétereinek használatával **különböző elemzőket** is beállíthat az indexeléshez és lekérdezésekhez. Az adatok előkészítéséhez és lekéréséhez különböző elemzőket kellene használni, ha az egyik tevékenységhez szükséges egy adott átalakítás, amelyet a másik nem igényel.

Az **analizátor** vagy a **indexAnalyzer** egy már fizikailag létrehozott mezőhöz való hozzárendelésének engedélyezése nem engedélyezett. Ha a fentiek bármelyike nem egyértelmű, tekintse át a következő táblázatot annak részletezéséhez, hogy mely műveletek szükségesek a helyreállításhoz, és miért.
 
 | Alkalmazási helyzet | Hatás | Lépések |
 |----------|--------|-------|
 | Új mező hozzáadása | Minimális | Ha a mező még nem létezik a sémában, nem végezhető el a mező módosítása, mert a mező még nem rendelkezik fizikai jelenléttel az indexben. A [frissítési index](https://docs.microsoft.com/rest/api/searchservice/update-index) használatával új mezőket adhat hozzá egy meglévő indexhez, és [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) a feltöltéshez.|
 | **Elemző** vagy **indexAnalyzer** hozzáadása egy meglévő indexelt mezőhöz. | [újraépítése](search-howto-reindex.md) | Az adott mező fordított indexét újra létre kell hozni az alapoktól, és a mezők tartalmát újra kell indexelni. <br/> <br/>Az aktív fejlesztés alatt álló indexek esetében [törölje](https://docs.microsoft.com/rest/api/searchservice/delete-index) és [hozza létre](https://docs.microsoft.com/rest/api/searchservice/create-index) az indexet az új mező definíciójának kiválasztásához. <br/> <br/>Az éles környezetben lévő indexek esetében egy új mező létrehozásával elhalaszthatja az újraépítést, és megkezdheti a módosítást, és a régi helyett használhatja azt. A [frissítési index](https://docs.microsoft.com/rest/api/searchservice/update-index) használatával vegye fel az új mezőt és a [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) a feltöltéshez. Később, a tervezett indexek karbantartásának részeként törölheti az indexet az elavult mezők eltávolításához. |

## <a name="when-to-add-analyzers"></a>Mikor lehet elemzőket felvenni

Az adatelemzők hozzáadásához és hozzárendeléséhez a legjobb idő az aktív fejlesztés során, amikor az indexek eldobása és újbóli létrehozása rutinos.

Ahogy az index definíciója megszilárdul, új elemzési szerkezeteket lehet hozzáfűzni egy indexhez, de a **allowIndexDowntime** jelzőt át kell adni az [index frissítéséhez](https://docs.microsoft.com/rest/api/searchservice/update-index) , ha el szeretné kerülni ezt a hibát:

*"Az index frissítése nem engedélyezett, mert az állásidőt okoz. Ahhoz, hogy új elemzőket, tokenizers, jogkivonat-szűrőket vagy karakterstílusokat adjon hozzá egy meglévő indexhez, állítsa a "allowIndexDowntime" lekérdezési paramétert "true" értékre az index frissítési kérelmében. Vegye figyelembe, hogy ez a művelet legalább néhány másodpercig offline állapotba helyezi az indexet, így az indexelés és a lekérdezési kérelmek sikertelenek lesznek. Az index teljesítményének és írásának rendelkezésre állása az index frissítése után több percig is eltarthat, vagy a nagyon nagy indexek esetében már nem. "*

Ugyanez igaz, ha az analizátort egy mezőhöz rendeli. Az analizátor a mező definíciójának szerves részét képezi, így csak a mező létrehozásakor adható hozzá. Ha a meglévő mezőkhöz is hozzá kívánja adni a elemzőket, el kell [dobnia és újra kell építenie](search-howto-reindex.md) az indexet, vagy hozzá kell adnia egy új mezőt a kívánt elemzőhöz.

Ahogy azt említettük, kivételt jelent a **searchAnalyzer** -változat. Az elemzők (**Analyzer**, **indexAnalyzer**, **searchAnalyzer**) megadásának három módja közül csak a **searchAnalyzer** attribútum módosítható egy meglévő mezőben.

## <a name="recommendations-for-working-with-analyzers"></a>Az elemzők használatának javaslatai

Ez a szakasz az elemzők használatáról nyújt útmutatást.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Egyetlen elemző az írható-olvasható, hacsak nem rendelkezik konkrét követelményekkel

Az Azure Cognitive Search lehetővé teszi különböző elemzők megadását az indexeléshez és a kereséshez további **indexAnalyzer** és **searchAnalyzer** mezők paramétereinek használatával. Ha nincs megadva, az **Analyzer tulajdonsággal** beállított analizátor az indexeléshez és a kereséshez is használatos. Ha `analyzer` nincs meghatározva, a rendszer az alapértelmezett standard Lucene Analyzert használja.

Az általános szabály az, hogy ugyanazt az elemzőt használja mind az indexeléshez, mind a lekérdezéshez, kivéve, ha a konkrét követelmények másképp nem rendelkeznek. Ügyeljen arra, hogy alaposan tesztelje. Ha a szöveg feldolgozása a keresés és az indexelés során különbözik, akkor a lekérdezési feltételek és az indexelt kifejezések közötti eltérés kockázata nem egyezik meg, ha a keresési és az indexelési elemző konfigurációja nincs igazítva.

### <a name="test-during-active-development"></a>Tesztelés az aktív fejlesztés során

A standard Analyzer felülbírálásához index-Újraépítés szükséges. Ha lehetséges, döntse el, hogy mely elemzőket szeretné használni az aktív fejlesztés során, mielőtt az indexet az éles környezetbe helyezné.

### <a name="inspect-tokenized-terms"></a>Jogkivonatos kifejezések vizsgálata

Ha a keresés sikertelen a várt eredmények visszaadására, a legvalószínűbb forgatókönyv a lekérdezésben szereplő lejárati és az indexben található jogkivonatok közötti eltérés. Ha a jogkivonatok nem egyeznek, a egyezések nem fognak megvalósulni. A tokenizer-kimenet vizsgálatához javasolt az [API elemzése](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) vizsgálati eszközként. A válasz egy adott elemző által generált jogkivonatokból áll.

<a name="examples"></a>

## <a name="rest-examples"></a>REST-példák

Az alábbi példák az analizátor-definíciókat mutatják be néhány fő forgatókönyv esetében.

+ [Egyéni analizátor – példa](#Custom-analyzer-example)
+ [Elemzők kiosztása például egy mezőhöz](#Per-field-analyzer-assignment-example)
+ [Elemzők keverése az indexeléshez és a kereséshez](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Language Analyzer – példa](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Egyéni analizátor – példa

Ez a példa egy Analyzer-definíciót mutat be egyéni beállításokkal. A char szűrők, a tokenizers és a jogkivonat-szűrők egyéni beállításai külön vannak megadva, mint nevesített szerkezetek, majd a rendszer az analizátor definíciójában hivatkozik rá. Az előre definiált elemeket a rendszer a (z) értékre használja, és egyszerűen hivatkozik a név alapján.

A következő példa:

* Az elemzők egy kereshető mező mező osztályának tulajdonsága.
* Az egyéni elemző egy index definíciójának része. Lehet, hogy ez a beállítás egyedileg testreszabható (például egyetlen lehetőség testreszabása egy szűrőben), vagy több helyen is testreszabható.
* Ebben az esetben az egyéni analizátor "my_analyzer", amely egy testreszabott szabványos tokenizer "my_standard_tokenizer" és két jogkivonat-szűrőt használ: kisbetűs és testreszabott asciifolding szűrő "my_asciifolding".
* Emellett 2 egyéni char-szűrőt is definiál: "map_dash" és "remove_whitespace". Az első lecseréli az aláhúzással ellátott kötőjeleket, míg a másodikban az összes szóköz el lett távolítva. A szóközöknek UTF-8 kódolással kell rendelkezniük a leképezési szabályokban. A char szűrők a jogkivonatok létrehozása előtt lesznek alkalmazva, és hatással vannak az eredményül kapott jogkivonatokra (a normál tokenizer kötőjeleken és szóközökön, de nem aláhúzáson).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Felhasználónkénti elemző-hozzárendelési példa

A standard Analyzer az alapértelmezett. Tegyük fel, hogy az alapértelmezett értéket egy másik előre definiált elemzővel szeretné lecserélni, például a minta-elemzőt. Ha nem állít be egyéni beállításokat, csak név alapján kell megadnia a mező definíciójában.

A "Analyzer" elem felülbírálja a standard elemzőt egy mező – mező alapján. Nincs globális felülbírálás. Ebben a példában a `text1` a minta analizátort használja, és `text2`, amely nem ad meg elemzőt, az alapértelmezett értéket használja.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Elemzők keverése az indexeléshez és a keresési műveletekhez

Az API-k további index-attribútumokkal rendelkeznek a különböző elemzők indexeléshez és kereséshez való megadásához. A **searchAnalyzer** és a **indexAnalyzer** attribútumot párosítva kell megadni, az egyetlen **elemző** attribútum helyett.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Language Analyzer – példa

A különböző nyelvű karakterláncokat tartalmazó mezők nyelvi elemzőt is használhatnak, míg más mezők megőrzik az alapértelmezett értéket (vagy más előre definiált vagy egyéni elemzőt használnak). Ha nyelvi elemzőt használ, azt az indexelési és a keresési műveletekhez is használni kell. A Language Analyzert használó mezők nem rendelkezhetnek különböző elemzővel az indexeléshez és a kereséshez.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="c-examples"></a>C#Példák

Ha a .NET SDK-kód mintáit használja, ezeket a példákat az elemzők használatára vagy konfigurálására használhatja.

+ [Beépített analizátor kiosztása](#Assign-a-language-analyzer)
+ [Analizátor konfigurálása](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Nyelvi elemző kijelölése

Bármely, konfiguráció nélkül használt analizátor meg van adva egy mező definíciójában. A Analyzer-szerkezet létrehozásához nincs szükség. 

Ez a példa a Microsoft angol és francia elemzőit rendeli hozzá a Description (Leírás) mezőkhöz. Ez egy olyan kódrészlet, amely a [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) minta Hotels.cs fájljában a Hotel osztály használatával jön létre.

Hívja meg az [analizátort](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), adja meg az Azure Cognitive Search által támogatott [AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) -típust.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Egyéni analizátor definiálása

Ha testreszabásra vagy konfigurálásra van szükség, hozzá kell adnia egy Analyzer-szerkezetet egy indexhez. A Definiálás után hozzáadhatja a mező definícióját az előző példában bemutatott módon.

Hozzon létre egy [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) objektumot. További Példákért lásd: [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>További lépések

+ Tekintse át a [teljes szöveges keresés az Azure Cognitive Search-ban való működésének](search-lucene-query-architecture.md)részletes leírását. Ez a cikk példákat használ az olyan viselkedések magyarázatára, amelyek a felületen intuitív módon jelenhetnek meg.

+ További lekérdezési szintaxist a [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) example szakasz vagy az [egyszerű lekérdezési szintaxis](query-simple-syntax.md) a Search Explorerben a portálon.

+ Megtudhatja, hogyan alkalmazhat [nyelvi specifikus lexikális elemzőket](index-add-language-analyzers.md).

+ [Egyéni elemzők konfigurálása](index-add-custom-analyzers.md) az egyes mezők minimális feldolgozásához vagy speciális feldolgozásához.

## <a name="see-also"></a>Lásd még

 [Dokumentumok keresése REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Egyszerű lekérdezési szintaxis](query-simple-syntax.md) 

 [Teljes Lucene lekérdezési szintaxis](query-lucene-syntax.md) 
 
 [A keresési eredmények kezelése](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
