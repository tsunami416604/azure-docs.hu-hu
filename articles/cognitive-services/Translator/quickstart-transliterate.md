---
title: 'Gyors útmutató: Szöveges parancsfájl konvertálása – Translator Text'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan átbetűzés (konvertálhatja) szövegét az egyik parancsfájlból a másikba a Translator Text REST API használatával. Ebben a példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 08/12/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: b1f794b833d228d5903859f8cc75a51fc6ef2fe0
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968523"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text"></a>Gyors útmutató: A Translator Text API átbetűzés szövegének használata

Ebből a rövid útmutatóból megtudhatja, hogyan átbetűzés (konvertálhatja) szövegét az egyik parancsfájlból a másikba a Translator Text REST API használatával. A megadott példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.

Ehhez a rövid útmutatóhoz szükség van egy [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely tartalmaz egy Translator Text-erőforrást. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# quickstart](includes/transliterate-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java quickstart](includes/transliterate-java.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python quickstart](includes/transliterate-python.md)]

::: zone-end

::: zone pivot="programming-language-nodejs"

[!INCLUDE [Node.js quickstart](includes/transliterate-nodejs.md)]

::: zone-end

::: zone pivot="programming-language-go"

[!INCLUDE [Go quickstart](includes/transliterate-go.md)]

::: zone-end

## <a name="see-also"></a>Lásd még

* [Szöveg lefordítása](quickstart-translate.md)
* [A beviteli nyelv azonosítása](quickstart-detect.md)
* [Alternatív fordítások beolvasása](quickstart-dictionary.md)
* [A támogatott nyelvek listájának beolvasása](quickstart-languages.md)
* [Bemenet mondatai hosszának meghatározása](quickstart-sentences.md)
