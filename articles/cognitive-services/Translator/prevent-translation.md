---
title: Tartalom fordításának megakadályozása – Translator Text API
titleSuffix: Azure Cognitive Services
description: A tartalom fordításának megakadályozása a Translator Text API. A Translator Text API lehetővé teszi a tartalom címkézését, hogy az ne legyen lefordítva.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: swmachan
ms.openlocfilehash: 15a36451c18d65df6667f24284f3f69f3d1c06b8
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326762"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>A tartalom fordításának megakadályozása a Translator Text API

A Translator Text API lehetővé teszi a tartalom címkézését, hogy az ne legyen lefordítva. Előfordulhat például, hogy meg szeretné címkézni a kódot, a márkanevet vagy egy olyan szót vagy kifejezést, amely nincs értelme a honosított értéknél.

## <a name="methods-for-preventing-translation"></a>A fordítás megakadályozásának módszerei
1. Escape to a Twitter-címke @somethingtopassthrough vagy #somethingtopassthrough. Kilépés a fordítás után. Érvényes Twitter-címkék esetén ez a reguláris kifejezés: `\B@[A-Za-z]+[A-Za-z0-9_]+)`. A címkének "@" jellel kell kezdődnie, amelyet egy karakter követ, majd egy vagy több karaktert, számjegyet vagy aláhúzást kell követnie. Javasoljuk, hogy rövid idő alatt tartsa meg a címkéket, és a nyitó címkét szóköz előtt kell megadni.

2. A tartalom címkézése `notranslate`. Úgy tervezték, hogy ez csak akkor működik, ha a bemeneti textType HTML-ként van beállítva

   Példa:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. A [dinamikus szótár](dynamic-dictionary.md) használatával egy adott fordítást írhat elő.

4. Ne adja át a karakterláncot a fordítási Translator Text APInak.

5. Egyéni fordító: használjon egy [szótárt az egyéni fordítóban](custom-translator/what-is-dictionary.md) egy kifejezés fordításának megírásához 100%-os valószínűséggel.


## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Kerülje a fordítást a Translator API-hívásban](reference/v3-0-translate.md)
