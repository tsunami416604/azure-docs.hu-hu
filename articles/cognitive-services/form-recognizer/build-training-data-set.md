---
title: Hogyan hozhat létre egy egyéni modell - űrlap felismerő tanítási adathalmazt
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan biztosítson a tanítási adathalmazt egy űrlap felismerő modell tanítása van optimalizálva.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 611d5f7983c61fab12c55a46fedf35a3c420c4c8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454817"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Egy egyéni modell a tanítási adathalmazt létrehozása

Az űrlap felismerő egyéni modell használata esetén, adja meg a saját betanítási adatok, a modell betaníthatja az iparág-specifikus űrlapokon. Öt ki vannak töltve űrlapok vagy egy üres képernyő modell betaníthatja (beleértve a fájl neve "üres" szó) két ki vannak töltve az űrlap emellett. Még ha van elegendő ki vannak töltve űrlapok a taníthat be, a tanítási adathalmazt ad hozzá egy üres képernyő javíthatja a modell pontosságát.

## <a name="training-data-tips"></a>Betanítási adatok tippek

Fontos egy adatkészlet, amely optimalizált használata képzéshez. Az alábbi tippek segítségével győződjön meg arról, hogy a legjobb eredmények elérése érdekében a get használatát a [tanítási modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) műveletet:

* Ha lehetséges használjon szöveges PDF-dokumentumok lemezkép-alapú dokumentumok helyett. A beolvasott PDF-fájlok, képek kezeli.
* Egy üres képernyő és a két ki vannak töltve űrlapok használja, ha azok érhető el.
* Kitöltött űrlapok használja a példa, amelyek az összes saját kitölti a mezőket.
* Egyes mezők eltérő értékek űrlapokat használnak.
* Ha az űrlap lemezképek alacsonyabb minőségű, használjon nagyobb (például a 10 – 15 képek).

## <a name="general-input-requirements"></a>Általános bemeneti követelmények

Ügyeljen, hogy a tanítási adathalmazt is minden űrlap felismerő tartalom bemeneti követelményeinek.
[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre a tanítási adathalmazt, kövesse a gyors útmutató: egyéni űrlap felismerő modellek betanítása és vegye használatba az űrlapokon.

* [Rövid útmutató: A modell betanítását, és bontsa ki az adatokat a cURL használatával](./quickstarts/curl-train-extract.md)
* [Rövid útmutató: A modell betanítását, és bontsa ki az űrlapadatok REST API használatával a Python használatával](./quickstarts/python-train-extract.md)

