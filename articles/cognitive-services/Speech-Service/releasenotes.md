---
title: Kognitív szolgáltatások beszéd SDK-dokumentáció |} Microsoft Docs
description: Kibocsátási megjegyzések – a legfrissebb verziókban változásai
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0b1559d288380cf3d0c180a225278cc13d22a5d0
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "35350018"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Kognitív szolgáltatások beszéd SDK 0.4.0: 2018-júniusi kiadásba az

**Funkcionális változások**

- AudioInputStream

  A felismerő most is felhasználhatnak adatfolyam hang forrásaként. Részletes információkért tekintse meg a kapcsolódó [útmutató útmutató](how-to-use-audio-input-streams.md).

- Részletes kimenet formátuma

  Létrehozása során egy `SpeechRecognizer`, kérhet `Detailed` vagy `Simple` kimeneti formátum. A `DetailedSpeechRecognitionResult` egy megbízhatósági pontszám, a felismert szöveg, a nyers lexikális képernyőt, a normalizált formája és a normalizált formája tartalmazza a maszkolt Profanitás.

**Megtörje módosítása**

- Váltson `SpeechRecognitionResult.Text` a `SpeechRecognitionResult.RecognizedText` C# nyelven íródtak.

**Hibajavítások**

- Hárítsa el a lehetséges visszahívási problémát USP réteg a leállítás során.

- A felismerő felhasznált bemeneti hangfájl, ha azt lett rendelkezik szükséges fájlleírót.

- Üzenet szivattyú és felismerő között számos holtpont eltávolítva.

- Tűz egy `NoMatch` vezethet, ha a szolgáltatás válasza túllépte az időkorlátot.

- A Windows media foundation fájltípusai késleltetve betöltött. Ebben a könyvtárban van csak a mikrofon bemenet szükséges.

- A feltöltési sebességét hangadatok sebességéről kétszer eredeti hang korlátozva.

- Windows, a C# .NET szerelvényeket erős-neve.

- Dokumentáció javítás: `Region` a felismerő létrehozásához szükséges adatokat.

Több minta hozzá vannak adva, és folyamatosan változik. A minták legújabb készletét, tekintse meg a [beszéd SDK minta GitHub-tárházban](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Kognitív szolgáltatások beszéd SDK 0.2.12733: 2018-előfordulhat, hogy kiadás

Az első nyilvános előzetes kiadása a kognitív beszéd SDK-t.
