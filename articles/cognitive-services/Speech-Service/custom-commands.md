---
title: Egyéni parancsok (előzetes verzió) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Az egyéni parancsok (előzetes verzió) funkcióinak, képességeinek és korlátozásának áttekintése, amely a hangsegédek létrehozására szolgáló megoldás.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 62210bf480d09ce2a256a44b7554ac53aa06eb0c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579706"
---
# <a name="custom-commands-preview"></a>Egyéni parancsok (előzetes verzió)

A [hangvezérelt asszisztensek](voice-assistants.md) meghallgatják a felhasználókat, és válaszként reagálnak, gyakran beszélnek vissza. A felhasználók beszédének [szövegét](speech-to-text.md) használják a felhasználó beszédének átírására, majd a szöveg természetes nyelvének megismerésére. Ez a művelet gyakran tartalmazza a [szöveg-beszéd](text-to-speech.md)kapcsolattal generált segédből származó beszédes kimenetet. Az eszközök a Speech SDK `DialogServiceConnector` objektumával csatlakoznak a segédekhez.

Az **egyéni parancsok (előzetes verzió)** egy egyszerűsített megoldás a hangsegéd létrehozásához. Egységes szerzői élményt, automatikus üzemeltetési modellt és viszonylag alacsony komplexitást biztosít, valamint más asszisztensek létrehozási lehetőségeit, például a [közvetlen vonalas beszédet](direct-line-speech.md). Ez az egyszerűsítés azonban a rugalmasság csökkenésével jár. Így az egyéni parancsok (előzetes verzió) a feladatok befejezéséhez és a parancs-és vezérlési forgatókönyvekhez legmegfelelőbbek. Különösen jól illeszkedik eszközök internetes hálózata (IoT) és a fej nélküli eszközökhöz.

Az összetett beszélgetésekhez és a más megoldásokkal, például a [virtuális asszisztensi megoldáshoz és a vállalati sablonhoz](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) való integrációhoz javasolt a közvetlen vonalas beszéd használata.

Az egyéni parancsok (előzetes verzió) jó jelöltje egy rögzített, jól definiált változóval rendelkező szókincs. A Home Automation-feladatok, például a Termosztátok szabályozása ideális megoldás.

   ![Példák feladat-végrehajtási forgatókönyvekre](media/voice-assistants/task-completion-examples.png "feladatok befejezésére vonatkozó példák")

## <a name="getting-started-with-custom-commands-preview"></a>Egyéni parancsok – első lépések (előzetes verzió)

Az egyéni parancsok (előzetes verzió) használatának első lépése, hogy hangsegédet kapjon a [beszédfelismerési előfizetési kulcs beszerzéséhez](get-started.md) , és hozzáfér a [Speech studióhoz](https://speech.microsoft.com)tartozó egyéni (előzetes verziójú) szerkesztőhöz. Innen létrehozhat egy új egyéni parancs (előzetes verzió) alkalmazást, és közzéteheti azt, amely után az eszközön futó alkalmazások kommunikálhatnak a Speech SDK használatával.

   ![Egyéni parancsok létrehozási folyamata (előzetes verzió)](media/voice-assistants/custom-commands-flow.png "Az egyéni parancsok (előzetes verzió) létrehozási folyamata")

Olyan gyors útmutatókat is kínálunk, amelyek kevesebb, mint 10 perc alatt futtatják a kódot.

* [Egyéni parancsok (előzetes verzió) alkalmazás létrehozása](quickstart-custom-speech-commands-create-new.md)
* [Egyéni parancsok (előzetes verzió) alkalmazásának létrehozása paraméterekkel](quickstart-custom-speech-commands-create-parameters.md)
* [Kapcsolódás egyéni parancsokhoz (előzetes verzió) alkalmazáshoz a Speech SDK-valC#](quickstart-custom-speech-commands-speech-sdk.md)

## <a name="sample-code"></a>Mintakód

Az egyéni parancsokkal (előzetes verzió) rendelkező hangsegéd létrehozásához használható mintakód a GitHubon érhető el.

* [Hangsegéd-minták (SDK)](https://aka.ms/csspeech/samples)

## <a name="customization"></a>Testreszabás

Az Azure Speech Services használatával készített hangsegédek teljes körű testreszabási lehetőségeket használhatnak a [beszéd-szöveg](speech-to-text.md), a [szöveg-beszéd](text-to-speech.md)és az [Egyéni kulcsszavak kiválasztásához](speech-devices-sdk-create-kws.md).

> [!NOTE]
> A testreszabási lehetőségek nyelv/területi beállítás szerint változnak (lásd a [támogatott nyelveket](supported-languages.md)).

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>További lépések

* [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
* [A Speech SDK beszerzése](speech-sdk.md)
