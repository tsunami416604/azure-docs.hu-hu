---
title: A beszédszintézis alapjai – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan konvertálhatja a beszédfelismerési SDK-t a szövegfelolvasáshoz. Ebben a cikkben megismerheti az objektumépítést, a támogatott hangkimeneti formátumokat és a beszédszintézis egyéni konfigurációs beállításait.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: b95b55588c51c4e202d7a02c6c158b26cdcd7ac7
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986232"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>Ismerje meg a beszédszintézis alapjait

Ebben a cikkben a beszédfelismerési SDK használatával a szövegfelolvasó szintézis gyakori tervezési mintáit ismerheti meg. Először az alapvető konfigurációt és szintetizálást végzi el, majd lépjen tovább az egyéni alkalmazások fejlesztésére vonatkozó speciális példákra, beleértve a következőket:

* Válaszok beszerzése memórián belüli adatfolyamként
* A kimeneti mintasebesség és átviteli sebesség testreszabása
* Szintéziskérelmek küldése SSML-sel (beszédszintézis-jelölőnyelv)
* Neurális hangok használata

> [!TIP]
> Ha még nem volt alkalma befejezni az egyik rövid útmutatónkat, javasoljuk, hogy rúgja ki a gumikat, és próbálja meg a beszédfelismerést.
> * [Beszéd felismerése mikrofonból](quickstarts/text-to-speech.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>További lépések

* [Bevezetés a Custom Voice szolgáltatásba](how-to-custom-voice.md)
* [Szintézis javítása az SSML-lel](speech-synthesis-markup.md)