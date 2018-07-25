---
title: A LUIS - az Azure data átalakítás fogalmak megértésében |} A Microsoft Docs
description: Ismerje meg, hogyan utterances is módosítható előtt előrejelzések a Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: 05a02191af171936dd9f5c4bf469b9c84aa144a3
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223701"
---
# <a name="data-conversion-concepts-in-luis"></a>A LUIS átalakítás – fogalmak
A LUIS utterances kimondott kimondott szöveg átalakítása előtt előrejelzés szöveges utterances lehetővé teszi. 

## <a name="speech-to-intent-conversion-concepts"></a>Beszéd szándékának átalakítás alapelveinek
Átalakítás beszéd szöveggé LUIS lehetővé teszi egy végpont kimondott utterances küldésére és fogadására a LUIS előrejelzési választ. A folyamat nem integrációs vagy a [Speech](https://docs.microsoft.com/azure/cognitive-services/Speech) szolgáltatás az intelligens hangfelismerési szolgáltatással. 

### <a name="key-requirements"></a>Fő
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

