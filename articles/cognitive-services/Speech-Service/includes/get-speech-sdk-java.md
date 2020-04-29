---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 47cb20f3a23caf586777523e56902af20b747ea1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400023"
---
:::row:::
    :::column span="3":::
        Az Androidhoz <a href="https://developer.android.com/studio/projects/android-library" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>készült Java SDK a szükséges kódtárakat és a szükséges Android-engedélyeket tartalmazza. A `https://csspeechstorage.blob.core.windows.net/maven/` szolgáltatás egy Maven-tárházban található a csomagban `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0`.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Ha a csomagot a Android Studio-projektből kívánja felhasználni, hajtsa végre a következő módosításokat:

1. A projekt szintű *Build. gradle* fájlban adja hozzá a következőt a `repository` szakaszhoz:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. A modul szintű *Build. gradle* fájlban adja hozzá a következőt a `dependencies` szakaszhoz:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

A Java SDK a [Speech Devices SDK](../speech-devices-sdk.md)része is.

#### <a name="additional-resources"></a>További háttéranyagok

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android-specifikus Java gyors útmutató forráskódja<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java Runtime (JRE) gyors útmutató forráskódja<span class="docon docon-navigate-external x-hidden-focus"></span></a>