---
title: Előrejelzési pontszámok – LUIS
titleSuffix: Azure Cognitive Services
description: Az előrejelzési pontszám azt jelzi, hogy a LUIS API szolgáltatásnak milyen megbízhatósági foka van az előrejelzési eredményekhez a felhasználók teljes száma alapján.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 34ec5588a510574f4ea9f01bd23c6f6487e288da
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638359"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Az előrejelzési pontszámok előrejelzési pontosságot jeleznek a szándék és az entitások számára

Az előrejelzési pontszám azt jelzi, hogy a LUIS megbízhatósági foka az előrejelzési eredményekre utal, a felhasználók teljes száma alapján.

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

Minden utterance (kifejezés) előrejelzési adja vissza egy felső pontozási szándékot. Ez az előrejelzés az előrejelzési pontszámok számszerű összehasonlítását. A 2 legfontosabb pontszámok között nagyon kis különbség lehet. LUIS nem jelzi ezt a közelséget, mint a felső pontszámot.  

## <a name="return-prediction-score-for-all-intents"></a>Minden leképezések előrejelzési pontszámának visszaadása

Teszt vagy a végpont eredményeképpen minden leképezések is tartalmazhatnak. Ez a beállítás be van állítva a [végpont](https://aka.ms/v1-endpoint-api-docs) az a `verbose=true` lekérdezési karakterlánc név-érték pár.

## <a name="review-intents-with-similar-scores"></a>Tekintse át a hasonló pontszámok leképezések

Tekintse át az összes leképezések pontszámának módja a helyes győződjön meg arról, hogy nem csak a megfelelő leképezés azonosított azonban, hogy a következő azonosítja a leképezés a pontszám jelentősen alacsonyabb egységesen a kimondott szöveg.

Ha több leképezések Bezárás előrejelzési pontok utterance (kifejezés) egy, a környezet alapján LUIS a leképezések is végrehajtható. A probléma megoldásához folytassa a hosszúságú kimondott szöveg hozzáadásával az egyes szándékokhoz a kontextusbeli különbségek széles skáláját, vagy megadhatja, hogy az ügyfélalkalmazás, például a csevegési robot, hogyan kezelje a 2 legfontosabb szándékot.

A nem determinisztikus betanítás miatt a 2 cél, amely túl szorosan mutat, megfordítható. A felső pontszám második felső válhat, és a második felső pontszám válhat az első felső pontszámot. Az ilyen helyzetek megelőzése érdekében vegyen fel például hosszúságú kimondott szöveg az első két cél elé, hogy a szó kiválasztásával és kontextusával a 2 szándékot megkülönböztetse. A két szándék kapcsolatos példa utterances azonos számú kell rendelkeznie. El, hogy a betanítás, mert invertálásának tapasztalatok 15 %-os különbség a pontszámokat.

Kikapcsolhatja a nem determinisztikus képzést az [összes adattal](luis-how-to-train.md#train-with-all-data)betanítva.

## <a name="differences-with-predictions-between-different-training-sessions"></a>A különböző képzési munkamenetek közötti előrejelzések közötti különbségek

Ha egy másik alkalmazásban ugyanazt a modellt tanítja, és a pontszámok nem azonosak, ez a különbség azért van, mert nem determinisztikus (véletlenszerű) képzésre van szüksége. Másodszor az utterance (kifejezés) egynél több beszédszándék bármely átfedésének azt jelenti, hogy az azonos utterance (kifejezés) a felső leképezésének képzési alapján módosíthatja.

Ha a csevegési robot egy konkrét LUIS-pontszámra van szüksége, hogy jelezze a megbízhatóságot egy szándékban, használja a pontszám különbségét az első két cél között. Ez a helyzet rugalmasságot biztosít a betanítás változásaihoz.

## <a name="e-exponent-notation"></a>E (kitevő) jelölés

Előrejelzési pontszámok használható kitevő jelöléssel *jelennek meg* fent a 0 – 1 tartományban, mint például `9.910309E-07`. Ezt az értéket arra utalhat, hogy egy rendkívül **kis** számát.

|E jelöléssel pontszám |Tényleges eredmény|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Absztrakt

[További](luis-concept-utterance.md#punctuation-marks) információ a központozás használatáról vagy kihagyásáról. 

## <a name="next-steps"></a>További lépések

Lásd: [entitások hozzáadása](luis-how-to-add-entities.md) további információ entitások hozzáadása a LUIS-alkalmazás.
