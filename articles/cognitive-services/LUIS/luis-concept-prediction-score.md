---
title: Előrejelzési pontszámok - LUIS
titleSuffix: Azure Cognitive Services
description: Az előrejelzési pontszám azt jelzi, hogy a LUIS API-szolgáltatás megbízhatósági foka a felhasználói utterance (kifejezés) alapján az előrejelzési eredmények.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280827"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Az előrejelzési pontszámok a szándék és az entitások előrejelzési pontosságát jelzik

Az előrejelzési pontszám azt jelzi, hogy a LUIS milyen megbízhatósággal rendelkezik a felhasználói utterance (kifejezés) előrejelzési eredményeihez.

Az előrejelzési pontszám nulla (0) és egy (1) között van. Egy rendkívül magabiztos LUIS-pontszám például 0,99. Egy példa a pontszám az alacsony megbízhatósági 0,01. 

|Pontszám értéke|Megbízhatóság|
|--|--|
|1|határozott egyezés|
|0.99|nagy bizalommal|
|0.01|alacsony megbízhatóság|
|0|határozott sikertelenség|

## <a name="top-scoring-intent"></a>A gólszerzési szándék

Minden kimondott szöveg előrejelzése egy csúcspontozási szándékot ad vissza. Ez az előrejelzés az előrejelzési pontszámok számszerű összehasonlítása. 

## <a name="proximity-of-scores-to-each-other"></a>Pontszámok egymáshoz való közelsége

A top 2 pontszámok lehet egy nagyon kis különbség közöttük. A LUIS nem jelzi ezt a közelséget, csak a legmagasabb pontszám visszaadása.  

## <a name="return-prediction-score-for-all-intents"></a>Visszatérési előrejelzési pontszám az összes leképezéshez

A teszt vagy végpont eredménye tartalmazhat minden szándékot. Ez a konfiguráció a végponton a megfelelő querystring név/érték pár használatával van beállítva.

|Prediction API|Querystring neve|
|--|--|
|V3|`show-all-intents=true`|
|2. verzió|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Hasonló pontszámmal rendelkező leképezések áttekintése

A pontszám áttekintése az összes szándékot egy jó módja annak ellenőrzésére, hogy nem csak a helyes szándék azonosított, de hogy a következő azonosított szándék pontszám jelentősen és következetesen alacsonyabb utterances.

Ha több leképezések close előrejelzési pontszámok, egy utterance (kifejezés) környezete alapján, luis válthat a szándékok között. A helyzet megoldásához továbbra is adjon utterances minden szándékhoz a környezetfüggő különbségek szélesebb skáláját, vagy beállíthatja az ügyfélalkalmazást, például egy csevegőrobotot, programozott döntéseket hozhat a 2 fő leképezés kezelésével kapcsolatban.

A 2 szándék, amelyek túl szorosan pontozott, lehet megfordítani miatt **nem determinisztikus képzés**. A legmagasabb pontszám lehet a második top, és a második top pontszám válhat az első top pontszámot. Ennek érdekében a helyzet elkerülése érdekében adjon hozzá példa utterances az első két leképezések, hogy az utterance (kifejezés) a szó választás és a környezet, amely megkülönbözteti a 2 szándékok. A két szándéknak körülbelül ugyanannyi példa utterances kell rendelkeznie. A szabály az ökölszabály a szétválasztás, hogy megakadályozzák inverzió miatt a képzés, egy 15%-os különbség a pontszámok.

A **nem determinisztikus oktatást** az [összes adattal való betanítással](luis-how-to-train.md#train-with-all-data)kapcsolhatja ki.

## <a name="differences-with-predictions-between-different-training-sessions"></a>Különbségek a különböző edzések előrejelzéseivel

Ha ugyanazt a modellt egy másik alkalmazásban tanítja be, és a pontszámok nem azonosak, ez a különbség azért van, mert **nem determinisztikus képzés** (a véletlenszerűség egyik eleme). Másodszor, egy utterance (kifejezés) egy több szándék átfedése azt jelenti, hogy a legfelső szándék az azonos utterance (kifejezés) módosításra lehet a képzés alapján.

Ha a csevegési robot egy adott LUIS-pontszámra van szüksége a szándékmegbízhatóság jelzéséhez, akkor a pontszámkülönbséget kell használnia a felső két leképezés között. Ez a helyzet rugalmasságot biztosít a képzés változásaihoz.

A **nem determinisztikus oktatást** az [összes adattal való betanítással](luis-how-to-train.md#train-with-all-data)kapcsolhatja ki.

## <a name="e-exponent-notation"></a>E (exponens) jelölés

Az előrejelzési pontszámok exponens jelöléssel _jelenhetnek meg,_ amely `9.910309E-07`a 0-1 tartomány felett jelenik meg, például . Ez a pontszám azt jelzi, egy nagyon **kis** szám.

|E jelölési pontszám |Tényleges pontszám|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Írásjelek

[További információ](luis-concept-utterance.md#punctuation-marks) az írásjelek használatáról és figyelmen kívül hagyásáról. 

## <a name="next-steps"></a>További lépések

További információ az [entitások hozzáadása](luis-how-to-add-entities.md) a LUIS-alkalmazáshoz való hozzáadásáról.
