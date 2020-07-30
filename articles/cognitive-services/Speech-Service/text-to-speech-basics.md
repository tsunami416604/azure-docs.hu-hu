---
title: Speech szintézis alapjai – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan alakíthat át szöveg-beszéd átalakítást a Speech SDK használatával. Ebben a cikkben megismerheti az objektumok kialakításával, a támogatott hangkimeneti formátumokkal és a beszédfelismerés egyéni konfigurációs lehetőségeivel kapcsolatos tudnivalókat.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: trbye
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-two-with-js
ms.openlocfilehash: b571c5ee64e12021acf699f979d85d37af7bac7d
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407633"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>Ismerkedjen meg a beszédfelismerés alapjaival

Ebből a cikkből megtudhatja, hogyan végezheti el a szövegek és a beszédek szintézisét a Speech SDK használatával. Első lépésként alapkonfigurációt és szintézist kell elvégeznie, és az egyéni alkalmazások fejlesztéséhez további speciális példákra kell lépnie, például:

* Válaszok a memóriában tárolt adatfolyamok beolvasására
* A kimeneti mintavételezési arány és a bitsűrűség testreszabása
* Összegző kérelmek elküldése a SSML használatával (Speech szintézis Markup Language)
* Neurális hangok használata

> [!TIP]
> Ha még nem volt lehetősége az egyik rövid útmutató elvégzésére, javasoljuk, hogy indítson el egy abroncsot, és próbálja meg saját maga is a szöveget.
> * [Beszédszintézis hangszóróra](quickstarts/text-to-speech.md)

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

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Custom Voice szolgáltatásba](how-to-custom-voice.md)
* [Szintézis javítása az SSML-lel](speech-synthesis-markup.md)
