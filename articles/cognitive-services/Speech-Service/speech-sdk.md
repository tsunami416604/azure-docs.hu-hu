---
title: A Cognitive Services beszédszolgáltatások SDK
description: Az SDK-k a beszédfelismerési szolgáltatás áttekintése.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jerkin
ms.openlocfilehash: 20fe0c3501e562584cd7762555479457a34a9297
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281323"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>A Cognitive Services beszédszolgáltatások SDK

A Cognitive Services beszédfelismerő szoftver Development Kit (SDK) az alkalmazások natív hozzáférést biztosít a függvények a beszédfelismerési szolgáltatás, így könnyebben fejleszthet szoftvereket. Az SDK jelenleg hozzáférést biztosít **Speech to Text**, **beszédalapú fordítási**, és **szándékot felismerés**.

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-windows-sdk"></a>A Windows SDK beszerzése

A beszédfelismerés SDK Windows verziója tartalmazza a 32 bites és 64 bites C/C++-klienskódtárakat, valamint az felügyelt (.NET) kódtárak C# környezetben való használatra. A Visual Studióban NuGet; használatával telepítheti az SDK-val egyszerűen keressen `Microsoft.CognitiveServices.Speech`.

## <a name="get-the-linux-sdk"></a>A Linux SDK beszerzése

Győződjön meg arról, hogy Ön rendelkezik a szükséges fordító és tárak a következő rendszerhéj-parancsok futtatásával:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> Ezek az utasítások feltételezik, hogy Ubuntu 16.04 (x86 vagy x64) a számítógépen futtatja. Egy másik Ubuntu-verzió, vagy egy másik Linux-disztribúció alkalmazkodik a környezet az alábbi lépéseket.

Ezt követően [az SDK letöltéséhez](https://aka.ms/csspeech/linuxbinary) és csomagolja ki a fájlokat, egy olyan tetszőleges könyvtárba. Ez a táblázat bemutatja az SDK mappastruktúra.

|Útvonal|Leírás|
|-|-|
|`license.md`|Licenc|
|`third-party-notices.md`|Harmadik fél közleményei|
|`include`|C és C++ fejlécfájlok|
|`lib/x64`|Natív x64 az alkalmazással kapcsolásának könyvtár|
|`lib/x86`|Natív x86 az alkalmazással kapcsolásának könyvtár|

Alkalmazás létrehozása, másolja vagy a szükséges bináris fájlokat (és könyvtárak) helyezze át a fejlesztési környezetet, és ezeket a buildelési folyamatába szükség szerint.

## <a name="get-the-java-sdk"></a>GET, a Java SDK

Az Androidhoz készült Java SDK van csomagolva, mint egy [AAR (Androidos függvénytár)](https://developer.android.com/studio/projects/android-library), amely tartalmazza a szükséges kódtárak, valamint a szükséges Android-engedélyek útmutatójához.
A Maven adattárban található `https://csspeechstorage.blob.core.windows.net/maven/` csomagként `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`.
A felhasználás az Android Studio-projektek-csomagot a következő módosításokat:

* A projekt szintű `build.gradle` fájlt, adja hozzá a következő, a `repository` szakaszban:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* A modul szinten `build.gradle` fájlt, adja hozzá a következő, a `dependencies` szakaszban:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.5.0'
  ```

A Java SDK egyben része a [Speech Devices SDK-val](speech-devices-sdk.md).

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
