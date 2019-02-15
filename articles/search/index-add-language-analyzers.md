---
title: Nyelvi elemzők – Azure Search hozzáadása
description: Többnyelvű lexikai szöveg elemzése nem angol nyelvű lekérdezések és az indexek az Azure Search szolgáltatásban.
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
ms.manager: cgronlun
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
ms.openlocfilehash: bb7fbdeea9c19b8a6fabe06687261296110b4064
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301804"
---
# <a name="add-language-analyzers-to-an-azure-search-index"></a>Nyelvi elemzők az Azure Search-index hozzáadása

A *nyelvi elemző* egy adott típusú [szöveg analyzer](search-analyzers.md) , amely végrehajtja a Célnyelv nyelvi szabályai lexikai elemzés. Minden kereshető mező egy **analyzer** tulajdonság. Az index tartalmazza a lefordított karakterláncokat, például az angol nyelvű és kínai szöveg, különálló mezőben megadhatja nyelvi elemzők mindegyik mező elérni ezeket az elemzők a gazdag nyelvi képességeit.  

Az Azure Search Lucene által támogatott 35 elemzők és a szellemi tulajdont képező Microsoft természetes nyelvi feldolgozási technológia, amellyel a Bing és az Office által támogatott 50 elemzők támogatja.

## <a name="comparing-analyzers"></a>Elemzők összehasonlítása

Néhány fejlesztő inkább Lucene több ismerős, egyszerű, nyílt forráskódú megoldás. Lucene nyelvi elemzők gyorsabb, de a Microsoft elemzők speciális képességek, például morfológiai, a word decompounding (például a német, dán, holland, svéd, norvég, észt, Befejezés, magyar, Szlovák nyelveken) és az entitás elismerés (URL-címek, e-mailek, dátumokat, számokat). Ha lehetséges futtassa a Microsoft és a Lucene-elemzőt eldönteni, melyik felel meg leginkább a összehasonlítását. 

A Microsoft elemzők az indexelő van átlagos két-három alkalommal lassabb, mint a Lucene megfelelőjükre a nyelvtől függően. Keresés teljesítménye jelentősen megőrződik átlagos mérete lekérdezések. 

### <a name="english-analyzers"></a>Angol nyelvű elemzők

Az alapértelmezett elemző a Standard Lucene, amely jól az angol nyelvű tájékoztatáshoz, de talán nem angol nyelvű a Lucene-elemző eszköz vagy a Microsoft angol nyelvű analyzer és működik. 
 
+ Lucene az angol nyelvű analyzer kiterjeszti a standard szintű analyzer. Azt eltávolítja possessives (záró a) a szavak, megfelelően Porter, amely szerint algoritmus, amely szerint alkalmazza, valamint angol nyelvű stopszavak.  

+ A Microsoft angol nyelvű analyzer morfológiai helyett, amely szerint végzi. Ez azt jelenti, hogy sokkal jobban mi eredményez több megfelelő keresési eredmények ragozott és szabálytalan word űrlapok képes kezelni 

 > [!Tip]
 > A [keresési Analyzer bemutató](https://alice.unearth.ai/) biztosít a standard Lucene-elemzőt, a Lucene angol nyelvi elemző és a Microsoft természetes angol nyelvű processzor által előállított eredmények összehasonlítása egymás mellett. Az egyes keresési feltétele alapján ad meg, minden egyes analyzer eredményeinek szomszédos ablaktáblán jelennek meg.

## <a name="configuring-analyzers"></a>Elemzők konfigurálása

Nyelvi elemzők használják-van. Az index definícióját minden egyes mezőjéhez, beállíthatja a **analyzer** -elemző eszköz a neve, mely nyelvi és szállítói tulajdonságot. Az azonos elemző lépnek érvénybe, ha indexelés és keresés, ezt a mezőt. Például rendelkezhet külön mezőkben angol, francia és spanyol Szálloda panelelemek leírását, amely ugyanahhoz az indexhez egymás mellett szerepel.  

Használja a **searchFields** lekérdezési paramétert adja meg, melyik nyelvspecifikus mezővel rákereshet ellen a lekérdezésekben. Az elemző tulajdonság tartalmazza a dokumentumok keresése példák a lekérdezésekre tekintheti meg. 

Index-tulajdonságokkal kapcsolatos további információkért lásd: [a Create Index &#40;Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Az Azure Search elemzési kapcsolatos további információkért lásd: [elemzők az Azure Search](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Nyelvi elemző listája 
 Az alábbi, a lucene-től és Microsoft elemző nevek együtt támogatott nyelvek listáját.  

|Nyelv|A Microsoft elemző neve|Lucene-elemzőt neve|  
|--------------|-----------------------------|--------------------------|  
|arab|ar.microsoft|ar.lucene|  
|örmény||HY.lucene|  
|Bengáli|bn.microsoft||  
|baszk||eu.lucene|  
|bolgár|bg.microsoft|bg.lucene|  
|katalán|ca.microsoft|CA.lucene|  
|kínai (egyszerűsített)|zh-Hans.microsoft|zh-Hans.lucene|  
|kínai (hagyományos)|zh-Hant.microsoft|zh-Hant.lucene|  
|horvát|hr.microsoft||  
|cseh|cs.microsoft|cs.lucene|  
|dán|da.microsoft|da.lucene|  
|holland|nl.microsoft|nl.lucene|  
|Angol|en.microsoft|en.lucene|  
|észt|et.microsoft||  
|finn|fi.microsoft|fi.lucene|  
|francia|fr.microsoft|FR.lucene|  
|galíciai||GL.lucene|  
|német|de.microsoft|de.lucene|  
|görög|el.microsoft|el.lucene|  
|gudzsaráti|gu.microsoft||  
|héber|he.microsoft||  
|hindi|hi.microsoft|hi.lucene|  
|magyar|hu.microsoft|hu.lucene|  
|izlandi|is.microsoft||  
|Indonéz (Bahasa)|id.microsoft|id.lucene|  
|ír||ga.lucene|  
|olasz|it.microsoft|it.lucene|  
|japán|ja.microsoft|ja.lucene|  
|kannada|ka.microsoft||  
|koreai|ko.microsoft|ko.lucene|  
|lett|lv.microsoft|LV.lucene|  
|litván|lt.microsoft||  
|malajálam|ml.microsoft||  
|Maláj (latin betűs)|ms.microsoft||  
|maráthi|mr.microsoft||  
|norvég|nb.microsoft|No.lucene|  
|perzsa||fa.lucene|  
|lengyel|pl.microsoft|pl.lucene|  
|portugál (brazíliai)|pt-Br.microsoft|pt-Br.lucene|  
|portugál (általános)|pt-Pt.microsoft|pt-Pt.lucene|  
|pandzsábi|pa.microsoft||  
|román|ro.microsoft|ro.lucene|  
|orosz|ru.microsoft|ru.lucene|  
|szerb (cirill betűs)|sr-cyrillic.microsoft||  
|szerb (latin betűs)|sr-latin.microsoft||  
|szlovák|sk.microsoft||  
|szlovén|sl.microsoft||  
|spanyol|es.microsoft|es.lucene|  
|svéd|sv.microsoft|sv.lucene|  
|tamil|ta.microsoft||  
|telugu|te.microsoft||  
|thai|th.microsoft|TH.lucene|  
|török|tr.microsoft|tr.lucene|  
|ukrán|uk.microsoft||  
|urdu|ur.microsoft||  
|vietnami|vi.microsoft||  

 Az összes elemzőt osztállyal nevekkel **Lucene** működteti [nyelvi elemzők Apache Lucene](https://lucene.apache.org/core/4_9_0/core/overview-summary.html ).

## <a name="see-also"></a>Lásd még  
 [Index létrehozása &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
 [AnalyzerName osztályban](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  
 [Videó: az Azure Search MVA bemutató modulja 7](https://channel9.msdn.com/Series/Adding-Microsoft-Azure-Search-to-Your-Websites-and-Apps/07).  

