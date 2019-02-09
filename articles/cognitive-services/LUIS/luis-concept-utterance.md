---
title: Jó példa kimondott szöveg
titleSuffix: Language Understanding - Azure Cognitive Services
description: Beszédmódok vannak adjon meg a felhasználó az alkalmazás által igényelt értelmezése. Kifejezések, amelyek úgy gondolja, hogy a felhasználók be fogja gyűjteni. Vegye fel ugyanazt jelenti, de tevődnek utterances eltérően word hossza és a word elhelyezési.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: ae3a2788a0d5451736433a6338a71eda3d0ad9ca
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984358"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Jó utterances Mik a LUIS alkalmazás ismertetése

**Beszédmódok** vannak a bemeneti adatok a felhasználó az alkalmazás által igényelt értelmezése. LUIS kinyerésére szándékok és entitások őket betanításához fontos rögzítése egy másik példa utterances az egyes szándékot számos. Aktív tanulás, vagy a folyamat az új utterances betanításához folyamatos elengedhetetlen a gép megtanult intelligencia, amely a LUIS biztosít.

Kimondott szöveg, amely úgy gondolja, hogy a felhasználók be fogja gyűjteni. Többek között a kimondott szöveg, amely ugyanazt jelenti, de a számos különböző módon tevődnek:

* Utterance (kifejezés) hossza – rövid, közepes vagy hosszú az ügyfélalkalmazás számára
* A Word és kifejezés hossza 
* A Word elhelyezési - entitás elején, a középső és a végén az utterance (kifejezés)
* Szintaxis 
* Pluralization
* Amely szerint
* Főnevet és művelet kiválasztása
* Absztrakt - jó különböző helyes, és a nem megfelelő, és nincs szintaxis

## <a name="how-to-choose-varied-utterances"></a>Változatos utterances kiválasztása

Amikor Ön először első lépésként [példa beszédmódok hozzáadása](luis-how-to-add-example-utterances.md) figyelembe kell venni néhány alapelvet, a LUIS-modell, Íme.

### <a name="utterances-arent-always-well-formed"></a>Beszédmódok mindig nem megfelelő

Elképzelhető, hogy egy mondatot, például "Book egy jegyet Párizsba számomra", vagy egy kódrészletet egy mondatot, például a "Foglalás" vagy "Párizs repülési."  Felhasználók gyakran ellenőrizze a helyesírási hibákat. Az alkalmazás megtervezésekor vegye figyelembe, e használhatja [a Bing Spell Check](luis-tutorial-bing-spellcheck.md) a LUIS való továbbítás előtt javítsa ki a felhasználói bevitel. 

Ha Ön nem helyesírás-ellenőrzés felhasználói kimondott szöveg, a LUIS kell vonat a a kimondott szöveg, amely tartalmazza a gépelési és helyesírási hibák.

### <a name="use-the-representative-language-of-the-user"></a>A felhasználó nyelvi használata

Beszédmódok kiválasztásakor vegye figyelembe, hogy mit gondol gyakori kifejezés vagy kifejezés, lehetséges, hogy nem megfelelő-e az ügyfélalkalmazás jellemző felhasználói számára. Előfordulhat, hogy nem rendelkeznek tartományi felhasználói élményt. Ügyeljen arra, hogy szavakat vagy kifejezéseket, hogy a felhasználó csak megemlíteni ha voltak szakértői használatakor.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Válassza ki a változatos terminológia, valamint a megfogalmazása drasztikusnak

Akkor is, ha változatos mondat minták létrehozása erőfeszítést van szüksége, fog továbbra is ugyanezt az egyes szókincsből eredőket találja.

Hajtsa végre az alábbi példa kimondott szöveg:

|Példák kimondott szövegekre|
|--|
|Hogyan szerezhetem be a számítógépet?|
|Hol találom ezt a számítógépet?|
|Szeretnék beolvasni egy olyan számítógépen, hogyan tájékozódhatom arról?|
|Mikor lehet egy számítógép?| 

Itt, a core kifejezés a "számítógép", nem változott. Lehetőségeket, például az asztali számítógép, hordozható számítógép, munkaállomás vagy akár csak gép használja. A LUIS intelligensen kikövetkezteti a szinonimák környezetből, de képzéshez utterances létrehozásakor még jobb eltérő őket.

## <a name="example-utterances-in-each-intent"></a>Példa utterances az egyes leképezés

Minden egyes szándékot példa utterances, hogy legalább 15 rendelkeznie kell. Ha megjelölésű példa megszólalásokat nem rendelkező, nem lesz képes LUIS betanításához. Ha az egy vagy néhány példa utterances megjelölésű, LUIS fog nem előre pontosan a célt. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Az Authoring Tool törzsének 15 utterances kis csoportok hozzáadása

A modell minden egyes ismétléskor ne adjon hozzá egy nagy mennyiségű kimondott szöveg. Beszédmódok hozzáadása 15 mennyiségét. [Train](luis-how-to-train.md), [közzététele](luis-how-to-publish-app.md), és [tesztelése](luis-interactive-test.md) újra.  

A LUIS épít fel, hogy alaposan választja ki a LUIS-modell Szerző megcímkézzen hatékony modelleket. Túl sok beszédmódok hozzáadása nem értékes, mert ez zavart.  

Néhány utterances, majd indítása jobb [tekintse át a végpont utterances](luis-how-to-review-endoint-utt.md) megfelelő szándék előrejelzése és egyéb entitások kivonási.

## <a name="punctuation-marks"></a>Központozási jelek

A LUIS nem hagyja figyelmen kívül írásjelek, alapértelmezés szerint, mert egyes ügyfélalkalmazások helyezhet többszörösére. Ezek közé. Ellenőrizze, hogy a példa utterances használatával írásjelek és a nem absztrakt ahhoz, hogy mindkét stílusok azonos relatív pontszámokat ad vissza. Ha írásjelek nincs meghatározott jelentéssel az ügyfélalkalmazásban, érdemes lehet [figyelmen kívül hagyja a központozási](#ignoring-words-and-punctuation) minták használatával. 

## <a name="ignoring-words-and-punctuation"></a>Szavak és írásjelek figyelmen kívül hagyása

Ha azt szeretné, figyelmen kívül hagyja a szó vagy az a példában utterance (kifejezés) absztrakt, egy [minta](luis-concept-patterns.md#pattern-syntax) az a _figyelmen kívül hagyása_ szintaxist. 

## <a name="training-utterances"></a>Képzési kimondott szöveg

Képzési lehetőségek a lehetséges általában nem determinisztikus: az utterance (kifejezés) előrejelzési némileg eltérőek lehetnek verziók és az alkalmazások között. Eltávolíthatja a nem determinisztikus képzési frissítésével a [verzió beállítások](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API-t a `UseAllTrainingData` név-érték pár összes betanítási adatok használatához.

## <a name="testing-utterances"></a>Tesztelési kimondott szöveg 

A fejlesztők el kell valódi forgalmat a LUIS alkalmazás tesztelése a kimondott szöveg küldése a [előrejelzési végpont](luis-how-to-azure-subscription.md) URL-CÍMÉT. Ezek a kimondott szöveg a szándékok és entitások teljesítményének javítását szolgálják [tekintse át a kimondott szöveg](luis-how-to-review-endoint-utt.md). A LUIS-webhely panel tesztelése az elküldött tesztek nem kap a végponton keresztül, és ezért nem jelentenek aktív tanulás. 

## <a name="review-utterances"></a>Tekintse át a kimondott szöveg

Miután a modellt, betanított, a közzétett és a fogadó [végpont](luis-glossary.md#endpoint) lekérdezéseket, [tekintse át a kimondott szöveg](luis-how-to-review-endoint-utt.md) LUIS által javasolt. A LUIS választja ki a végpont kimondott szöveg, amely rendelkezik a leképezés vagy egy entitás alacsony értékeket. 

## <a name="best-practices"></a>Ajánlott eljárások

Felülvizsgálat [ajánlott eljárások](luis-concept-best-practices.md) és alkalmazza őket a rendszeres szerzői ciklus részeként.

## <a name="next-steps"></a>További lépések
Lásd: [példa beszédmódok hozzáadása](luis-how-to-add-example-utterances.md) a LUIS-alkalmazások felhasználói utterances megértéséhez képzési információkat.

