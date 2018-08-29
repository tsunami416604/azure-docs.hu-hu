---
title: A Cognitive Services beszédszolgáltatások SDK
description: Az SDK-k a beszédfelismerési szolgáltatás áttekintése.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 08/16/2018
ms.author: v-jerkin
ms.openlocfilehash: c26aeb1d29c3b2c8b5b43d1a1face818295e9d2f
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122082"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>A Cognitive Services beszédszolgáltatások SDK

A Cognitive Services beszédfelismerő szoftver Development Kit (SDK) az alkalmazások natív hozzáférést biztosít a függvények a beszédfelismerési szolgáltatás, így könnyebben fejleszthet szoftvereket. Az SDK jelenleg hozzáférést biztosít **Speech to Text**, **beszédalapú fordítási**, és **szándékot felismerés**.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Az SDK beszerzése

### <a name="windows"></a>Windows

Az Windows a következő nyelveket támogatja:

* C# (UWP- és .NET), a C++: hivatkozhat, és a Speech SDK NuGet-csomag legújabb verzióját használja.
  A csomag az 32 bites és 64 bites klienskódtárak, valamint az felügyelt (.NET) kódtárak tartalmazza.
  A Visual Studióban NuGet; használatával telepítheti az SDK-val egyszerűen keressen `Microsoft.CognitiveServices.Speech`.

* Java: Is hivatkozhat és a Speech SDK Maven-csomag, amely támogatja a Windows x64 csak legújabb verzióját használja.
  A Maven-projektben vegye fel `https://csspeechstorage.blob.core.windows.net/maven/` további tárházat, és hivatkozás `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` függőségként. 

### <a name="linux"></a>Linux

> [!NOTE]
> Csak jelenleg az Ubuntu 16.04 (x86 vagy C++ fejlesztési x64, a .NET Core- és Java x64) a számítógépen.

Győződjön meg arról, hogy Ön rendelkezik a szükséges fordító és kódtárak telepítése a következő rendszerhéj-parancsok futtatásával:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: Hivatkozhat és legújabb verzióját használja, a beszéd SDK NuGet-csomagot.
  Az SDK-ra hivatkozik, adja hozzá a következő csomag hivatkozását a projektbe:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="0.6.0" />
  ```

* Java: Is hivatkozhat és a Speech SDK Maven csomag legújabb verzióját használja.
  A Maven-projektben vegye fel `https://csspeechstorage.blob.core.windows.net/maven/` további tárházat, és hivatkozás `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` függőségként. 

* C++: Töltse le az SDK-t, mint egy [.tar-csomag](https://aka.ms/csspeech/linuxbinary) és csomagolja ki a fájlokat, egy olyan tetszőleges könyvtárba. Az alábbi táblázat az SDK mappastruktúra.

  |Útvonal|Leírás|
  |-|-|
  |`license.md`|Licenc|
  |`ThirdPartyNotices.md`|Harmadik fél közleményei|
  |`include`|C és C++ fejlécfájlok|
  |`lib/x64`|Natív x64 az alkalmazással kapcsolásának könyvtár|
  |`lib/x86`|Natív x86 az alkalmazással kapcsolásának könyvtár|

  Alkalmazás létrehozása, másolja vagy a szükséges bináris fájlokat (és könyvtárak) helyezze át a fejlesztési környezetet, és ezeket a buildelési folyamatába szükség szerint.

### <a name="android"></a>Android

Az Androidhoz készült Java SDK van csomagolva, mint egy [AAR (Androidos függvénytár)](https://developer.android.com/studio/projects/android-library), amely tartalmazza a szükséges kódtárak, valamint a szükséges Android-engedélyek útmutatójához.
A Maven adattárban található `https://csspeechstorage.blob.core.windows.net/maven/` csomagként `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0`.
Felhasználásához az Android Studio-projektek-csomagot a következő módosításokat:

* A projekt szintű `build.gradle` fájlt, adja hozzá a következő, a `repository` szakaszban:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* A modul szinten `build.gradle` fájlt, adja hozzá a következő, a `dependencies` szakaszban:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.6.0'
  ```

A Java SDK egyben része a [Speech Devices SDK-val](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
