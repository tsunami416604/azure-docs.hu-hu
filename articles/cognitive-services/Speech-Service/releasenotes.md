---
title: A cognitive Services beszédfelismerő SDK-dokumentáció |} A Microsoft Docs
description: Kibocsátási megjegyzések – mi változott a legfrissebb verziókban
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 08/16/2018
ms.author: wolfma
ms.openlocfilehash: 0900588f818855d72e415678338c96fb6505318d
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "41987468"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services beszédfelismerő SDK 0.6.0: 2018 augusztus kiadás

**Új funkciók**

* A beszédfelismerés SDK-val készített UWP-alkalmazás most már adhat át a Windows App Certification Kit (WACK).
  Tekintse meg a [UWP rövid](quickstart-csharp-uwp.md).
* A .NET Standard 2.0 Linux (Ubuntu 16.04 x 64) támogatása.
* Kísérleti funkció: Támogatja a Java 8 (64 bites) Windows és Linux (Ubuntu 16.04 x 64).
  Tekintse meg a [Java futtatási környezet a rövid útmutató](quickstart-java-jre.md)

**Funkcionális változások**

* További részletes információ a hibáról a csatlakozási hibák is közzéteheti.

**Használhatatlanná tévő változásai**

* Java (Android) a `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` függvény már nem csak egy elérésiút-paraméter. Az elérési út most már automatikusan észleli az összes támogatott platformon.
* A get-hozzáférő tulajdonság `EndpointUrl` Java-és C# el lett távolítva.

**Hibajavítások**

* A Java a fordítási felismerő hang összefoglaló eredménye most valósítja meg.
* Kijavítva a hiba, inaktív a szálak és a egy nyílt és a fel nem használt sockets megnövelt számú okozhatja.
* Rögzített probléma, ahol egy hosszú ideig futó felismerése sikerült leállítani a középső továbbítására.
* Rögzített versenyhelyzet felismerő leállítása.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services beszédfelismerő SDK 0.5.0-s: 2018 július kiadás

**Új funkciók**

* Támogatás Android platform (API 23: Android 6.0-s Marshmallow vagy újabb).
  Tekintse meg a [Android rövid](quickstart-java-android.md).
* A .NET Standard 2.0 támogatja a Windows.
  Tekintse meg a [.NET Core rövid](quickstart-csharp-dotnetcore-windows.md).
* Kísérleti: Támogatás Windows (1709-es vagy újabb verzió) az UWP
  * Tekintse meg a [UWP rövid](quickstart-csharp-uwp.md).
  * Megjegyzés: A Speech SDK-val készített UWP-alkalmazás nem még meg a Windows App Certification Kit (WACK).
* Támogatja az automatikus újracsatlakozás hosszú ideig futó felismerése.

**Funkcionális változások**

* `StartContinuousRecognitionAsync()` támogatja a hosszú ideig futó felismerése
* A felismerés eredményét további mezőket tartalmazza: audio elején és a felismert szöveget (az órajelben) időtartama további értéket jelölő felismerése állapotát, például tolva `InitialSilenceTimeout`, `InitialBabbleTimeout`.
* Támogatási AuthorizationToken előállító példányok létrehozásához.

**Használhatatlanná tévő változásai**

* Elismerés események: NoMatch eseménytípus hibaesemény van egyesítve.
* C# nyelven SpeechOutputFormat OutputFormat, hogy a c++ igazított új neve.
* A visszatérési típus az egyes módszerek a `AudioInputStream` felület kis mértékben módosítani:
   * A Java a `read` metódus most visszatért `long` helyett `int`.
   * A C# a `Read` metódus most visszatért `uint` helyett `int`.
   * A C++ a `Read` és `GetFormat` most visszatérési módszerek `size_t` helyett `int`.
* C++: audio bemeneti streamekhez példányai most már csak argumentumként átadhatók egy `shared_ptr`.

**Hibajavítások**

* Rögzített helytelen visszatérési értékek eredményben amikor `RecognizeAsync()` túllépi az időkorlátot.
* A Windows media foundation kódtárak függőség eltávolítása. Az SDK alapvető hang API-kat használ.
* Dokumentációs javítás: hozzáadott egy régió oldal írja le, melyek a támogatott régiók.

**Ismert problémák**

* A beszédfelismerés SDK for Android nem készít jelentést speech összefoglaló eredmények a fordítás.
  Ez a következő kiadás javítja.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services beszédfelismerő SDK 0.4.0: 2018 június kiadás

**Funkcionális változások**

- AudioInputStream

  Egy felismerő most már felhasználhatja a stream hang forrásaként. Részletes információkért tekintse meg a kapcsolódó [gyakorlati útmutató](how-to-use-audio-input-streams.md).

- Részletes kimeneti formátum

  Létrehozása közben egy `SpeechRecognizer`, kérheti a `Detailed` vagy `Simple` kimeneti formátum. A `DetailedSpeechRecognitionResult` maszkolt cenzúrázása egy megbízhatósági pontszám, felismert szöveget, lexikai nyers formában, normalizált űrlap és normalizált űrlap tartalmazza.

**Kompatibilitástörő változás**

- Váltson `SpeechRecognitionResult.Text` a `SpeechRecognitionResult.RecognizedText` C#-ban.

**Hibajavítások**

- Kijavítottuk a lehetséges visszahívási USP rétegben leállítás során.

- Egy felismerő felhasznált bemeneti hangfájl, ha azt volt üzemben szükséges hosszabb fájlleírót.

- Üzenet szivattyú és felismerő között számos holtpontok eltávolítva.

- Fire egy `NoMatch` vezethet, ha a szolgáltatás válasza túllépte az időkorlátot.

- Az adathordozó foundation kódtárak a Windows rendszer töltöttek be. Ebben a könyvtárban van, csak a mikrofon-bemenet szükséges.

- A feltöltési sebességét hívásaiból sebessége kétszer az eredeti hang korlátozva.

- Windows, a C# nyelvet használó .NET-szerelvények erős-neve.

- Dokumentációs javítás: `Region` az információt kötelező megadni egy felismerő létrehozásához.

További példák lettek hozzáadva, és folyamatosan változik. A legújabb minták, lásd: a [beszéd SDK minta GitHub-adattár](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services beszédfelismerő SDK 0.2.12733: 2018 – május kiadás

A Cognitive Services beszédfelismerő SDK első nyilvános előzetes verziója.
