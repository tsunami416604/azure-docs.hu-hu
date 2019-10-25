---
title: Nyelvi elemzők hozzáadása karakterlánc-mezőkhöz indexben
titleSuffix: Azure Cognitive Search
description: Többnyelvű lexikális szöveg elemzése nem angol nyelvű lekérdezésekhez és indexekhez az Azure Cognitive Searchban.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: f5833da5b15c893499b0d786972eff61c7391137
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790142"
---
# <a name="add-language-analyzers-to-an-azure-cognitive-search-index"></a>Nyelvi elemzők hozzáadása egy Azure Cognitive Search indexhez

A *Language Analyzer* egy adott típusú [szöveges elemző](search-analyzers.md) , amely lexikális analízist hajt végre a célnyelv nyelvi szabályainak használatával. Minden kereshető mező rendelkezik egy **Analyzer** tulajdonsággal. Ha az index lefordított karakterláncokat tartalmaz, például a különböző angol és kínai nyelvű szövegeket, megadhatja az egyes mezők nyelvi elemzőit az elemzők gazdag nyelvi képességeinek eléréséhez.  

Az Azure Cognitive Search támogatja a Lucene által támogatott 35-elemzőket, valamint az Office-ban és a Bingben használt, saját tulajdonú Microsoft természetes nyelvi feldolgozási technológiával támogatott 50-elemzőket.

## <a name="comparing-analyzers"></a>Elemzők összehasonlítása

Előfordulhat, hogy egyes fejlesztők inkább a Lucene ismerős, egyszerű és nyílt forráskódú megoldását részesítik előnyben. A Lucene nyelvi elemzői gyorsabbak, de a Microsoft-elemzők fejlett funkciókkal rendelkeznek, mint például a morfológiai elemzéshez, a szavak összetétele (például német, dán, holland, svéd, norvég, észt, finn, magyar, szlovák) és Entity felismerés (URL-címek, e-mailek, dátumok, számok). Ha lehetséges, a Microsoft és a Lucene elemzők összehasonlítását is el kell végeznie, hogy eldöntse, melyik egy jobb illeszkedés. 

A Microsoft-elemzők indexelése a nyelvtől függően a Lucene egyenértékűként átlagosan két-háromszor lassabban történik. A keresési teljesítmény nem lehet jelentős hatással az átlagos méretű lekérdezéseknél. 

### <a name="english-analyzers"></a>Angol analizátorok

Az alapértelmezett elemző a standard Lucene, amely az angol nyelvhez jól működik, de talán nem, valamint a Lucene angol analizátor vagy a Microsoft angol analizátora. 
 
+ A Lucene angol analizátora kiterjeszti a standard Analyzert. Eltávolítja a birtokosok (záró) szavakat a szavakból, és a porton kívüli algoritmust alkalmazza, és eltávolítja az angol leállítási szavakat.  

+ A Microsoft angol Analyzer morfológiai elemzéshez hajt végre a művelet helyett. Ez azt jelenti, hogy képes kezelni az ragozott és a szabálytalan Word-űrlapokat, ami sokkal jobbat jelent, ami nagyobb jelentőségű keresési eredményeket eredményez 

## <a name="configuring-analyzers"></a>Elemzők konfigurálása

A nyelvi elemzők a következőképpen használhatók:. Az index definíciójának minden mezőjénél megadhatja az **analizátor** tulajdonságot egy Analyzer-névre, amely megadja a nyelv és a nyelvi készlet (Microsoft vagy Lucene) értékét. Ugyanez az analizátor lesz alkalmazva a mező indexeléséhez és kereséséhez. Megadhatja például, hogy az angol, a francia és a spanyol nyelvű szállodai leírások külön mezői legyenek, amelyek ugyanabban az indexben vannak egymás mellett. Másik lehetőségként a **indexAnalyzer** **és a** **searchAnalyzer** használatával különböző elemzési szabályok is tartozhatnak az indexelési idő és a lekérdezési idő alapján. 

A **searchFields** lekérdezési paraméterrel megadásával meghatározhatja, hogy melyik nyelvspecifikus mezőt szeretné keresni a lekérdezésekben. Áttekintheti azokat a lekérdezési példákat is, amelyek tartalmazzák a Analyzer tulajdonságot a [keresési dokumentumokban](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

További információ az index tulajdonságairól: [create index &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Az Azure Cognitive Search elemzésével kapcsolatos további információkért lásd: [elemzők az Azure-ban Cognitive Search](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Nyelvi analizátorok listája 
 Alább látható a támogatott nyelvek listája, valamint a Lucene és a Microsoft Analyzer neve.  

|Nyelv|Microsoft Analyzer neve|Lucene Analyzer neve|  
|--------------|-----------------------------|--------------------------|  
|arab|ar. Microsoft|ar. Lucene|  
|örmény||Lucene|  
|Bangla|bn. Microsoft||  
|baszk||EU. Lucene|  
|bolgár|BG. Microsoft|BG. Lucene|  
|katalán|CA. Microsoft|CA. Lucene|  
|kínai (egyszerűsített)|zh-Hans. Microsoft|zh-Hans. Lucene|  
|kínai (hagyományos)|zh-Hant. Microsoft|zh-Hant. Lucene|  
|horvát|HR. Microsoft||  
|cseh|cs. Microsoft|cs. Lucene|  
|dán|da. Microsoft|da. Lucene|  
|holland|nl. Microsoft|nl. Lucene|  
|Angol|en. Microsoft|en. Lucene|  
|észt|et. Microsoft||  
|finn|Fi. Microsoft|Fi. Lucene|  
|francia|fr. Microsoft|fr. Lucene|  
|galíciai||Gl. Lucene|  
|német|de. Microsoft|de. Lucene|  
|görög|el. Microsoft|el. Lucene|  
|gudzsaráti|Gu. Microsoft||  
|héber|ő. Microsoft||  
|hindi|Hi. Microsoft|Hi. Lucene|  
|magyar|hu. Microsoft|hu. Lucene|  
|izlandi|a. Microsoft||  
|Indonéz (Bahasa)|azonosító. Microsoft|ID. Lucene|  
|ír||ga. Lucene|  
|olasz|it. Microsoft|it. Lucene|  
|japán|ja. Microsoft|ja. Lucene|  
|kannada|KN. Microsoft||  
|koreai|ko. Microsoft|ko. Lucene|  
|lett|lv. Microsoft|lv. Lucene|  
|litván|lt. Microsoft||  
|malajálam|ml. Microsoft||  
|Maláj (latin betűs)|MS. Microsoft||  
|marathi|Mr. Microsoft||  
|norvég|NB. Microsoft|nem. Lucene|  
|perzsa||fa. Lucene|  
|lengyel|pl. Microsoft|pl. Lucene|  
|Portugál (brazíliai)|pt-br. Microsoft|pt-br. Lucene|  
|Portugál (portugáliai)|PT-pt. Microsoft|PT-pt. Lucene|  
|pandzsábi|PA. Microsoft||  
|román|ro. Microsoft|ro. Lucene|  
|orosz|ru. Microsoft|ru. Lucene|  
|szerb (cirill betűs)|SR-cirill betűs. Microsoft||  
|Szerb (latin betűs)|SR-latin. Microsoft||  
|szlovák|sk. Microsoft||  
|szlovén|SL. Microsoft||  
|spanyol|es. Microsoft|es. Lucene|  
|svéd|Sv. Microsoft|Sv. Lucene|  
|tamil|ta. Microsoft||  
|telugu|te. Microsoft||  
|thai|th. Microsoft|th. Lucene|  
|török|TR. Microsoft|TR. Lucene|  
|ukrán|Egyesült Királyság. Microsoft||  
|urdu|a. Microsoft||  
|vietnami|VI. Microsoft||  

 Az [Apache Lucene nyelvi elemzői](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )a **Lucene** -mel ellátott nevekkel rendelkező elemzőket használják.

## <a name="see-also"></a>Lásd még:  

+ [Index &#40;létrehozása az Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [AnalyzerName osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

