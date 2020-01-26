---
title: A Speech SDK – beszédfelismerés szolgáltatás ismertetése
titleSuffix: Azure Cognitive Services
description: A Speech szoftverfejlesztői készlet (SDK) natív hozzáférést biztosít az alkalmazásai számára a beszédfelismerési szolgáltatás funkcióihoz, így egyszerűbbé válik a szoftverek fejlesztése. Ez a cikk további részleteket tartalmaz a Windows, Linux és Android rendszerhez készült SDK-ról.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: wolfma
ms.openlocfilehash: ea879cbfc3b9b1d1a627add52f26a473aca53cdf
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759875"
---
# <a name="about-the-speech-sdk"></a>A Speech SDK ismertetése

A Speech szoftverfejlesztői készlet (SDK) lehetővé teszi az alkalmazások számára, hogy hozzáférjenek a beszédfelismerési szolgáltatás funkcióihoz, így egyszerűbbé válik a beszédfelismerést támogató szoftverek fejlesztése. Az SDK-k jelenleg hozzáférést biztosítanak a **beszéd-szöveg**, a **szöveg-beszéd**, a **beszédfelismerés**, a **szándék-felismerés**és a **bot-keretrendszer közvetlen vonalas beszéd csatornája**számára.

A hanganyagot könnyedén rögzítheti egy mikrofonból, beolvashatja egy streamből, vagy a hangfájlokat a tárolóból is elérheti a Speech SDK-val. A Beszéd SDK 16 bites, 16 kHz/8 kHz frekvenciájú, egycsatornás WAV/PCM hangot támogat a beszédfelismeréshez. További hangformátumok a [beszéd – szöveg Rest-végpont](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) vagy a [Batch transzkripciós szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)használatával támogatottak.

A képességek és a támogatott platformok általános áttekintése a dokumentációs [bejegyzés oldalon](https://aka.ms/csspeech)található.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Az SDK beszerzése

### <a name="windows"></a>Windows

> [!WARNING]
> A Speech SDK támogatja a Windows 10-es vagy újabb verziókat. A korábbi Windows-verziók **nem támogatottak**.

A Windows esetében a következő nyelveket támogatjuk:

* C#(UWP és .NET) C++: a Speech SDK NuGet-csomagjának legújabb verzióját használhatja. A csomag 32 bites és 64 bites ügyféloldali kódtárakat és felügyelt (.NET) kódtárakat tartalmaz. Az SDK a Visual Studióban a NuGet, a [Microsoft. CognitiveServices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech)használatával telepíthető.

* Java: a Speech SDK Maven-csomag legújabb verziójára hivatkozhat és használhat, amely csak a Windows x64-et támogatja. A Maven-projektben adja hozzá a `https://csspeechstorage.blob.core.windows.net/maven/`t további tárházként és hivatkozási `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0`ként függőségként.

### <a name="linux"></a>Linux

> [!NOTE]
> Jelenleg csak az Ubuntu 16,04, az Ubuntu 18,04 és a Debian 9 támogatott a következő cél architektúrán:
> - x86, x64 és ARM64 C++ fejlesztéshez
> - x64 és ARM64 Javához
> - x64 a .NET Core-hoz és a Pythonhoz

A következő rendszerhéj-parancsok futtatásával győződjön meg arról, hogy a szükséges kódtárak telepítve vannak:

Ubuntu rendszeren:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Debian 9 rendszeren:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

* C#: A Speech SDK NuGet-csomag legújabb verzióját használhatja. Az SDK-ra való hivatkozáshoz adja hozzá a következő csomag-hivatkozást a projekthez:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: a Speech SDK Maven-csomag legújabb verzióját használhatja. A Maven-projektben adja hozzá a `https://csspeechstorage.blob.core.windows.net/maven/`t további tárházként és hivatkozási `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`ként függőségként.

* C++: Töltse le az SDK-t [. tar-csomagként](https://aka.ms/csspeech/linuxbinary) , és csomagolja ki a fájlokat egy tetszőleges könyvtárba. A következő táblázat az SDK-mappa szerkezetét mutatja be:

  |Útvonal|Leírás|
  |-|-|
  |`license.md`|Licenc|
  |`ThirdPartyNotices.md`|Harmadik felekkel kapcsolatos közlemények|
  |`include`|A C és a fejléc fájljaiC++|
  |`lib/x64`|Natív x64-es függvénytár az alkalmazással való összekapcsoláshoz|
  |`lib/x86`|Natív x86-függvénytár az alkalmazással való összekapcsoláshoz|

  Alkalmazás létrehozásához másolja vagy helyezze át a szükséges bináris fájlokat (és könyvtárakat) a fejlesztői környezetbe. A létrehozási folyamat során szükség szerint vegye fel őket.

### <a name="android"></a>Android

Az Androidhoz készült Java SDK a szükséges kódtárakat és a szükséges [Android-engedélyeket](https://developer.android.com/studio/projects/android-library)tartalmazza. Egy Maven-tárházban üzemel, `https://csspeechstorage.blob.core.windows.net/maven/` csomag `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Ha a csomagot a Android Studio-projektből kívánja felhasználni, hajtsa végre a következő módosításokat:

* A projekt szintű Build. gradle fájlban adja hozzá a következőt a `repository` szakaszhoz:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* A modul szintű Build. gradle fájlban adja hozzá a következőt a `dependencies` szakaszhoz:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

A Java SDK a [Speech Devices SDK](speech-devices-sdk.md)része is.

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Következő lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: beszédfelismerés felismeréseC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
