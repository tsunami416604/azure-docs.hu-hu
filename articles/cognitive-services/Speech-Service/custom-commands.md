---
title: Egyéni parancsok (előzetes verzió) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Az egyéni parancsok (előzetes verzió) funkcióinak, képességeinek és korlátozásának áttekintése, hangalapú alkalmazások létrehozására szolgáló megoldás.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 9349969f1f9b037a271cb727233517daba999acd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446981"
---
# <a name="custom-commands-preview"></a>Egyéni parancsok (előzetes verzió)

A hangvezérelt alkalmazások, például a [hangsegédek](voice-assistants.md) figyelik a felhasználókat, és válaszként reagálnak, gyakran beszélnek vissza. A felhasználók beszédének [szövegét](speech-to-text.md) használják a felhasználó beszédének átírására, majd a szöveg természetes nyelvének megismerésére. Ez a művelet gyakran tartalmazza a [szöveg-beszéd](text-to-speech.md)kapcsolattal generált segédből származó beszédes kimenetet. Az eszközök a Speech SDK `DialogServiceConnector` objektumával csatlakoznak a segédekhez.

Az **egyéni parancsok (előzetes verzió)** egy egyszerűsített megoldás a hangalapú alkalmazások létrehozásához. Egységes szerzői élményt, automatikus üzemeltetési modellt és viszonylag alacsony komplexitást biztosít, valamint más lehetőségeket, például a [közvetlen vonalas beszédet](direct-line-speech.md). Ez az egyszerűsítés azonban a rugalmasság csökkenésével jár. Így az egyéni parancsok (előzetes verzió) a feladatok befejezéséhez és a parancs-és vezérlési forgatókönyvekhez legmegfelelőbbek. Különösen jól illeszkedik eszközök internetes hálózata (IoT) és a fej nélküli eszközökhöz.

Az összetett beszélgetésekhez és a más megoldásokkal, például a [virtuális asszisztensi megoldáshoz és a vállalati sablonhoz](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) való integrációhoz javasolt a közvetlen vonalas beszéd használata.

Az egyéni parancsok (előzetes verzió) jó jelöltje egy rögzített, jól definiált változóval rendelkező szókincs. A Home Automation-feladatok, például a Termosztátok szabályozása ideális megoldás.

   ![Példák feladat-végrehajtási forgatókönyvekre](media/voice-assistants/task-completion-examples.png "feladatok befejezésére vonatkozó példák")

## <a name="getting-started-with-custom-commands-preview"></a>Egyéni parancsok – első lépések (előzetes verzió)

Az egyéni parancsok (előzetes verzió) használatának első lépése, hogy hangalkalmazást kapjon a [beszédfelismerési előfizetési kulcs beszerzéséhez](get-started.md) , és hozzáfér a [Speech studióhoz](https://speech.microsoft.com)tartozó egyéni parancsok (előzetes verzió) szerkesztőhöz. Innen létrehozhat egy új egyéni parancs (előzetes verzió) alkalmazást, és közzéteheti azt, amely után az eszközön futó alkalmazások kommunikálhatnak a Speech SDK használatával.

   ![Egyéni parancsok létrehozási folyamata (előzetes verzió)](media/voice-assistants/custom-commands-flow.png "Az egyéni parancsok (előzetes verzió) létrehozási folyamata")

Olyan gyors útmutatókat is kínálunk, amelyek kevesebb, mint 10 perc alatt futtatják a kódot.

* [Egyéni parancsok (előzetes verzió) alkalmazás létrehozása](quickstart-custom-speech-commands-create-new.md)
* [Egyéni parancsok (előzetes verzió) alkalmazásának létrehozása paraméterekkel](quickstart-custom-speech-commands-create-parameters.md)
* [Kapcsolódás egyéni parancsokhoz (előzetes verzió) alkalmazáshoz a Speech SDK-valC#](quickstart-custom-speech-commands-speech-sdk.md)

Ha elkészült a gyors útmutatókkal, ismerkedjen meg a How-TOS szolgáltatással.

- [Érvényesítések hozzáadása az egyéni parancs paramétereinek](./how-to-custom-speech-commands-validations.md)
- [Parancsok végrehajtása az ügyfélen a Speech SDK-val](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Megerősítés hozzáadása egyéni parancshoz](./how-to-custom-speech-commands-confirmations.md)
- [Egy lépésből álló javítás hozzáadása egyéni parancshoz](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Következő lépések

* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](get-started.md)
* [Az egyéni parancsok kipróbálásához nyissa meg a Speech studiót](https://speech.microsoft.com)
* [A Speech SDK beszerzése](speech-sdk.md)
