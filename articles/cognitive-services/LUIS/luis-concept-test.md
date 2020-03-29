---
title: A LUIS-alkalmazás tesztelése
titleSuffix: Azure Cognitive Services
description: Tesztelés a folyamat a luis minta utterances a LUIS és a LUIS által felismert szándékok és entitások válaszának leadása.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 25b360f90a0920aad2ea5e68cda31a68be5d37a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73486682"
---
# <a name="testing-example-utterances-in-luis"></a>Példa kimondott szövegtesztelése a LUIS-ban

Tesztelés a folyamat a luis minta utterances a LUIS és a LUIS által felismert szándékok és entitások válaszának leadása. 

Luis interaktívan, egy utterance (kifejezés) egy időben, vagy egy utterances. Tesztelés közben összehasonlíthatja az aktuális aktív modell előrejelzési válasz át a közzétett modell előrejelzési válasz. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Mi az a pontszám a tesztelés?
Tekintse meg [előrejelzési pontszám](luis-concept-prediction-score.md) fogalmak, ha többet megtudni előrejelzés pontszámok.

## <a name="interactive-testing"></a>Interaktív tesztelés
Az interaktív tesztelés a LUIS portál **Teszt** paneljén történik. Megadhat egy utterance (kifejezés) a szándékok és az entitások azonosításának és pontozásának megtekintéséhez. Ha a LUIS nem előre jelzi a szándékokat és az entitásokat, ahogy a tesztelési panelen egy utterance (utterance ( utterance (utterance ( utterance (kifejezés) a várt módon, másolja a **szándék** lapra egy új utterance (kifejezés). Ezután címkézze fel az utterance (kifejezés) részeit entitások számára, és a LUIS betanítása. 

## <a name="batch-testing"></a>Kötegelt tesztelés
Tekintse meg [a kötegelt tesztelést,](luis-concept-batch-test.md) ha egyszerre több utterance (kifejezés) tesztelése.

## <a name="endpoint-testing"></a>Végpontvizsgálat
A [végpont](luis-glossary.md#endpoint) ot az alkalmazás legfeljebb két verziójával tesztelheti. Az alkalmazás fő vagy élő verziója **éles** végpontként beállított, adjon hozzá egy második verziót az **átmeneti** végponthoz. Ez a megközelítés egy utterance (kifejezés) három verzióját adja meg: az aktuális modellt a [LUIS-webhely](luis-reference-regions.md) tesztablakában, és a két különböző végpontkét verzióját. 

Minden végponttesztelés beleszámít a használati kvótába. 

## <a name="do-not-log-tests"></a>A tesztek naplózásának elmaradásának elmaradása
Ha egy végponton tesztel, és nem szeretné, hogy az `logging=false` utterance (kifejezés) naplózza, ne felejtse el használni a lekérdezési karakterlánc konfigurációját.

## <a name="where-to-find-utterances"></a>Hol találhatók a kimondott szövegek?
A LUIS tárolja az összes naplózott utterances a lekérdezési napló, letölthető a LUIS portálon az **alkalmazások** listalapjáról, valamint a LUIS [authoring API-k.](https://go.microsoft.com/fwlink/?linkid=2092087) 

A LUIS által nem biztos kimondott szövegek a [LUIS-webhely](luis-reference-regions.md) **[végpontkimondott szövegének áttekintése](luis-how-to-review-endpoint-utterances.md)** lapján vannak felsorolva. 

## <a name="remember-to-train"></a>Ne feledje, hogy a vonat
Ne felejtse el [beképezni](luis-how-to-train.md) a LUIS-t, miután módosította a modellt. A LUIS-alkalmazás módosításai nem láthatók a tesztelés során, amíg az alkalmazás be van tanítva. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg [az ajánlott eljárásokat](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

* További információ a kimondott [szövegek teszteléséről.](luis-interactive-test.md)
