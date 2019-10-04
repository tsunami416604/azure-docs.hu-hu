---
title: Tartalom fordításának megakadályozása – Translator Text API
titleSuffix: Azure Cognitive Services
description: A tartalom fordításának megakadályozása a Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f9b48b094713f6ee141c5c15d5636ca965ad61b9
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595228"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>A tartalom fordításának megakadályozása a Translator Text API

A Translator Text API lehetővé teszi a tartalom címkézését, hogy az ne legyen lefordítva. Előfordulhat például, hogy meg szeretné címkézni a kódot, a márkanevet vagy egy olyan szót vagy kifejezést, amely nincs értelme a honosított értéknél.

## <a name="methods-for-preventing-translation"></a>A fordítás megakadályozásának módszerei
1. Escape to a Twitter- @somethingtopassthrough címke vagy #somethingtopassthrough. Kilépés a fordítás után.

2. A tartalom címkézése `notranslate`a alkalmazással.

   Példa:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. A [dinamikus szótár](dynamic-dictionary.md) használatával egy adott fordítást írhat elő.

4. Ne adja át a karakterláncot a fordítási Translator Text APInak.

5. Egyéni fordító: Használjon egy [szótárt egyéni fordítóban](custom-translator/what-is-dictionary.md) egy kifejezés fordításának megírásához 100%-os valószínűséggel.


## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Kerülje a fordítást a Translator API-hívásban](reference/v3-0-translate.md)
