---
title: Adatátalakítás - LUIS
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan lehet a kimondott szövegeket módosítani, mielőtt előrejelzések nyelvi megértés (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: aeb9094db83b14af988f70485788934a7854200c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68619807"
---
# <a name="convert-data-format-of-utterances"></a>Kimondott szöveg adatformátumának konvertálása
A LUIS a következő konverziókat biztosítja a felhasználói utterance (kifejezés) előrejelzés előtt"

* Beszéd-szöveg a [Cognitive Services beszédszolgáltatásával.](../Speech-Service/overview.md) 

## <a name="speech-to-text"></a>Diktálás

A szövegfelismerés a LUIS-szal való integrációként biztosított. 

### <a name="intent-conversion-concepts"></a>Szándékkonverziós fogalmak
A beszéd szöveggé konvertálása a LUIS lehetővé teszi, hogy küldjön szóbeli kimondott szöveget egy végpontra, és kap egy LUIS előrejelzési választ. A folyamat a [beszédszolgáltatás](https://docs.microsoft.com/azure/cognitive-services/Speech) és a LUIS integrálása. További információ a beszédfelismerési szándékról az [oktatóanyaggal.](../speech-service/how-to-recognize-intents-from-speech-csharp.md)

### <a name="key-requirements"></a>Fő követelmények
Ehhez az integrációhoz nem kell **Bing Speech API-kulcsot** létrehoznia. Az Azure Portalon létrehozott **Language Understanding** kulcs ehhez az integrációhoz működik. Ne használja a LUIS indítókulcsot.

### <a name="pricing-tier"></a>Tarifacsomag
Ez az integráció a szokásos nyelvi megértési tarifacsomagoktól eltérő [árképzési](luis-boundaries.md#key-limits) modellt használ. 

### <a name="quota-usage"></a>Kvótahasználat
Az információk [kulcskorlátai](luis-boundaries.md#key-limits) című témakörben található. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Adatok kinyerése](luis-concept-data-extraction.md)

