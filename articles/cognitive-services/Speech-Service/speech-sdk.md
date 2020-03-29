---
title: A Beszéd SDK - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A Beszédszoftver-fejlesztő készlet (SDK) natív hozzáférést biztosít az alkalmazások számára a beszédszolgáltatás funkcióihoz, megkönnyítve a szoftverek fejlesztését. Ez a cikk további részleteket tartalmaz a Windows, Linux és Android SDK-ról.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331093"
---
# <a name="about-the-speech-sdk"></a>A Speech SDK ismertetése

A Beszédszoftver-fejlesztő készlet (SDK) hozzáférést biztosít az alkalmazások számára a beszédszolgáltatás funkcióihoz, megkönnyítve a beszédalapú szoftverek fejlesztését. Jelenleg az SDK-k hozzáférést biztosítanak a **beszéd-szöveg,** **a szövegfelolvasás, a** **beszédfordítás,** **a szándékfelismerés**és a **Bot Framework közvetlen vonali beszédcsatornájához.**

A beszédközbeni SDK segítségével könnyedén rögzítheti a mikrofonból származó hangot, elolvashatja az adatfolyamból, vagy hozzáférhet a tárolóból származó hangfájlokhoz. A Speech SDK támogatja a WAV/PCM 16 bites, 16 kHz/8 kHz-es, egycsatornás hangot a beszédfelismeréshez. További hangformátumok támogatottak a [beszéd-szöveg REST-végpont](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) vagy a [kötegelt átírási szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)használatával.

A képességekről és a támogatott platformokról szóló általános áttekintés a [dokumentációbeviteli oldalon](https://aka.ms/csspeech)található .

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Az SDK beszerezni

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> A Beszéd SDK támogatja a Windows 10-es vagy újabb verziókat. A korábbi Windows-verziók **nem támogatottak.**

Windows rendszerben a következő nyelveket támogatjuk:

* C# (UWP és .NET), C++: A Speech SDK NuGet csomag legújabb verziójára hivatkozhat, és használhatja azokat. A csomag 32 bites és 64 bites ügyféltárakat és felügyelt (.NET) könyvtárakat tartalmaz. Az SDK a NuGet, [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech)használatával telepíthető a Visual Studióba.

* Java: A Speech SDK Maven csomag legújabb verziójára hivatkozhat, és használhatja azokat, amelyek csak a Windows x64 rendszert támogatják. A Maven-projektben `https://csspeechstorage.blob.core.windows.net/maven/` adja hozzá további `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` tárházként, és hivatkozásként függőségként.

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> Jelenleg csak az Ubuntu 16.04, Ubuntu 18.04, Debian 9, Red Hat Enterprise Linux (RHEL) 8 és CentOS 8 verzióit támogatjuk a következő célarchitektúrákon:
> - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) és ARM64 (Debian/Ubuntu) C++ fejlesztéshez
> - x64, ARM32 (Debian/Ubuntu) és ARM64 (Debian/Ubuntu) Java-hoz
> - x64 a .NET Core és a Python

Győződjön meg arról, hogy a szükséges könyvtárak telepítve vannak a következő parancsértelmezőparancsok futtatásával:

Ubuntu:On Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

A Debian 9-en:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

A RHEL/CentOS 8-on:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> Az RHEL/CentOS 8 rendszeren kövesse az [OpenSSL linuxos beállítására](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)vonatkozó utasításokat.

* C#: A Speech SDK NuGet csomag legújabb verziójára hivatkozhat, és használhatja azokat. Az SDK-ra való hivatkozáshoz adja hozzá a következő csomaghivatkozást a projekthez:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: A Speech SDK Maven csomag legújabb verziójára hivatkozhat, és használhatja azokat. A Maven-projektben `https://csspeechstorage.blob.core.windows.net/maven/` adja hozzá további `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` tárházként, és hivatkozásként függőségként.

* C++: Töltse le az SDK-t [.tar csomagként,](https://aka.ms/csspeech/linuxbinary) és csomagolja ki a fájlokat egy ön által választott könyvtárba. Az alábbi táblázat az SDK mappastruktúráját mutatja be:

  |Útvonal|Leírás|
  |-|-|
  |`license.md`|Licenc|
  |`ThirdPartyNotices.md`|Harmadik felekkel kapcsolatos közlemények|
  |`include`|C és C++ fejlécfájljai|
  |`lib/x64`|Natív x64-es könyvtár az alkalmazással való összekapcsoláshoz|
  |`lib/x86`|Natív x86-os könyvtár az alkalmazással való összekapcsoláshoz|

  Alkalmazás létrehozásához másolja vagy helyezze át a szükséges bináris fájlokat (és könyvtárakat) a fejlesztői környezetbe. Szükség szerint adja meg őket a létrehozási folyamatba.

# <a name="android"></a>[Android](#tab/android)

Az Android-alapú Java SDK [AAR (Android Library)](https://developer.android.com/studio/projects/android-library)néven van csomagolva, amely tartalmazza a szükséges könyvtárakat és a szükséges Android engedélyeket. Ez a házigazdája egy Maven `https://csspeechstorage.blob.core.windows.net/maven/` repository `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`a csomag .

Ha az Android Studio-projektből szeretné felhasználni a csomagot, hajtsa végre a következő módosításokat:

* A projektszintű build.gradle fájlban adja hozzá `repository` a következőt a szakaszhoz:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* A modulszintű build.gradle fájlban adja hozzá `dependencies` a következőt a szakaszhoz:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

A Java SDK a [Beszédeszközök SDK](speech-devices-sdk.md)részét is képezi.

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszéd felismerése C-ben #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
