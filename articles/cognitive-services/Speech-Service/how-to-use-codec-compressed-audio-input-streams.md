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
ms.openlocfilehash: 4585538e552e73f8f7a4b7b105153a9d26eeb4c4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934096"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>A codec tömörített hangbemenetének használata a Speech SDK-val

A Speech Service SDK **tömörített hangbemeneti stream** API lehetővé teszi a tömörített hang továbbítását a beszédfelismerési szolgáltatásba vagy a vagy a használatával `PullStream` `PushStream` .

A streaming tömörített bemeneti hang jelenleg a C#, a C++, a Java és a Python Windows rendszeren támogatott (UWP-alkalmazások nem támogatottak) és a Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 7/8, CentOS 7/8). A Java is támogatott az Androidban.
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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan ismerheti fel a beszédfelismerést](quickstarts/speech-to-text-from-microphone.md)
