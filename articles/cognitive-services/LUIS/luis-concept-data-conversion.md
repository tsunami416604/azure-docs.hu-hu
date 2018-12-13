---
title: Adatok átalakítása
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ismerje meg, hogyan utterances is módosítható előtt előrejelzések a Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: dc9040661eee4cafc655deb2436130f1abcfcfd5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094797"
---
# <a name="convert-data-format-of-utterances"></a>Beszédmódok adatok formátumának konvertálása
LUIS a Cognitive Services beszéd szolgáltatást használja a beszélt utterances kimondott szöveg átalakítása előtt előrejelzés szöveges utterances. 

## <a name="speech-to-intent-conversion-concepts"></a>Beszéd szándékának átalakítás alapelveinek
Átalakítás beszéd szöveggé LUIS lehetővé teszi egy végpont kimondott utterances küldésére és fogadására a LUIS előrejelzési választ. A folyamat nem integrációs vagy a [Speech](https://docs.microsoft.com/azure/cognitive-services/Speech) szolgáltatás az intelligens hangfelismerési szolgáltatással. 

### <a name="key-requirements"></a>Fő követelmények
Nem szeretne létrehozni egy **Bing Speech API** kulcsával ezt az integrációt. A **Language Understanding** működik ez az integráció az Azure Portalon létrehozott kulcsot. Ne használja a LUIS alapszintű kulcsot, nem fog működni a ezt az integrációt.

### <a name="new-endpoint"></a>Új végpont 
Ez az integráció létrehoz egy új végpontot és [díjszabás](luis-boundaries.md#key-limits) modell. A végpont keresztül a [beszéd SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk), mindkét beszélt fogadására képes, és lehetővé teszi, hogy egyetlen végpontot szöveg kimondott szöveg. 

### <a name="quota-usage"></a>Fiókkvóta-használat
Lásd: [korlátok kulcs](luis-boundaries.md#key-limits) információt. 

### <a name="data-retention"></a>Adatmegőrzés
Küldött adatok a végponthoz, a beszéd SDK függetlenül speech vagy a szöveg, csak használatos a beszédmodellel növelése érdekében. Nem használatos a modell túl általános kapacitásban beszéd vagy a LUIS növelése érdekében. A LUIS-alkalmazás törlése esetén a megőrzött adatok is törlődik.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Diktálás használata](luis-tutorial-speech-to-intent.md)

