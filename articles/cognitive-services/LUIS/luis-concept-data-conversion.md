---
title: A LUIS - Azure konvertálás – fogalmak megismerése |} Microsoft Docs
description: Ismerje meg, hogyan utterances előrejelzéseket a nyelvi ismertetése (LUIS) előtt módosíthatók
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 56de113b41be419a5e39d705a22466139c1c29ce
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266104"
---
# <a name="data-conversion-concepts-in-luis"></a>A LUIS konvertálás – fogalmak
LUIS utterances szóbeli utterances átalakítása szöveges utterances előtt előrejelzés lehetőséget biztosít. 

## <a name="speech-to-intent-conversion-concepts"></a>Beszédfelismerés leképezési átalakítás fogalmak
LUIS szöveggé beszéd átalakítás lehetővé teszi szóbeli utterances küldhet a végpont és LUIS előrejelzés választ. A folyamat az integrációs vagy a [beszéd](https://docs.microsoft.com/azure/cognitive-services/Speech) LUIS szolgáltatás. 

### <a name="key-requirements"></a>Fő követelményekre
Nem szeretne létrehozni egy **Bing Diktálásfelismerési API** kulcsát, ezt az integrációt. Ez az integráció a LUIS kulcs működik.

### <a name="new-endpoint"></a>Új végpont 
Ez az integráció létrehoz egy új végpontot és [árképzési](luis-boundaries.md#key-limits) modell. A végpont nem fogadhat mindkét szóbeli és a szöveg utterances, hogy lehetővé teszi egy végpontot használni. 

### <a name="quota-usage"></a>Kvótahasználat
Lásd: [korlátok kulcs](luis-boundaries.md#key-limits) információt. 

### <a name="data-retention"></a>Adatmegőrzés
A küldött adatok a végpont, attól függetlenül történik beszéd vagy a szöveg, ha csak használt javítása érdekében a beszédfelismerés modell. Nem használható a modell túl általános minőségben beszéd vagy LUIS javítása érdekében. A LUIS alkalmazás törlésekor a megőrzött adatokat is törlődik.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az oktatóanyaghoz helyesen hibák](luis-tutorial-bing-spellcheck.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions