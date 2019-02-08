---
title: Az elemzők nyelvi és szöveges feldolgozására – Azure Search
description: Az alapértelmezett egyéni, előre definiált vagy nyelvspecifikus alternatívákkal standard Lucene hozzárendelése egy cserélje le az indexben lévő kereshető szöveges mezők elemzők számát.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 121b5542f9388355b97744aa224ac824dd8d8728
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867205"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Az Azure Search szövegfeldolgozást elemzők

Egy *analyzer* összetevője [teljes szöveges keresés](search-lucene-query-architecture.md) felelős a lekérdezési karakterláncok és az indexelt dokumentumok feldolgozásáért. A következő átalakításokra tipikus elemzés során:

+ Nem lényeges szavakat (áll), és írásjelek el lesznek távolítva.
+ Mondatok és elválasztható szavak bontásban összetevő részre.
+ A rendszer-nagybetűs szavak alacsonyabb kisbetűsek.
+ Szavak, hogy függetlenül igeidőt egyezés található a legfelső szintű forms csökken.

Nyelvi elemzők konvertálása szöveges primitív bemeneteként vagy a legfelső szintű képernyő, amely hatékony a információ tárolásához és lekéréséhez. Átalakítás következik-e, ha az index felépítését, az indexelés és keresés, ha az index olvasható során újra. Ön nagyobb valószínűséggel kap a keresési eredmények között, ha az azonos szöveg elemző használhatja mindkét műveletek várt.

Az Azure Search használja a [Standard Lucene-elemzőt](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) alapértelmezés szerint. Felülbírálhatja az alapértelmezett mező szerint történik. Ez a cikk ismerteti a választási lehetőségek körét, és ajánlott eljárások az egyéni elemző kínál. Főbb alkalmazási helyzetek például konfigurációi is tartalmazza.

## <a name="supported-analyzers"></a>Támogatott elemzők

Az alábbi lista ismerteti, hogy melyik elemzők az Azure Search használata támogatott.

| Kategória | Leírás |
|----------|-------------|
| [Standard Lucene-elemzőt](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Default (Alapértelmezett): Nincs meghatározása vagy a konfiguráció nem szükséges. Az általános célú analyzer és a legtöbb nyelvek és forgatókönyvek hajt végre.|
| Előre definiált elemzők | Érhető el, ha egy kész termék kívánják használható – van, korlátozott testreszabási beállításokkal. <br/>Két típusa van: speciális és a nyelvet. Miből őket "előre meghatározott" neve, nincsenek testreszabási beállításokkal hivatkozhat. <br/><br/>[Specializált (nyelvtől) elemzők](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) használatosak, amikor a bemeneti szöveg speciális feldolgozó vagy a minimális feldolgozást igényelnek. Például nem előre definiált nyelvelemzők **Asciifolding**, **kulcsszó**, **minta**, **egyszerű**, **leállítása**, **Szóköz**.<br/><br/>[Nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support) használatosak, amikor az egyes nyelveket a gazdag nyelvi támogatás szükséges. Az Azure Search 35 Lucene nyelvi elemzőkkel és a Microsoft természetes nyelvek feldolgozása 50 elemzők támogatja. |
|[Egyéni elemzők](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Egy felhasználó által definiált konfigurációs meglévő elemeket, egy jogkivonatokat létrehozó (kötelező) és a választható szűrők (char vagy token) álló kombináció.|

Testre szabhatja egy előre meghatározott elemző eszköz, például **minta** vagy **leállítása**ahhoz, hogy alternatív beállítások részletes ismertetését lásd: [előre meghatározott Analyzer referencia](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Csak az előre meghatározott elemzők néhány közül választhat, amely lehet. Mivel a testreszabás, adja meg az új konfiguráció néven, például *myPatternAnalyzer* csatornától való megkülönböztetés a Lucene-minta analyzer.

## <a name="how-to-specify-analyzers"></a>Elemzők megadása

1. (a csak egyéni elemzőket) Hozzon létre egy **analyzer** az index definícióját szakaszát. További információkért lásd: [a Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) és is [egyéni elemzőket > Létrehozás](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. Az egy [definíció mező](https://docs.microsoft.com/rest/api/searchservice/create-index) az indexben, állítsa be a **analyzer** tulajdonságát egy cél-elemző eszköz nevére (például `"analyzer" = "keyword"`. Az érvényes értékek egy előre meghatározott elemző, nyelvi elemző vagy az indexséma még definiált egyéni elemző neve.

3. Szükség esetén helyett **analyzer** tulajdonság, beállíthatja az indexelés és a lekérdezési a használatával különböző elemzők a **indexAnalyzer** és **searchAnalyzer "** mező a paraméterek. 

3. Az index az írási művelet egy elemző ad hozzá egy mezőt definíció tekintetében. Ha hozzáad egy **analyzer** létező indexek, vegye figyelembe a következőket:
 
 | Forgatókönyv | Hatás | Lépések |
 |----------|--------|-------|
 | Adjon hozzá egy új mezőt | Minimális | Ha a mező a séma még nem létezik, nem végezhet, mert a mező még nem rendelkezik egy fizikai jelenlét az index nem mező változat. Használat [Index frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) és [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) erre a célra.|
 | Egy elemző hozzáadása egy meglévő indexelt mező. | Újraépítése | Ezt a mezőt fordított indexe létre kell hozni az alapoktól fel, és újra kell indexelni a tartalmat az adott mezők. <br/> <br/>Az aktív fejlesztés alatt indexek [törlése](https://docs.microsoft.com/rest/api/searchservice/delete-index) és [létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) folytattuk a munkát az új mező definíció indexe. <br/> <br/>Az indexek éles környezetben hozzon létre egy új mezőt a módosított meghatározása és használatba. Használat [Index frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) és [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) építhetnek be az új mező. Később, az index tervezett karbantartás részeként távolíthatja el az indexet elavult mezők eltávolítása. |

## <a name="tips-and-best-practices"></a>Tippek és ajánlott eljárások

Ez a szakasz tanácsokat ad az elemzők használatának módját.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Egy elemző eszköz, az olvasási és írási, kivéve, ha nincsenek egyedi igényei

Az Azure Search lehetővé teszi, hogy adja meg a különböző elemzők az indexelés és keresés használatával további `indexAnalyzer` és `searchAnalyzer` paraméterek mezőben. Ha nincs megadva, az elemző beállított a `analyzer` tulajdonság szolgál az indexelés és keresés. Ha `analyzer` van meghatározva, az alapértelmezett Standard Lucene-elemzőt használja.

Általános szabály, hogy az azonos elemző indexelése és lekérdezése, kivéve, ha az adott igények miatt más módon. Győződjön meg arról, alaposan tesztelni. Szöveg feldolgozási eltér a keresési indexelő időpontban, amikor Ön a veszély, lekérdezési kifejezéseket és indexelt feltételeket, ha a Keresés és az indexelő analyzer konfigurációk nincsenek egyeztetve nem egyezik.

### <a name="test-during-active-development"></a>Aktív fejlesztés során tesztelése

Az indexkészítés felülbírálja a szabványos elemző szükséges. Ha lehetséges hozza meg, melyik elemzők, mielőtt éles környezetben az index aktív fejlesztés során használ.

### <a name="inspect-tokenized-terms"></a>A feltételek tokenekre vizsgálata

A keresés nem várt eredményeket adjon vissza, ha a legvalószínűbb token fenntartásában kifejezés bemenetei között a lekérdezésre, és az index tokenekre feltételek között. Ha a jogkivonatok nem ugyanaz, egyezések tényleges táblává alakíthatóak sikertelen. Vizsgálhatja meg a kimeneti tokenizer, javasoljuk a [elemezheti API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) vizsgálati eszköz. A válasz tokenek, áll, például egy adott elemző eszköz által létrehozott.

### <a name="compare-english-analyzers"></a>Angol nyelvű elemzők összehasonlítása

A [keresési Analyzer bemutató](https://alice.unearth.ai/) egy külső bemutató alkalmazás egy egymás mellett hasonlítja, a standard Lucene-elemzőt, a Lucene angol nyelvi elemző és a Microsoft természetes angol nyelvű processzor. Az index rögzített; egy népszerű történetet szöveget tartalmaz. Az egyes keresési feltétele alapján ad meg, az egyes analyzer eredmények jelennek meg a szomszédos csomópontoknak, így jobban, hogy minden egyes analyzer miként dolgozza fel ugyanazt a karakterláncot. 

## <a name="examples"></a>Példák

Az alábbi példák néhány főbb forgatókönyvek megvalósítását analyzer definíciói.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>1. példa: Egyéni beállítások

Ebben a példában egy egyéni beállításokkal analyzer definícióját mutatja be. Egyéni beállítások a char szűrők, tokenizers és token szűrők megadott külön-külön elnevezett szerkezeteket, és ezután az elemző-definícióban hivatkozott. Előre meghatározott elemek használják- és neve egyszerűen hivatkozik.

Ebben a példában ajánljuk figyelmébe:

* Elemzők a mező osztály kereshető mező tulajdonsága.
* Egyéni elemző egy indexdefiníciót részét képezi. Előfordulhat, hogy alaposan testreszabható (például egy szűrő az egyetlen lehetőség testreszabása) vagy testre szabott több helyen is.
* Ebben az esetben az egyéni elemző-e "my_analyzer", amely viszont használja a "my_standard_tokenizer" testre szabott standard jogkivonatokat létrehozó és két token szűrők: kis- és testre szabott asciifolding szűrő "my_asciifolding".
* 2 egyéni char szűrők "map_dash" és "remove_whitespace" is meghatározza. Az elsőt összes kötőjelek lecseréli aláhúzásjeleket tartalmazhat, míg a másodiknál eltávolítja az összes nem állhat kizárólag szóközökből. Az elválasztó karakterek kell lennie az UTF-8 kódolású található a leképezési szabályokat. A char szűrők előtt a jogkivonatok érvényesek, és hatással lesz az eredményül kapott jogkivonatokkal (a standard szintű tokenizer oldaltörések dash és a tárolóhelyek azonban nem aláhúzásjelet).

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

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>2. példa Bírálja felül az alapértelmezett elemző

A standard szintű elemző eszköz az alapértelmezett érték. Tegyük fel, hogy az alapértelmezett cserélje le egy másik előre meghatározott elemző eszköz, például a minta analyzer. Ha nem egyéni beállításainak, csak adja meg azt a mezőt definícióban található nevének kell.

A "analyzer"-elem felülbírálja a szabványos analyzer mező szerint történik. Nincs nincs globális felülbírálás. Ebben a példában `text1` a minta elemzőt használ, és `text2`, amely nem adja meg egy elemzőt használja az alapértelmezett.

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

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>3. példa: Az indexelési és keresési műveletek különböző elemzők

Az API-k különböző elemzők az indexelés és keresés megadásával további index attribútumait tartalmazza. A `searchAnalyzer` és `indexAnalyzer` párban, és cserélje le az egyetlen kötelező attribútumok `analyzer` attribútum.


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

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>4. példa: Nyelvi elemző

Különböző nyelveken karakterláncokat tartalmazó mezők használhatja egy nyelvi elemzőt, míg a többi mező tartsa meg az alapértéket (vagy valamilyen más előre definiált vagy egyéni elemzőt használja). Ha egy nyelvi elemzőt használ, akkor az indexelési és keresési műveletek kell használható. Mezőt, amely egy nyelvi elemzőt használja nem rendelkezik a különböző elemzők az indexelés és keresés.

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

## <a name="next-steps"></a>További lépések

+ Tekintse át az átfogó ismertetése [teljes szöveges keresés működése az Azure Search](search-lucene-query-architecture.md). Ebben a cikkben példák azt ismertetik, tűnhet, hogy a Surface counter-intuitive viselkedés tapasztalható.

+ Próbálja ki a további lekérdezési szintaxis a [dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) példa szakaszban vagy [egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) a keresési ablakban a portálon.

+ Ismerje meg, hogyan alkalmazhatja a [nyelvspecifikus lexikai elemzőket](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Egyéni elemzők konfigurálása](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) minimális feldolgozás vagy specializált feldolgozási az egyes mezőket.

+ [Hasonlítsa össze a standard és az angol nyelvű elemzők](https://alice.unearth.ai/) bemutató webhelyhez a szomszédos ablaktáblán. 

## <a name="see-also"></a>Lásd még

 [REST API-val dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Teljes Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [A keresési eredmények kezelése](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
