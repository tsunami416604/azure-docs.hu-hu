---
title: Jó példa kimondott szöveg
titleSuffix: Language Understanding - Azure Cognitive Services
description: Beszédmódok vannak adjon meg a felhasználó az alkalmazás által igényelt értelmezése. Kifejezések, amelyek úgy gondolja, hogy a felhasználók be fogja gyűjteni. Vegye fel ugyanazt jelenti, de tevődnek utterances eltérően word hossza és a word elhelyezési.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: diberry
ms.openlocfilehash: 4f6bb367e706771707b65445c91619d439257207
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014837"
---
# <a name="utterances-in-luis"></a>A LUIS kimondott szöveg

**Beszédmódok** vannak a bemeneti adatok a felhasználó az alkalmazás által igényelt értelmezése. LUIS kinyerésére szándékok és entitások őket betanításához fontos számos különböző adatbevitelek esetében minden egyes szándékot rögzítése. Aktív tanulás, vagy a folyamat az új utterances betanításához folyamatos elengedhetetlen a gép megtanult intelligencia, amely a LUIS biztosít.

Kifejezések, amelyek úgy gondolja, hogy a felhasználók be fogja gyűjteni. Vegye fel ugyanazt jelenti, de tevődnek utterances eltérően word hossza és a word elhelyezési. 

## <a name="how-to-choose-varied-utterances"></a>Változatos utterances kiválasztása
Amikor Ön először első lépésként [példa beszédmódok hozzáadása](luis-how-to-add-example-utterances.md) figyelembe kell venni néhány alapelvet, a LUIS-modell, Íme.

### <a name="utterances-arent-always-well-formed"></a>Beszédmódok mindig nem megfelelő
Elképzelhető, hogy egy mondatot, például "Book me egy jegyet Párizs", vagy egy kódrészletet egy mondatot, például a "Foglalás" vagy "Párizs repülési."  Felhasználók gyakran ellenőrizze a helyesírási hibákat. Az alkalmazás megtervezésekor vegye figyelembe a-e, helyesírás-ellenőrzésének felhasználói bevitel LUIS való továbbítás előtt. A [Bing Spell Check API] [ BingSpellCheck] az intelligens hangfelismerési szolgáltatással integrálható. Társíthatja a LUIS-alkalmazás egy külső kulcs a Bing Spell Check API számára való közzétételekor. Ha Ön nem helyesírás-ellenőrzés felhasználói kimondott szöveg, a LUIS kell vonat a a kimondott szöveg, amely tartalmazza a gépelési és helyesírási hibák.

### <a name="use-the-representative-language-of-the-user"></a>A felhasználó nyelvi használata
Beszédmódok kiválasztásakor vegye figyelembe, hogy mit gondol gyakori kifejezés vagy kifejezés, lehetséges, hogy nem a tipikus felhasználónak az ügyfélalkalmazás. Előfordulhat, hogy nem rendelkeznek tartományi felhasználói élményt. Ezért legyen óvatos, szavakat vagy kifejezéseket, hogy a felhasználó csak megemlíteni ha voltak szakértői használatakor.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Válassza ki a változatos terminológia, valamint a megfogalmazása drasztikusnak
Akkor is, ha változatos mondat minták létrehozása erőfeszítést van szüksége, fog továbbra is ugyanezt az egyes szókincsből eredőket találja.

Hajtsa végre az alábbi példa kimondott szöveg:

|Példák kimondott szövegekre|
|--|
|Hogyan szerezhetem be a számítógépet?|
|Hol találom ezt a számítógépet?|
|Szeretnék beolvasni egy olyan számítógépen, hogyan tájékozódhatom arról?|
|Mikor lehet egy számítógép?| 

Itt, a core kifejezés a "számítógép", nem változott. Asztali számítógép, hordozható számítógép, munkaállomás vagy akár csak gépen sikerült mondják. A LUIS intelligensen kikövetkezteti a szinonimák környezetből, de képzéshez utterances létrehozásakor még jobb eltérő őket.

## <a name="example-utterances-in-each-intent"></a>Példa utterances az egyes leképezés
Minden egyes szándékot példa utterances, legalább 10-15 rendelkeznie kell. Ha megjelölésű példa megszólalásokat nem rendelkező, nem lesz képes LUIS betanításához. Ha az egy vagy néhány példa utterances megjelölésű, LUIS fog nem előre pontosan a célt. 

## <a name="add-small-groups-of-10-15-utterances-for-each-authoring-iteration"></a>Az Authoring Tool törzsének 10 – 15 utterances kis csoportok hozzáadása
A modell minden egyes ismétléskor ne adjon hozzá egy nagy mennyiségű kimondott szöveg. Beszédmódok hozzáadása több tíz mennyiségét. [Train](luis-how-to-train.md), [közzététele](luis-how-to-publish-app.md), és [tesztelése](luis-interactive-test.md) újra.  

A LUIS, gondosan ki van jelölve, hogy megcímkézzen hatékony modelleket hoz létre. Túl sok beszédmódok hozzáadása nincs értékes keveredési, mert.  

Néhány utterances, majd indítása jobb [tekintse át a végpont utterances](luis-how-to-review-endoint-utt.md) megfelelő szándék előrejelzése és egyéb entitások kivonási.

## <a name="punctuation-marks"></a>Központozási jelek

A LUIS nem hagyja figyelmen kívül írásjelek, alapértelmezés szerint, mert egyes ügyfélalkalmazások helyezhet többszörösére. Ezek közé. Ellenőrizze, hogy a példa utterances használatával írásjelek és a nem absztrakt ahhoz, hogy mindkét stílusok azonos relatív pontszámokat ad vissza. Ha írásjelek nincs meghatározott jelentéssel az ügyfélalkalmazásban, érdemes lehet [figyelmen kívül hagyja a központozási](#ignoring-words-and-punctuation) minták használatával. 

## <a name="ignoring-words-and-punctuation"></a>Szavak és írásjelek figyelmen kívül hagyása
Ha azt szeretné, figyelmen kívül hagyja a szó vagy az a példában utterance (kifejezés) absztrakt, egy [minta](luis-concept-patterns.md#pattern-syntax) az a _figyelmen kívül hagyása_ szintaxist. 

## <a name="training-utterances"></a>Képzési kimondott szöveg
Képzési nem determinisztikus: az utterance (kifejezés) előrejelzési némileg eltérőek lehetnek verziók és az alkalmazások között.

## <a name="testing-utterances"></a>Tesztelési kimondott szöveg 

A fejlesztők el kell indulnia a LUIS alkalmazás valódi forgalom tesztelés utterances a végpontra küldésével. Ezek a kimondott szöveg a szándékok és entitások teljesítményének javítását szolgálják [tekintse át a kimondott szöveg](luis-how-to-review-endoint-utt.md). A LUIS-webhely panel tesztelése az elküldött tesztek nem kap a végponton keresztül, és ezért nem jelentenek aktív tanulás. 

## <a name="review-utterances"></a>Tekintse át a kimondott szöveg
Miután a modellt, betanított, a közzétett és a fogadó [végpont](luis-glossary.md#endpoint) lekérdezéseket, [tekintse át a kimondott szöveg](luis-how-to-review-endoint-utt.md) LUIS által javasolt. A LUIS választja ki a végpont kimondott szöveg, amely rendelkezik a leképezés vagy egy entitás alacsony értékeket. 

## <a name="best-practices"></a>Ajánlott eljárások
Felülvizsgálat [ajánlott eljárások](luis-concept-best-practices.md) további.

## <a name="next-steps"></a>További lépések
Lásd: [példa beszédmódok hozzáadása](luis-how-to-add-example-utterances.md) a LUIS-alkalmazások felhasználói utterances megértéséhez képzési információkat.

[BingSpellCheck]: https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/proof-text