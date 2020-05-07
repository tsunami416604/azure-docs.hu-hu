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
ms.openlocfilehash: dd5748cf8afe19a49e5ea406aea9b558432eeaf3
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82802180"
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

## <a name="sample-code"></a>Mintakód

Egy kulcsszó modell integrálására szolgáló mintakód elérhető a GitHubon. Ezek a minták a kulcsszó több népszerű programozási nyelven való integrálására szolgáló ügyfélalkalmazás.

* [Oktatóanyag: hang – aktiválja a terméket a Speech SDK-val, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Hangsegéd-minták (SDK)](https://aka.ms/csspeech/samples)

## <a name="tutorial"></a>Oktatóanyag

* [Egyéni kulcsszó létrehozása a Speech Studio használatával](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
* A [termék hangvezérelt aktiválása a SPEECH SDK használatával](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="reference-docs"></a>Dokumentációs dokumentumok

* [Egyéni kulcsszó elnevezési irányelvei](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-kws-guidelines)
* [Beszéd SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>További lépések

* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](get-started.md)
* [Egyéni kulcsszó létrehozása](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
* [A Speech SDK beszerzése](speech-sdk.md)
* [További információ a hangsegédekről](voice-assistants.md)
