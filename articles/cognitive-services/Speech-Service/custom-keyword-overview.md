---
title: Egyéni kulcsszavak – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Az egyéni kulcsszavak funkcióinak, képességeinek és korlátozásának áttekintése a Speech szoftverfejlesztői készlet (SDK) használatával.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: 0a6df626f9567b51a8371c17004b454f9c5b4597
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202046"
---
# <a name="what-is-a-keyword"></a>Mi az a kulcsszó?

A kulcsszó egy szó vagy rövid kifejezés, amely lehetővé teszi, hogy a termék hangvezérelt legyen. Például a "Hey Cortana" a Cortana segéd kulcsszava. A Hangaktiválás lehetővé teszi, hogy a felhasználók a kulcsszóval teljesen megkezdhetik a termékkel való interakciót. Mivel a termék folyamatosan figyeli a kulcsszót, az összes hang helyileg lesz feldolgozva a felhasználó eszközén mindaddig, amíg észlelés történik, hogy az adatok a lehető legmagának legyenek.

## <a name="core-features-of-custom-keyword"></a>Az egyéni kulcsszó alapvető funkciói

Az egyéni kulcsszavak testreszabási, teljesítmény-és integrációs funkciói révén a hangalapú aktiválást testre szabhatja a termék jövőképének és felhasználói igényeinek megfelelően.

| Szolgáltatás | Leírás |
|----------|----------|
| Kulcsszó testreszabása | A márka kiterjesztéseként egy kulcsszó megerősíti az ügyfeleivel létrehozott saját tőkét. A Speech Studio egyéni kulcsszavas portálján bármely olyan szót vagy rövid kifejezést megadhat, amely a legjobban jelképezi a márkáját. A kulcsszót a megfelelő kiejtések kiválasztásával személyre szabhatja, amelyet a generált Kulcsszóválasztó modell tiszteletben tart.
| Kulcsszó ellenőrzése | Ha a kulcsszót helyileg észlelik, a rendszer a hangot a felhőbe küldi, hogy további ellenőrzés után a felhasználó a kulcsszót is elmondja. A kulcsszó-ellenőrzés egy további biztonsági réteget biztosít azáltal, hogy csökkenti a helytelen helyi észlelés és a felhasználói adatok védelmének hatásait.
| Hangsegéd & Speech SDK-integráció | A Speech Studióban az egyéni kulcsszó alapján létrehozott kulcsszavak könnyen integrálhatók az eszközön vagy alkalmazáson belül a Speech SDK használatával. Egyszerűen mutasson az SDK-ra a Speech Studio által biztosított kulcsszó-modellre, és a termék hangvezérelt lesz, a kulcsszó-ellenőrzés által támogatottak. A saját [hangsegéd](voice-assistants.md)létrehozásával elvégezheti a termék hangélményét.

## <a name="get-started-with-custom-keywords"></a>Ismerkedés az egyéni kulcsszavakkal

* Oktatóanyag: [egyéni kulcsszó létrehozása a Speech Studio használatával](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
* Oktatóanyag: a [termék hangvezérelt aktiválása a SPEECH SDK-val a C# használatával](tutorial-voice-enable-your-bot-speech-sdk.md)
* Gyors útmutató: [kulcsszavak felismerése a SPEECH SDK-val univerzális Windows-platform C# használatával](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* Gyors útmutató: [kulcsszavak felismerése a SPEECH SDK-val, Androidon a Java használatával](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Következő lépések

* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](get-started.md)
* [A Speech SDK beszerzése](speech-sdk.md)
* [További információ a hangsegédekről](voice-assistants.md)
