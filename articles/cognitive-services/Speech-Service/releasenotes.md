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
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 71a5edfbe388f2d4bfb48255b901cb0037665252
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069430"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

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

**Hibajavítások**

* Rögzített helytelen visszatérési értékek eredményben amikor `RecognizeAsync()` túllépi az időkorlátot.
* A Windows media foundation kódtárak függőség eltávolítása. Az SDK alapvető hang API-kat használ.
* Dokumentációs javítás: hozzáadott egy régió oldal írja le, melyek a támogatott régiók.

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
