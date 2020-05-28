---
title: Tartalom fordításának megakadályozása – fordító
titleSuffix: Azure Cognitive Services
description: A tartalom fordításának megakadályozása a fordítóval. A fordító lehetővé teszi a tartalom címkézését, hogy az ne legyen lefordítva.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 37d2e4c0131569ab50ebf49ff73b6adf7a420713
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996176"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>A tartalom fordításának megakadályozása a fordítóval

A fordító lehetővé teszi a tartalom címkézését, hogy az ne legyen lefordítva. Előfordulhat például, hogy meg szeretné címkézni a kódot, a márkanevet vagy egy olyan szót vagy kifejezést, amely nincs értelme a honosított értéknél.

## <a name="methods-for-preventing-translation"></a>A fordítás megakadályozásának módszerei

1. A tartalom címkézése a alkalmazással `notranslate` . Úgy tervezték, hogy ez csak akkor működik, ha a bemeneti textType HTML-ként van beállítva

   Példa:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. A [dinamikus szótár](dynamic-dictionary.md) használatával egy adott fordítást írhat elő.

3. Ne adja át a karakterláncot a fordítónak a fordításhoz.

4. Egyéni fordító: használjon egy [szótárt az egyéni fordítóban](custom-translator/what-is-dictionary.md) egy kifejezés fordításának megírásához 100%-os valószínűséggel.


## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Kerülje a fordítást a fordítói hívásban](reference/v3-0-translate.md)
