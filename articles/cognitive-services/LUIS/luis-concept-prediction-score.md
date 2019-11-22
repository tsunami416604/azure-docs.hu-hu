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
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: b360bc82b80e834492b524acc5c4535b0409eda1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280827"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Az előrejelzési pontszámok előrejelzési pontosságot jeleznek a szándék és az entitások számára

Az előrejelzési pontszám azt jelzi, hogy a LUIS megbízhatósági foka a felhasználók teljes számának előrejelzési eredményeire utal.

Egy előrejelzési pontszám, nulla (0) és a egy (1) között. Egy példát egy magas abban a LUIS-pontszám 0.99. Egy példa egy alacsony megbízhatósági pontszám, 0,01. 

|Pontszám értéke|Megbízhatóság|
|--|--|
|1|egyértelmű egyezés|
|0.99|magas megbízhatóság|
|0.01|alacsony megbízhatósági|
|0|megfelelő határozott hiba|

## <a name="top-scoring-intent"></a>Felső-pontozás leképezés

Minden utterance (kifejezés) előrejelzési adja vissza egy felső pontozási szándékot. Ez az előrejelzés az előrejelzési pontszámok számszerű összehasonlítását. 

## <a name="proximity-of-scores-to-each-other"></a>A pontszámok közelsége egymáshoz

A 2 legfontosabb pontszámok között nagyon kis különbség lehet. LUIS nem jelzi ezt a közelséget, mint a felső pontszámot.  

## <a name="return-prediction-score-for-all-intents"></a>Minden leképezések előrejelzési pontszámának visszaadása

Teszt vagy a végpont eredményeképpen minden leképezések is tartalmazhatnak. Ez a konfiguráció a végponton a megfelelő querystring név/érték párok használatával van beállítva.

|Prediction API|QueryString neve|
|--|--|
|V3|`show-all-intents=true`|
|2\. verzió|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Tekintse át a hasonló pontszámok leképezések

Az összes cél pontszámának áttekintése jó módszer annak ellenőrzésére, hogy nem csak a megfelelő szándék van-e azonosítva, de a következő azonosított szándékok pontszáma jelentősen és következetesen alacsonyabb a hosszúságú kimondott szöveg.

Ha több leképezések Bezárás előrejelzési pontok utterance (kifejezés) egy, a környezet alapján LUIS a leképezések is végrehajtható. A probléma megoldásához folytassa a hosszúságú kimondott szöveg hozzáadásával az egyes szándékokhoz a kontextusbeli különbségek széles skáláját, vagy megadhatja, hogy az ügyfélalkalmazás, például a csevegési robot, hogyan kezelje a 2 legfontosabb szándékot.

A **nem determinisztikus betanítás**miatt a 2 cél, amely túl szorosan mutat, megfordítható. A felső pontszám második felső válhat, és a második felső pontszám válhat az első felső pontszámot. Az ilyen helyzetek megelőzése érdekében vegyen fel például hosszúságú kimondott szöveg az első két cél elé, hogy a szó kiválasztásával és kontextusával a 2 szándékot megkülönböztetse. A két szándék kapcsolatos példa utterances azonos számú kell rendelkeznie. El, hogy a betanítás, mert invertálásának tapasztalatok 15 %-os különbség a pontszámokat.

Kikapcsolhatja a **nem determinisztikus képzést** az [összes adattal betanítva](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>A különböző képzési munkamenetek közötti előrejelzések közötti különbségek

Ha egy másik alkalmazásban ugyanazt a modellt tanítja, és a pontszámok nem azonosak, ez a különbség azért van, mert nem determinisztikus (véletlenszerű) **képzésre** van szüksége. Másodszor az utterance (kifejezés) egynél több beszédszándék bármely átfedésének azt jelenti, hogy az azonos utterance (kifejezés) a felső leképezésének képzési alapján módosíthatja.

Ha a csevegési robot egy konkrét LUIS-pontszámra van szüksége, hogy jelezze a megbízhatóságot egy szándékban, használja a pontszám különbségét az első két cél között. Ez a helyzet rugalmasságot biztosít a betanítás változásaihoz.

Kikapcsolhatja a **nem determinisztikus képzést** az [összes adattal betanítva](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>E (kitevő) jelölés

Előrejelzési pontszámok használható kitevő jelöléssel _jelennek meg_ fent a 0 – 1 tartományban, mint például `9.910309E-07`. Ezt az értéket arra utalhat, hogy egy rendkívül **kis** számát.

|E jelöléssel pontszám |Tényleges eredmény|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Absztrakt

[További](luis-concept-utterance.md#punctuation-marks) információ a központozás használatáról vagy kihagyásáról. 

## <a name="next-steps"></a>Következő lépések

Lásd: [entitások hozzáadása](luis-how-to-add-entities.md) további információ entitások hozzáadása a LUIS-alkalmazás.
