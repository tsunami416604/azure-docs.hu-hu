---
title: Szöveg-beszéd rövid útmutató – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan alakíthat át szöveg-beszéd átalakítást a Speech SDK használatával. Ebben a rövid útmutatóban megismerheti az objektumok kialakításával és kialakításával, a támogatott hangkimeneti formátumokkal, a beszédfelismerési parancssori felülettel és az egyéni konfigurációs beállításokkal kapcsolatos beszédfelismerési lehetőségeket.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/01/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: speech-full-stack
keywords: szöveg – beszéd
ms.openlocfilehash: 79409f95d698e015d15d9131dcf1f27b34b03343
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400906"
---
# <a name="get-started-with-text-to-speech"></a>Ismerkedés a szövegfelolvasással

Ebben a rövid útmutatóban megtudhatja, hogyan végezheti el a szöveg-beszéd szintézist a Speech SDK használatával. Első lépésként alapkonfigurációt és szintézist kell elvégeznie, és az egyéni alkalmazások fejlesztéséhez további speciális példákra kell lépnie, például:

* Válaszok a memóriában tárolt adatfolyamok beolvasására
* A kimeneti mintavételezési arány és a bitsűrűség testreszabása
* Összegző kérelmek elküldése a SSML használatával (Speech szintézis Markup Language)
* Neurális hangok használata

> [!TIP]
> Ha egyenesen a mintakód kihagyása mellett szeretne kiugrani, tekintse meg a gyors üzembe helyezési [példákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) a githubon.

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-spx"
[!INCLUDE [CLI include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Custom Voice szolgáltatásba](how-to-custom-voice.md)
* [Szintézis javítása az SSML-lel](speech-synthesis-markup.md)
* Ismerje meg, hogyan használhatja a hosszú hangalapú [API](long-audio-api.md) -t nagyméretű szöveges mintákhoz, mint például a könyvek és hírek
* Tekintse meg a gyors üzembe helyezési [mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) a githubon
