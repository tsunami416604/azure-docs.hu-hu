---
title: Előrejelzési pontszámok
titleSuffix: Language Understanding - Azure Cognitive Services
description: Az intelligens HANGFELISMERÉSI API szolgáltatás előrejelzési eredményeket, rendelkezik megbízhatósági mértékét jelzi az előrejelzési pontszámot egy felhasználó utterance (kifejezés) alapján.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 383ce4c4248f7e21f745f503c74a29cb613983e2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121757"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Előrejelzés-hoz közeliek előrejelzési pontosság szándékot és entitások

Előrejelzési pontszámot előrejelzési eredményeket, LUIS rendelkezik megbízhatósági mértékét jelzi a felhasználó utterance (kifejezés) alapján.

Egy előrejelzési pontszám, nulla (0) és a egy (1) között. Egy példát egy magas abban a LUIS-pontszám 0.99. Egy példa egy alacsony megbízhatósági pontszám, 0,01. 

|Pontszám értéke|Megbízhatóság|
|--|--|
|1|egyértelmű egyezés|
|0.99|magas megbízhatóság|
|0.01|alacsony megbízhatósági|
|0|megfelelő határozott hiba|

Az utterance (kifejezés) egy alacsony-megbízhatósági pontszám eredményez, amikor a LUIS kiemeli, hogy a a [LUIS](luis-reference-regions.md) webhely **szándékot** oldala, amelyen az azonosított **feliratú szándékot** piros vázolt.

![Pontszám eltérés](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Felső-pontozás leképezés

Minden utterance (kifejezés) előrejelzési adja vissza egy felső pontozási szándékot. Ezt nevezzük előrejelzési pontszámok numerikus összehasonlítása. Az első 2 pontszámok rendelkezhet egy kis különbsége. A LUIS nem jelzi a közelségi eltérő a felső pontszám visszaadása.  

## <a name="return-prediction-score-for-all-intents"></a>Minden leképezések előrejelzési pontszámának visszaadása

Teszt vagy a végpont eredményeképpen minden leképezések is tartalmazhatnak. Ez a beállítás be van állítva a [végpont](https://aka.ms/v1-endpoint-api-docs) az a `verbose=true` lekérdezési karakterlánc név-érték pár.

## <a name="review-intents-with-similar-scores"></a>Tekintse át a hasonló pontszámok leképezések

Tekintse át az összes leképezések pontszámának módja a helyes győződjön meg arról, hogy nem csak a megfelelő leképezés azonosított azonban, hogy a következő azonosítja a leképezés a pontszám jelentősen alacsonyabb egységesen a kimondott szöveg.

Ha több leképezések Bezárás előrejelzési pontok utterance (kifejezés) egy, a környezet alapján LUIS a leképezések is végrehajtható. A probléma megoldásához, továbbra is minden egyes beszédszándék környezetfüggő különbségek szélesebb körű számos beszédmódok hozzáadása vagy is rendelkezik, az ügyfélalkalmazás, például csevegőrobotot, győződjön meg arról, hogyan legyen kezelve a 2 felső leképezések programozott megállapításánál.

A 2 szándék fog vonatkozni, túl – szorosan pontozott, amely nem determinisztikus képzési miatt előfordulhat, hogy megfordítása. A felső pontszám második felső válhat, és a második felső pontszám válhat az első felső pontszámot. Ez a helyzet elkerülése érdekében az egyes adott utterance (kifejezés), a word választási lehetőség és környezetet, amely a 2 leképezések különbözteti meg a felső két szándék példa beszédmódok hozzáadása. A két szándék kapcsolatos példa utterances azonos számú kell rendelkeznie. El, hogy a betanítás, mert invertálásának tapasztalatok 15 %-os különbség a pontszámokat.

Felhasználók bármikor kikapcsolhatják a determinisztikus képzés által [összes adatot képzési](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Az előrejelzések különböző képzés közötti különbségek

Ha egy másik alkalmazásban ugyanannak a modellnek a vonat, pontszámokat nem azonosak, ez a különbség azért, mert nem determinisztikus képzési (a véletlenszerűségre azért elem). Másodszor az utterance (kifejezés) egynél több beszédszándék bármely átfedésének azt jelenti, hogy az azonos utterance (kifejezés) a felső leképezésének képzési alapján módosíthatja.

A csevegőrobot szükség van egy adott LUIS pontszám bizalom a megjelölésű jelzi, ha a felső két szándék pontszám különbsége kell használnia. Ez a helyzet képzési-változások rugalmasságot biztosít.

## <a name="e-exponent-notation"></a>E (kitevő) jelölés

Előrejelzési pontszámok használható kitevő jelöléssel *jelennek meg* fent a 0 – 1 tartományban, mint például `9.910309E-07`. Ezt az értéket arra utalhat, hogy egy rendkívül **kis** számát.

|E jelöléssel pontszám |Tényleges eredmény|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Absztrakt

Absztrakt egy külön jogkivonatot a LUIS. Az utterance (kifejezés), amely tartalmaz egy időszak végén, és a egy utterance (kifejezés), amely tartalmaz egy időszak végén két külön kimondott szöveg, és előfordulhat, hogy két különböző előrejelzés beolvasása. Győződjön meg arról, hogy a modell absztrakt vagy kezeli a a [példa utterances](luis-concept-utterance.md) (kellene és írásjelek nélkül) vagy a a [minták](luis-concept-patterns.md) figyelmen kívül a speciális szintaxissal írásjelek könnyebben: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>További lépések

Lásd: [entitások hozzáadása](luis-how-to-add-entities.md) további információ entitások hozzáadása a LUIS-alkalmazás.
