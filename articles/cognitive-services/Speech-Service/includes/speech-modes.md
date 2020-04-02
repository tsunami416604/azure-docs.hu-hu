---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: dapine
ms.openlocfilehash: bb177963b824e6d5d80d5f60e8b9f995b6ecd121
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80537009"
---
## <a name="speech-modes"></a>Beszédmódok

**Interaktív**
- Parancsnoki és irányítási forgatókönyvekhez szánták.
- Szegmentálási idővel rendelkezik, ami x.
- Egy felismert utterance (kifejezés) végén a szolgáltatás leállítja a kérelemazonosítóból származó hang feldolgozását, és befejezi a fordulatot. A kapcsolat nincs lezárva.
- Az elismerés maximális határa 20 év.
- Tipikus szén-dioxid-hívás meghívni a `RecognizeOnceAsync`.

**Beszélgetés**
- Hosszabb ideig futó elismeréseket szánt.
- A szegmentálási idő túlárazási értéke Y. (Y != X)
- Több teljes utterances feldolgozása a turn befejezése nélkül.
- Véget vet a kanyarnak, hogy túl sok csendet hallgassunk.
- Carbon továbbra is egy új kérelem azonosítóés visszajátszás audio szükség szerint.
- A szolgáltatás 10 perc beszédfelismerés után erőszakkal bontja a kapcsolatot.
- A Carbon újra csatlakozik, és visszajátssza a nem ismert hangot.
- A meghívni `StartContinuousRecognition`a Carbon with.

**Diktálás**
- Lehetővé teszi a felhasználók számára, hogy írásjeleket adjanak meg a beszédével.
- A Carbon meghívása `EnableDictation` az `SpeechConfig` objektumon történő megadásával, függetlenül attól, hogy az API-hívás elindítja-e a felismerést.
- Az első<sup>féltől</sup> `speech.fragment` származó fürt a köztes eredmények, a 3<sup>rd</sup> party vissza `speech.hypothesis` üzeneteket ad vissza.