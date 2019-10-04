---
title: Stream codec tömörített hang az iOS-Speech szolgáltatás Speech SDK-val
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan továbbíthatja a tömörített hanganyagokat az Azure Speech Servicesbe az iOS-es Speech SDK-val.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 9a66e4ecf2230caad233a4eff12c0fadc95409d5
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803810"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>A codec tömörített hangbemenetének használata az iOS-es Speech SDK-val

A Speech SDK **tömörített hangbemeneti stream** API lehetővé teszi a tömörített hang továbbítását a beszédfelismerési szolgáltatásba egy lekéréses vagy leküldéses adatfolyam használatával.

> [!IMPORTANT]
> Az iOS rendszerű tömörített hang folyamatos átviteléhez a Speech SDK 1.7.0 vagy újabb verziója szükséges. A [ C++, C#a és a Java Linux rendszeren is támogatott (Ubuntu 16,04, Ubuntu 18,04, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md) és [Java Androidon.](how-to-use-codec-compressed-audio-input-streams-android.md)

A WAV/PCM esetében tekintse meg a fővonal beszédének dokumentációját.  A WAV/PCM-en kívül a következő codec tömörített bemeneti formátumok támogatottak:

- MP3
- OPUS/OGG
- FLAC
- ATÖRVÉNY a WAV-tárolóban
- MULAW a WAV-tárolóban

## <a name="prerequisites"></a>Előfeltételek

A tömörített hang kezelésére a [GStreamer](https://gstreamer.freedesktop.org)használatával kerül sor.
Licencelési okokból ezek a függvények nem tölthetők le az SDK-val, de az ezeket a funkciókat tartalmazó burkoló függvénytárat az alkalmazás-fejlesztőknek kell felépíteni, és az SDK használatával kell elszállítani őket.
A burkoló könyvtár létrehozásához először töltse le és telepítse a [GSTREAMER SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)-t.
Ezután töltse le a [burkoló könyvtár](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)Xcode-projektjét.
Nyissa meg a projektet a Xcode-ben, és hozza létre az **általános iOS-eszközök** céljához – ez nem fog működni, hogy egy adott célra felépítse.
A Build lépés egy dinamikus keretrendszerű köteget hoz létre egy dinamikus könyvtárral a `GStreamerWrapper.framework` nevű összes szükséges architektúrához.
Ezt a keretrendszert minden olyan alkalmazásnak tartalmaznia kell, amely a Speech Services SDK-val tömörített hangadatfolyamokat használ.

A következő beállítások alkalmazása a Xcode-projektben a következőképpen valósítható meg:

1. Másolja ki az imént létrehozott `GStreamerWrapper.framework` csomagot, valamint a Cognitive Services Speech SDK keretrendszerét, amelyet [innen](https://aka.ms/csspeech/iosbinary)tölthet le a minta projektet tartalmazó könyvtárba.
1. Állítsa be a keretrendszerek elérési útját a *projekt beállításai*között.
    1. A **beágyazott bináris fájlok** fejlécének **általános** LAPJÁN adja hozzá az SDK-tárat keretrendszerként: **Beágyazott bináris fájlok hozzáadása** >  **...** > navigáljon a kiválasztott könyvtárhoz, és válassza ki mindkét keretrendszert.
    1. Lépjen a **Build Settings** lapra, és engedélyezze az **összes** beállítást.
1. Vegye fel a könyvtárat `$(SRCROOT)/..` a keretrendszer-keresési útvonalak közé (*Framework Search Paths* a **Search Paths** részben).

## <a name="example-code-using-codec-compressed-audio-input"></a>Kód tömörített hangbemenetet használó programkód

Ha tömörített hangformátumot szeretne továbbítani a Speech Servicesbe, hozzon létre egy `SPXPullAudioInputStream` vagy `SPXPushAudioInputStream` értéket.
Az alábbi kódrészletből megtudhatja, hogyan hozhat létre egy `SPXAudioConfiguration` egy `SPXPushAudioInputStream`-példányból, amely az adatfolyam tömörítési formátumának megfelelő MP3-értéket ad meg.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

A következő kódrészlet azt mutatja be, hogyan olvashatók be a tömörített hangadatok egy fájlból, és hogyan tölthetők be a `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
