---
title: Nyelvi elemzők hozzáadása karakterlánc-mezőkhöz
titleSuffix: Azure Cognitive Search
description: Többnyelvű lexikális szöveg elemzése nem angol nyelvű lekérdezésekhez és indexekhez az Azure Cognitive Searchban.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283146"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Nyelvi elemzők hozzáadása karakterlánc-mezőkhöz Azure Cognitive Search indexben

A *Language Analyzer* egy adott típusú [szöveges elemző](search-analyzers.md) , amely lexikális analízist hajt végre a célnyelv nyelvi szabályainak használatával. Minden kereshető mező rendelkezik egy **Analyzer** tulajdonsággal. Ha az index lefordított karakterláncokat tartalmaz, például a különböző angol és kínai nyelvű szövegeket, megadhatja az egyes mezők nyelvi elemzőit az elemzők gazdag nyelvi képességeinek eléréséhez.  

Az Azure Cognitive Search támogatja a Lucene által támogatott 35-elemzőket, valamint az Office-ban és a Bingben használt, saját tulajdonú Microsoft természetes nyelvi feldolgozási technológiával támogatott 50-elemzőket.

## <a name="comparing-analyzers"></a>Elemzők összehasonlítása

Előfordulhat, hogy egyes fejlesztők inkább a Lucene ismerős, egyszerű és nyílt forráskódú megoldását részesítik előnyben. A Lucene nyelvi elemzői gyorsabbak, de a Microsoft-elemzők fejlett funkciókkal rendelkeznek, például a morfológiai elemzéshez, a szavak összetételét (például a német, a dán, a holland, a svéd, a norvég, az észt, a Finish, a magyar és a szlovák nyelv) és az entitások felismerését (URL-címek, e-mailek, dátumok Ha lehetséges, a Microsoft és a Lucene elemzők összehasonlítását is el kell végeznie, hogy eldöntse, melyik egy jobb illeszkedés. 

A Microsoft-elemzők indexelése a nyelvtől függően a Lucene egyenértékűként átlagosan két-háromszor lassabban történik. A keresési teljesítmény nem lehet jelentős hatással az átlagos méretű lekérdezéseknél. 

### <a name="english-analyzers"></a>Angol analizátorok

Az alapértelmezett elemző a standard Lucene, amely az angol nyelvhez jól működik, de talán nem, valamint a Lucene angol analizátor vagy a Microsoft angol analizátora. 
 
+ A Lucene angol analizátora kiterjeszti a standard Analyzert. Eltávolítja a birtokosok (záró) szavakat a szavakból, és a porton kívüli algoritmust alkalmazza, és eltávolítja az angol leállítási szavakat.  

+ A Microsoft angol Analyzer morfológiai elemzéshez hajt végre a művelet helyett. Ez azt jelenti, hogy az ragozott és a szabálytalan Word-űrlapokat sokkal jobban kezelheti, ami több releváns keresési eredményt eredményez 

## <a name="configuring-analyzers"></a>Elemzők konfigurálása

A nyelvi elemzők a következőképpen használhatók:. Az index definíciójának minden mezőjénél megadhatja az **analizátor** tulajdonságot egy Analyzer-névre, amely megadja a nyelv és a nyelvi készlet (Microsoft vagy Lucene) értékét. Ugyanez az analizátor lesz alkalmazva a mező indexeléséhez és kereséséhez. Megadhatja például, hogy az angol, a francia és a spanyol nyelvű szállodai leírások külön mezői legyenek, amelyek ugyanabban az indexben vannak egymás mellett.

> [!NOTE]
> Egy adott mezőnél nem lehet más nyelvi elemzőt használni, mint a lekérdezés időpontjában. Ez a képesség [Egyéni elemzők](index-add-custom-analyzers.md)számára van fenntartva. Ezért ha a **searchAnalyzer** vagy a **indexAnalyzer** tulajdonságot a Language Analyzer nevére próbálja beállítani, a REST API hibaüzenetet ad vissza. Ehelyett a **Analyzer** tulajdonságot kell használnia.

A **searchFields** lekérdezési paraméterrel megadásával meghatározhatja, hogy melyik nyelvspecifikus mezőt szeretné keresni a lekérdezésekben. Áttekintheti azokat a lekérdezési példákat is, amelyek tartalmazzák a Analyzer tulajdonságot a [keresési dokumentumokban](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

További információ az index tulajdonságairól: [index létrehozása &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Az Azure Cognitive Search elemzésével kapcsolatos további információkért lásd: [elemzők az Azure-ban Cognitive Search](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Nyelvi analizátorok listája 
 Alább látható a támogatott nyelvek listája, valamint a Lucene és a Microsoft Analyzer neve.  

|Nyelv|Microsoft Analyzer neve|Lucene Analyzer neve|  
|--------------|-----------------------------|--------------------------|  
|Arab|ar. Microsoft|ar. Lucene|  
|örmény||Lucene|  
|Bangla|bn. Microsoft||  
|Baszk||EU. Lucene|  
|Bolgár|BG. Microsoft|BG. Lucene|  
|Katalán|CA. Microsoft|CA. Lucene|  
|kínai (egyszerűsített)|zh-Hans. Microsoft|zh-Hans. Lucene|  
|kínai (hagyományos)|zh-Hant. Microsoft|zh-Hant. Lucene|  
|Horvát|HR. Microsoft||  
|Cseh|cs. Microsoft|cs. Lucene|  
|Dán|da. Microsoft|da. Lucene|  
|Holland|nl. Microsoft|nl. Lucene|  
|Angol|en. Microsoft|en. Lucene|  
|Észt|et. Microsoft||  
|Finn|Fi. Microsoft|Fi. Lucene|  
|Francia|fr. Microsoft|fr. Lucene|  
|Gallego||Gl. Lucene|  
|Német|de. Microsoft|de. Lucene|  
|Görög|el. Microsoft|el. Lucene|  
|gudzsaráti|Gu. Microsoft||  
|Héber|ő. Microsoft||  
|Hindi|Hi. Microsoft|Hi. Lucene|  
|Magyar|hu. Microsoft|hu. Lucene|  
|Izlandi|a. Microsoft||  
|Indonéz (Bahasa)|azonosító. Microsoft|ID. Lucene|  
|Ír||ga. Lucene|  
|Olasz|it. Microsoft|it. Lucene|  
|Japán|ja. Microsoft|ja. Lucene|  
|kannada|KN. Microsoft||  
|Koreai|ko. Microsoft|ko. Lucene|  
|Lett|lv. Microsoft|lv. Lucene|  
|Litván|lt. Microsoft||  
|malajálam|ml. Microsoft||  
|Maláj (latin betűs)|MS. Microsoft||  
|marathi|Mr. Microsoft||  
|Norvég|NB. Microsoft|nem. Lucene|  
|perzsa||fa. Lucene|  
|Lengyel|pl. Microsoft|pl. Lucene|  
|Portugál (Brazília)|pt-br. Microsoft|pt-br. Lucene|  
|Portugál (Portugália)|PT-pt. Microsoft|PT-pt. Lucene|  
|pandzsábi|PA. Microsoft||  
|Román|ro. Microsoft|ro. Lucene|  
|Orosz|ru. Microsoft|ru. Lucene|  
|Szerb (cirill betűs)|SR-cirill betűs. Microsoft||  
|Szerb (latin betűs)|SR-latin. Microsoft||  
|Szlovák|sk. Microsoft||  
|Szlovén|SL. Microsoft||  
|Spanyol|es. Microsoft|es. Lucene|  
|Svéd|Sv. Microsoft|Sv. Lucene|  
|tamil|ta. Microsoft||  
|telugu|te. Microsoft||  
|Thai|th. Microsoft|th. Lucene|  
|Török|TR. Microsoft|TR. Lucene|  
|Ukrán|Egyesült Királyság. Microsoft||  
|urdu|a. Microsoft||  
|Vietnami|VI. Microsoft||  

 Az [Apache Lucene nyelvi elemzői](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )a **Lucene** -mel ellátott nevekkel rendelkező elemzőket használják.

## <a name="see-also"></a>Lásd még  

+ [Index létrehozása &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [AnalyzerName osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

