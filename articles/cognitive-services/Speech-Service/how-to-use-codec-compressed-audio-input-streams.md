---
title: Stream codec tömörített hang a Speech SDK-Speech szolgáltatással
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan továbbíthat tömörített hangot a beszédfelismerési szolgáltatásnak a Speech SDK-val. Elérhető a C++, a C# és a Java for Linux, a Java az Androidban és az Objective-C-ben iOS-ben.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: ace584b51f0aa457e30d8e48d97834aa735a1a57
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026573"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>A codec tömörített hangbemenetének használata a Speech SDK-val

A Speech Service SDK **tömörített hangbemeneti stream** API lehetővé teszi a tömörített hang továbbítását a beszédfelismerési szolgáltatásba vagy a vagy a használatával `PullStream` `PushStream` .

Platform | Nyelvek | Támogatott GStreamer-verzió
| :--- | ---: | :---:
Windows (UWP nélkül)  | C++, C#, Java, Python | [1.15.1](https://gstreamer.freedesktop.org/data/pkg/windows/1.15.1/)
Linux  | C++, C#, Java, Python | [támogatott Linux-disztribúciók és-megcélzott architektúrák](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>A tömörített hangbemenethez szükséges Speech SDK-verzió
* A Speech SDK 1.10.0 vagy újabb verziójára van szükség a RHEL 8 és a CentOS 8 esetében
* A Windows rendszerhez a Speech SDK 1.11.0 vagy újabb verziója szükséges.

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

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Kód tömörített hangbemenetet használó programkód

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan ismerheti fel a beszédfelismerést](./get-started-speech-to-text.md)