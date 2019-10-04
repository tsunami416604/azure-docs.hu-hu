---
title: Adatkonverzió – LUIS
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan utterances is módosítható előtt előrejelzések a Language Understanding (LUIS)
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
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619807"
---
# <a name="convert-data-format-of-utterances"></a>Beszédmódok adatok formátumának konvertálása
A LUIS a következő konverziókat biztosítja a felhasználók teljes leírásához az előrejelzés előtt: "

* Beszéd a szövegben [Cognitive Services Speech](../Speech-Service/overview.md) Service használatával. 

## <a name="speech-to-text"></a>Beszédfelismerés

A beszéd szövege a LUIS-nal való integrációként van megadva. 

### <a name="intent-conversion-concepts"></a>Szándék-átalakítási fogalmak
Átalakítás beszéd szöveggé LUIS lehetővé teszi egy végpont kimondott utterances küldésére és fogadására a LUIS előrejelzési választ. A folyamat nem integrációs vagy a [Speech](https://docs.microsoft.com/azure/cognitive-services/Speech) szolgáltatás az intelligens hangfelismerési szolgáltatással. További információ a beszédfelismerési szándékról az [oktatóanyaghoz](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Fő követelmények
Nem szeretne létrehozni egy **Bing Speech API** kulcsával ezt az integrációt. A **Language Understanding** működik ez az integráció az Azure Portalon létrehozott kulcsot. Ne használja a LUIS Starter-kulcsot.

### <a name="pricing-tier"></a>Tarifacsomag
Ez az integráció eltérő [díjszabási](luis-boundaries.md#key-limits) modellt használ, mint a szokásos Language Understanding árképzési szintek. 

### <a name="quota-usage"></a>Fiókkvóta-használat
Lásd: [korlátok kulcs](luis-boundaries.md#key-limits) információt. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Adatok kinyerése](luis-concept-data-extraction.md)

