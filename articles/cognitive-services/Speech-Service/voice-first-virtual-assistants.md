---
title: Egyéni hang-és felhőközpontú virtuális asszisztensek (előzetes verzió) – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: A Funkciók, funkciókat és korlátozásokat egyéni hang-és felhőközpontú virtuális asszisztensek használatával a közvetlen vonal beszédfelismerő csatornát a Bot Framework és a Cognitive Services beszédfelismerő szoftver Development Kit (SDK) áttekintése.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: 1344de526564ab623a51eb903951b6a2e866048d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523475"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>Egyéni hang-és felhőközpontú virtuális asszisztensek előzetes tudnivalók

Az Azure Speech Services segítségével egyéni virtuális asszisztensek amelyekkel a fejlesztők természetes, emberi intelligenciára hasonlító párbeszédfelületeket a saját alkalmazások és az elemek létrehozásához. A Bot Framework közvetlen vonal beszédfelismerő csatornát ezeket a képességeket egy összehangolt, előkészített belépési pontot egy kompatibilis robot, amely lehetővé teszi a beszéd a hangalapú, alacsony késéssel és magas megbízhatósággal-szal való megadásával növeli. E robotok természetes nyelvi interakció használhatja a Microsoft Language Understanding (LUIS). A közvetlen vonal Speech eszközök, a beszéd Software Development Kit (SDK) használatával érhető el.

   ![A közvetlen vonal speech vezénylési szolgáltatás flow fogalmi diagramja](media/voice-first-virtual-assistants/overview.png "a beszédfelismerő csatornát folyamat")

A közvetlen vonal beszéd- és a kapcsolódó funkciók működését, hang-és felhőközpontú egyéni virtuális asszisztensek az ideális kiegészítés a [virtuális Segéd megoldás](https://docs.microsoft.com/azure/bot-service/bot-builder-virtual-assistant-introduction) és [vállalati sablon](https://docs.microsoft.com/azure/bot-service/bot-builder-virtual-assistant-introduction). Közvetlen vonal Speech dolgozhat bármilyen kompatibilis robot, bár ezeket az erőforrásokat biztosítanak magas színvonalú természetes nyelvi felhasználói környezet, valamint a közös támogató képességek és a modellek az első lépések gyorsan alapkonfigurációt újrafelhasználhatók.

## <a name="core-features"></a>Alapfunkciók

| Category | Funkciók |
|----------|----------|
|[Egyéni ébresztési word](speech-devices-sdk-create-kws.md) | Engedélyezheti a felhasználóknak beszélgetések kezdődniük robotokat egy egyéni kulcsszóval, például a "Hey Contoso." A feladat végrehajtását a Speech SDK-ban, amely egy egyéni ébresztési word a konfigurálható egyéni ébresztési word motorral [, itt is létrehozhat](speech-devices-sdk-create-kws.md). A közvetlen vonal beszédfelismerő csatornát magában foglalja a Szolgáltatásoldali ébresztési word annak ellenőrzése, hogy az ébresztési word aktiválás és az eszköz önálló javul.
|[Beszédfelismerés](speech-to-text.md) | A közvetlen vonal beszédfelismerő csatornát tartalmazza a felismert szöveget egy hang-, valós idejű beszédátírási [hang-szöveg transzformációs](speech-to-text.md) Azure Speech Services. Ezt a szöveget a robot, mind az ügyfélalkalmazás számára érhető el, ahogy azt a megjelenített érzéseket.
|[Szövegfelolvasás](text-to-speech.md) | A robot szöveges parancsválaszait fog synthesized használatával [szöveg-hang transzformációs](text-to-speech.md) Azure Speech Services. Az összefoglaló ezután lesz elérhető az ügyfélalkalmazásnak a audio-adatfolyamokat. A Microsoft kínál, így a saját egyéni, magas minőségű Neurális beszédfelismerő, amely hozzáférést biztosít a hangot a márka, további [lépjen kapcsolatba velünk](mailto:mstts@microsoft.com).
|[Közvetlen vonal beszéd](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Csatornán belül a Bot Framework közvetlen vonal beszédfelismerés lehetővé teszi, hogy az ügyfélalkalmazás kompatibilis bot és az Azure Speech Services képességeit zökkenőmentes kapcsolatát. A robot a közvetlen vonal beszédfelismerő csatornát használja konfigurálásáról további információért lásd: [az oldal a Bot Framework dokumentációjában](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="sample-code"></a>Mintakód

Mintakód egy hang-és felhőközpontú virtuális asszisztensek létrehozása a Githubon érhető el. Ezek a minták a robot a számos olyan népszerű programozási nyelvet való kapcsolódáshoz az ügyfélalkalmazás terjed ki.

* [Hang-és felhőközpontú virtuális asszisztensek minták (SDK)](https://aka.ms/csspeech/samples)
* [Gyors útmutató: hang-és felhőközpontú virtuális asszisztensek (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [Gyors útmutató: hang-és felhőközpontú virtuális asszisztensek (Java)](quickstart-virtual-assistant-java-jre.md)

## <a name="customization"></a>Testreszabás

Hang-és felhőközpontú Azure Speech Services használatával létrehozott virtuális asszisztensek használható a rendelkezésre álló testreszabási lehetőségek széles [hang-szöveg transzformációs](speech-to-text.md), [szöveg-hang transzformációs](text-to-speech.md), és [egyéni kulcsszó Kijelölés](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Testreszabási lehetőségek minden nyelvi és területi beállítása szerint (lásd: [támogatott nyelvek](supported-languages.md)).

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>További lépések

* [Ingyenes beszédszolgáltatások előfizetési kulcs lekérése](get-started.md)
* [A beszédfelismerés SDK beszerzése](speech-sdk.md)
* [Létrehozni és üzembe helyezni egy alapszintű robot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [A virtuális Segéd megoldás és a vállalati sablon](https://github.com/Microsoft/AI)
