---
title: A Speech Service SDK dokumentációja
titlesuffix: Azure Cognitive Services
description: Kibocsátási megjegyzések – mi változott a legfrissebb verziókban
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: b5e4d239121b2449a45dfce826c99765f1c3f4be
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471137"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

## <a name="speech-service-sdk-101"></a>Beszédszolgáltatás 1.0.1 SDK

Hibajavításokat tartalmaz, és megbízhatóság fejlesztései:

* Felismerő értékesítésére versenyhelyzet miatt rögzített lehetséges végzetes hiba
* Rögzített lehetséges végzetes hiba esetén az adott Orientation tulajdonságait.
* A hozzáadott további hiba és a paraméter ellenőrzése.
* Objective-c rögzített lehetséges végzetes hiba okozza a NSString felülbírálása neve.
* Objective-c igazítva látható-e API
* JavaScript: Rögzített eseményeket és azok is észleltünk adattartalmakat.
* Dokumentáció fejlesztései.

Az a [mintaadattár](https://aka.ms/csspeech/samples), egy új mintát, JavaScript hozzá lett adva.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services beszédfelismerő SDK 1.0.0-s: 2018. szeptember kiadás

**Új funkciók**

* Objective-C támogatása IOS-eszközökön. Tekintse meg a [Objective-C rövid útmutató iOS-es](quickstart-objectivec-ios.md).
* A böngészőben JavaScript támogatása. Tekintse meg a [JavaScript rövid](quickstart-js-browser.md).

**Használhatatlanná tévő változásai**

* Ebben a kiadásban bevezetett kompatibilitástörő változások számos.
  Ellenőrizze a [ezt oldal](https://aka.ms/csspeech/breakingchanges_1_0_0) részleteiről.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services beszédfelismerő SDK 0.6.0: 2018 augusztus kiadás

**Új funkciók**

* UWP-alkalmazás most már a Speech SDK-val készített továbbíthatja a Windows App Certification Kit (WACK).
  Tekintse meg a [UWP rövid](quickstart-csharp-uwp.md).
* A .NET Standard 2.0 Linux (Ubuntu 16.04 x 64) támogatása.
* Kísérleti funkció: Támogatja a Java 8 (64 bites) Windows és Linux (Ubuntu 16.04 x 64).
  Tekintse meg a [Java Runtime Environment rövid](quickstart-java-jre.md).

**Funkcionális módosítása**

* Tegyen elérhetővé további részletes információ a hibáról a csatlakozási hibák.

**Használhatatlanná tévő változásai**

* Java (Android) a `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` függvény már nem csak egy elérésiút-paraméter. Az elérési út most már automatikusan észleli az összes támogatott platformon.
* A get-hozzáférő tulajdonság `EndpointUrl` Java-és C# el lett távolítva.

**Hibajavítások**

* A Java a fordítási felismerő hang összefoglaló eredménye van megvalósítva, mostantól.
* Kijavítva a hiba, inaktív a szálak és a egy nyílt és a fel nem használt sockets megnövelt számú okozhatja.
* Rögzített probléma, ahol egy hosszú ideig futó felismerése sikerült leállítani a közepén továbbítására.
* Rögzített versenyhelyzet felismerő leállítása.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services beszédfelismerő SDK 0.5.0-s: 2018 július kiadás

**Új funkciók**

* Támogatás Android platform (API 23: Android 6.0-s Marshmallow vagy újabb). Tekintse meg a [Android rövid](quickstart-java-android.md).
* A .NET Standard 2.0 támogatja a Windows. Tekintse meg a [.NET Core rövid](quickstart-csharp-dotnetcore-windows.md).
* Kísérleti: Támogatás Windows (1709-es vagy újabb verzió) az UWP.
  * Tekintse meg a [UWP rövid](quickstart-csharp-uwp.md).
  * Megjegyzés: A Speech SDK-val készített UWP-alkalmazás nem még meg a Windows App Certification Kit (WACK).
* Támogatja az automatikus újracsatlakozás hosszú ideig futó felismerése.

**Funkcionális változások**

* `StartContinuousRecognitionAsync()` támogatja a hosszú ideig futó felismerése.
* A felismerés eredményét további mezőket tartalmaz. Hang elején és időtartama (a órajel során végbemenő is) a felismert szöveget és további értékek, amelyek a felismerés állapotát, például tolva vagyunk `InitialSilenceTimeout` és `InitialBabbleTimeout`.
* Támogatási AuthorizationToken előállító példányok létrehozásához.

**Használhatatlanná tévő változásai**

* Elismerés események: NoMatch eseménytípus hibaesemény lett egyesítve.
* C# nyelven SpeechOutputFormat átnevezte OutputFormat igazított c++ marad.
* A visszatérési típus az egyes módszerek a `AudioInputStream` felület kis mértékben módosítani:
   * A Java a `read` metódus most visszatért `long` helyett `int`.
   * A C# a `Read` metódus most visszatért `uint` helyett `int`.
   * A C++ a `Read` és `GetFormat` most visszatérési módszerek `size_t` helyett `int`.
* C++: Mostantól a hang bemeneti streamekhez példányait csak argumentumként átadhatók egy `shared_ptr`.

**Hibajavítások**

* Az eredmény nem megfelelő visszatérési értékek rögzített amikor `RecognizeAsync()` túllépi az időkorlátot.
* A Windows media foundation kódtárak függőség el lett távolítva. Az SDK mostantól az alapvető hang API-k.
* Dokumentációs javítás: hozzáadott egy [régiók](regions.md) a támogatott régiók leíró oldalon.

**Ismert hiba**

* A beszédfelismerés SDK for Android nem jelentést speech összefoglaló eredmények a fordítás. A probléma a következő kiadás javítja.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services beszédfelismerő SDK 0.4.0: 2018 június kiadás

**Funkcionális változások**

- AudioInputStream

  Egy felismerő most már felhasználhatja a stream hang forrásaként. További információkért tekintse meg a kapcsolódó [gyakorlati útmutató](how-to-use-audio-input-streams.md).

- Részletes kimeneti formátum

  Amikor létrehoz egy `SpeechRecognizer`, kérheti a `Detailed` vagy `Simple` kimeneti formátum. A `DetailedSpeechRecognitionResult` maszkolt cenzúrázása egy megbízhatósági pontszám, felismert szöveget, lexikai nyers formában, normalizált űrlap és normalizált űrlap tartalmazza.

**Kompatibilitástörő változás**

- A módosított `SpeechRecognitionResult.Text` a `SpeechRecognitionResult.RecognizedText` C#-ban.

**Hibajavítások**

- Kijavítottuk a lehetséges visszahívási a USP rétegben leállítás során.

- Egy felismerő felhasznált bemeneti hangfájl, ha azt volt üzemben szükséges hosszabb fájlleírót.

- Az üzenet szivattyú és a felismerő között számos holtpontok eltávolítva.

- Fire egy `NoMatch` vezethet, ha a szolgáltatás válasza túllépte az időkorlátot.

- A Windows media foundation könyvtáraiban betöltött késleltetés. Ez a kódtár mikrofon bemeneti csak szükség.

- A feltöltési sebességét hívásaiból sebessége kétszer az eredeti hang korlátozva.

- Windows, a C# nyelvet használó .NET-szerelvények most strong neve.

- Dokumentációs javítás: `Region` az információt kötelező megadni egy felismerő létrehozásához.

További példák lettek hozzáadva, és folyamatosan változik. A legújabb minták, lásd: a [beszéd SDK-minták GitHub-adattár](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services beszédfelismerő SDK 0.2.12733: 2018 – május kiadás

Ebben a kiadásban a Cognitive Services beszédfelismerő SDK első nyilvános előzetes verziója.
