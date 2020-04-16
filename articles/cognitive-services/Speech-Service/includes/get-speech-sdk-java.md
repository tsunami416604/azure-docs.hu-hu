---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 47cb20f3a23caf586777523e56902af20b747ea1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400023"
---
:::row:::
    :::column span="3":::
        Az Android-alapú Java SDK <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (Android <span class="docon docon-navigate-external x-hidden-focus"> </span>Library) </a>néven van csomagolva, amely tartalmazza a szükséges könyvtárakat és a szükséges Android engedélyeket. Ez a házigazdája egy Maven `https://csspeechstorage.blob.core.windows.net/maven/` repository `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0`a csomag .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Ha az Android Studio-projektből szeretné felhasználni a csomagot, hajtsa végre a következő módosításokat:

1. A projektszintű *build.gradle* fájlban adja hozzá `repository` a következőt a szakaszhoz:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. A modulszintű *build.gradle* fájlban adja hozzá `dependencies` a következőt a szakaszhoz:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

A Java SDK a [Beszédeszközök SDK](../speech-devices-sdk.md)részét is képezi.

#### <a name="additional-resources"></a>További források

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android-specifikus Java gyorsindítási forráskód<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java Runtime (JRE) rövid útmutató forráskódja<span class="docon docon-navigate-external x-hidden-focus"></span></a>