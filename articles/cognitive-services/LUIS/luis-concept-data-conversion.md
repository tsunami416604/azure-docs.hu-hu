---
title: A LUIS - Azure konvertálás – fogalmak megismerése |} Microsoft Docs
description: Ismerje meg, hogyan utterances előrejelzéseket a nyelvi ismertetése (LUIS) előtt módosíthatók
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/27/2018
ms.author: v-geberr;
ms.openlocfilehash: 16b0df4b81220885e2c3747470272cee9536e10c
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063561"
---
# <a name="data-conversion-concepts-in-luis"></a>A LUIS konvertálás – fogalmak
LUIS utterances szóbeli utterances átalakítása szöveges utterances előtt előrejelzés lehetőséget biztosít. 

## <a name="speech-to-intent-conversion-concepts"></a>Beszédfelismerés leképezési átalakítás fogalmak
LUIS szöveggé beszéd átalakítás lehetővé teszi szóbeli utterances küldhet a végpont és LUIS előrejelzés választ. A folyamat az integrációs vagy a [beszéd](https://docs.microsoft.com/azure/cognitive-services/Speech) LUIS szolgáltatás. 

### <a name="key-requirements"></a>Fő követelményekre
Nem szeretne létrehozni egy **Bing Diktálásfelismerési API** kulcsát, ezt az integrációt. A **nyelvi ismertetése** az Azure-portálon létrehozott kulcsot akkor működik, ha ezt az integrációt. Ne használja a LUIS alapszintű kulcs, az ezt az integrációt nem működik.

### <a name="new-endpoint"></a>Új végpont 
Ez az integráció létrehoz egy új végpontot és [árképzési](luis-boundaries.md#key-limits) modell. A végpont keresztül a [beszéd SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk), mindkettő szóbeli fogadhat és a szöveg utterances, hogy lehetővé teszi egy végpontot használni. 

### <a name="quota-usage"></a>Kvótahasználat
Lásd: [korlátok kulcs](luis-boundaries.md#key-limits) információt. 

### <a name="data-retention"></a>Adatmegőrzés
A küldött adatok a végponthoz, a beszédfelismerés SDK keresztül függetlenül beszéd vagy a szöveg, ha csak használt javítása érdekében a beszédfelismerés modell. Nem használható a modell túl általános minőségben beszéd vagy LUIS javítása érdekében. A LUIS alkalmazás törlésekor a megőrzött adatokat is törlődik.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beszéd segítségével szöveg](luis-tutorial-speech-to-intent.md)

