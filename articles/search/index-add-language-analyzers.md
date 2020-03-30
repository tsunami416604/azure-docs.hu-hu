---
title: Nyelvi elemzők hozzáadása karakterláncmezőkhöz
titleSuffix: Azure Cognitive Search
description: Többnyelvű lexikális szövegelemzés nem angol lekérdezésekhez és indexekhez az Azure Cognitive Search-ben.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
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
ms.openlocfilehash: a97bee27b74aa211b4d4d56547726555edefa87a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283146"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Nyelvi elemzők hozzáadása karakterláncmezőkhöz az Azure Cognitive Search indexében

A *nyelvi analizátor* egy adott típusú [szöveganalizátor,](search-analyzers.md) amely lexikai elemzést végez a célnyelv nyelvi szabályainak használatával. Minden kereshető mezőrendelkezik **analizátor** tulajdonsággal. Ha az index lefordított karakterláncokat tartalmaz, például az angol és a kínai szöveg külön mezőit, az egyes mezőkben megadhatja a nyelvi elemzőket, hogy hozzáférjen az elemzők gazdag nyelvi képességeihez.  

Az Azure Cognitive Search 35, A Lucene által támogatott elemzőt és 50 elemzőt támogat, amelyeket az Office és a Bing által használt, saját fejlesztésű Microsoft természetes nyelvi feldolgozási technológia támogat.

## <a name="comparing-analyzers"></a>Elemzők összehasonlítása

Egyes fejlesztők a Lucene ismerősebb, egyszerűbb, nyílt forráskódú megoldását részesíthetik előnyben. Lucene nyelvi analizátorok gyorsabbak, de a Microsoft analizátorok fejlett képességek, mint például a lemmatization, szó decompounding (nyelveken, mint a német, dán, holland, svéd, norvég, észt, finish, magyar, szlovák) és a szervezet (URL-címek, e-mailek, dátumok, számok). Ha lehetséges, akkor mind a Microsoft, mind a Lucene analizátorok összehasonlítását kell futtatnia annak eldöntéséhez, hogy melyik a jobb illeszkedés. 

A Microsoft-analizátorokkal történő indexelés a nyelvtől függően átlagosan két-háromszor lassabb, mint a Lucene-megfelelőik. Az átlagos méretű lekérdezések esetében a keresési teljesítményt nem kell jelentősen érinteni. 

### <a name="english-analyzers"></a>Angol analizátorok

Az alapértelmezett analizátor a Standard Lucene, amely jól működik az angol, de talán nem olyan jól, mint Lucene angol analizátor vagy a Microsoft angol analizátor. 
 
+ Lucene angol analizátora kiterjeszti a standard analizátort. Eltávolítja a birtokosokat (záró 's) a szavakból, a Porter Stemming algoritmus szerint alkalmazandó, és eltávolítja az angol stop szavakat.  

+ A Microsoft angol analizátora lemmatization-t végez a származtatás helyett. Ez azt jelenti, hogy sokkal jobban tudja kezelni az inflected és a szabálytalan szóformákat, ami relevánsabb keresési eredményeket eredményez 

## <a name="configuring-analyzers"></a>Analizátorok konfigurálása

A nyelvi analizátorokat a hogy-van.Language analyzeers are used as-is. Az indexdefiníció minden mezőjéhez beállíthatja az **analizátor** tulajdonságot egy elemző névre, amely meghatározza a nyelvi és nyelvészeti vermet (Microsoft vagy Lucene). Ugyanez az analizátor lesz alkalmazva az indexeléskor és a mező keresésekor. Lehetnek például külön mezők az angol, a francia és a spanyol hotelleírások számára, amelyek egymás mellett léteznek ugyanabban az indexben.

> [!NOTE]
> Indexeléskor nem lehet más nyelvi elemzőt használni, mint egy mező lekérdezési idején. Ez a képesség az [egyéni elemzők](index-add-custom-analyzers.md)számára van fenntartva. Emiatt, ha megpróbálja beállítani a **searchAnalyzer** vagy **indexAnalyzer** tulajdonságait egy nyelvi elemző nevére, a REST API egy hibaválaszt ad vissza. Ehelyett az **analizátor tulajdonságot kell használnia.**

A **searchFields** lekérdezési paraméterrel megadhatja, hogy melyik nyelvspecifikus mezőben keressen a lekérdezésekben. Áttekintheti azana tulajdonságot tartalmazó lekérdezési példákat a [Dokumentumok keresése című dokumentumban.](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

Az indextulajdonságokról további információt az Index létrehozása &#40;Az Azure Cognitive Search REST API&#41;című témakörben [talál. ](https://docs.microsoft.com/rest/api/searchservice/create-index) Az Azure Cognitive Search elemzéséről az Elemzők az Azure Cognitive Search alkalmazásban című [témakörben](https://docs.microsoft.com/azure/search/search-analyzers)olvashat bővebben.

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Nyelvi elemzőlista 
 Az alábbi lista a támogatott nyelvek együtt Lucene és a Microsoft analizátor nevét.  

|Nyelv|Microsoft Analyzer neve|Lucene Analizátor neve|  
|--------------|-----------------------------|--------------------------|  
|Arab|ar.microsoft|ar.lucene|  
|örmény||hy.lucene|  
|Bangla|bn.microsoft||  
|Baszk||eu.lucene|  
|Bolgár|bg.microsoft|bg.lucene|  
|Katalán|ca.microsoft|ca.lucene|  
|kínai (egyszerűsített)|zh-Hans.microsoft|zh-Hans.lucene|  
|kínai (hagyományos)|zh-Hant.microsoft|zh-Hant.lucene|  
|Horvát|óra.microsoft||  
|Cseh|cs.microsoft|cs.lucene|  
|Dán|da.microsoft|da.lucene|  
|Holland|nl.microsoft|nl.lucene|  
|Angol|hu.microsoft|en.lucene|  
|Észt|et.microsoft||  
|Finn|fi.microsoft|fi.lucene|  
|Francia|fr.microsoft|fr.lucene|  
|Gallego||gl.lucene|  
|Német|de.microsoft|de.lucene|  
|Görög|el.microsoft|el.lucene|  
|gudzsaráti|gu.microsoft||  
|Héber|ő.microsoft||  
|Hindi|szia.microsoft|szia.lucene|  
|Magyar|hu.microsoft|hu.lucene|  
|Izlandi|is.microsoft||  
|Indonéz (Bahasa)|id.microsoft|id.lucene|  
|Ír||ga.lucene|  
|Olasz|it.microsoft|it.lucene|  
|Japán|ja.microsoft|ja.lucene|  
|kannada|kn.microsoft||  
|Koreai|ko.microsoft|ko.lucene|  
|Lett|lv.microsoft|lv.lucene|  
|Litván|lt.microsoft||  
|malajálam|ml.microsoft||  
|Maláj (latin betűs)|ms.microsoft||  
|marathi|mr.microsoft||  
|Norvég|nb.microsoft|no.lucene|  
|perzsa||fa.lucene|  
|Lengyel|pl.microsoft|pl.lucene|  
|Portugál (Brazília)|pt-Br.microsoft|pt-Br.lucene|  
|Portugál (Portugália)|pt-Pt.microsoft|pt-Pt.lucene|  
|pandzsábi|pa.microsoft||  
|Román|ro.microsoft|ro.lucene|  
|Orosz|ru.microsoft|ru.lucene|  
|Szerb (cirill betűs)|sr-cyrillic.microsoft||  
|Szerb (latin betűs)|sr-latin.microsoft||  
|Szlovák|sk.microsoft||  
|Szlovén|sl.microsoft||  
|Spanyol|es.microsoft|es.lucene|  
|Svéd|sv.microsoft|sv.lucene|  
|tamil|ta.microsoft||  
|telugu|te.microsoft||  
|Thai|th.microsoft|th.lucene|  
|Török|tr.microsoft|tr.lucene|  
|Ukrán|uk.microsoft||  
|urdu|az ön.microsoft||  
|Vietnami|vi.microsoft||  

 A **Lucene-nel** jegyzetekkel ellátott összes analizátrát [az Apache Lucene nyelvi elemzői működtetik.](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )

## <a name="see-also"></a>Lásd még  

+ [Index létrehozása &#40;Azure Cognitive Search REST API-&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [AnalyzerName osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

