---
title: Elemző készülékek nyelvi és szövegfeldolgozáshoz
titleSuffix: Azure Cognitive Search
description: Az indexkereshető szövegmezőihez rendelhet elemzőket, hogy az alapértelmezett normál Lucene-t egyéni, előre definiált vagy nyelvspecifikus alternatívákkal helyettesítse.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 2e4a6ab8825982969ffa4654c2418f7a9d168d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460717"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Elemzőelemzők szövegfeldolgozáshoz az Azure Cognitive Search szolgáltatásban

Az *analizátor* a [teljes szöveges keresőmotor](search-lucene-query-architecture.md) összetevője, amely a lekérdezési karakterláncokban és az indexelt dokumentumokban lévő szöveg feldolgozásáért felelős. A különböző elemzők a forgatókönyvtől függően különböző módon manipulálják a szöveget. A nyelvi elemzők nyelvi szabályok használatával dolgozzák fel a szöveget a keresési minőség javítása érdekében, míg más elemzők több alapvető feladatot végeznek el, például a karakterek kisbetűssé alakítását. 

Nyelvi elemzők a leggyakrabban használt, és van alapértelmezett nyelvi analizátor hozzárendelt minden kereshető mező egy Azure Cognitive Search index. A szövegelemzés során a következő nyelvi átalakítások jellemzőek:

+ A nem lényeges szavak (stopwords) és az írásjelek törlődnek.
+ A kifejezések és az elválasztott szavak összetevőire vannak bontva.
+ A kisbetűs szavak kisbetűsek.
+ A szavak gyökéralakra redukálódnak, így a párapáróktól függetlenül megtalálhatók.

A nyelvi elemzők a szövegbevitelt primitív vagy gyökérformákká alakítják, amelyek hatékonyak az információk tárolásához és visszakereséséhez. A konverzió az indexelés során, az index létrehozásakor, majd az index olvasásakor a keresés során történik. Nagyobb valószínűséggel kapja meg a várt keresési eredményeket, ha ugyanazt az elemzőt használja mindkét művelethez.

## <a name="default-analyzer"></a>Alapértelmezett elemző  

Az Azure Cognitive Search az [Apache Lucene Standard analizátort (standard lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) használja alapértelmezettként, amely a szöveget a ["Unicode text segmentation"](https://unicode.org/reports/tr29/) szabályokat követő elemekre bontja. Ezenkívül a standard analizátor az összes karaktert kisbetűs formába konvertálja. Mind az indexelt dokumentumok, mind a keresési kifejezések az indexelés és a lekérdezésfeldolgozás során az elemzésen mennek keresztül.  

Minden kereshető mezőben automatikusan használják. Az alapértelmezett értéket mezőenként felülbírálhatja. Az alternatív elemzők lehetnek [nyelvi analizátor](index-add-language-analyzers.md), [egyéni analizátor,](index-add-custom-analyzers.md)vagy előre definiált analizátor a [rendelkezésre álló elemzők listájából.](index-add-custom-analyzers.md#AnalyzerTable)


## <a name="types-of-analyzers"></a>Az analizátorok típusai

Az alábbi lista ismerteti, hogy mely elemzők érhetők el az Azure Cognitive Search szolgáltatásban.

| Kategória | Leírás |
|----------|-------------|
| [Standard Lucene analizátor](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Default (Alapértelmezett): Nincs szükség specifikációra vagy konfigurációra. Ez az általános célú analizátor a legtöbb nyelven és forgatókönyvben jól teljesít.|
| Előre definiált analizátorok | Késztermékként kínálva, ahogy van. <br/>Két típusa van: speciális és nyelv. Mi teszi őket "előre meghatározott", hogy hivatkozik rájuk név szerint, nem konfiguráció vagy testreszabás. <br/><br/>[Speciális (nyelv-agnosztikus) analizátorok](index-add-custom-analyzers.md#AnalyzerTable) at használnak, ha a szövegbemenetek speciális feldolgozást vagy minimális feldolgozást igényelnek. Nem nyelvi előre definiált elemzők közé **Tartozik Asciifolding**, **Kulcsszó,** **Minta,** **Egyszerű,** **Stop**, **Whitespace**.<br/><br/>[A nyelvi elemzők](index-add-language-analyzers.md) akkor használatosak, ha az egyes nyelvek hez gazdag nyelvi támogatásra van szüksége. Az Azure Cognitive Search 35 Lucene nyelvi elemzőt és 50 Microsoft természetes nyelvi feldolgozási elemzőt támogat. |
|[Egyéni elemzők](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | A meglévő elemek kombinációjának felhasználó által definiált konfigurációjára utal, amely egy tokenizerből (kötelező) és választható szűrőkből (karakter vagy jogkivonat) áll.|

Néhány előre definiált elemzők, mint például **a Minta** vagy **a Stop**, támogatja a korlátozott számú konfigurációs beállításokat. Ezeknek a beállításoknak a beállításához hatékonyan hozhat létre egy egyéni elemzőt, amely az előre definiált elemzőből és az [Előre definiált elemző hivatkozás](index-add-custom-analyzers.md#AnalyzerTable)ban dokumentált alternatív lehetőségek egyikéből áll. Mint minden egyéni konfiguráció, adja meg az új konfiguráció egy nevet, például *a myPatternAnalyzer* megkülönböztetni a Lucene Pattern analizátor.

## <a name="how-to-specify-analyzers"></a>Az analizátorok megadása

1. (csak egyéni elemzők esetén) Hozzon létre egy elnevezett **elemző** szakaszt az indexdefinícióban. További információt az [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) és egyéni elemzők [hozzáadása](index-add-custom-analyzers.md)című témakörben talál.

2. Az index [egyik meződefinícióján](https://docs.microsoft.com/rest/api/searchservice/create-index) állítsa a mező **elemzőtulajdonságát** egy célelemző nevére `"analyzer" = "keyword"`(például . Az érvényes értékek közé tartozik egy előre definiált elemző, nyelvi elemző vagy az indexsémában is definiált egyéni elemző neve. Tervezze meg, hogy az indexdefiníciós fázisban hozzárendeli az analizátort, mielőtt az index létrejön a szolgáltatásban.

3. Opcionálisan egy **elemző** tulajdonság helyett különböző elemzőket állíthat be az indexanalyzer és a **searchAnalyzer** mező paramétereinek használatával az indexanalyzer és a **searchAnalyzer** mező paramétereivel. Az adatok előkészítéséhez és lekéréséhez különböző elemzőket kell használnia, ha az egyik ilyen tevékenység hez a másik által nem szükséges adott átalakítás szükséges.

> [!NOTE]
> Indexeléskor nem lehet más [nyelvi elemzőt](index-add-language-analyzers.md) használni, mint egy mező lekérdezési idején. Ez a képesség az [egyéni elemzők](index-add-custom-analyzers.md)számára van fenntartva. Emiatt, ha megpróbálja beállítani a **searchAnalyzer** vagy **indexAnalyzer** tulajdonságait egy nyelvi elemző nevére, a REST API egy hibaválaszt ad vissza. Ehelyett az **analizátor tulajdonságot kell használnia.**

Az **analizátor** vagy **az indexanalyzer** hozzárendelése egy már fizikailag létrehozott mezőhöz nem engedélyezett. Ha ezek bármelyike nem egyértelmű, tekintse át az alábbi táblázatban annak részletezését, hogy mely műveletek igényelnek újraépítést, és miért.
 
 | Forgatókönyv | Hatás | Lépések |
 |----------|--------|-------|
 | Új mező hozzáadása | Minimális | Ha a mező még nem létezik a sémában, nincs mezőmódosítás, mert a mező nek még nincs fizikai jelenléte az indexben. [Az Index frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) segítségével új mezőt adhat egy meglévő indexhez, és [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) segítségével feltöltheti azt.|
 | Adjon hozzá egy **elemzőt** vagy **indexanalyzeregy** meglévő indexelt mezőt. | [Újjáépíteni](search-howto-reindex.md) | A mező fordított indexét az alapoktól kezdve újra létre kell hozni, és a mezők tartalmát újra indexelni kell. <br/> <br/>Aktív fejlesztés alatt lévő indexek esetén [törölje](https://docs.microsoft.com/rest/api/searchservice/delete-index) és [hozza létre](https://docs.microsoft.com/rest/api/searchservice/create-index) az indexet az új meződefiníció felvételéhez. <br/> <br/>A termelésben lévő indexek esetében elhalaszthatja az újraépítést egy új mező létrehozásával, amely biztosítja a módosított definíciót, és a régi helyett elkezdi használni. Az [Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index) segítségével beépítheti az új mezőt, és [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) feltöltheti azt. Később a tervezett indexkarbantartás részeként az index et az elavult mezők eltávolításához törölje. |

## <a name="when-to-add-analyzers"></a>Mikor kell analizátorokat hozzáadni?

A legjobb alkalom, hogy adjunk hozzá és rendeljen analizátorok az aktív fejlesztés során, amikor csepegtető és újbóli indexek rutin.

Az indexdefiníció megszilárdulásaként új elemzési konstrukciókat fűzhet egy indexhez, de az **allowIndexDowntime** jelzőt át kell adnia az [Index frissítési indexnek,](https://docs.microsoft.com/rest/api/searchservice/update-index) ha el szeretné kerülni ezt a hibát:

*"Az indexfrissítés nem engedélyezett, mert állásidőt okozna. Annak érdekében, hogy új elemzők, tokenizers, jogkivonat-szűrők vagy karakterszűrők egy meglévő index, állítsa be az "allowIndexDowntime" lekérdezési paraméter "true" az index frissítési kérelemben. Vegye figyelembe, hogy ez a művelet legalább néhány másodpercre offline állapotba helyezi az indexet, ami az indexelési és lekérdezési kérelmek sikertelensítését okozza. Az index teljesítménye és írási elérhetősége az index frissítése után néhány percig, vagy a nagyon nagy indexek esetében hosszabb ideig csökkenthető."*

Ugyanez igaz akkor is, ha egy elemzőt rendel egy mezőhöz. Az analizátor a mező definíciójának szerves része, ezért csak a mező létrehozásakor veheti fel. Ha elemzőket szeretne hozzáadni a meglévő mezőkhöz, el kell [dobnia és újra](search-howto-reindex.md) kell építenie az indexet, vagy új mezőt kell hozzáadnia a kívánt elemzővel.

Mint említettük, kivétel a **searchAnalyzer** változat. Az analizátorok (**elemző**, **indexAnalyzer**, **searchAnalyzer**) megadásának három módja közül csak a **searchAnalyzer** attribútum módosítható egy meglévő mezőben.

## <a name="recommendations-for-working-with-analyzers"></a>Ajánlások az analizátorokkal való munkához

Ez a rész tanácsokat ad az analizátorokkal való munkához.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Egy analizátor az olvasáshoz és az olvasáshoz, hacsak nincsenek speciális követelményei

Az Azure Cognitive Search lehetővé teszi, hogy különböző elemzők indexelése és a keresés révén további **indexAnalyzer** és **searchAnalyzer** mező paramétereit. Ha nincs megadva, az **analizátor** tulajdonsággal beállított analizátor az indexeléshez és a kereséshez is használható. Ha `analyzer` nincs megadva, a rendszer az alapértelmezett Standard Lucene analizátort használja.

Általános szabály, hogy ugyanazt az analizátort használja mind az indexeléshez, mind a lekérdezéshez, kivéve, ha a konkrét követelmények másként írják. Győződjön meg róla, hogy alaposan tesztelje. Ha a szövegfeldolgozás keresési és indexelési időpontban eltérő, fennáll a veszélye annak, hogy nem egyezik a lekérdezési kifejezések és az indexelt kifejezések között, ha a keresési és indexelő elemző konfigurációk nincsenek egymáshoz igazítva.

### <a name="test-during-active-development"></a>Teszt az aktív fejlesztés során

A standard analizátor felüli megdöntéséhez index-újraépítésszükséges. Ha lehetséges, döntse el, hogy mely elemzőket használja az aktív fejlesztés során, mielőtt egy indexet éles környezetbe görgetne.

### <a name="inspect-tokenized-terms"></a>Tokenizált kifejezések vizsgálata

Ha a keresés nem adja vissza a várt eredményeket, a legvalószínűbb forgatókönyv a lekérdezés kifejezésbemenetei és az index tokenizált kifejezései közötti tokeneltérések. Ha a tokenek nem azonosak, az egyezések nem valósulnak meg. A tokenizer kimenetének vizsgálatához azt javasoljuk, hogy az [Api elemzése](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) a vizsgálati eszköz ként. A válasz egy adott elemző által generált jogkivonatokból áll.

<a name="examples"></a>

## <a name="rest-examples"></a>REST példák

Az alábbi példák néhány kulcsfontosságú forgatókönyv elemződefinícióit mutatják be.

+ [Példa egyéni elemzőre](#Custom-analyzer-example)
+ [Példa analizátorok hozzárendelése mezőhöz](#Per-field-analyzer-assignment-example)
+ [Keverési analizátorok indexeléshez és kereséshez](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Példa nyelvi elemzőre](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Példa egyéni elemzőre

Ez a példa egy egyéni beállításokkal rendelkező elemződefiníciót mutat be. A karakterszűrők, tokenizerek és tokenszűrők egyéni beállításai külön-külön vannak megadva elnevezett szerkezetként, majd hivatkoznak az analizátor definíciójában. Az előre definiált elemek et a rendszer a következőképpen használja, és egyszerűen név szerint hivatkozik.

Séta a példa:

* Az elemzők a mezőosztály tulajdonságai egy kereshető mezőhöz.
* Az egyéni analizátor egy indexdefiníció része. Lehet, hogy enyhén testre (például testre egyetlen lehetőség egy szűrőben), vagy testre több helyen.
* Ebben az esetben az egyéni analizátor "my_analyzer", amely viszont egy testreszabott szabványos tokenizer "my_standard_tokenizer" és két token szűrőt használ: kisbetűs és testreszabott asciifolding szűrő "my_asciifolding".
* Azt is meghatározza 2 egyéni karakter szűrők "map_dash" és a "remove_whitespace". Az első az összes kötőjelet aláhúzással helyettesíti, míg a második eltávolítja az összes szóközt. A szóközöknek UTF-8-nak kell lenniük a leképezési szabályokban. A char szűrők alkalmazása a tokenizálás előtt történik, és hatással lesz az eredményül kapott tokenekre (a szabványos tokenizer szünetek kötőjel és szóközök, de nem aláhúzás).

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

### <a name="per-field-analyzer-assignment-example"></a>Példa mezőelemzői hozzárendelésre

A Standard analizátor az alapértelmezett. Tegyük fel, hogy az alapértelmezett értéket egy másik előre definiált elemzőre, például a mintaelemzőre szeretné cserélni. Ha nem ad meg egyéni beállításokat, akkor csak név szerint kell megadnia a meződefinícióban.

Az "analizátor" elem mezőenként felülbírálja a Standard analizátort. Nincs globális felülbírálás. Ebben a `text1` példában a mintaelemző, a `text2`, amely nem ad meg elemzőt, az alapértelmezett.

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

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Keverési elemzők indexeléshez és keresési műveletekhez

Az API-k további indexattribútumokat tartalmaznak a különböző elemzők indexeléséhez és kereséséhez. A **searchAnalyzer** és **az indexAnalyzer** attribútumokat párként kell megadni, és az egyetlen **elemző** attribútumot kell helyettesíteni.


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

### <a name="language-analyzer-example"></a>Példa nyelvi elemzőre

A különböző nyelvű karakterláncokat tartalmazó mezők használhatnak nyelvi analizátort, míg más mezők megtartják az alapértelmezett értéket (vagy más előre definiált vagy egyéni analizátort használhatnak). Ha nyelvi analizátort használ, azt indexelési és keresési műveletekhez is használni kell. A nyelvi elemzőt használó mezők nem rendelkezhetnek különböző elemzőkkel az indexeléshez és a kereséshez.

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

## <a name="c-examples"></a>C# példák

Ha a .NET SDK kódmintákat használja, hozzáfűzheti ezeket a példákat az elemzők használatához vagy konfigurálásához.

+ [Beépített elemző hozzárendelése](#Assign-a-language-analyzer)
+ [Analizátor konfigurálása](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Nyelvi elemző hozzárendelése

Minden olyan elemző, amelyet a rendszer konfiguráció nélkül használ, meződefinícióban van megadva. Nincs szükség analizátor-szerkezet létrehozására. 

Ez a példa a Microsoft angol és francia elemzőket rendeli hozzá a leírásmezőkhöz. Ez egy részlet vett egy nagyobb meghatározása a szállodák index, ami segítségével a Hotel osztály a hotels.cs fájlt a [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) minta.

Hívás [elemző](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), adja meg az [AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) típusú, amely az Azure Cognitive Search által támogatott szövegelemző.

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

### <a name="define-a-custom-analyzer"></a>Egyéni elemző definiálása

Ha testreszabásra vagy konfigurációra van szükség, hozzá kell adnia egy elemző szerkezetet egy indexhez. Miután definiálta, hozzáadhatja a meződefiníciót az előző példában bemutatott módon.

Hozzon létre egy [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) objektumot. További példákat [a CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

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

+ Tekintse át átfogó magyarázatunkat [arról, hogyan működik a teljes szöveges keresés az Azure Cognitive Search szolgáltatásban.](search-lucene-query-architecture.md) Ez a cikk példákat használ, hogy ismertesse a viselkedések, amelyek úgy tűnhet, ellentétes intuitív a felszínen.

+ Próbálkozzon további lekérdezési szintaxissal a [Keresési dokumentumok](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) példa szakaszból, vagy az Egyszerű [lekérdezés szintaxisából](query-simple-syntax.md) a kereséskezelőben a portálon.

+ További információ a [nyelvspecifikus lexikális elemzők alkalmazásáról.](index-add-language-analyzers.md)

+ [Egyéni elemzők konfigurálása](index-add-custom-analyzers.md) minimális feldolgozáshoz vagy speciális feldolgozáshoz az egyes mezőkön.

## <a name="see-also"></a>Lásd még

 [Dokumentumok keresése – REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Egyszerű lekérdezési szintaxis](query-simple-syntax.md) 

 [Teljes Lucene lekérdezési szintaxis](query-lucene-syntax.md) 
 
 [A keresési eredmények kezelése](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
