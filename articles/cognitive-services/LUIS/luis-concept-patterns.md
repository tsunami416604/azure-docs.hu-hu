---
title: Minták segítenek előrejelzés - LUIS
titleSuffix: Azure Cognitive Services
description: A minta lehetővé teszi, hogy nagyobb pontosságot egy szándék ot anélkül, hogy sokkal több utterances.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6c1b548de25369c162b4a08dfa20fce62c17f99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75890287"
---
# <a name="patterns-improve-prediction-accuracy"></a>A minták javítják az előrejelzés pontosságát
A minták célja, hogy javítsa a pontosságot, ha több kimondott szöveg nagyon hasonló.  A minta lehetővé teszi, hogy nagyobb pontosságot egy szándék ot anélkül, hogy sokkal több utterances. 

## <a name="patterns-solve-low-intent-confidence"></a>A minták megoldják az alacsony leképezési megbízhatóságot
Fontolja meg egy Emberi erőforrások alkalmazást, amely egy alkalmazotthoz viszonyítva jelentést tesz a szervezeti diagramon. Adott egy alkalmazott nevét és kapcsolatát, a LUIS az érintett alkalmazottakat adja vissza. Vegyünk egy alkalmazottat, Tomot, akinek alice nevű menedzserük van, és egy csapat beosztottnak, akiknek a neve: Michael, Rebecca és Carl.

![Szervezeti diagram képe](./media/luis-concept-patterns/org-chart.png)

|Beszédmódok|Előre jelzett szándék|Szándék pontszáma|
|--|--|--|
|Ki Tom beosztottja?|GetOrgDiagram|.30|
|Ki Tom beosztottja?|GetOrgDiagram|.30|

Ha egy alkalmazás 10 és 20 közötti kimondott szöveggel rendelkezik, különböző hosszúságú mondatokkal, különböző szósorrenddel és még különböző szavakkal is (az "alárendelt", "manage", "jelentés" szinonimái), a LUIS alacsony megbízhatósági pontszámot adhat vissza. Hozzon létre egy mintát, amely segít a LUIS-nak megérteni a szósorrend fontosságát. 

A minták a következő helyzeteket oldják meg: 

* A szándék pontszám a alacsony
* A helyes szándék nem a legmagasabb pontszámot, de túl közel a legmagasabb pontszámot. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>A minták nem garantálják a szándékot
A minták előrejelzési technológiák kombinációját használják. A minta kimondott szövegének egy sablon utterance (kifejezés) beállítása nem garantálja a szándék előrejelzését, de erős jel. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>A minták nem javítják a gép által megtanult entitásészlelést

A minta elsősorban célja, hogy segítse a szándékok és szerepkörök előrejelzését. A _pattern.any_ entitás szabad képernyős entitások kinyerésére szolgál. Míg a minták entitásokat használnak, a minta nem segít a gép által megtanult entitás észlelése.  

Ne számítson jobb entitás-előrejelzésre, ha egyetlen mintába több kimondott szöveget csuk össze. Egyszerű entitások tűz, kimondott szöveget kell hozzáadnia, vagy használja a lista entitások, különben a minta nem fog kigyulladni.

## <a name="patterns-use-entity-roles"></a>A minták entitásszerepköröket használnak
Ha egy mintában két vagy több entitás környezetfüggő, a minták [entitásszerepkörök](luis-concept-roles.md) használatával nyerik ki az entitások környezetfüggő adatait.  

## <a name="prediction-scores-with-and-without-patterns"></a>Előrejelzés pontszámok és minták nélkül
Adott esetben a kimondott szöveg, LUIS képes lenne növelni előrejelzési megbízhatósági minták nélkül. A minták növelik a megbízhatósági pontszám, anélkül, hogy annyi utterances.  

## <a name="pattern-matching"></a>Mintaegyeztetés
A minta egyeztetése alapján észleli az entitások a mintában először, majd érvényesíti a többi szó és a szó sorrendje a minta. Entitások szükségesek a minta egyezéshez a minta. A minta a jogkivonat szintjén kerül alkalmazásra, nem a karakter szintjén. 

## <a name="pattern-only-apps"></a>Csak mintázatú alkalmazások
Létrehozhat egy alkalmazást olyan szándékokkal, amelyek nem rendelkeznek példa utterances, mindaddig, amíg van egy minta az egyes szándékok. Egy minta-csak alkalmazás, a minta nem tartalmazhat gép megtanult entitásokat, mert ezek példa utterances. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg [az ajánlott eljárásokat](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Minta szintaxisa

Ismerje meg a minta szintaxisát a [minta szintaxishivatkozásból.](reference-pattern-syntax.md) 

## <a name="next-steps"></a>További lépések

További információ a mintákról:

* [Mintázatok hozzáadása](luis-how-to-model-intent-pattern.md)
* [A pattern.any entitás hozzáadása](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Mintázatok szintaxisa](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan valósíthat meg mintákat ebben az oktatóanyagban](luis-tutorial-pattern.md)
