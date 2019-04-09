---
title: Szöveg-hang transzformációs Azure Speech szolgáltatással
titleSuffix: Azure Cognitive Services
description: Az Azure Speech Services szöveg-hang transzformációs egy szolgáltatása, amely lehetővé teszi az alkalmazások, eszközök és eszközök szöveg átalakítása beszéddé természetes emberszerű szintetizált. Standard és a Neurális beszédhangot közül választhat, vagy létrehozhat saját egyéni beszédfelismerési a termék vagy márka egyedi. 75 + standard beszédhangot legfeljebb 45 nyelv és területi beállítás érhető el, és 5 Neurális beszédhangot 4 nyelv és területi beállítás érhető el.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 597932ae9ad4dba76428fa6a4882c50f6ff98754
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263111"
---
# <a name="what-is-text-to-speech"></a>Mi az szöveg-hang transzformációs?

Az Azure Speech Services szöveg-hang transzformációs egy szolgáltatása, amely lehetővé teszi az alkalmazások, eszközök és eszközök szöveg átalakítása beszéddé természetes emberszerű szintetizált. Standard és a Neurális beszédhangot közül választhat, vagy létrehozhat saját egyéni beszédfelismerési a termék vagy márka egyedi. 75 + standard beszédhangot legfeljebb 45 nyelv és területi beállítás érhető el, és 5 Neurális beszédhangot 4 nyelv és területi beállítás érhető el. A teljes listát lásd: [támogatott nyelvek](language-support.md#text-to-speech).

Szöveg-hang transzformációs technológia lehetővé teszi a tartalomkészítők, a felhasználóknak a különböző módokon zajló interakciót. Szöveg-hang transzformációs javíthatja a kisegítő lehetőségek biztosításával a felhasználók és használhatja a tartalmakat hallhatóan lehetőség. Hogy a felhasználó egy készült kisegítő funkciók, tanulás fogyatékossággal, vagy elérésével navigációs információra van szüksége, szöveg-hang transzformációs javíthatja a meglévő felületet nyújt. Szöveg-hang transzformációs is egy hang robotok és a virtuális asszisztensek értékes bővítmény.

### <a name="standard-voices"></a>Standard beszédhangot

Standard szintű beszédhangot statisztikai paraméteres összefoglaló és/vagy összefűző összefoglaló technikák használatával jön létre. Ezek adott beszédhangot magas érthető, és hang-meglehetősen természetes. Mondjon ki több mint 45 nyelvet, az alábbi beállítások közül számos az alkalmazások könnyen engedélyezheti. Ezek adott beszédhangot adja meg a magas írásmódja pontosság, beleértve az rövidítéseket, betűszó bővülésből, dátum és idő értelmezések, polyphones és egyéb támogatása. Használjon szabványos hallhatóan kommunikál a tartalmakat a felhasználók számára az alkalmazások és szolgáltatások akadálymentesítés érdekében.

### <a name="neural-voices"></a>Neurális beszédhangot

Neurális beszédhangot neurálishálózat használatával elhárítja a hagyományos szöveg-hang transzformációs rendszerek magas terhelés, és használja a beszélt nyelv, és a speech egységeket szintetizáló be a számítógép hang intonation utaló megfelelő korlátozásait. Standard szintű, szöveg-hang transzformációs felszámolja prosody külön nyelvi elemzés és akusztikai előrejelzési lépések, amelyek független modellek vonatkoznak rájuk. Amely muffled, buzzy hangalapú összefoglaló eredményezhet. A Neurális képesség hajtja végre prosody előrejelzési és hangra vonatkozó összefoglaló egyidejűleg, ami egy további képlékeny és természetes hangzó hangalapú eredményez.

Győződjön meg arról, látás- és a virtuális asszisztensek interakció természetesebb, és vonzó, digitális szövegek, például az e-könyvek átalakítása audiobooks, és javíthatja a autós navigációs rendszerek Neurális beszédhangot használható. Az emberi intelligenciára hasonlító elemeket természetes prosody és egyértelmű csuklópontot szó Neurális beszédhangot jelentősen csökkentheti figyel-e fáradás AI-rendszerekkel való kommunikáció során. Neurális beszédhangot kapcsolatos további információkért lásd: [támogatott nyelvek](language-support.md#text-to-speech).

Neurális beszédhangot előnyeivel kapcsolatos további tudnivalókért lásd: [a Microsoft új Neurális szöveg-hang transzformációs szolgáltatás segítségével például a felhasználók beszélnek gépek](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Egyéni beszédhangot

Hangalapú testreszabási egy könnyen felismerhető névre, egy-az-maga nemében egyedülálló hang, a saját márkáját létrehozását teszi lehetővé. Szeretne létrehozni az egyéni hangtípusú, győződjön meg arról, a studio felvételt, és töltse fel a betanítási adatok, a kapcsolódó parancsprogramokat. A szolgáltatás ekkor létrehoz egy egyéni modell a rögzítése használatára vannak konfigurálva. Az egyéni hangtípusú használatával beszédfelismerési szintetizálásához. További információkért lásd: [egyéni beszédhangot](how-to-customize-voice-font.md).

## <a name="core-features"></a>Alapfunkciók

Ez a táblázat felsorolja a szöveg-hang transzformációs alapvető funkciói:

| Használati eset | SDK | REST |
|----------|-----|------|
| Szöveg-beszéd átalakítás. | Igen | Igen |
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

### <a name="sdk-quickstarts"></a>SDK rövid útmutatók

| Rövid útmutató (SDK) | Platform | API-referencia |
|------------|----------|---------------|
| [C#, .NET-keretrendszer](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Tallózás](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Tallózás](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>REST-útmutatók

| Rövid útmutató (REST) | Platform | API-referencia |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Tallózás](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Window, macOS, Linux | [Tallózás](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Window, macOS, Linux | [Tallózás](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Mintakód

A szöveg-hang transzformációs mintakód a Githubon érhető el. Ezek a minták a legnépszerűbb programnyelv a szöveg-beszéd átalakítás terjed ki.

* [Szöveg-hang transzformációs minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Szöveg-hang transzformációs minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Beszédeszközök SDK](speech-devices-sdk.md)
* [REST API: Diktálás](rest-speech-to-text.md)
* [REST API: Szövegfelolvasás](rest-text-to-speech.md)
* [REST API: A Batch beszédátírási és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

* [Ingyenes Speech Services előfizetés beszerzése](get-started.md)
* [Hozzon létre egyéni hangtípust](how-to-customize-voice-font.md)
