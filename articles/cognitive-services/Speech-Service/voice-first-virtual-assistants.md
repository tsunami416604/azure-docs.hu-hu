---
title: Egyéni hang – első virtuális asszisztensek (előzetes verzió) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Az egyéni hangsegédek funkcióinak, képességeinek és korlátozásainak áttekintése, a bot Framework közvetlen vonalas hangcsatornájának használatával és a Cognitive Services Speech szoftverfejlesztői készlettel (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: f77a95af1edcd37043ea1e067560577140a81a04
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967592"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>Az egyéni hang – első virtuális asszisztensek előzetes verziója

Az Azure Speech Servicest használó egyéni virtuális asszisztensek lehetővé teszik a fejlesztők számára, hogy természetes, emberi jellegű társalgási felületet hozzanak létre alkalmazásaikban és tapasztalataikban. A bot Framework közvetlen vonalas beszédének csatornája a következő funkciókat biztosítja: egy koordinált, összehangolt belépési pont egy olyan kompatibilis robot számára, amely lehetővé teszi a hangfelismerést, az alacsony késéssel és a magas megbízhatósággal való interakciót. Ezek a robotok a Microsoft Language Understanding (LUIS) nyelvét használhatják a természetes nyelvi interakcióhoz. A Direct line Speech szolgáltatást a Speech szoftverfejlesztői készletet (SDK) használó eszközök érhetik el.

   ![A közvetlen vonalas beszédfelismerési szolgáltatás folyamatának fogalmi diagramja](media/voice-first-virtual-assistants/overview.png "A beszédfelismerési csatorna folyamata")


A közvetlen vonalas beszéd és a hozzá tartozó funkciók az egyéni hangalapú virtuális asszisztensekhez ideális kiegészítést jelentenek a [virtuális asszisztensi megoldás és a vállalati sablon](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)számára. Habár a közvetlen vonalas beszéd bármilyen kompatibilis robottal működhet, ezek az erőforrások újrafelhasználható alapkonfigurációt biztosítanak a kiváló minőségű beszélgetési élményekhez, valamint a gyors kezdéshez szükséges általános támogatási képességeket és modelleket.


## <a name="core-features"></a>Alapvető funkciók

| Category | Szolgáltatások |
|----------|----------|
|[Egyéni ébresztési szó](speech-devices-sdk-create-kws.md) | Lehetővé teheti a felhasználók számára, hogy egy egyéni kulcsszóval, például a "Hey contoso"-val kezdjenek beszélgetésekbe. Ez a feladat a Speech SDK egyéni ébresztési motorjának használatával érhető el, amely az [itt létrehozható](speech-devices-sdk-create-kws.md)egyéni ébresztési Szóval konfigurálható. A közvetlen vonalas beszéd csatorna olyan szolgáltatás-oldali ébresztési ellenőrzést tartalmaz, amely javítja az ébresztési szó aktiválásának és az eszköznek a pontosságát.
|[Beszéd szövege](speech-to-text.md) | A közvetlen vonalas hangcsatorna valós idejű átírást tartalmaz a hanganyagból az felismert szövegbe az Azure Speech Servicesből származó [beszéd – szöveg](speech-to-text.md) használatával. Ez a szöveg a robot és az ügyfélalkalmazás számára is elérhető, ahogy azt az átirata is.
|[Szöveg – beszéd](text-to-speech.md) | A robotból érkező szöveges válaszokat az Azure Speech Servicesből származó [szöveg-beszéd](text-to-speech.md) használatával készíti elő. Ezt a szintézist ezután az ügyfélalkalmazás fogja elérhetővé tenni egy hangadatfolyamként. A Microsoft lehetőséget nyújt saját, kiváló minőségű neurális TTS-hang létrehozására, amely hangvételt biztosít a márka számára, hogy további kapcsolatfelvételt tudjon meg [velünk](mailto:mstts@microsoft.com).
|[Közvetlen vonalas beszéd](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | A bot Framework csatornán belül a közvetlen vonalas beszéd zökkenőmentes és zökkenőmentes kapcsolatot biztosít az ügyfélalkalmazás, a kompatibilis robot és az Azure Speech Services képességei között. A bot Direct line Speech Channel használatára való konfigurálásával kapcsolatos további információkért tekintse meg [az oldalát a bot Framework dokumentációjában](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="get-started-with-virtual-assistants"></a>Ismerkedés a virtuális asszisztensekkel

Olyan gyors útmutatókat is kínálunk, amelyek kevesebb, mint 10 perc alatt futtatják a kódot. Ez a táblázat a hang-első virtuális asszisztensi rövid útmutatók listáját tartalmazza, nyelv szerint rendezve.

| Első lépések | Platform | API-referencia |
|------------|----------|---------------|
| C#, UWP | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Tallózás](https://aka.ms/csspeech/javaref) |
| Java | Android | [Tallózás](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Mintakód

A hang-első virtuális asszisztens létrehozásához használt mintakód a GitHubon érhető el. Ezek a minták lefedik az ügyfélalkalmazás számára a robothoz való csatlakozást számos népszerű programozási nyelven.

* [Hang – első virtuális asszisztensi minták (SDK)](https://aka.ms/csspeech/samples)
* [Oktatóanyag: Hang engedélyezése a robotnak a Speech SDK-valC#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Oktatóanyag
Egy oktatóanyag, amely bemutatja, hogyan engedélyezheti a [robotot a SPEECH SDK és a Direct line Speech Channel használatával](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Testreszabás

Az Azure Speech Services használatával létrehozott első virtuális asszisztensek teljes körű testreszabási lehetőségeket használhatnak a [beszéd-szöveg](speech-to-text.md), a [szöveg-beszéd](text-to-speech.md)és az [Egyéni kulcsszavak](speech-devices-sdk-create-kws.md)kiválasztásához.

> [!NOTE]
> A testreszabási lehetőségek nyelv/területi beállítás szerint változnak (lásd a [támogatott nyelveket](supported-languages.md)).

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>További lépések

* [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
* [A Speech SDK beszerzése](speech-sdk.md)
* [Alapszintű robot létrehozása és üzembe helyezése](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [A virtuális asszisztens megoldás és a vállalati sablon beszerzése](https://github.com/Microsoft/AI)
