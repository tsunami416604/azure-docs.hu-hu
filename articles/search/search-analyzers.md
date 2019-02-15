---
title: Az elemzők nyelvi és szöveges feldolgozására – Azure Search
description: Az alapértelmezett egyéni, előre definiált vagy nyelvspecifikus alternatívákkal standard Lucene hozzárendelése egy cserélje le az indexben lévő kereshető szöveges mezők elemzők számát.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 5c3894b1f19a6baa65323391526ea5492d79f8a7
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301332"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Az Azure Search szövegfeldolgozást elemzők

Egy *analyzer* összetevője a [teljes szöveges keresőmotorja](search-lucene-query-architecture.md) felelős a lekérdezési karakterláncok és az indexelt dokumentumok feldolgozásáért. Nincsenek nyelvi elemzőkkel és a szöveg adatkezelési elemzőket. Nyelvi elemzők a leggyakoribb, és az Azure Search-index minden karakterlánc típusú rendelt alapértelmezett nyelvi elemzőt.

Szöveg elemzése során jellemzően a következő nyelvi átalakítások:

+ Nem lényeges szavakat (áll), és írásjelek el lesznek távolítva.
+ Mondatok és elválasztható szavak bontásban összetevő részre.
+ A rendszer-nagybetűs szavak alacsonyabb kisbetűsek.
+ Szavak, hogy függetlenül igeidőt egyezés található a legfelső szintű forms csökken.

Nyelvi elemzők konvertálása szöveges primitív bemeneteként vagy a legfelső szintű képernyő, amely hatékony a információ tárolásához és lekéréséhez. Átalakítás következik-e, ha az index felépítését, az indexelés és keresés, ha az index olvasható során újra. Ön nagyobb valószínűséggel kap a keresési eredmények között, ha az azonos elemző használhatja mindkét műveletek várt.

## <a name="default-analyzer"></a>Alapértelmezett elemző  

Az Azure Search használja a [Apache Lucene Standard analyzer (standard lucene)](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) az alapértelmezett, amely szöveges bontja a következő elemeket a ["Unicode szöveg Szegmentálás"](https://unicode.org/reports/tr29/) szabályok. A standard szintű analyzer emellett a kisbetűs formában alakítja az összes karaktert. Indexelt dokumentumok és a keresési feltételek próbálja ki az elemzés az indexelés és a lekérdezés feldolgozása során.  

Használható automatikusan az összes kereshető mezőt. Felülbírálhatja az alapértelmezett mező szerint történik. Alternatív elemzők lehet egy [nyelvi elemző](index-add-language-analyzers.md), [egyéni elemző](index-add-custom-analyzers.md), vagy egy előre meghatározott Analyzer programban a [elérhető elemzők listája](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Elemzők típusai

Az alábbi lista ismerteti, hogy melyik elemzők az Azure Search szolgáltatásban érhetők el.

| Kategória | Leírás |
|----------|-------------|
| [Standard Lucene-elemzőt](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Default (Alapértelmezett): Nincs meghatározása vagy a konfiguráció nem szükséges. Az általános célú analyzer és a legtöbb nyelvek és forgatókönyvek hajt végre.|
| Előre definiált elemzők | Érhető el, ha egy kész termék kívánják használható – van. <br/>Két típusa van: speciális és a nyelvet. Miből őket "előre meghatározott" neve, konfigurálása és testreszabása nélkül hivatkozhat. <br/><br/>[Specializált (nyelvtől) elemzők](index-add-custom-analyzers.md#AnalyzerTable) használatosak, amikor a bemeneti szöveg speciális feldolgozó vagy a minimális feldolgozást igényelnek. Például nem előre definiált nyelvelemzők **Asciifolding**, **kulcsszó**, **minta**, **egyszerű**, **leállítása**, **Szóköz**.<br/><br/>[Nyelvi elemzők](index-add-language-analyzers.md) használatosak, amikor az egyes nyelveket a gazdag nyelvi támogatás szükséges. Az Azure Search 35 Lucene nyelvi elemzőkkel és a Microsoft természetes nyelvek feldolgozása 50 elemzők támogatja. |
|[Egyéni elemzők](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Egy felhasználó által definiált konfigurációjának meglévő elemeket, egy jogkivonatokat létrehozó (kötelező) és a választható szűrők (char vagy token) kombinációja jelenti.|

Néhány előre meghatározott elemzők, mint például **minta** vagy **leállítása**, támogatja a korlátozott számú konfigurációs beállításokat. Ezekkel a beállításokkal hatékonyan hozzon létre egy egyéni elemző eszköz, az előre meghatározott analzer álló és az alternatív lehetőségek közül dokumentált [előre meghatározott Analyzer referencia](index-add-custom-analyzers.md#AnalyzerTable). Mivel minden olyan egyéni konfigurációval, adja meg az új konfiguráció néven, például *myPatternAnalyzer* csatornától való megkülönböztetés a Lucene-minta analyzer.

## <a name="how-to-specify-analyzers"></a>Elemzők megadása

1. (a csak egyéni elemzőket) Hozzon létre egy elnevezett **analyzer** az index definícióját szakaszát. További információkért lásd: [a Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) és is [adja hozzá az egyéni elemzőket](index-add-custom-analyzers.md).

2. Az egy [definíció mező](https://docs.microsoft.com/rest/api/searchservice/create-index) az indexben, állítsa be a mező **analyzer** tulajdonságát egy cél-elemző eszköz nevére (például `"analyzer" = "keyword"`. Az érvényes értékek egy előre meghatározott elemző, nyelvi elemző vagy az indexséma még definiált egyéni elemző neve. Tervezze meg az index definícióját fázisban a szolgáltatásban az index létrehozása előtt analyzer hozzárendelésével.

3. Szükség esetén helyett **analyzer** tulajdonság, beállíthatja az indexelés és a lekérdezési a használatával különböző elemzők a **indexAnalyzer** és **searchAnalyzer** mező a paraméterek. 

3. Az index az írási művelet egy elemző ad hozzá egy mezőt definíció tekintetében. Ha hozzáad egy **analyzer** létező indexek, vegye figyelembe a következőket:
 
 | Forgatókönyv | Hatás | Lépések |
 |----------|--------|-------|
 | Adjon hozzá egy új mezőt | Minimális | Ha a mező a séma még nem létezik, nem végezhet, mert a mező még nem rendelkezik egy fizikai jelenlét az index nem mező változat. Használat [Index frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) új mező hozzáadása egy meglévő index.|
 | Egy elemző hozzáadása egy meglévő indexelt mező. | [Újraépítése](search-howto-reindex.md) | Ezt a mezőt fordított indexe létre kell hozni az alapoktól fel, és újra kell indexelni a tartalmat az adott mezők. <br/> <br/>Az aktív fejlesztés alatt indexek [törlése](https://docs.microsoft.com/rest/api/searchservice/delete-index) és [létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) folytattuk a munkát az új mező definíció indexe. <br/> <br/>Az indexek éles környezetben újraépítési kiadásuktól hozzon létre egy új mezővel, adja meg a módosított meghatározása és vegye használatba a régit helyett. Használat [Index frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) építhetnek be az új mező és [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) itbet feltöltéséhez. Később, az index tervezett karbantartás részeként távolíthatja el az indexet elavult mezők eltávolítása. |

## <a name="when-to-add-analyzers"></a>Mikor érdemes hozzáadni az elemzők

Megadhat több egyéni elemzőket szűrők kombinációit eltérő, de minden mező csak használhat egy elemző indexelés, elemzési és a egy a search-elemzést.  

Konfigurálnia kell elemzők aktív fejlesztés során továbbra is a fluxus indexdefiníciót esetén. Egy elemző egy mezőben megadva a mező-definíció szerves részét képezi, így csak akkor adhat hozzá, ha a mező jön létre. Ha azt szeretné, elemzők hozzáadása a meglévő mezőket kell [dobja el, és építse újra](search-howto-reindex.md) az index.

Kivétel ez alól a searchAnalyzer változatot. Adja meg az elemzők három módja van: **analyzer**, **indexAnalyzer**, **searchAnalyzer**. Az első esetéhez **analyzer**, az indexelés és a lekérdezési kérelmek szolgál. A másik kettőt engedélyezése, hogy melyik elemzők szolgálnak az egyes kérelmek szabályozása.

Mindkét **analyzer** és **indexAnalyzer** kell a mező kezdeti definíciója adható meg. A **searchAnalyzer** attribútum egy mezőt, amely már létezik, újraépítési követelmény nem lehet hozzáadni.

## <a name="recommendations-for-working-with-analyzers"></a>Javaslatok használata esetén

Ez a szakasz tanácsokat ad az elemzők használatának módját.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Egy elemző eszköz, az olvasási és írási, kivéve, ha nincsenek egyedi igényei

Az Azure Search lehetővé teszi, hogy adja meg a különböző elemzők az indexelés és keresés használatával további **indexAnalyzer** és **searchAnalyzer** paraméterek mezőben. Ha nincs megadva, az elemző beállított a **analyzer** tulajdonság szolgál az indexelés és keresés. Ha `analyzer` van meghatározva, az alapértelmezett Standard Lucene-elemzőt használja.

Általános szabály, hogy az azonos elemző indexelése és lekérdezése, kivéve, ha az adott igények miatt más módon. Győződjön meg arról, alaposan tesztelni. Szöveg feldolgozási eltér a keresési indexelő időpontban, amikor Ön a veszély, lekérdezési kifejezéseket és indexelt feltételeket, ha a Keresés és az indexelő analyzer konfigurációk nincsenek egyeztetve nem egyezik.

### <a name="test-during-active-development"></a>Aktív fejlesztés során tesztelése

Az indexkészítés felülbírálja a szabványos elemző szükséges. Ha lehetséges hozza meg, melyik elemzők, mielőtt éles környezetben az index aktív fejlesztés során használ.

### <a name="inspect-tokenized-terms"></a>A feltételek tokenekre vizsgálata

A keresés nem várt eredményeket adjon vissza, ha a legvalószínűbb token fenntartásában kifejezés bemenetei között a lekérdezésre, és az index tokenekre feltételek között. Ha a jogkivonatok nem ugyanaz, egyezések tényleges táblává alakíthatóak sikertelen. Vizsgálhatja meg a kimeneti tokenizer, javasoljuk a [elemezheti API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) vizsgálati eszköz. A válasz tokenek, áll, például egy adott elemző eszköz által létrehozott.

### <a name="compare-english-analyzers"></a>Angol nyelvű elemzők összehasonlítása

A [keresési Analyzer bemutató](https://alice.unearth.ai/) egy külső bemutató alkalmazás egy egymás mellett hasonlítja, a standard Lucene-elemzőt, a Lucene angol nyelvi elemző és a Microsoft természetes angol nyelvű processzor. Az index rögzített; egy népszerű történetet szöveget tartalmaz. Az egyes keresési feltétele alapján ad meg, az egyes analyzer eredmények jelennek meg a szomszédos csomópontoknak, így jobban, hogy minden egyes analyzer miként dolgozza fel ugyanazt a karakterláncot. 

## <a name="examples"></a>Példák

Az alábbi példák néhány főbb forgatókönyvek megvalósítását analyzer definíciói.

+ [Egyéni elemző példa](#Example1)
+ [Egy mező példa elemzők hozzárendelése](#Example2)
+ [Az indexelési és keresési elemzők keverése](#Example3)
+ [Nyelvi elemző példa](#Example4)

<a name="Example1"></a>

### <a name="custom-analyzer-example"></a>Egyéni elemző példa

Ebben a példában egy egyéni beállításokkal analyzer definícióját mutatja be. Egyéni beállítások a char szűrők, tokenizers és token szűrők megadott külön-külön elnevezett szerkezeteket, és ezután az elemző-definícióban hivatkozott. Előre meghatározott elemek használják- és neve egyszerűen hivatkozik.

Ebben a példában ajánljuk figyelmébe:

* Elemzők a mező osztály kereshető mező tulajdonsága.
* Egyéni elemző egy indexdefiníciót részét képezi. Előfordulhat, hogy alaposan testreszabható (például egy szűrő az egyetlen lehetőség testreszabása) vagy testre szabott több helyen is.
* Ebben az esetben az egyéni elemző-e "my_analyzer", amely viszont használja a "my_standard_tokenizer" testre szabott standard jogkivonatokat létrehozó és két token szűrők: kis- és testre szabott asciifolding szűrő "my_asciifolding".
* 2 egyéni char szűrők "map_dash" és "remove_whitespace" is meghatározza. Az elsőt összes kötőjelek szerepelhetnek, míg a másodiknál minden szóközt eltávolít aláhúzásjeleket lecseréli. UTF-8 kódolású a leképezési szabályokat a tárolóhelyek kell. A char szűrők előtt a jogkivonatok érvényesek, és hatással lesz az eredményül kapott jogkivonatokkal (a standard szintű tokenizer oldaltörések dash és a tárolóhelyek azonban nem aláhúzásjelet).

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

### <a name="per-field-analyzer-assignment-example"></a>Mező elemző eszköz hozzárendelés példa

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

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Az indexelési és keresési műveletek elemzők keverése

Az API-k különböző elemzők az indexelés és keresés megadásával további index attribútumait tartalmazza. A **searchAnalyzer** és **indexAnalyzer** párban, és cserélje le az egyetlen kötelező attribútumok **analyzer** attribútum.


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

### <a name="language-analyzer-example"></a>Nyelvi elemző példa

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

+ Próbálja ki a további lekérdezési szintaxis a [dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) példa szakaszban vagy [egyszerű lekérdezési szintaxis](query-simple-syntax.md) a keresési ablakban a portálon.

+ Ismerje meg, hogyan alkalmazhatja a [nyelvspecifikus lexikai elemzőket](index-add-language-analyzers.md).

+ [Egyéni elemzők konfigurálása](index-add-custom-analyzers.md) minimális feldolgozás vagy specializált feldolgozási az egyes mezőket.

+ [Hasonlítsa össze a standard és az angol nyelvű elemzők](https://alice.unearth.ai/) bemutató webhelyhez a szomszédos ablaktáblán. 

## <a name="see-also"></a>Lásd még

 [REST API-val dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Egyszerű lekérdezési szintaxis](query-simple-syntax.md) 

 [Teljes Lucene lekérdezési szintaxis](query-lucene-syntax.md) 
 
 [A keresési eredmények kezelése](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
