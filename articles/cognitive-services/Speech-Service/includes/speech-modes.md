---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81422340"
---
## <a name="speech-modes"></a>Beszédfelismerési módok

**Interaktív**
- A parancs-és vezérlési forgatókönyvek esetében jelent meg.
- Szegmentálási időtúllépési érték (X).
- Az egyik felismertség végén a szolgáltatás leállítja a hangfeldolgozást a kérelem-AZONOSÍTÓból, és befejezi a bekapcsolást. A rendszer nem zárja be a kapcsolatokat.
- Az elismerés maximális korlátja 20-as.
- A meghívó tipikus széndioxid-hívás `RecognizeOnceAsync` .

**Beszélgetés**
- A már futó felismerések esetében jelent meg.
- A szegmentálási időtúllépési értéke Y. (Y! = X)
- Több teljes hosszúságú kimondott szöveg fog feldolgozni a turn befejezése nélkül.
- A túl sok csend bekapcsolását fogja eredményezni.
- A Carbon továbbra is egy új kérelem-azonosítót fog használni, és szükség szerint visszajátssza a hanganyagot.
- A szolgáltatás 10 percnyi beszédfelismerés után kényszeríti a kapcsolatot.
- A Carbon újrakapcsolódik, és visszafogja a nem nyugtázott hangot.
- Meghívása a szén és a között `StartContinuousRecognition` .

**Diktálás**
- Lehetővé teszi a felhasználók számára a központozás megadását.
- Meghívása a széndioxidban az objektum megadásával, `EnableDictation` `SpeechConfig` az elismerést indító API-hívástól függetlenül.
- Az 1<sup>.</sup> fél fürt a `speech.fragment` közbenső eredményekhez tartozó üzeneteket ad vissza, a 3<sup>Távoli asztali</sup> fél pedig visszaküldi az üzeneteket `speech.hypothesis` .