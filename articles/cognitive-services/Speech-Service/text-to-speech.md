---
title: Szöveg-hang transzformációs Azure Speech szolgáltatással
titleSuffix: Azure Cognitive Services
description: Az Azure Speech Services szöveg-hang transzformációs egy REST-alapú szolgáltatás, amely lehetővé teszi az alkalmazások, eszközök és eszközök szöveg átalakítása beszéddé természetes emberszerű szintetizált. Standard és a Neurális beszédhangot közül választhat, vagy létrehozhat saját egyéni beszédfelismerési a termék vagy márka egyedi. 75 + standard beszédhangot legfeljebb 45 nyelv és területi beállítás érhető el, és 5 Neurális beszédhangot 4 nyelv és területi beállítás érhető el.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 05028704c08ebd06f9b9e4e3f45c5137eb1e6b58
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226912"
---
# <a name="what-is-text-to-speech"></a>Mi az szöveg-hang transzformációs?

Az Azure Speech Services szöveg-hang transzformációs egy REST-alapú szolgáltatás, amely lehetővé teszi az alkalmazások, eszközök és eszközök szöveg átalakítása beszéddé természetes emberszerű szintetizált. Standard és a Neurális beszédhangot válasszon, vagy létrehozhatja a saját [egyéni beszédfelismerési](#custom-voice-fonts) a termék vagy márka egyedi. 75 + standard beszédhangot legfeljebb 45 nyelv és területi beállítás érhető el, és 5 Neurális beszédhangot 4 nyelv és területi beállítás érhető el. A teljes listát lásd: [támogatott nyelvek](language-support.md#text-to-speech).

Szöveg-hang transzformációs technológia lehetővé teszi a tartalomkészítők, a felhasználóknak a különböző módokon zajló interakciót. Szöveg-hang transzformációs javíthatja a kisegítő lehetőségek biztosításával a felhasználók és használhatja a tartalmakat hallhatóan lehetőség. Hogy a felhasználó egy készült kisegítő funkciók, tanulás fogyatékossággal, vagy elérésével navigációs információra van szüksége, szöveg-hang transzformációs javíthatja a meglévő felületet nyújt. Szöveg-hang transzformációs is egy hang robotok és a virtuális asszisztensek értékes bővítmény.

### <a name="neural-voices"></a>Neurális beszédhangot

Győződjön meg arról, látás- és a virtuális asszisztensek interakció természetesebb, és vonzó, digitális szövegek, például az e-könyvek átalakítása audiobooks, és javíthatja a autós navigációs rendszerek Neurális beszédhangot használható. Az emberi intelligenciára hasonlító elemeket természetes prosody és egyértelmű csuklópontot szó Neurális beszédhangot jelentősen csökkentheti figyel-e fáradás AI-rendszerekkel való kommunikáció során. Neurális beszédhangot kapcsolatos további információkért lásd: [támogatott nyelvek](language-support.md#text-to-speech).

### <a name="custom-voices"></a>Egyéni beszédhangot

Hangalapú testreszabási egy könnyen felismerhető névre, egy-az-maga nemében egyedülálló hang, a saját márkáját létrehozását teszi lehetővé. Szeretne létrehozni az egyéni hangtípusú, győződjön meg arról, a studio felvételt, és töltse fel a betanítási adatok, a kapcsolódó parancsprogramokat. A szolgáltatás ekkor létrehoz egy egyéni modell a rögzítése használatára vannak konfigurálva. Az egyéni hangtípusú használatával beszédfelismerési szintetizálásához. További információkért lásd: [egyéni beszédhangot](how-to-customize-voice-font.md).

## <a name="core-features"></a>Alapfunkciók

Ez a táblázat felsorolja a szöveg-hang transzformációs alapvető funkciói:

| Használati eset | SDK | REST |
|----------|-----|------|
| Szöveg-beszéd átalakítás. | Nem | Igen |
| Töltse fel a hangalapú betanítás adatkészletek. | Nem | igen\* |
| Hozzon létre, és hang betűtípus modellek kezelése. | Nem | igen\* |
| Hozzon létre, és hang betűtípus központi telepítések felügyeletéhez szükséges. | Nem | igen\* |
| Létrehozhat és kezelhet hangalapú betűtípus teszteket. | Nem | igen\* |
| Előfizetések kezelése. | Nem | igen\* |

\* *Ezek a szolgáltatások érhetők el a cris.ai végpont használatával. Lásd: [referencia Swagger](https://westus.cris.ai/swagger/ui/index).*

> [!NOTE]
> A szöveg-hang transzformációs végpont valósítja meg, amely korlátozza a kérelmek 25, 5 másodpercenként szabályozás. Szabályozás akkor fordul elő, ha értesítést is üzenetfejlécekben keresztül.

## <a name="get-started-with-text-to-speech"></a>Szöveg-beszéd átalakítás használatának első lépései

Rövid útmutatók, kevesebb mint 10 perc múlva a kódot futtató úgy tervezték, hogy biztosítunk. Ez a táblázat szöveg-hang transzformációs útmutatóink nyelv szerint rendezett listáját tartalmazza.

| Első lépések | Platform | API-referencia |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Tallózás](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Window, macOS, Linux | [Tallózás](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |
| [Python](quickstart-python-text-to-speech.md) | Window, macOS, Linux | [Tallózás](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |

## <a name="sample-code"></a>Mintakód

A szöveg-hang transzformációs mintakód a Githubon érhető el. Ezek a minták a legnépszerűbb programnyelv a szöveg-beszéd átalakítás terjed ki.

* [Szöveg-hang transzformációs minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Szöveg-hang transzformációs](rest-text-to-speech.md)
* [REST API: A Batch beszédátírási és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

* [Ingyenes Speech Services előfizetés beszerzése](get-started.md)
* [Hozzon létre egyéni hangtípust](how-to-customize-voice-font.md)
