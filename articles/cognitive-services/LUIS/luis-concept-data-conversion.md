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
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: bdce1a49ce6c6531ce344de5aa157717fe72c609
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560808"
---
# <a name="convert-data-format-of-utterances"></a>Beszédmódok adatok formátumának konvertálása
LUIS a Cognitive Services beszéd szolgáltatást használja a beszélt utterances kimondott szöveg átalakítása előtt előrejelzés szöveges utterances. 

## <a name="speech-to-intent-conversion-concepts"></a>Beszéd szándékának átalakítás alapelveinek
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

