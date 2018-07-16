---
title: A LUIS - Azure által visszaadott előrejelzési pontszám ismertetése |} A Microsoft Docs
description: Ismerje meg, mi az előrejelzési pontszám azt jelenti, hogy a LUIS
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: dd5bea791fed0c16195eadca03ba2f9a8c11da1b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044951"
---
# <a name="prediction-score"></a>Előrejelzési pontszám
Előrejelzési pontszámot rendelkezik a LUIS-előrejelzési eredményeket megbízhatósági mértékét jelzi. 

Előrejelzési pontszámot általában a nulla (0) és a egy (1) között. Egy példát egy magas abban a LUIS-pontszám 0.99. Egy példa egy alacsony megbízhatósági pontszám, 0,01. 

|Pontszám értéke|Megbízhatóság|
|--|--|
|1|egyértelmű egyezés|
|0.99|magas megbízhatóság|
|0.01|alacsony megbízhatósági|
|0|megfelelő határozott hiba|

Az utterance (kifejezés) egy alacsony-megbízhatósági pontszám eredményez, amikor a LUIS kiemeli, hogy a a [LUIS](luis-reference-regions.md) webhely **szándékot** oldala, amelyen az azonosított **feliratú szándékot** piros vázolt. 

![Pontszám eltérés](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Felső-pontozás leképezés
Minden utterance (kifejezés) előrejelzési adja vissza egy felső pontozási szándékot. Ez az előrejelzési eredmények numerikus összehasonlítása. A felső két pontszámok rendelkezhet egy kis különbsége. A LUIS a közelségi eltérő pontszámok adatszolgáltató nem jelzi.  

Ha a felső pontszámok közelében, az összes leképezések pontszámának kell visszaadnia. Beszédmódok vagy hozzáadása a két szándék, amelyek jelzik a különbségek a word választási lehetőséget és megállapodás vagy Ön is a LUIS-hívása alkalmazás, például egy csevegőrobotot, győződjön meg arról, hogyan legyen kezelve a két leggyakoribb leképezések programozott megállapításánál. 

## <a name="return-prediction-score-for-all-intents"></a>Minden leképezések előrejelzési pontszámának visszaadása
Teszt vagy a végpont eredményeképpen minden leképezések is tartalmazhatnak. Ez a beállítás be van állítva a [végpont](https://aka.ms/v1-endpoint-api-docs) az a `verbose=true` lekérdezési karakterlánc név-érték pár. 

## <a name="review-intents-with-similar-scores"></a>Tekintse át a hasonló pontszámok leképezések
Tekintse át az összes leképezések pontszámának módja a helyes győződjön meg arról, hogy nem csak a megfelelő leképezés azonosított azonban, hogy a következő azonosítja a leképezés a pontszám jelentősen alacsonyabb egységesen a kimondott szöveg. 

Ha több leképezések Bezárás előrejelzési pontok utterance (kifejezés) egy, a környezet alapján LUIS a leképezések is végrehajtható. A probléma megoldásához, továbbra is minden egyes beszédszándék környezetfüggő különbségek szélesebb körű számos beszédmódok hozzáadása.   

## <a name="e-exponent-notation"></a>E (kitevő) jelölés

Előrejelzési pontszámok használható kitevő jelöléssel *jelennek meg* fent a 0 – 1 tartományban, mint például `9.910309E-07`. Ezt az értéket arra utalhat, hogy egy rendkívül **kis** számát.

|E jelöléssel pontszám |Tényleges eredmény|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="differences-with-predictions"></a>Az előrejelzések különbségek
Ha egy másik alkalmazásban ugyanannak a modellnek a vonat, és a pontszámok nem ez ugyanaz, ennek az az oka egy elem, a képzés a véletlenszerűségre azért van. Másodszor az utterance (kifejezés) egynél több beszédszándék bármely átfedésének azt jelenti, hogy az azonos utterance (kifejezés) a felső leképezésének képzési alapján módosíthatja.

A csevegőrobot szükség van egy adott LUIS pontszám bizalom a megjelölésű jelzi, ha Ehelyett használjon a felső két szándék pontszám különbsége. Ezáltal rugalmasan képzési-változások. 

## <a name="punctuation"></a>Absztrakt
Absztrakt egy külön jogkivonatot a LUIS. Az utterance (kifejezés), amely egy időszak végén, és a egy utterance (kifejezés), amely nem tartalmaz két külön kimondott szöveg, és előfordulhat, hogy két különböző előrejelzés beolvasása. Győződjön meg arról, hogy a modell absztrakt vagy kezeli a a [példa utterances](luis-concept-utterance.md) (kellene és írásjelek nélkül) vagy a a [könnyebben figyelmen kívül a speciális szintaxissal írásjelek patterns}(luis-concept-patterns.md): `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>További lépések

Lásd: [entitások hozzáadása](luis-how-to-add-entities.md) további információ entitások hozzáadása a LUIS-alkalmazás.