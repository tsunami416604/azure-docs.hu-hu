---
title: Közvetlen vonalas beszéd – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A hangsegédek funkcióinak, képességeinek és korlátozásainak áttekintése a Speech szoftverfejlesztői készlettel (SDK) közvetlen vonalas beszédfelismerés használatával.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 72029d1d56c9d561e629cc750244fd7de8ec113a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507175"
---
# <a name="about-direct-line-speech"></a>Közvetlen vonalas beszéd

A [hangvezérelt asszisztensek](voice-assistants.md) meghallgatják a felhasználókat, és válaszként reagálnak, gyakran beszélnek vissza. A felhasználók beszédének [szövegét](speech-to-text.md) használják a felhasználó beszédének átírására, majd a szöveg természetes nyelvének megismerésére. Ez a művelet gyakran tartalmazza a [szöveg-beszéd](text-to-speech.md)kapcsolattal generált segédből származó beszédes kimenetet. Az eszközök a Speech SDK `DialogServiceConnector` objektumával csatlakoznak a segédekhez.

A **Direct line Speech** egy robusztus, teljes körű megoldás, amellyel rugalmas, bővíthető hangasszisztenst hozhat létre, amely a bot Framework és az általa használt közvetlen vonalas beszédfelismerési csatorna segítségével érhető el.

A közvetlen vonalas beszédfelismerés a lehető legmagasabb szintű testreszabást és kifinomultságot kínálja a hangsegédek számára. Kiválóan alkalmas olyan társalgási forgatókönyvekhez, amelyek nyílt végű, természetes vagy hibridek a feladatok befejezésével vagy a parancs-és vezérlési használattal. Ez a nagyfokú rugalmasság nagyobb bonyolultságot jelent, és a természetes nyelvi bevitelt használó, jól definiált feladatokra vonatkozó forgatókönyvek esetében előfordulhat, hogy az [egyéni parancsokat (előzetes verzió)](custom-commands.md) érdemes megfontolni a zökkenőmentes megoldáshoz.

## <a name="getting-started-with-direct-line-speech"></a>Első lépések a közvetlen vonalas Beszédtel

A közvetlen vonalas beszédfelismerést használó hangsegédek létrehozásának első lépései [egy beszédfelismerési előfizetési kulcs beszerzése](get-started.md), az előfizetéshez társított új robot létrehozása, valamint a robot csatlakoztatása a közvetlen vonalas hangcsatornához.

   ![A közvetlen vonalas beszédfelismerési szolgáltatás folyamatának fogalmi diagramja](media/voice-assistants/overview-directlinespeech.png "A beszédfelismerési csatorna folyamata")

Az egyszerű hangsegédek közvetlen vonalas Beszédtel való létrehozásával kapcsolatos részletes útmutatóért tekintse meg [a beszédfelismerésre vonatkozó oktatóanyagot, amely lehetővé teszi, hogy a robot a SPEECH SDK-val és a közvetlen vonalas beszédfelismerési csatornával](tutorial-voice-enable-your-bot-speech-sdk.md)legyen felhasználva.

Olyan gyors útmutatókat is kínálunk, amelyek kevesebb, mint 10 perc alatt futtatják a kódot. Ez a táblázat a hangsegédek nyelv szerinti rendezésének listáját tartalmazza.

| Gyors útmutató | Platform | API-referencia |
|------------|----------|---------------|
| C#, UWP | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Tallózás](https://aka.ms/csspeech/javaref) |
| Java | Android | [Tallózás](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Mintakód

Hangsegéd létrehozásához használható mintakód a GitHubon érhető el. Ezek a minták lefedik az ügyfélalkalmazás számára a Segédhez való csatlakozást számos népszerű programozási nyelven.

* [Hangsegéd-minták (SDK)](https://aka.ms/csspeech/samples)
* [Oktatóanyag: hang engedélyezése a Segéd számára a Speech SDK-valC#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Testreszabás

Az Azure Speech Services használatával készített hangsegédek teljes körű testreszabási lehetőségeket használhatnak a [beszéd-szöveg](speech-to-text.md), a [szöveg-beszéd](text-to-speech.md)és az [Egyéni kulcsszavak kiválasztásához](speech-devices-sdk-create-kws.md).

> [!NOTE]
> A testreszabási lehetőségek nyelv/területi beállítás szerint változnak (lásd a [támogatott nyelveket](supported-languages.md)).

A közvetlen vonalas beszéd és a hozzá tartozó funkciók a hangsegédek számára ideális kiegészítőek a [virtuális asszisztensi megoldáshoz és a vállalati sablonhoz](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Habár a közvetlen vonalas beszéd bármilyen kompatibilis robottal működhet, ezek az erőforrások újrafelhasználható alapkonfigurációt biztosítanak a kiváló minőségű beszélgetési élményekhez, valamint a gyors kezdéshez szükséges általános támogatási képességeket és modelleket.

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>További lépések

* [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
* [A Speech SDK beszerzése](speech-sdk.md)
* [Alapszintű robot létrehozása és üzembe helyezése](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [A virtuális asszisztens megoldás és a vállalati sablon beszerzése](https://github.com/Microsoft/AI)
