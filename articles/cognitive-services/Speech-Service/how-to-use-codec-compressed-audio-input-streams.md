---
title: Stream codec tömörített hang a Speech SDK-Speech szolgáltatással
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan továbbíthat tömörített hangot a beszédfelismerési szolgáltatásnak a Speech SDK-val. Elérhető for C++, C#, és Java for Linux, Java Androidon és Objective-C iOS-ben.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: 3fab02d3dc567a2c54edad5bfb05abe7d99f7b7c
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943821"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>A codec tömörített hangbemenetének használata a Speech SDK-val

A Speech Service SDK **tömörített hangbemeneti stream** API lehetővé teszi a tömörített hang továbbítását a beszédfelismerési szolgáltatásnak egy `PullStream` vagy `PushStream`használatával.

> [!IMPORTANT]
> A streaming tömörített bemeneti hang jelenleg a C#, C++a Java Linux rendszeren (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8) támogatott. A Java az iOS platformon is támogatja az Android és Objective-C nyelveken.
> A Speech SDK 1.7.0 vagy újabb verziója szükséges (1.10.0 vagy újabb verzió a RHEL 8, CentOS 8 esetében).

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Előfeltételek

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Kód tömörített hangbemenetet használó programkód

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan ismerheti fel a beszédfelismerést](quickstarts/speech-to-text-from-microphone.md)
