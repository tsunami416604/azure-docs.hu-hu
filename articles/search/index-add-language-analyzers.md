---
title: Nyelvi elemzők hozzáadása – Azure Search
description: Többnyelvű lexikális szöveg elemzése a nem angol nyelvű lekérdezésekhez és indexekhez Azure Searchban.
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
ms.openlocfilehash: 88d229d33f549755479d7e1c7cf012d0391bccbb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881511"
---
# <a name="add-language-analyzers-to-an-azure-search-index"></a>Nyelvi elemzők hozzáadása Azure Search indexhez

A *Language Analyzer* egy adott típusú [szöveges elemző](search-analyzers.md) , amely lexikális analízist hajt végre a célnyelv nyelvi szabályainak használatával. Minden kereshető mező rendelkezik egy **Analyzer** tulajdonsággal. Ha az index lefordított karakterláncokat tartalmaz, például a különböző angol és kínai nyelvű szövegeket, megadhatja az egyes mezők nyelvi elemzőit az elemzők gazdag nyelvi képességeinek eléréséhez.  

Azure Search támogatja a Lucene által támogatott 35-elemzőket, valamint az Office-ban és a Bingben használt, saját tulajdonú Microsoft természetes nyelvi feldolgozó technológiával támogatott 50-elemzőket.

## <a name="comparing-analyzers"></a>Elemzők összehasonlítása

Előfordulhat, hogy egyes fejlesztők inkább a Lucene ismerős, egyszerű és nyílt forráskódú megoldását részesítik előnyben. A Lucene nyelvi elemzői gyorsabbak, de a Microsoft-elemzők fejlett funkciókkal rendelkeznek, mint például a morfológiai elemzéshez, a szavak összetétele (például német, dán, holland, svéd, norvég, észt, finn, magyar, szlovák) és Entity felismerés (URL-címek, e-mailek, dátumok, számok). Ha lehetséges, a Microsoft és a Lucene elemzők összehasonlítását is el kell végeznie, hogy eldöntse, melyik egy jobb illeszkedés. 

A Microsoft-elemzők indexelése a nyelvtől függően a Lucene egyenértékűként átlagosan két-háromszor lassabban történik. A keresési teljesítmény nem lehet jelentős hatással az átlagos méretű lekérdezéseknél. 

### <a name="english-analyzers"></a>Angol analizátorok

Az alapértelmezett elemző a standard Lucene, amely az angol nyelvhez jól működik, de talán nem, valamint a Lucene angol analizátor vagy a Microsoft angol analizátora. 
 
+ A Lucene angol analizátora kiterjeszti a standard Analyzert. Eltávolítja a birtokosok (záró) szavakat a szavakból, és a porton kívüli algoritmust alkalmazza, és eltávolítja az angol leállítási szavakat.  

+ A Microsoft angol Analyzer morfológiai elemzéshez hajt végre a művelet helyett. Ez azt jelenti, hogy képes kezelni az ragozott és a szabálytalan Word-űrlapokat, ami sokkal jobbat jelent, ami nagyobb jelentőségű keresési eredményeket eredményez 

## <a name="configuring-analyzers"></a>Elemzők konfigurálása

A nyelvi elemzők a következőképpen használhatók:. Az index definíciójának minden mezőjénél megadhatja az **analizátor** tulajdonságot egy Analyzer-névre, amely megadja a nyelv és a nyelvi készlet (Microsoft vagy Lucene) értékét. Ugyanez az analizátor lesz alkalmazva a mező indexeléséhez és kereséséhez. Megadhatja például, hogy az angol, a francia és a spanyol nyelvű szállodai leírások külön mezői legyenek, amelyek ugyanabban az indexben vannak egymás mellett. Másik lehetőségként a **IndexAnalyzer** és a **searchAnalyzer** használatával különböző elemzési szabályok is tartozhatnak az indexelési idő és a lekérdezési idő alapján. 

A **searchFields** lekérdezési paraméterrel megadásával meghatározhatja, hogy melyik nyelvspecifikus mezőt szeretné keresni a lekérdezésekben. Áttekintheti azokat a lekérdezési példákat is, amelyek tartalmazzák a Analyzer tulajdonságot a [keresési dokumentumokban](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

További információ az index tulajdonságairól: [create index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). A Azure Search elemzésével kapcsolatos további információkért lásd: [elemzők a Azure Searchban](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Nyelvi analizátorok listája 
 Alább látható a támogatott nyelvek listája, valamint a Lucene és a Microsoft Analyzer neve.  

|Nyelv|Microsoft Analyzer neve|Lucene Analyzer neve|  
|--------------|-----------------------------|--------------------------|  
|arab|ar.microsoft|ar. Lucene|  
|örmény||Lucene|  
|Bengáli|bn.microsoft||  
|baszk||EU. Lucene|  
|bolgár|bg.microsoft|bg.lucene|  
|katalán|CA. Microsoft|CA. Lucene|  
|kínai (egyszerűsített)|zh-Hans.microsoft|zh-Hans.lucene|  
|kínai (hagyományos)|zh-Hant.microsoft|zh-Hant. Lucene|  
|horvát|HR. Microsoft||  
|cseh|cs. Microsoft|cs. Lucene|  
|dán|da.microsoft|da. Lucene|  
|holland|nl. Microsoft|nl. Lucene|  
|Angol|en. Microsoft|en. Lucene|  
|észt|et. Microsoft||  
|finn|fi.microsoft|Fi. Lucene|  
|francia|fr. Microsoft|fr. Lucene|  
|galíciai||Gl. Lucene|  
|német|de.microsoft|de. Lucene|  
|görög|el. Microsoft|el. Lucene|  
|gudzsaráti|gu.microsoft||  
|héber|ő. Microsoft||  
|hindi|hi.microsoft|Hi. Lucene|  
|magyar|hu. Microsoft|hu. Lucene|  
|izlandi|is.microsoft||  
|Indonéz (Bahasa)|id.microsoft|id.lucene|  
|ír||ga. Lucene|  
|olasz|it. Microsoft|it. Lucene|  
|japán|ja. Microsoft|ja. Lucene|  
|kannada|kn.microsoft||  
|koreai|ko.microsoft|ko. Lucene|  
|lett|lv.microsoft|lv. Lucene|  
|litván|lt.microsoft||  
|malajálam|ml.microsoft||  
|Maláj (latin betűs)|ms.microsoft||  
|maráthi|mr.microsoft||  
|norvég|nb.microsoft|nem. Lucene|  
|perzsa||fa. Lucene|  
|lengyel|pl.microsoft|pl. Lucene|  
|portugál (brazíliai)|pt-Br.microsoft|pt-Br.lucene|  
|portugál (általános)|pt-Pt.microsoft|pt-Pt.lucene|  
|pandzsábi|pa.microsoft||  
|román|ro.microsoft|ro. Lucene|  
|orosz|ru.microsoft|ru. Lucene|  
|szerb (cirill betűs)|sr-cyrillic.microsoft||  
|szerb (latin betűs)|sr-latin.microsoft||  
|szlovák|sk.microsoft||  
|szlovén|SL. Microsoft||  
|spanyol|es. Microsoft|es. Lucene|  
|svéd|sv.microsoft|Sv. Lucene|  
|tamil|ta.microsoft||  
|telugu|te. Microsoft||  
|thai|th.microsoft|th. Lucene|  
|török|TR. Microsoft|TR. Lucene|  
|ukrán|uk.microsoft||  
|urdu|a. Microsoft||  
|vietnami|vi.microsoft||  

 Az [Apache Lucene nyelvi elemzői](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )a **Lucene** -mel ellátott nevekkel rendelkező elemzőket használják.

## <a name="see-also"></a>Lásd még  
 [Index &#40;Azure Search szolgáltatás létrehozása REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
 [AnalyzerName osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  
 [Videó: Azure Search MVA-bemutató 7](https://channel9.msdn.com/Series/Adding-Microsoft-Azure-Search-to-Your-Websites-and-Apps/07). modulja.  

