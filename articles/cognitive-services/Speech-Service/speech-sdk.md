---
title: SDK - Speech Services beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: A beszédfelismerő szoftver Development Kit (SDK) hozzáférést biztosít az alkalmazások natív a funkciók a beszédfelismerési szolgáltatás így könnyebben fejleszthet szoftvereket. Ez a cikk ismerteti az SDK-t a Windows, Linux- és Android további részleteit.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 1d81426853a9a8e28fa04e34fada3ce64798ccfd
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958150"
---
# <a name="about-the-speech-sdk"></a>Tudnivalók a Speech SDK

A beszédfelismerő szoftver Development Kit (SDK) hozzáférést biztosít az alkalmazások natív a funkciók a beszédfelismerési szolgáltatás így könnyebben fejleszthet szoftvereket. Az SDK jelenleg hozzáférést biztosít **Speech to Text**, **beszédalapú fordítási**, és **szándékot felismerés**.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Az SDK beszerzése

### <a name="windows"></a>Windows

Az Windows a következő nyelveket támogatja:

* C#(UWP- és .NET), a C++: Hivatkoznak, és a legújabb verzióját használja, a beszéd SDK NuGet-csomagot. A csomag az 32 bites és 64 bites klienskódtárak és felügyelt (.NET) kódtárak tartalmazza. Az SDK-t a Visual Studio NuGet használatával telepíthető. Keresse meg **Microsoft.CognitiveServices.Speech**.

* Java: Hivatkozhat, és a Speech SDK Maven-csomag, amely támogatja a csak a Windows x64 legújabb verzióját használja. A Maven-projektben vegye fel `https://csspeechstorage.blob.core.windows.net/maven/` referencia és további tárház `com.microsoft.cognitiveservices.speech:client-sdk:1.3.1` függőségként.

### <a name="linux"></a>Linux

> [!NOTE]
> Jelenleg csak az Ubuntu 16.04 és 18.04 (x86 vagy x64 C++ fejlesztési és a .NET Core, Java és Python x64) a számítógépen.

Győződjön meg arról, hogy Ön rendelkezik a szükséges fordító és kódtárak telepítése a következő rendszerhéj-parancsok futtatásával:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

* C#: Hivatkoznak, és a legújabb verzióját használja, a beszéd SDK NuGet-csomagot. Az SDK-ra hivatkozik, adja hozzá a következő csomag hivatkozását a projekthez:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.3.1" />
  ```

* Java: Hivatkozhat, és a Speech SDK Maven csomag legújabb verzióját használja. A Maven-projektben vegye fel `https://csspeechstorage.blob.core.windows.net/maven/` referencia és további tárház `com.microsoft.cognitiveservices.speech:client-sdk:1.3.1` függőségként.

* C++: Töltse le az SDK-t, mint egy [.tar-csomag](https://aka.ms/csspeech/linuxbinary) és csomagolja ki a fájlt egy tetszőleges könyvtárban. Az alábbi táblázat az SDK mappastruktúra:

  |Útvonal|Leírás|
  |-|-|
  |`license.md`|Licenc|
  |`ThirdPartyNotices.md`|Harmadik felekkel kapcsolatos közlemények|
  |`include`|C és C++ fejlécfájlok|
  |`lib/x64`|Natív x64 az alkalmazással kapcsolásának könyvtár|
  |`lib/x86`|Natív x86 az alkalmazással kapcsolásának könyvtár|

  Alkalmazás létrehozása, másolja, vagy a szükséges bináris fájlokat (és könyvtárak) helyezze át a fejlesztési környezetet. Tartalmazza azokat a létrehozási folyamat által megkövetelt módon.

### <a name="android"></a>Android

Az Androidhoz készült Java SDK van csomagolva, mint egy [AAR (Androidos függvénytár)](https://developer.android.com/studio/projects/android-library), amely tartalmazza a szükséges kódtárak és Android-engedélyek megadása kötelező. A Maven adattárban található `https://csspeechstorage.blob.core.windows.net/maven/` csomagként `com.microsoft.cognitiveservices.speech:client-sdk:1.3.1`.

Az Android Studio-projektek-csomagot használhat, a következő módosításokat:

* A projekt szintű build.gradle fájlban adja hozzá a következőt a `repository` szakaszban:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* A modul szintű build.gradle fájlban adja hozzá a következőt a `dependencies` szakaszban:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.3.1'
  ```

A Java SDK egyben része a [Speech Devices SDK-val](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
