---
title: Adatok átalakítása
titleSuffix: Language Understanding - Azure Cognitive Services
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
ms.openlocfilehash: a148c849d0935978f049e01dd254c4c18800ee3b
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496989"
---
# <a name="convert-data-format-of-utterances"></a>Beszédmódok adatok formátumának konvertálása
LUIS a Cognitive Services beszéd szolgáltatást használja a beszélt utterances kimondott szöveg átalakítása előtt előrejelzés szöveges utterances. 

## <a name="speech-to-intent-conversion-concepts"></a>Beszéd szándékának átalakítás alapelveinek
Átalakítás beszéd szöveggé LUIS lehetővé teszi egy végpont kimondott utterances küldésére és fogadására a LUIS előrejelzési választ. A folyamat nem integrációs vagy a [Speech](https://docs.microsoft.com/azure/cognitive-services/Speech) szolgáltatás az intelligens hangfelismerési szolgáltatással. További információ a Beszédszándék egy [oktatóanyag](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Fő követelmények
Nem szeretne létrehozni egy **Bing Speech API** kulcsával ezt az integrációt. A **Language Understanding** működik ez az integráció az Azure Portalon létrehozott kulcsot. Ne használja a LUIS alapszintű kulcsot.

### <a name="pricing-tier"></a>Tarifacsomag
Ez az integráció használja egy másik [díjszabás](luis-boundaries.md#key-limits) , mint a szokásos Language Understanding tarifacsomagok modell. 

### <a name="quota-usage"></a>Fiókkvóta-használat
Lásd: [korlátok kulcs](luis-boundaries.md#key-limits) információt. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Adatok kinyerése](luis-concept-data-extraction.md)

