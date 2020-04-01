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
ms.openlocfilehash: 6aabd27ae38cac0bb9effad2adcadc4935a28c6e
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409598"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Kodek tömörített hangbemenet használata a beszédfelismerési SDK-val

A beszédfelismerési szolgáltatás SDK **tömörített hangbemeneti stream** API lehetővé teszi, hogy a tömörített hangot a `PullStream` vagy `PushStream`a használatával streamelje a beszédfelismerési szolgáltatásba.

> [!IMPORTANT]
> A tömörített bemeneti hang streamelése jelenleg támogatott A C#, C++, Java linuxos (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8) esetében. Az iOS platformon a Java és az Objective-C is támogatott.
> Beszéd SDK 1.7.0-s vagy újabb verzió szükséges (1.10.0-s vagy újabb verzió rhel 8, CentOS 8 esetén).

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
