---
title: 'Rövid útmutató: Szövegparancsfájl konvertálása – Fordítószöveg'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban megtudhatja, hogyan transzírhatja (konvertálja) a szöveget az egyik parancsfájlból a másikba a Translator Text API használatával. Ebben a példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/10/2020
ms.author: erhopf
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 4247983fd39cd8d9acf256d313ae10aa2002585f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525117"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text"></a>Rövid útmutató: A Fordító szöveg API-jával átírta a szöveget

Ebben a rövid útmutatóban megtudhatja, hogyan transzírhatja (konvertálja) a szöveget az egyik parancsfájlból a másikba a Translator Text REST API használatával. A megadott példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.

Ehhez a rövid útmutatóhoz szükség van egy [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely tartalmaz egy Translator Text-erőforrást. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# quickstart](includes/transliterate-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Go quickstart](includes/transliterate-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java quickstart](includes/transliterate-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Node.js quickstart](includes/transliterate-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python quickstart](includes/transliterate-python.md)]
::: zone-end

## <a name="see-also"></a>Lásd még

* [Szöveg lefordítása](quickstart-translate.md)
* [A beviteli nyelv azonosítása](quickstart-detect.md)
* [Alternatív fordítások beolvasása](quickstart-dictionary.md)
* [A támogatott nyelvek listájának beolvasása](quickstart-languages.md)
* [Bemenet mondatai hosszának meghatározása](quickstart-sentences.md)
