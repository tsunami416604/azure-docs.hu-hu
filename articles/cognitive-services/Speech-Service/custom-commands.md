---
title: Egyéni parancsok (előzetes verzió) – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Az egyéni parancsok (előzetes verzió) szolgáltatásainak, képességeinek és korlátozásainak áttekintése, amely a hangalkalmazások létrehozására szolgáló megoldás.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367839"
---
# <a name="what-are-custom-commands-preview"></a>Mik azok az egyéni parancsok (előzetes verzió)?

A hangalapú alkalmazások, például [a hangasszisztensek figyelik a felhasználókat,](voice-assistants.md) és válaszként cselekszenek, gyakran visszabeszélve. [Beszédről szövegre](speech-to-text.md) történő írást használnak a felhasználó beszédének átírásához, majd lépéseket tesznek a szöveg természetes nyelvi megértésére. Ez a művelet gyakran tartalmazza a [szövegfelolvasással](text-to-speech.md)létrehozott asszisztens szóbeli kimenetét. Az eszközök a beszédskós sdk objektummal rendelkező asszisztensekhoz csatlakoznak. `DialogServiceConnector`

**Az egyéni parancsok (előzetes verzió)** egy egyszerűsített megoldás hangalkalmazások létrehozására. Egységes szerzői élményt, automatikus üzemeltetési modellt és viszonylag kisebb összetettséget biztosít más beállításokkal, például [a Közvetlen vonal beszéddel](direct-line-speech.md)szemben. Ez az egyszerűsítés azonban a rugalmasság csökkenésével jár. Így az egyéni parancsok (előzetes verzió) a legalkalmasabbak a feladat befejezéséhez vagy a parancs-és vezérlési forgatókönyvekhez. Különösen jól illeszkedik az eszközök internetéhez (IoT) és a fej nélküli eszközökhöz.

Az összetett társalgási interakció és integráció más megoldásokkal, például a virtuális asszisztens megoldással és a [vállalati sablonnal,](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) javasoljuk, hogy használja a Direct Line Speech-t.

Az egyéni parancsok (előnézet) jó jelöltjei rögzített szókincstel rendelkeznek, jól definiált változókészletekkel. Például az otthoni automatizálási feladatok, például a termosztát vezérlése, ideálisak.

   ![Példák feladatbefejezési forgatókönyvekre](media/voice-assistants/task-completion-examples.png "példák feladat befejezésére")

## <a name="getting-started-with-custom-commands-preview"></a>Az egyéni parancsok – első lépések (előzetes verzió)

Az egyéni parancsok (előzetes verzió) hangalkalmazás létrehozására szolgáló első lépés [a beszédfelismerési előfizetési kulcs beolvasása](get-started.md) és az Egyéni parancsok (előzetes verzió) szerkesztőjének elérése a [Beszédstúdióban.](https://speech.microsoft.com) Innen létrehozhat egy új egyéni parancsokat (előzetes verzió) alkalmazást, és közzéteheti azt, amely után az eszközre szóló alkalmazás kommunikálhat vele a beszédfelismerési SDK használatával.

   ![Szerzői folyamat egyéni parancsokhoz (előzetes verzió)](media/voice-assistants/custom-commands-flow.png "Az egyéni parancsok (előzetes verzió) szerzői folyamata")

Rövid útmutatókat kínálunk, amelyek célja, hogy kevesebb mint 10 perc alatt futtassa a kódot.

* [Egyéni parancsok (előzetes verzió) alkalmazás létrehozása](quickstart-custom-speech-commands-create-new.md)
* [Egyéni parancsok (előzetes verzió) alkalmazás létrehozása paraméterekkel](quickstart-custom-speech-commands-create-parameters.md)
* [Csatlakozás egyéni parancsokhoz (előzetes verzió) alkalmazáshoz a Beszéd SDK, C #](quickstart-custom-speech-commands-speech-sdk.md)

Miután végzett a rövid útmutatókkal, fedezze fel útmutatóinkat.

- [Érvényesítés hozzáadása az egyéni parancsparaméterekhez](./how-to-custom-speech-commands-validations.md)
- [Parancsok teljesítése az ügyfélen a beszédsdk-vel](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Megerősítés hozzáadása egyéni parancshoz](./how-to-custom-speech-commands-confirmations.md)
- [Egylépéses javítás hozzáadása egyéni parancshoz](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>További lépések

* [Ingyenes beszédfelismerési szolgáltatás-előfizetési kulcs beszerezni](get-started.md)
* [Az Egyéni parancsok kipróbálása a Beszédstúdióban](https://speech.microsoft.com)
* [A beszédfelismerési SDK beolvasása](speech-sdk.md)
