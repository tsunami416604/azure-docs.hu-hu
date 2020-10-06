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
ms.openlocfilehash: ec39b3692a90f22409e85b5502d3ea874e3282d6
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742060"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>A tartalom fordításának megakadályozása a fordítóval

A fordító lehetővé teszi a tartalom címkézését, hogy az ne legyen lefordítva. Érdemes lehet például kódokat, márkaneveket vagy olyan szavakat/kifejezéseket megjelölni, amelyek a fordításban értelmüket vesztik.

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

2. A tartalom címkézése a alkalmazással `translate="no"` . Ez csak akkor működik, ha a bemeneti textType HTML-ként van beállítva

   Példa:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>

3. Use the [dynamic dictionary](dynamic-dictionary.md) to prescribe a specific translation.

4. Don't pass the string to the Translator for translation.

5. Custom Translator: Use a [dictionary in Custom Translator](custom-translator/what-is-dictionary.md) to prescribe the translation of a phrase with 100% probability.


## Next steps
> [!div class="nextstepaction"]
> [Use the Translate operation to translate text](reference/v3-0-translate.md)
