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
ms.openlocfilehash: 5b8d97005d8f404a296ddb45e92b65e4aa811aa3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486770"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Az előrejelzési pontszámok előrejelzési pontosságot jeleznek a szándék és az entitások számára

Az előrejelzési pontszám azt jelzi, hogy a LUIS megbízhatósági foka a felhasználók teljes számának előrejelzési eredményeire utal.

Az előrejelzési pontszám nulla (0) és egy (1) között van. Egy példa arra, hogy egy nagyon magabiztos LUIS-pontszám 0,99. Az alacsony megbízhatósági pontszám például 0,01. 

|Pontszám értéke|megbízhatósági|
|--|--|
|1|határozott egyezés|
|0,99|magas megbízhatóság|
|0,01|alacsony megbízhatóság|
|0|nem megfelelőnek határozott hiba|

## <a name="top-scoring-intent"></a>Top-pontozási szándék

Minden Kimondás becslése egy top-pontozási szándékot ad vissza. Ez az előrejelzés az előrejelzési pontszámok számszerű összehasonlítását. 

## <a name="proximity-of-scores-to-each-other"></a>A pontszámok közelsége egymáshoz

A 2 legfontosabb pontszámok között nagyon kis különbség lehet. LUIS nem jelzi ezt a közelséget, mint a felső pontszámot.  

## <a name="return-prediction-score-for-all-intents"></a>Az összes cél visszaadott előrejelzési pontszáma

Egy tesztelési vagy végponti eredmény az összes leképezést tartalmazhatja. Ez a konfiguráció a végponton a megfelelő querystring név/érték párok használatával van beállítva.

|Prediction API|QueryString neve|
|--|--|
|V3|`show-all-intents=true`|
|2\. verzió|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Hasonló pontszámokkal rendelkező leképezések áttekintése

Az összes cél pontszámának áttekintése jó módszer annak ellenőrzésére, hogy nem csak a megfelelő szándék van-e azonosítva, de a következő azonosított szándékok pontszáma jelentősen és következetesen alacsonyabb a hosszúságú kimondott szöveg.

Ha több cél is van, a Kimondás kontextusa alapján az előrejelzési pontszámok is megváltozhatnak a leképezések között. A probléma megoldásához folytassa a hosszúságú kimondott szöveg hozzáadásával az egyes szándékokhoz a kontextusbeli különbségek széles skáláját, vagy megadhatja, hogy az ügyfélalkalmazás, például a csevegési robot, hogyan kezelje a 2 legfontosabb szándékot.

A **nem determinisztikus betanítás**miatt a 2 cél, amely túl szorosan mutat, megfordítható. A legfelső pontszámot lehet a második csúcs, a második pedig az első legfelső pontszámot. Az ilyen helyzetek megelőzése érdekében vegyen fel például hosszúságú kimondott szöveg az első két cél elé, hogy a szó kiválasztásával és kontextusával a 2 szándékot megkülönböztetse. A két szándéknak azonos számú példa hosszúságú kimondott szöveg kell lennie. A leválasztásra szolgáló szabály, amely megakadályozza a betanítást, 15%-os különbség a pontszámokban.

Kikapcsolhatja a **nem determinisztikus képzést** az [összes adattal betanítva](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>A különböző képzési munkamenetek közötti előrejelzések közötti különbségek

Ha egy másik alkalmazásban ugyanazt a modellt tanítja, és a pontszámok nem azonosak, ez a különbség azért van, mert nem determinisztikus (véletlenszerű) **képzésre** van szüksége. Másodszor, hogy a Kimondás bármely átfedése egynél több szándékot jelent, az azonos Kimondás alapjául szolgáló cél a képzés alapján változhat.

Ha a csevegési robot egy konkrét LUIS-pontszámra van szüksége, hogy jelezze a megbízhatóságot egy szándékban, használja a pontszám különbségét az első két cél között. Ez a helyzet rugalmasságot biztosít a betanítás változásaihoz.

Kikapcsolhatja a **nem determinisztikus képzést** az [összes adattal betanítva](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>E (kitevő) jelölés

Az előrejelzési pontszámok használhatnak kitevő-jelölést, amely az 0-1 tartomány fölött *jelenik* meg, például `9.910309E-07`. Ez a pontszám nagyon **kis** számot jelez.

|E jelölési pontszám |Tényleges pontszám|
|--|--|
|9.910309 e-07|.0000009910309|

## <a name="punctuation"></a>központozási

[További](luis-concept-utterance.md#punctuation-marks) információ a központozás használatáról vagy kihagyásáról. 

## <a name="next-steps"></a>További lépések

Az entitások LUIS-alkalmazáshoz való hozzáadásával kapcsolatos további tudnivalókért tekintse meg az [entitások hozzáadása](luis-how-to-add-entities.md) című témakört.
