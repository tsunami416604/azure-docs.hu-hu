---
title: Stream codec tömörített hang a Speech SDK-val Android-Speech Service-ben
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan továbbíthatja a tömörített hanganyagokat az Azure Speech Servicesbe az Androidon futó Speech SDK-val.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: df5eb123a2fd47a3eceea8153786442bf56a2718
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803832"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk-on-android"></a>A kodekkel tömörített hangbemenet használata az Androidon futó Speech SDK-val

A Speech SDK **tömörített hangbemeneti stream** API-val a PullStream vagy a PushStream segítségével továbbíthatja a tömörített hangokat a beszédfelismerési szolgáltatásba.

> [!IMPORTANT]
> A streaming tömörített bemeneti hang jelenleg a [ C++, C#a és a Java Linux rendszeren (Ubuntu 16,04, Ubuntu 18,04, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md)támogatott. A Java az iOS platformon is támogatja az Android és [Objective-C](how-to-use-codec-compressed-audio-input-streams-ios.md) nyelveken.
> A Speech SDK 1.7.0 vagy újabb verziója szükséges.

A WAV/PCM esetében tekintse meg a fővonal beszédének dokumentációját.  A WAV/PCM-en kívül a következő codec tömörített bemeneti formátumok támogatottak:

- MP3
- OPUS/OGG
- FLAC
- ATÖRVÉNY a WAV-tárolóban
- MULAW a WAV-tárolóban

## <a name="prerequisites-to-using-codec-compressed-audio-input-on-android"></a>A kodek tömörített hangbemenetének használatának előfeltételei az Androidon

A kodekkel tömörített hang a [GStreamer](https://gstreamer.freedesktop.org)használatával valósítható meg. Licencelési okokból a GStreamer bináris fájljai nincsenek lefordítva az SDK-val. Az Androidhoz készült előre elkészített bináris fájlokat kell használnia. Az előre elkészített kódtárak letöltéséhez lásd: [telepítés Android-fejlesztéshez](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c). 

`libgstreamer_android.so` szükséges. Győződjön meg arról, hogy a GStreamer beépülő modul a `libgstreamer_android.so`-ban van-e csatolva.

```make
GSTREAMER_PLUGINS := coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
```

Alább `Android.mk` és `Application.mk` fájlt kell megadnia. A GStreamer megosztott objektum létrehozásához kövesse az alábbi lépéseket: `libgstreamer_android.so`.

```make
# Android.mk
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := dummy
LOCAL_SHARED_LIBRARIES := gstreamer_android
LOCAL_LDLIBS := -llog
include $(BUILD_SHARED_LIBRARY)


ifndef GSTREAMER_ROOT_ANDROID
$(error GSTREAMER_ROOT_ANDROID is not defined!)
endif

ifndef APP_BUILD_SCRIPT
$(error APP_BUILD_SCRIPT is not defined!)
endif

ifndef TARGET_ARCH_ABI
$(error TARGET_ARCH_ABI is not defined!)
endif

ifeq ($(TARGET_ARCH_ABI),armeabi)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm
else ifeq ($(TARGET_ARCH_ABI),armeabi-v7a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/armv7
else ifeq ($(TARGET_ARCH_ABI),arm64-v8a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm64
else ifeq ($(TARGET_ARCH_ABI),x86)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86
else ifeq ($(TARGET_ARCH_ABI),x86_64)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86_64
else
$(error Target arch ABI not supported: $(TARGET_ARCH_ABI))
endif

GSTREAMER_NDK_BUILD_PATH  := $(GSTREAMER_ROOT)/share/gst-android/ndk-build/
include $(GSTREAMER_NDK_BUILD_PATH)/plugins.mk
GSTREAMER_PLUGINS         :=  coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
GSTREAMER_EXTRA_LIBS      := -liconv
include $(GSTREAMER_NDK_BUILD_PATH)/gstreamer-1.0.mk
```

```make
# Application.mk
APP_STL = c++_shared
APP_PLATFORM = android-21
APP_BUILD_SCRIPT = Android.mk
```

Az Ubuntu 16,04-as vagy 18,04-es verziójában a következő paranccsal hozhat létre `libgstreamer_android.so`. A következő parancssorok csak a [GStreamer Android-verzió 1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2) lettek tesztelve [Android NDK b16b.](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip)

```sh
# assuming wget and unzip already installed on the system
mkdir buildLibGstreamer
cd buildLibGstreamer
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip -q -o android-ndk-r16b-linux-x86_64.zip
export PATH=$PATH:$(pwd)/android-ndk-r16b
export NDK_PROJECT_PATH=$(pwd)/android-ndk-r16b
wget https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2
mkdir gstreamer_android
tar -xjf gstreamer-1.0-android-universal-1.14.4.tar.bz2 -C $(pwd)/gstreamer_android/
export GSTREAMER_ROOT_ANDROID=$(pwd)/gstreamer_android

mkdir gstreamer
# Copy the Application.mk and Android.mk from the documentation above and put it inside $(pwd)/gstreamer

# Enable only one of the following at one time to create the shared object for the targeted ABI
echo "building for armeabi-v7a. libgstreamer_android.so will be placed in $(pwd)/armeabi-v7a"
ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=armeabi-v7a NDK_LIBS_OUT=$(pwd)

#echo "building for arm64-v8a. libgstreamer_android.so will be placed in $(pwd)/arm64-v8a"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=arm64-v8a NDK_LIBS_OUT=$(pwd)

#echo "building for x86_64. libgstreamer_android.so will be placed in $(pwd)/x86_64"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86_64 NDK_LIBS_OUT=$(pwd)

#echo "building for x86. libgstreamer_android.so will be placed in $(pwd)/x86"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86 NDK_LIBS_OUT=$(pwd)
```

Miután a megosztott objektum (libgstreamer_android. so) létrehozta az alkalmazás fejlesztőjét, el kell helyeznie a megosztott objektumot az Android-alkalmazásban, hogy az a Speech SDK-val is betölthető legyen.

## <a name="example-code-using-codec-compressed-audio-input"></a>Kód tömörített hangbemenetet használó programkód

Ha tömörített hangformátumot szeretne továbbítani a Speech Servicesbe, hozzon létre `PullAudioInputStream` vagy `PushAudioInputStream` értéket. Ezután hozzon létre egy `AudioConfig` értéket a stream osztály egy példányáról, és adja meg az adatfolyam tömörítési formátumát.

Tegyük fel, hogy van egy `myPullStream` nevű bemeneti adatfolyam-osztály, amely OPUS/OGG-t használ. A kód így néz ki:

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = AudioStreamFormat.getCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig = AudioConfig.fromStreamInput(myPullStream, audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get()

String text = result.getText();
```

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
