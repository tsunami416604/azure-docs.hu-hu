---
title: Hangsegédek – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A hangsegédek funkcióinak, képességeinek és korlátozásainak áttekintése a Speech szoftverfejlesztői készlet (SDK) használatával.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: c97f6414876441290cade68b8f9a054970586402
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507736"
---
# <a name="about-voice-assistants"></a>A hangsegédek ismertetése

Az Azure Speech Servicest használó hangsegédek lehetővé teszik a fejlesztők számára, hogy természetes, emberi jellegű társalgási felületet hozzanak létre alkalmazásaikban és tapasztalataikban.

A hangsegéd szolgáltatás gyors, megbízható interakciót tesz lehetővé egy eszköz és egy asszisztens implementációja között, amely vagy (1) a bot Framework közvetlen vonalú beszédfelismerési csatornáját, vagy (2) az integrált egyéni parancsok (előzetes verzió) szolgáltatást használja a feladatok befejezéséhez.

Az alkalmazások a beszédfelismerési szoftverfejlesztői készlettel (SDK) csatlakoznak a Voice Assistant szolgáltatáshoz.

   ![A hangsegéd-előkészítési szolgáltatás folyamatának fogalmi diagramja](media/voice-assistants/overview.png "A hangsegéd folyamata")

## <a name="core-features"></a>Alapvető funkciók

| Kategória | Jellemzők |
|----------|----------|
|[Egyéni kulcsszó](speech-devices-sdk-create-kws.md) | A felhasználók olyan egyéni kulcsszóval indíthatnak el beszélgetéseket, mint például a "Hey contoso". Az alkalmazás a Speech SDK-ban egy egyéni kulcsszavas motorral rendelkezik, amely konfigurálható egy egyéni kulcsszóval, [amelyet itt is](speech-devices-sdk-create-kws.md)létrehozhat. A hangsegédek kiszolgálóoldali ellenőrzés használatával növelhetik a kulcsszó aktiválásának pontosságát (az eszköz helyett).
|[Beszéd szövege](speech-to-text.md) | A hangsegédek valós idejű hangfelismerést végeznek az Azure Speech Servicesből származó [beszéd – szöveg](speech-to-text.md) használatával. Ez a szöveg a Segéd implementációjában és az ügyfélalkalmazásban is elérhető.
|[Szöveg – beszéd](text-to-speech.md) | A Segéd szöveges válaszai az Azure Speech Servicesből származó [szöveges – beszéd szövegből](text-to-speech.md) lettek szintetizálva. Ezt a szintézist ezután az ügyfélalkalmazás elérhetővé válik egy hangadatfolyamként. A Microsoft lehetőséget nyújt saját, kiváló minőségű neurális TTS-hang létrehozására, amely hangvételt biztosít a márka számára. További információért [vegye fel velünk a kapcsolatot](mailto:mstts@microsoft.com).

## <a name="comparing-assistant-solutions"></a>Asszisztensi megoldások összehasonlítása

A hangsegéd szolgáltatás összekapcsolja az eszközön lévő alkalmazást az egyedi asszisztensi implementációval. A fejlesztők hangsegédeket készítenek (1) a bot Framework [közvetlen vonalas beszédfelismerési](direct-line-speech.md) csatornáját vagy (2) az [egyéni parancsok (előzetes verzió)](custom-commands.md) megoldás használatával.

   ![Asszisztensi megoldások összehasonlítása](media/voice-assistants/assistant-solution-comparison.png "Asszisztensi megoldások összehasonlítása")

| Megoldás | Jellemzők |
|----------|----------|
|[Egyéni parancsok (előzetes verzió)](custom-commands.md) | Az egyéni parancsok (előzetes verzió) egyszerűsített szerzői és üzemeltetési megoldást biztosítanak a hangsegédek számára. Ez a feladat-befejezési és a parancs-vezérlési forgatókönyvek igényeihez igazodik.
|[Közvetlen vonalas beszéd](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | A közvetlen vonalas beszéd zökkenőmentes és zökkenőmentes kapcsolatot tesz lehetővé (1) az ügyfélalkalmazás, (2) egy kompatibilis robot, és (3) az Azure Speech Services képességei. A robot közvetlen vonalas hangcsatorna használatára való konfigurálásával kapcsolatos további információkért tekintse meg az [oldalát a bot Framework dokumentációjában](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

Miután létrehozott egy hangsegédet az egyik megoldással, az eszközön lévő alkalmazást a Speech SDK `DialogServiceConnector` segítségével kapcsolja össze. További részletekért tekintse meg a gyors üzembe helyezést és a mintákat az egyes megoldásokhoz.

## <a name="getting-started-with-voice-assistants"></a>Első lépések a hangsegédek használatába

Olyan gyors útmutatókat is kínálunk, amelyek kevesebb, mint 10 perc alatt futtatják a kódot. Ez a táblázat a hangsegéd-gyors üzembe helyezések listáját tartalmazza, nyelv szerint rendezve.

| Gyors útmutató | Platform | API-referencia |
|------------|----------|---------------|
| C#, UWP | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Tallózás](https://aka.ms/csspeech/javaref) |
| Java | Android | [Tallózás](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Mintakód

Hangsegéd létrehozásához használható mintakód a GitHubon érhető el. Ezek a minták lefedik az ügyfélalkalmazás számára a Segédhez való csatlakozást számos népszerű programozási nyelven.

* [Hangsegéd-minták (SDK)](https://aka.ms/csspeech/samples)
* [Oktatóanyag: hang engedélyezése a Segéd számára a Speech SDK-valC#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Oktatóanyag

Egy oktatóanyag, amely bemutatja, hogyan [engedélyezheti a Segédet a SPEECH SDK és a Direct line Speech Channel használatával](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Testreszabás

Az Azure Speech Services használatával készített hangsegédek teljes körű testreszabási lehetőségeket használhatnak a [beszéd-szöveg](speech-to-text.md), a [szöveg-beszéd](text-to-speech.md)és az [Egyéni kulcsszavak kiválasztásához](speech-devices-sdk-create-kws.md).

> [!NOTE]
> A testreszabási lehetőségek nyelv/területi beállítás szerint változnak (lásd a [támogatott nyelveket](supported-languages.md)).

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>További lépések

* [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
* [A Speech SDK beszerzése](speech-sdk.md)
* [További információ az egyéni parancsokról (előzetes verzió)](custom-commands.md)
* [További információ a közvetlen vonalas beszédről](direct-line-speech.md)