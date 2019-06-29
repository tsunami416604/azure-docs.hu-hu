---
title: Megakadályozza a tartalom fordítási – Translator Text API
titlesuffix: Azure Cognitive Services
description: Megakadályozza a tartalom a Translator Text API-jának.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 02c4e90879b81e29f3972d262f36d5c6b6d8e841
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448281"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Tartalom a Translator Text API-jának megakadályozása

A Translator Text API lehetővé teszi a címke tartalmat úgy, hogy nem a lefordított. Például érdemes címkézése a kódot, márkanév vagy a szó vagy kifejezés, amely nem érthető, ha honosítva legyen.

## <a name="methods-for-preventing-translation"></a>A fordítási módszerek
1. Escape-Twitter címkére @somethingtopassthrough vagy #somethingtopassthrough. Megszünteti – escape-fordítás után.

2. Tartalom megjelölése `notranslate`.

   Példa:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Használja a [dinamikus szótár](dynamic-dictionary.md) határozza meg a meghatározott fordítását.

4. A karakterlánc nem adja át a Translator Text API a fordítás.

5. Egyéni a fordítót: Használja a [egyéni a fordítót a szótár](custom-translator/what-is-dictionary.md) 100 %-os valószínűséget az határozza meg a kifejezések fordítása.


## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A Translator API-hívás a fordítási elkerülése](reference/v3-0-translate.md)
