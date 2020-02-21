---
title: 'Gyors útmutató: szöveges parancsfájl konvertálása – Translator Text'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan átbetűzés (konvertálhatja) szövegeit egyik parancsfájlból a másikba a Translator Text API használatával. Ebben a példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.
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
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525117"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text"></a>Gyors útmutató: a Translator Text API átbetűzés szövegének használata

Ebből a rövid útmutatóból megtudhatja, hogyan átbetűzés (konvertálhatja) szövegét az egyik parancsfájlból a másikba a Translator Text REST API használatával. A megadott példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.

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
