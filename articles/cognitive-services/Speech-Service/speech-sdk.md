---
title: A Speech SDK – beszédfelismerés szolgáltatás ismertetése
titleSuffix: Azure Cognitive Services
description: A Speech szoftverfejlesztői készlet (SDK) natív hozzáférést biztosít az alkalmazásai számára a beszédfelismerési szolgáltatás funkcióihoz, így egyszerűbbé válik a szoftverek fejlesztése. Ez a cikk ismerteti az SDK-t a Windows, Linux- és Android további részleteit.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331093"
---
# <a name="about-the-speech-sdk"></a>A Speech SDK ismertetése

A beszédfelismerő szoftver Development Kit (SDK) hozzáférést biztosít az alkalmazások a funkciók a beszédfelismerési szolgáltatás így könnyebben fejleszthet beszédfelismerő szoftver. Az SDK-k jelenleg hozzáférést biztosítanak a **beszéd-szöveg**, a **szöveg-beszéd**, a **beszédfelismerés**, a **szándék-felismerés**és a **bot-keretrendszer közvetlen vonalas beszéd csatornája**számára.

A hanganyagot könnyedén rögzítheti egy mikrofonból, beolvashatja egy streamből, vagy a hangfájlokat a tárolóból is elérheti a Speech SDK-val. A Speech SDK támogatja a WAV/PCM 16 bites, 16 kHz/8 kHz, egycsatornás hang használatát a beszédfelismeréshez. További hangformátumok a [beszéd – szöveg Rest-végpont](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) vagy a [Batch transzkripciós szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)használatával támogatottak.

A képességek és a támogatott platformok általános áttekintése a dokumentációs [bejegyzés oldalon](https://aka.ms/csspeech)található.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Az SDK beszerzése

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> A Speech SDK támogatja a Windows 10-es vagy újabb verziókat. A korábbi Windows-verziók **nem támogatottak**.

Az Windows a következő nyelveket támogatja:

* C# (UWP- és .NET), a C++: hivatkozhat, és a Speech SDK NuGet-csomag legújabb verzióját használja. A csomag az 32 bites és 64 bites klienskódtárak és felügyelt (.NET) kódtárak tartalmazza. Az SDK a Visual Studióban a NuGet, a [Microsoft. CognitiveServices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech)használatával telepíthető.

* Java: Is hivatkozhat és a Speech SDK Maven-csomag, amely támogatja a csak a Windows x64 legújabb verzióját használja. A Maven-projektben adja hozzá a `https://csspeechstorage.blob.core.windows.net/maven/`t további tárházként és hivatkozási `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0`ként függőségként.

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> Jelenleg csak az Ubuntu 16,04, Ubuntu 18,04, Debian 9, Red Hat Enterprise Linux (RHEL) 8 és a CentOS 8 támogatott a következő megcélzott architektúrák esetén:
> - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) és ARM64 (Debian/Ubuntu) fejlesztési célokra C++
> - x64, ARM32 (Debian/Ubuntu) és ARM64 (Debian/Ubuntu) Java-hoz
> - x64 a .NET Core-hoz és a Pythonhoz

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

RHEL/CentOS 8 rendszeren:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> A RHEL/CentOS 8 rendszeren kövesse az [OpenSSL Linux rendszerhez való konfigurálásának](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)utasításait.

* C#: Hivatkozhat és legújabb verzióját használja, a beszéd SDK NuGet-csomagot. Az SDK-ra hivatkozik, adja hozzá a következő csomag hivatkozását a projekthez:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: Is hivatkozhat és a Speech SDK Maven csomag legújabb verzióját használja. A Maven-projektben adja hozzá a `https://csspeechstorage.blob.core.windows.net/maven/`t további tárházként és hivatkozási `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`ként függőségként.

* C++: Töltse le az SDK-t [. tar-csomagként](https://aka.ms/csspeech/linuxbinary) , és csomagolja ki a fájlokat egy tetszőleges könyvtárba. Az alábbi táblázat az SDK mappastruktúra:

  |Útvonal|Leírás|
  |-|-|
  |`license.md`|Licenc|
  |`ThirdPartyNotices.md`|Harmadik felekkel kapcsolatos közlemények|
  |`include`|C és C++ fejlécfájlok|
  |`lib/x64`|Natív x64 az alkalmazással kapcsolásának könyvtár|
  |`lib/x86`|Natív x86 az alkalmazással kapcsolásának könyvtár|

  Alkalmazás létrehozása, másolja, vagy a szükséges bináris fájlokat (és könyvtárak) helyezze át a fejlesztési környezetet. Tartalmazza azokat a létrehozási folyamat által megkövetelt módon.

# <a name="android"></a>[Android](#tab/android)

Az Androidhoz készült Java SDK a szükséges kódtárakat és a szükséges [Android-engedélyeket](https://developer.android.com/studio/projects/android-library)tartalmazza. Egy Maven-tárházban üzemel, `https://csspeechstorage.blob.core.windows.net/maven/` csomag `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Az Android Studio-projektek-csomagot használhat, a következő módosításokat:

* A projekt szintű Build. gradle fájlban adja hozzá a következőt a `repository` szakaszhoz:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* A modul szintű Build. gradle fájlban adja hozzá a következőt a `dependencies` szakaszhoz:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

A Java SDK a [Speech Devices SDK](speech-devices-sdk.md)része is.

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Következő lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: beszédfelismerés felismeréseC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
