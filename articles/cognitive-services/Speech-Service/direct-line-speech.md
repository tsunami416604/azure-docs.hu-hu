---
title: Közvetlen vonalas beszéd – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A hangsegédek funkcióinak, képességeinek és korlátozásainak áttekintése a Speech szoftverfejlesztői készlettel (SDK) közvetlen vonalas beszédfelismerés használatával.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: e8c7e21b0784aacc85bd02e3c1702e1a710e76ab
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021881"
---
# <a name="what-is-direct-line-speech"></a>Mi az a Direct Line Speech?

A **Direct line Speech** egy robusztus, teljes körű megoldás egy rugalmas, bővíthető hangsegéd létrehozásához. A robot-keretrendszer és a közvetlen vonalas beszéd csatornája, amely hangvételre van optimalizálva, a botokkal való kommunikációhoz.

A [hangvezérelt asszisztensek](voice-assistants.md) meghallgatják a felhasználókat, és válaszként reagálnak, gyakran beszélnek vissza. A felhasználók beszédének [szövegét](speech-to-text.md) használják a felhasználó beszédének átírására, majd a szöveg természetes nyelvének megismerésére. Ez a művelet gyakran tartalmazza a [szöveg-beszéd](text-to-speech.md)kapcsolattal generált segédből származó beszédes kimenetet.

A közvetlen vonalas beszédfelismerés a lehető legmagasabb szintű testreszabást és kifinomultságot kínálja a hangsegédek számára. A szolgáltatás a két nyílt végű, természetes vagy hibrid, a feladatok befejezését, illetve a parancs-és vezérlési használatot használó beszélgetési forgatókönyvekhez készült. Ez a nagyfokú rugalmasság nagyobb bonyolultságot jelent, és a természetes nyelvi bevitelt használó, jól definiált feladatokra vonatkozó forgatókönyvek esetében előfordulhat, hogy az [egyéni parancsokat (előzetes verzió)](custom-commands.md) érdemes megfontolni a zökkenőmentes megoldáshoz.

## <a name="getting-started-with-direct-line-speech"></a>Első lépések a közvetlen vonalas Beszédtel

A közvetlen vonalas beszédfelismerést használó hangsegédek létrehozásának első lépései [egy beszédfelismerési előfizetési kulcs beszerzése](overview.md#try-the-speech-service-for-free), az előfizetéshez társított új robot létrehozása, valamint a robot csatlakoztatása a közvetlen vonalas hangcsatornához.

   ![A közvetlen vonalas beszédfelismerési szolgáltatás folyamatának fogalmi diagramja](media/voice-assistants/overview-directlinespeech.png "A beszédfelismerési csatorna folyamata")

Az egyszerű hangsegédek közvetlen vonalas Beszédtel való létrehozásával kapcsolatos részletes útmutatóért tekintse meg [a beszédfelismerésre vonatkozó oktatóanyagot, amely lehetővé teszi, hogy a robot a SPEECH SDK-val és a közvetlen vonalas beszédfelismerési csatornával legyen ellátva](tutorial-voice-enable-your-bot-speech-sdk.md).

Emellett olyan gyors útmutatókat is kínálunk, amelyekkel a kód futtatásával és az API-kkal való ismerkedéssel gyorsan elsajátítható. Ez a táblázat a hangsegédek nyelvén és platformon szervezett rövid útmutatóit sorolja fel.

| Gyorsútmutató | Platform | API-referencia |
|------------|----------|---------------|
| C#, UWP | Windows | [Tallózás](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows, macOS, Linux | [Tallózás](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [Tallózás](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>Mintakód

Hangsegéd létrehozásához használható mintakód a GitHubon érhető el. Ezek a minták lefedik az ügyfélalkalmazás számára a Segédhez való csatlakozást számos népszerű programozási nyelven.

* [Hangsegéd-minták (SDK)](https://aka.ms/csspeech/samples)
* [Oktatóanyag: hang engedélyezése a Segéd számára a Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Testreszabás

A beszédfelismerési szolgáltatással készített hangsegédek teljes körű testreszabási lehetőségeket használhatnak a [beszéd-szöveg](speech-to-text.md), a [szöveg-beszéd](text-to-speech.md)és az [Egyéni kulcsszavak kiválasztásához](./custom-keyword-basics.md).

> [!NOTE]
> A testreszabási lehetőségek nyelv/területi beállítás szerint változnak (lásd a [támogatott nyelveket](./language-support.md)).

A közvetlen vonalas beszéd és a hozzá tartozó funkciók a hangsegédek számára ideális kiegészítőek a [virtuális asszisztensi megoldáshoz és a vállalati sablonhoz](/azure/bot-service/bot-builder-enterprise-template-overview). Habár a közvetlen vonalas beszéd bármilyen kompatibilis robottal működhet, ezek az erőforrások újrafelhasználható alapkonfigurációt biztosítanak a kiváló minőségű beszélgetési élményekhez, valamint a gyors kezdéshez szükséges általános támogatási képességeket és modelleket.

## <a name="reference-docs"></a>Dokumentációs dokumentumok

* [Beszéd SDK](./speech-sdk.md)
* [Azure Bot Service](/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>További lépések

* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](overview.md#try-the-speech-service-for-free)
* [A Speech SDK beszerzése](speech-sdk.md)
* [Alapszintű robot létrehozása és üzembe helyezése](/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [A virtuális asszisztens megoldás és a vállalati sablon beszerzése](https://github.com/Microsoft/AI)