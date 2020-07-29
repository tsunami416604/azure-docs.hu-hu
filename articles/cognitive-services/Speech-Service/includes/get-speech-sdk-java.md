---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: d7d41a875d8e0c30085bafd346e316672359de26
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87375356"
---
:::row:::
    :::column span="3":::
        Az Androidhoz <a href="https://developer.android.com/studio/projects/android-library" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a>készült Java SDK a szükséges kódtárakat és a szükséges Android-engedélyeket tartalmazza. A szolgáltatás egy Maven-tárházban található a `https://csspeechstorage.blob.core.windows.net/maven/` csomagban `com.microsoft.cognitiveservices.speech:client-sdk:1.13.0` .
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
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.13.0'
  ```

A Java SDK a [Speech Devices SDK](../speech-devices-sdk.md)része is.

#### <a name="additional-resources"></a>További források

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android-specifikus Java gyors útmutató forráskódja<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java Runtime (JRE) gyors útmutató forráskódja<span class="docon docon-navigate-external x-hidden-focus"></span></a>