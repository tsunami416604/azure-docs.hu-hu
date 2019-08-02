---
title: A Speech SDK – beszédfelismerés szolgáltatás ismertetése
titleSuffix: Azure Cognitive Services
description: A Speech szoftverfejlesztői készlet (SDK) natív hozzáférést biztosít az alkalmazásai számára a beszédfelismerési szolgáltatás funkcióihoz, így egyszerűbbé válik a szoftverek fejlesztése. Ez a cikk ismerteti az SDK-t a Windows, Linux- és Android további részleteit.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: c5e12812a4305493be2cdc234946796b21dd26d8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558936"
---
# <a name="about-the-speech-sdk"></a>Tudnivalók a Speech SDK-ról

A Speech szoftverfejlesztői készlet (SDK) lehetővé teszi az alkalmazások számára a beszédfelismerési szolgáltatások funkcióinak elérését, így megkönnyítve a beszédfelismerésre képes szoftverek fejlesztését. Az SDK-k jelenleg hozzáférést biztosítanak a **beszéd-szöveg**, a **szöveg-beszéd**, a **beszédfelismerés**, a **szándék-felismerés**és a **bot-keretrendszer közvetlen vonalas beszéd csatornája**számára. A képességek és a támogatott platformok általános áttekintése a dokumentációs [bejegyzés oldalon](https://aka.ms/csspeech)található.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Az SDK beszerzése

### <a name="windows"></a>Windows

Az Windows a következő nyelveket támogatja:

* C#(UWP és .NET), C++: A Speech SDK NuGet-csomag legfrissebb verzióját is használhatja. A csomag az 32 bites és 64 bites klienskódtárak és felügyelt (.NET) kódtárak tartalmazza. Az SDK-t a Visual Studio NuGet használatával telepíthető. Keresse meg **Microsoft.CognitiveServices.Speech**.

* Java: Megtekintheti és használhatja a Speech SDK Maven-csomag legújabb verzióját, amely csak a Windows x64-et támogatja. A Maven-projektben vegye fel `https://csspeechstorage.blob.core.windows.net/maven/` referencia és további tárház `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0` függőségként.

### <a name="linux"></a>Linux

> [!NOTE]
> Jelenleg csak az Ubuntu 16,04, az C++ Ubuntu 18,04 és a Debian 9 támogatott a számítógépen (x86 vagy x64 fejlesztéshez és x64 for .net Core, Java és Python).

A következő rendszerhéj-parancsok futtatásával győződjön meg arról, hogy a szükséges kódtárak telepítve vannak:

On Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Debian 9 rendszeren:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

* C#: A Speech SDK NuGet-csomag legfrissebb verzióját is használhatja. Az SDK-ra hivatkozik, adja hozzá a következő csomag hivatkozását a projekthez:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.6.0" />
  ```

* Java: A Speech SDK Maven-csomag legújabb verziójának hivatkozását és használatát is használhatja. A Maven-projektben vegye fel `https://csspeechstorage.blob.core.windows.net/maven/` referencia és további tárház `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0` függőségként.

* C++: Töltse le az SDK-t [. tar](https://aka.ms/csspeech/linuxbinary) -csomagként, és csomagolja ki a fájlokat egy tetszőleges könyvtárba. Az alábbi táblázat az SDK mappastruktúra:

  |Útvonal|Leírás|
  |-|-|
  |`license.md`|Licenc|
  |`ThirdPartyNotices.md`|Harmadik felekkel kapcsolatos közlemények|
  |`include`|C és C++ fejlécfájlok|
  |`lib/x64`|Natív x64 az alkalmazással kapcsolásának könyvtár|
  |`lib/x86`|Natív x86 az alkalmazással kapcsolásának könyvtár|

  Alkalmazás létrehozása, másolja, vagy a szükséges bináris fájlokat (és könyvtárak) helyezze át a fejlesztési környezetet. Tartalmazza azokat a létrehozási folyamat által megkövetelt módon.

### <a name="android"></a>Android

Az Androidhoz készült Java SDK a szükséges kódtárakat és a szükséges Android-engedélyeket tartalmazza. [](https://developer.android.com/studio/projects/android-library) A Maven adattárban található `https://csspeechstorage.blob.core.windows.net/maven/` csomagként `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`.

Az Android Studio-projektek-csomagot használhat, a következő módosításokat:

* A projekt szintű build.gradle fájlban adja hozzá a következőt a `repository` szakaszban:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* A modul szintű build.gradle fájlban adja hozzá a következőt a `dependencies` szakaszban:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.6.0'
  ```

A Java SDK egyben része a [Speech Devices SDK-val](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
