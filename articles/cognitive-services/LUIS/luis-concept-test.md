---
title: A LUIS-alkalmazás tesztelése
titleSuffix: Language Understanding - Azure Cognitive Services
description: Tesztelés az a folyamat minta utterances biztosítva a LUIS, és a egy beérkező válasszal, LUIS elismert szándékokat és entitásokat.
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 6f18b0ec5d56fa8834bb0d1d6bc5c58831c832ff
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225329"
---
# <a name="testing-example-utterances-in-luis"></a>A LUIS példa utterances tesztelése

Tesztelés az a folyamat minta utterances biztosítva a LUIS, és a egy beérkező válasszal, LUIS elismert szándékokat és entitásokat. 

Is [tesztelése](luis-interactive-test.md) LUIS interaktívan, egyetlen utterance egyszerre, vagy adjon meg egy [batch](luis-concept-batch-test.md) a kimondott szöveg. Tesztelés, akkor összehasonlíthatja az aktuális [aktív](luis-concept-version.md#active-version) modell a közzétett modell. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Mi az a pontszám a tesztelés?
Lásd: [előrejelzési pontszám](luis-concept-prediction-score.md) fogalmak előrejelzési pontszámok tájékozódhat.

## <a name="interactive-testing"></a>Interaktív tesztelés
Interaktív vizsgálati végezheti el a **teszt** panel a webhely. Megadhatja az utterance (kifejezés), hogyan szándékok és entitások azonosítani és pontozunk. Ha az utterance (kifejezés), a tesztelési panelen a várt módon a szándékok és entitások nem előrejelzésére LUIS, másolja a a **szándékot** egy új utterance (kifejezés) oldalt. Ezután azon részeit, hogy utterance (kifejezés) felirat, és a LUIS betanításához. 

## <a name="batch-testing"></a>Kötegelt tesztelés
Lásd: [batch tesztelés](luis-concept-batch-test.md) egyszerre egynél több utterance (kifejezés) teszteléséhez.

## <a name="endpoint-testing"></a>Végpont tesztelése
Segítségével tesztelnie a [végpont](luis-glossary.md#endpoint) az alkalmazás két verziója, mely legfeljebb. Az állítja be az alkalmazás fő vagy élő verziójával a **éles** végpont, a második verzió hozzáadása a **átmeneti** végpont. Ez a megközelítés biztosítja az utterance (kifejezés) három verzióját: az aktuális modell vizsgálati ablaktábláján a [LUIS](luis-reference-regions.md) webhelyét, és a két verzió a két különböző végponton. 

Az összes endpoint tesztelési számát a használati kvóta felé. 

## <a name="do-not-log-tests"></a>Ne jelentkezzen tesztek
Ha tesztelése egy végponton, és nem naplózza az utterance (kifejezés) kíván, ne felejtse el használni a `logging=false` lekérdezési karakterláncok konfigurálása.

## <a name="where-to-find-utterances"></a>Hol található a kimondott szöveg
A LUIS tárolja az összes naplózott utterances a lekérdezési napló, letölthető a [LUIS](luis-reference-regions.md) webhely **alkalmazások** listáját tartalmazó lapon, valamint a LUIS [API-k készítése](https://aka.ms/luis-authoring-apis). 

LUIS párbeszédpaneléből megszólalásokat szerepelnek a **[tekintse át a végpont utterances](luis-how-to-review-endoint-utt.md)** lapján a [LUIS](luis-reference-regions.md) webhelyén. 

![A végpont beszédmódjainak áttekintése](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>Ne felejtse el betanítása
Ne felejtse el [betanításához](luis-how-to-train.md) LUIS után módosítja a modellt. A LUIS alkalmazás módosításai nem láthatók, amíg az alkalmazás be van tanítva teszteléshez. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg, [ajánlott eljárások](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [tesztelés](luis-interactive-test.md) a kimondott szöveg.
