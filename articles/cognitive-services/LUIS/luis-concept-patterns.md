---
title: Minták Súgó előrejelzése – LUIS
titleSuffix: Azure Cognitive Services
description: Egy minta segítségével megjelölésű a nagyobb pontosság számos további kimondott szöveg megadása nélkül.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6234a13d07ac024849d1c890d82ef03e19c11af4
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012013"
---
# <a name="patterns-improve-prediction-accuracy"></a>Minták előrejelzési pontosság növeléséhez.
Pontosság javításához, ha több utterances nagyon hasonló minták lettek kialakítva.  Egy minta segítségével megjelölésű a nagyobb pontosság számos további kimondott szöveg megadása nélkül. 

## <a name="patterns-solve-low-intent-confidence"></a>Minták szándék alacsony megbízhatósági megoldása
Fontolja meg egy emberi erőforrások alkalmazáson, amely a szervezeti diagram viszonyítva egy alkalmazott a jelentést. Az alkalmazottak és a kapcsolat, LUIS ad vissza, az alkalmazottak érint. Fontolja meg egy alkalmazott Tom, egy alkalmazott felettesétől az Alice nevét, és a beosztottak nevű csapat: Michael Rebecca és Carl.

![Szervezeti diagram képe](./media/luis-concept-patterns/org-chart.png)

|Beszédmódok|Előre jelzett leképezés|Leképezési pontszám|
|--|--|--|
|A Tom alárendelt ki?|GetOrgChart|.30|
|Tom beosztottja ki?|GetOrgChart|.30|

Ha egy alkalmazás közötti 10 és 20 mondat, különböző szórendjét és még más szavakat (szinonimák "alárendelt", "felügyelet", "jelentés") különböző hosszúságú kimondott szöveg, LUIS egy alacsony megbízhatósági pontszám adhatnak vissza. Hozzon létre egy mintát, amely segít megismerni a Word-sorrend fontosságát. 

Minták oldja meg a következő helyzetekben: 

* A cél pontszáma alacsony
* A megfelelő szándék nem a legfelső pontszám, hanem a legfelső pontszámhoz közeledik. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Minták amelyek nem garantálja az leképezés
Minták előrejelzési technológiák kombinációját használja. Egy leképezésének egy sablon utterance (kifejezés) a mintát, nem garantálja a szándék előrejelzési de erős védelem. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>A minták nem javítják a géppel megtanult entitások észlelését

A mintázat elsődleges célja, hogy segítse a szándékok és a szerepkörök előrejelzését. A minta. minden entitás a szabad formátumú entitások kinyerésére szolgál. Míg a minták entitásokat használnak, a minta nem segít felderíteni a géppel megtanult entitásokat.  

Ne várja meg a továbbfejlesztett entitások előrejelzését, ha több hosszúságú kimondott szöveg összecsuk egyetlen mintára. Az egyszerű entitások a hosszúságú kimondott szöveg hozzáadásához vagy a listához tartozó entitások használata más, mint a minta nem fog tüzet használni.

## <a name="patterns-use-entity-roles"></a>Minták entitás szerepkörök használata
Ha két vagy több entitások egy minta kontextusban kapcsolódó, mintákat használják az entitást [szerepkörök](luis-concept-roles.md) entitásokról környezeti információk kinyeréséhez.  

## <a name="prediction-scores-with-and-without-patterns"></a>Előrejelzési pontszámok rendelkező és anélküli minták
Adott elegendő példa utterances, LUIS elvileg látnia kell növelni az előrejelzés megbízhatósági minták nélkül. Minták növelheti a megbízhatósági pontszám annyi kimondott szöveg megadása nélkül.  

## <a name="pattern-matching"></a>Minták egyeztetése
A minta egyezik az entitások belül a mintát először észlelése, majd ellenőrzése a többi szavak és szórendjét, a minta alapján. Egyeztetendő mappaminta minta entitások szükséges. A minta a token szintjén, nem a karakter szint alkalmazzák. 

## <a name="pattern-only-apps"></a>Csak minta alkalmazások
Olyan alkalmazásokat hozhat létre, amelyek nem rendelkeznek példa hosszúságú kimondott szöveg, feltéve, hogy az egyes szándékok esetében minta van. Csak minta alkalmazás esetében a minta nem tartalmazhat géppel megtanult entitásokat, mert ezek a hosszúságú kimondott szöveg példaként szükségesek. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg, [ajánlott eljárások](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>A minta-szintaxis

A minta szintaxisának ismertetése a [minta szintaxisának](reference-pattern-syntax.md)leírásában. 

## <a name="next-steps"></a>Következő lépések

További információ a mintákról:

* [Mintázatok hozzáadása](luis-how-to-model-intent-pattern.md)
* [Minta hozzáadása bármely entitáshoz](luis-how-to-add-entities.md##add-a-patternany-entity)
* [Mintázatok szintaxisa](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Ebben az oktatóanyagban minták megvalósítása](luis-tutorial-pattern.md)
