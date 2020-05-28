---
title: 'Gyors útmutató: szöveges parancsfájl konvertálása – Translator'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan átbetűzés (konvertálhatja) szövegét az egyik parancsfájlból a másikba a fordító használatával. Ebben a példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 05/26/2020
ms.author: erhopf
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 1f66da399ecd23f8db2a3989effd56ed98833dfd
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996839"
---
# <a name="quickstart-use-the-translator-to-transliterate-text"></a>Gyors útmutató: a fordító használata szöveg átbetűzés

Ebből a rövid útmutatóból megtudhatja, hogyan átbetűzés (konvertálhatja) szövegeit egyik parancsfájlból a másikba a Translator REST API használatával. A megadott példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.

Ehhez a rövid útmutatóhoz egy fordítói erőforrással rendelkező [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) van szükség. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

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

## <a name="see-also"></a>További információ

* [Szöveg lefordítása](quickstart-translate.md)
* [A beviteli nyelv azonosítása](quickstart-detect.md)
* [Alternatív fordítások beolvasása](quickstart-dictionary.md)
* [A támogatott nyelvek listájának beolvasása](quickstart-languages.md)
* [Bemenet mondatai hosszának meghatározása](quickstart-sentences.md)
