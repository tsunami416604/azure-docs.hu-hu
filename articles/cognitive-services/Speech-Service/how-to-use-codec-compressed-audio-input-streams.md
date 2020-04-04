---
title: Kódc tömörített hang streamelése a Beszéd SDK - Beszédszolgáltatással
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan streamelhet tömörített hangot a beszédfelismerési sdk-vel a beszédfelismerési szolgáltatásba. Elérhető C++, C#, és Java for Linux, Java Android és Objective-C iOS.Available for C++, C#, and Java for Linux, Java in Android and Objective-C in iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: bd6b500a823bde654da4442704b75451806d6a46
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637276"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Kodek tömörített hangbemenet használata a beszédfelismerési SDK-val

A beszédfelismerési szolgáltatás SDK **tömörített hangbemeneti stream** API lehetővé teszi, hogy a tömörített hangot a `PullStream` vagy `PushStream`a használatával streamelje a beszédfelismerési szolgáltatásba.

A tömörített bemeneti hang streamelése jelenleg támogatott A C#, C++, Java windowsos (UWP alkalmazások nem támogatottak) és Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8) esetén. Az iOS platformon a Java és az Objective-C is támogatott.
* Az RHEL 8 és CentOS 8 esetén a beszédfelismerési SDK 1.10.0-s vagy újabb verziója szükséges
* A Beszéd SDK 1.11.0-s vagy újabb verziója szükséges a Windows rendszerhez.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Előfeltételek

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Példa kód kód kód kód tömörített audio bemenet

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A beszéd felismerésének ismertetése](quickstarts/speech-to-text-from-microphone.md)
