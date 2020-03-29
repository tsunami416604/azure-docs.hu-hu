---
title: Előre összeállított modellek - LUIS
titleSuffix: Azure Cognitive Services
description: Előre összeállított modellek tartományokat, szándékokat, kimondott szövegeket és entitásokat biztosítanak. Az alkalmazást előre összeállított tartománnyal indíthatja el, vagy később hozzáadhat egy megfelelő tartományt az alkalmazáshoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: e0b5b95a7524b60a7c3367035a15a7158fa7908a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280835"
---
# <a name="prebuilt-models"></a>Előre összeállított modellek

Előre összeállított modellek tartományokat, szándékokat, kimondott szövegeket és entitásokat biztosítanak. Az alkalmazást egy előre összeállított modellel indíthatja el, vagy később hozzáadhat egy megfelelő modellt az alkalmazáshoz. 

## <a name="types-of-prebuilt-models"></a>Az előre összeállított modellek típusai

A LUIS háromféle előre összeállított modellt biztosít. Minden modell bármikor hozzáadható az alkalmazáshoz. 

|Modell típusa|Beágyazások|
|--|--|
|[Tartomány](luis-reference-prebuilt-domains.md)|Szándékok, kimondott szövegek, entitások|
|Leképezések|Szándékok, kimondott szöveg|
|[Entitások](luis-reference-prebuilt-entities.md)|Csak entitások| 

## <a name="prebuilt-domains"></a>Előre összeállított tartományok

A Language Understanding (LUIS) *előre összeállított tartományokat*biztosít, amelyek előre betanított szándékok és [entitások,](luis-how-to-add-intents.md) amelyek együttműködnek a tartományok vagy az ügyfélalkalmazások közös kategóriái számára. [entities](luis-concept-entity-types.md) 

Az előre összeállított tartományok betanítása és a LUIS-alkalmazáshoz való hozzáadásra is készen állnak. Az előre összeállított tartomány leképezései és entitásai teljes mértékben testreszabhatók, miután hozzáadta őket az alkalmazáshoz. 

> [!TIP]
> Az előre összeállított tartomány szándékai és entitásai működnek a legjobban. Ha lehetséges, jobb, ha kombinálja a leképezéseket és az entitásokat ugyanabból a tartományból.
> A segédprogramok előre összeállított tartomány leképezések, amelyek testre szabható használatra bármely tartományban. Például hozzáadhatja `Utilities.Repeat` az alkalmazáshoz, és betaníthatja, hogy felismerje azokat a műveleteket, amelyeket a felhasználó meg szeretne ismételni az alkalmazásban. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Előre összeállított tartományi szándék viselkedésének módosítása

Előfordulhat, hogy egy előre összeállított tartomány tartalmaz egy szándékot, amely hasonló a kívánt szándékot szeretne a LUIS-alkalmazásban, de azt szeretné, hogy másképp viselkedjen. Például a **Helyek** előre összeállított `MakeReservation` tartomány egy éttermi foglalást szeretne, de azt szeretné, hogy az alkalmazás ezt a szándékot használja a szállásfoglaláshoz. Ebben az esetben módosíthatja a szándék viselkedését azáltal, hogy példa utterances hozzáadása a szándékkal, hogy a szállásfoglalás, és majd újra tanítsa az alkalmazást. 

Az előre összeállított tartományok teljes listáját az [Előre összeállított tartományok hivatkozásában](./luis-reference-prebuilt-domains.md)találja.

## <a name="prebuilt-intents"></a>Előre összeállított leképezések

A LUIS előre összeállított leképezéseket és azok kimondott szövegét biztosítja az egyes előre összeállított tartományokhoz. Leképezések a teljes tartomány hozzáadása nélkül is hozzáadható. A szándék hozzáadása a szándék és a kimondott szöveg hozzáadása az alkalmazáshoz. Mind a szándék neve és az utterance (kifejezési lista) módosítható.  

## <a name="prebuilt-entities"></a>Előre összeállított entitások

A LUIS előre összeállított entitásokat tartalmaz a gyakori típusú információk, például dátumok, időpontok, számok, mértékegységek és pénznem felismerésére. Az előre összeállított entitástámogatás a LUIS-alkalmazás kultúrájától függően változik. A LUIS által támogatott előre összeállított entitások teljes listáját, beleértve a kulturális támogatás megtekintését is, tekintse meg az [előre összeállított entitáshivatkozást.](./luis-reference-prebuilt-entities.md)

Ha egy előre összeállított entitás szerepel az alkalmazásban, az előrejelzések szerepelnek a közzétett alkalmazás. Az előre összeállított entitások viselkedése előre betanított, és **nem** módosítható. 

> [!NOTE]
> **builtin.datetime** elavult. Ez helyébe [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), amely elismeri a dátum és idő tartományok, valamint a jobb elismerését kétértelmű dátumok és időpontok.

## <a name="next-steps"></a>További lépések

További információ arról, hogyan [adhat hozzá előre összeállított entitásokat](luis-prebuilt-entities.md) az alkalmazáshoz.
