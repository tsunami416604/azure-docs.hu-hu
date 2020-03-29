---
title: Tartalomfordítás megakadályozása - Translator Text API
titleSuffix: Azure Cognitive Services
description: A Translator Text API-val megakadályozhatja a tartalom fordítását. A Translator Text API lehetővé teszi a tartalom címkézését, hogy ne legyen lefordítva.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052488"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Hogyan lehet megakadályozni a tartalom fordítását a Translator Text API-val

A Translator Text API lehetővé teszi a tartalom címkézését, hogy ne legyen lefordítva. Előfordulhat például, hogy olyan kódot, márkanevet vagy olyan szót/kifejezést szeretne címkézni, amelynek honosított nyelve nem lenne értelme.

## <a name="methods-for-preventing-translation"></a>A fordítás megelőzésére szolgáló módszerek

1. Címkézze fel `notranslate`a tartalmat a segítségével. Ez a tervezés, hogy ez csak akkor működik, ha a bemeneti textType van beállítva, mint HTML

   Példa:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. A [dinamikus szótár](dynamic-dictionary.md) segítségével felírhat egy adott fordítást.

3. Ne adja át a karakterláncot a Translator Text API fordításhoz.

4. Egyéni fordító: [Használjon szótárt az egyéni fordítóban](custom-translator/what-is-dictionary.md) egy 100%-os valószínűséggel rendelkező kifejezés fordításának előírásához.


## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Kerülje a fordítást a Translator API-hívásban](reference/v3-0-translate.md)
