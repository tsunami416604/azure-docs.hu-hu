---
title: Az előrejelzés pontszám LUIS - Azure által visszaadott megértése |} Microsoft Docs
description: Ismerje meg, mi az előrejelzés pontszám azt jelenti, hogy a LUIS
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 1620fe80b032245c6ca19279c3981dcff4b9820f
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "35349948"
---
# <a name="prediction-score"></a>Előrejelzés pontszám
Előrejelzési jelző pontszámot fokának abban, hogy LUIS rendelkezik-e előrejelzés eredmények jelzi. 

Előrejelzési jelző pontszámot az általában nulla (0) és egy (1) között. Például egy magas biztosnak LUIS pontszám érték 0,99. Alacsony abban, hogy a pontszám például 0,01. 

|Pontszám érték|Megbízhatóság|
|--|--|
|1|egyértelmű egyezés|
|0,99|magas megbízhatóság|
|0.01|alacsony abban, hogy|
|0|határozott nem felel meg|

Egy utterance alacsony – abban, hogy pontszámot eredményez, LUIS emeli, amelyek a [LUIS] [ LUIS] webhely **leképezés** lapon, a megadott **feliratú-leképezés**  piros vázolt. 

![Pontszám eltérést](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Felső pontozási leképezés
Minden utterance előrejelzésének felső pontozási leképezés adja vissza. Ez az előrejelzés pontszámok numerikus összehasonlítása. A felső két pontszámok nagyon kis különbség a kettő között lehet. LUIS nem jelzi a közelségi kapcsolat eltérő pontszámok ad vissza.  

Ha a felső pontszámok közelében térjen vissza az összes leképezések pontszámot. A két leképezések, amelyek jelzik, a word-adatbázissal apróságokban vagy utterances is hozzáadhat és megállapodás vagy is LUIS-hívása alkalmazás, például egy chatbot, arról, hogyan kezelje a két legfontosabb leképezések programozott lehetőségeket. 

## <a name="return-prediction-score-for-all-intents"></a>Térjen vissza az összes leképezések előrejelzési jelző pontszámot
Teszt- vagy endpoint eredményeként minden leképezések tartalmazhatnak. Ez a konfiguráció be van állítva a [végpont](https://aka.ms/v1-endpoint-api-docs) rendelkező a `verbose=true` lekérdezési karakterlánc név-érték pár. 

## <a name="review-intents-with-similar-scores"></a>Tekintse át a hasonló pontszámok leképezések
Tekintse át az összes leképezések pontszámot módja a helyes győződjön meg arról, hogy nem csak a helyes leképezés, amelynél, azonban, hogy a következő azonosított leképezés a pontszám jelentősen alacsonyabb következetesen utterances. 

Ha több leképezések Bezárás előrejelzés pontszámok, egy utterance keretében alapján LUIS a leképezések is végrehajtható. A javítás érdekében továbbra is minden leképezést és környezetfüggő különbségek többféle utterances hozzáadása.   

## <a name="e-exponent-notation"></a>E (hatványát) jelöléssel

Előrejelzés pontszámok használható kitevő notation *szereplő* fent a 0-1 között, például a `9.910309E-07`. Ez az érték utalhat, hogy egy nagyon **kis** számát.

|E notation pontszám |Tényleges pontszám|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="differences-with-predictions"></a>Az előrejelzés különbségek
Amikor a vonat ugyanannak a modellnek egy másik alkalmazásban, és a pontszámok nem ilyen azonos, ennek az az oka a képzés véletlenszerű eleme van. Másodszor az egynél több leképezés egy utterance bármely átfedésének azt jelenti, hogy az azonos utterance a felső leképezésének képzési alapján módosíthatja.

Ha a chatbot szükséges egy adott LUIS pontszám megjelölésű az vetett bizalmat jelzi, helyette használja a felső két leképezések pontszám különbsége. Változások a képzés rugalmasságot biztosít. 

## <a name="next-steps"></a>További lépések

Lásd: [entitások hozzáadása](luis-how-to-add-entities.md) entitások felvétele a LUIS app tájékozódhat.

[LUIS]:luis-reference-regions.md