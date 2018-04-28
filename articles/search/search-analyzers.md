---
title: Az Azure Search elemzőkkel |} Microsoft Docs
description: Hozzárendelése elemzőkkel lecseréli index kereshető szöveg mezők alapértelmezett szabványos Lucene egyéni, előre definiált vagy nyelvspecifikus lehetőségeket.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.openlocfilehash: e858966fb5a15b84af1952399a5eff3ca50d0d59
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="analyzers-in-azure-search"></a>Elemzők az Azure Search szolgáltatásban

Egy *analyzer* összetevője [teljes szöveges keresés](search-lucene-query-architecture.md) feladata, hogy feldolgozzák a lekérdezési karakterláncok és indexelt dokumentumok szöveget. A következő átalakítások tipikusan elemzés során:

+ A nem feltétlenül szükséges szavak (szavak) és írásjelek törlődnek.
+ A kifejezéseket és kötőjellel szavakat osztható összetevői.
+ A rendszer nagybetűből szavak alsó cased.
+ Szavakat, hogy időben függetlenül található egyezés a legfelső szintű űrlap csökken.

A nyelvi elemzőkkel convert szöveget bemeneteként egyszerű vagy, amelyek a információ tárolása és lekérése hatékonyan legfelső szintű űrlap. Átalakítás indexelő, ha az index épül, során fordul elő, ha az index olvasható keresési során újra majd. Biztosan nagyobb eséllyel tartják be a keresési eredmények között, ha mindkét műveletekhez használja az ugyanazon szöveg analyzer várt beolvasása.

Az Azure Search használja a [szabványos Lucene analyzer](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) alapértelmezés szerint. Felülbírálhatja az alapértelmezett mező által alapon. Ez a cikk ismerteti a választási lehetőségek teljes skáláját és ajánlott eljárások az egyéni elemzési kínál. Példa konfigurációi főbb forgatókönyvek is tartalmazza.

## <a name="supported-analyzers"></a>Támogatott elemzőkkel

Az alábbi lista ismerteti, hogy mely elemzőkkel az Azure Search támogatottak.

| Kategória | Leírás |
|----------|-------------|
| [Standard Lucene elemző eszköz](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Alapértelmezés szerint. Nincs meghatározása vagy a konfiguráció szükség. Az általános célú analyzer hajt végre, a legtöbb nyelveket és forgatókönyvek esetén.|
| Előre definiált elemzőkkel | Érhető el, ha a kész termék kívánják használható-van, korlátozott testreszabása. <br/>Két típusa van: speciális és nyelvet. Mi válnak "előre definiált" neve, a nem testreszabási hivatkozik. <br/><br/>[Speciális (nyelv független) elemzőkkel](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) akkor használatosak, ha a szöveg bemenetei speciális feldolgozó vagy minimális feldolgozási igényelnek. Nem előre definiált lekérdezések-tartalmaznak **Asciifolding**, **kulcsszó**, **mintát**, **egyszerű**, **leállítása**, **Szóköz**.<br/><br/>[Nyelvi elemzőkkel](https://docs.microsoft.com/rest/api/searchservice/language-support) használata, amikor az egyes nyelvek gazdag nyelvi támogatás van szüksége. Az Azure Search 35 Lucene nyelvi elemzőkkel és 50 Microsoft természetes nyelvű feldolgozási elemzőkkel támogatja. |
|[Egyéni elemzők](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | A meglévő elemet egy jogkivonatokat létrehozó (kötelező), és nem kötelező szűrők (char vagy token) álló kombinációja felhasználói konfiguráció.|

Testre szabhatja, mint egy előre meghatározott analyzer **mintát** vagy **leállítása**, alternatív a lehetőségek részletes ismertetését lásd: [előre definiált Analyzer hivatkozás](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Csak az előre definiált elemzőkkel néhány közül állíthatja be. A testreszabás, adja meg az új konfiguráció néven, például a módon *myPatternAnalyzer* azt megkülönböztetni a Lucene mintát analyzer.

## <a name="how-to-specify-analyzers"></a>Elemzőkkel megadása

1. (a csak egyéni lekérdezések esetén) Hozzon létre egy **analyzer** című szakaszában találhat az index definícióját. További információkért lásd: [a Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) és is [egyéni Lekérdezések > hozzon létre](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. Az egy [definition mezőben](https://docs.microsoft.com/rest/api/searchservice/create-index) az indexben, állítsa be a **analyzer** tulajdonság egy tároló analyzer nevét (például `"analyzer" = "keyword"`. Érvényes értékek a következők egy előre meghatározott analyzer, nyelvi elemzőt vagy egyéni analyzer is a sémában meghatározott elemnévvel index neve.

3. Másik lehetőségként helyett egy **analyzer** tulajdonság, a különböző elemzőkkel indexelő és lekérdezése használatával beállíthatja a **indexAnalyzer** és **searchAnalyzer "** mező a paraméterek. 

3. Egy elemző eszköz hozzáadása egy mező definition azt eredményezi, az indexben írási művelet azok háromszorosa. Ha ad hozzá egy **analyzer** létező indexek, vegye figyelembe a következőket:
 
 | Forgatókönyv | Hatás | Lépések |
 |----------|--------|-------|
 | Új mező hozzáadása | minimális | Ha a mező a sémában még nem létezik, nincs nincs mező változat végezhet, mert a mező még nincs fizikai jelenlét az indexben. Használjon [Index frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) és [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ehhez a feladathoz.|
 | Egy elemző eszköz hozzáadása egy meglévő indexelt mezőt. | Építse újra | A fordított index mező kell újra létrehoznia az alapoktól fel, és újra kell indexelni mezőket a tartalmát. <br/> <br/>Az indexek aktív fejlesztés alatt [törlése](https://docs.microsoft.com/rest/api/searchservice/delete-index) és [létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) átvételéhez mező új meghatározása az index. <br/> <br/>Éles környezetben indexek hozzon létre egy új mezőben adja meg a módosított meghatározása és használatba. Használjon [Index frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) és [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) átfogó az új mező. Később, a tervezett index karbantartás részeként is távolítja el az elavult mező eltávolítása az index. |

## <a name="tips-and-best-practices"></a>Tippek és ajánlott eljárások

Ez a szakasz a lekérdezések használata tanácsokat.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Egy elemző eszköz az olvasási és írási használni, ha nincs kapcsolatos követelmények

Az Azure Search lehetővé teszi a különböző elemzőkkel indexeléshez adja meg, és keressen további keresztül `indexAnalyzer` és `searchAnalyzer` paraméterek mezőben. Ha nincs megadva, a analyzer beállított a `analyzer` a tulajdonságot használja az indexelés és a keresést. Ha `analyzer` értéke nincs megadva, az alapértelmezett normál Lucene analyzer szolgál.

Általános szabály, hogy használja az ugyanazon analyzer indexelő és kérdez le, kivéve, ha a meghatározott követelmények ettől eltérő konfigurációt tesznek. Győződjön meg arról, hogy alaposan tesztelje. Szöveg feldolgozási eltér a keresési indexelő időpontban, amikor futtatja a lekérdezési kifejezések és indexelt feltételeit, ha a Keresés és indexelési analyzer konfigurációk nincsenek egyeztetve eltérő kockázatát.

### <a name="test-during-active-development"></a>Aktív a fejlesztés során tesztelése

Az index rebuild felülbírálja a szabványos elemző szükséges. Ha lehetséges mely elemzőkkel aktív, mielőtt éles környezetben az index a fejlesztés során használandó mellett dönt.

### <a name="inspect-tokenized-terms"></a>Vizsgálja meg a tokenekre feltételek

A keresés nem várt eredményt adja vissza, ha a legvalószínűbb token eltérés van a kifejezés bemenetek a lekérdezésre, és az index tokenekre feltételeit. Ha a jogkivonatok nem ugyanaz, megfelel nem materializálni. A vizsgálandó jogkivonatokat létrehozó kimeneti, azt javasoljuk, a [elemzése API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) vizsgálati eszköz. A válasz áll jogkivonatokat, például egy adott elemző eszköz által létrehozott.

### <a name="compare-english-analyzers"></a>Angol elemzőkkel összehasonlítása

A [keresési Analyzer bemutató](http://alice.unearth.ai/) egy külső bemutató alkalmazás megjeleníti a szabványos Lucene analyzer Lucene tartozó angol nyelvi elemzőt és a Microsoft angol természetes nyelvű processzor egymás melletti összehasonlítása. Az index rögzített; a népszerű szövegegység szöveget tartalmaz. Minden keresési bemeneti ad meg, mindegyik elemző eszköz eredményei jelennek meg szomszédos ablaktáblák, biztosítva, hogy miként dolgozza fel a mindegyik elemző ugyanazt a karakterláncot. 

## <a name="examples"></a>Példák

Az alábbi példák azt szemléltetik, néhány főbb forgatókönyvek analyzer definícióit.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>1. példa: Egyéni beállítások

Ez a példa egy egyéni beállításokkal analyzer definíciója mutatja be. Char, tokenizers, és token szűrőket egyéni beállításokat külön-külön megadott elnevezett szerkezetek, és majd hivatkozott analyzer definíciójában. Előre meghatározott elemek használatosak- és egyszerűen hivatkozás a név alapján.

Útmutató alapján ez a példa:

* Lekérdezések a kereshető mező mező osztály egy tulajdonságát.
* Egy egyéni analyzer egy Indexdefiníció részét képezi. Lehet, hogy lehet enyhén testre szabott (például egy szűrő az egyetlen lehetőség testreszabása) vagy több helyen is testre szabott.
* Ebben az esetben az egyéni analyzer "my_analyzer", amely viszont használja egy testreszabott szabványos jogkivonatokat létrehozó "my_standard_tokenizer" és a két token szűrők: kis- és testre szabott asciifolding szűrő "my_asciifolding".
* Cserélje le minden gondolatjelek aláhúzásjelek (a szabványos jogkivonatokat létrehozó oldaltörések kötőjel azonban nem aláhúzás) létrehozása előtt egyéni "map_dash" char szűrőt is meghatározza.

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
              "map_dash"
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
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
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
### <a name="example-2-override-the-default-analyzer"></a>2. példa: Az alapértelmezett analyzer felülbírálása

A szabványos elemző eszköz az alapértelmezett beállítás. Tegyük fel, az alapértelmezett lecseréli egy másik előre definiált elemző eszközt, például a minta analyzer. Ha nem egyéni beállítások megadása, csak adja meg azt a nevet adja meg a mező definícióban kell.

A "analyzer" elem felülbírálja a szabványos analyzer mező által alapon. Nincs globális felülbírálás van. Ebben a példában `text1` a minta elemzőt használ és `text2`, amely nem ad meg egy analyzer használja az alapértelmezett.

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
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>3. példa: Különböző elemzőkkel indexelő és a keresési műveletekhez

Az API-kat a különböző elemzőkkel az indexelő, és keresse meg a további index attribútumait tartalmazza. A `searchAnalyzer` és `indexAnalyzer` attribútumok meg kell adni a mag cseréje a egyetlen, két `analyzer` attribútum.


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
### <a name="example-4-language-analyzer"></a>4. példa: Nyelvi elemzőt

Különböző nyelveken karakterláncokat tartalmazó mezőket is használható, a nyelvi elemzőt más mezők tartsa meg az alapértéket (vagy valamilyen más előre definiált vagy egyéni analyzer használatának). Ha egy nyelvi elemzőt használ, azt kell használható indexelési és keresési műveletekhez. Rekord egy nyelvi elemzőt használó nem indexeléshez különböző elemzőkkel és keresés.

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

+ Tekintse át az átfogó magyarázata [hogyan teljes szöveges keresés működik az Azure Search](search-lucene-query-architecture.md). Ez a cikk példák segítségével viselkedéseket, amelyek a felületen counter-intuitive tűnhetnek ismertetik.

+ Próbálja meg a további lekérdezés szintaxisa a [dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) példa szakasz vagy [egyszerű lekérdezés szintaxisát](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) a keresési ablak a portálon.

+ Megtudhatja, hogyan alkalmazza [nyelvspecifikus lexikális elemzőkkel](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Konfigurálja az egyéni lekérdezések](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) minimális feldolgozási vagy speciális feldolgozását az egyes mezőket.

+ [Hasonlítsa össze a szabványos és az angol nyelvű elemzőkkel](http://alice.unearth.ai/) szomszédos ablaktáblán a bemutató webhelyen. 

## <a name="see-also"></a>Lásd még

 [REST API-t dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Teljes Lucene lekérdezés szintaxisa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [A keresési eredmények kezelése](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
