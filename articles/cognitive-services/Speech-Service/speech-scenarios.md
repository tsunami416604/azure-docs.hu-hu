---
title: Az Azure Cognitive Services beszédfelismerő forgatókönyvek |} Az Azure a Microsoft Docs
description: Forgatókönyvek
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 07/02/2018
ms.author: panosper
ms.openlocfilehash: 1488f95296bcc11a55a45aff56cee83b7708a789
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072214"
---
# <a name="speech-scenarios"></a>Beszédforgatókönyvek

Nincsenek számos forgatókönyv, amely képes lehetőségek technológia használatával. A leggyakoribb néhány vannak elemzése, és amelyet kiemelése a dokumentáció releváns szolgáltatások. A legtöbb, a tartalom a [SDK](speech-sdk.md) központi abban, hogy ezek a forgatókönyvek.

Az oldal ismerteti, hogyan lehet:
> [!div class="checklist"]
> * Aktivált hangvezérelt alkalmazások létrehozása
> * Hívás center hang hívások alapuló átírás
> * Hangalapú robotok

## <a name="voice-triggered-apps"></a>Hangvezérelt alkalmazások által aktivált

Hány felhasználó alkalmazásaikat a beszédbemenetet engedélyezni szeretne. Beszédbemenetet kiválóan alkalmas, hogy az alkalmazás rugalmas, legyen az azt használó ingyenes (például egy autó) a teljes vagy felgyorsítható a különböző feladatokat, köztük a megjelenő utasításokat a hírek és időjárási adatokat kéri. 

### <a name="voice-triggered-apps-with-baseline-models"></a>Hangalapú kiindulási modelleket alkalmazhatnak aktivált alkalmazások

Ha az alkalmazás által használt nyilvános környezetekben nem túl nagy, a háttérzaj, egyszerűen tölt le a legkönnyebb és leggyorsabb Ennek módja a [beszéd SDK](speech-sdk.md) és követni a megfelelő [ A minták](quickstart-csharp-dotnet-windows.md). Az SDK segítségével a [Azure-előfizetési kulcs](https://azure.microsoft.com/try/cognitive-services/) lehetővé teszi a fejlesztők számára, hogy az alapterv beszédfelismerési modelleket, melyek a Cortana és Skype hang feltöltése. A mdoels a legkorszerűbb, és a fent említett termékeket használják. Néhány perc alatt működőképes környezettel rendelkezik.

### <a name="voice-triggered-apps-with-custom-models"></a>Egyéni modellek hangalapú aktivált alkalmazások

Ha az alkalmazás-címek egy adott tartományban, (például kémia, biológiai vagy speciális előrejelzéséhez igénylő), akkor érdemes lehet érdemes figyelembe venni, hogy alkalmazkodjanak a [nyelvi modell](how-to-customize-language-model.md). Nyelvi modell gyakorlatainak hogyan a leggyakrabban használt kifejezések és az alkalmazás által használt szavakat kapcsolatban a dekóder. A dekódoló tudják a hang, adjon meg a referenciakonfiguráció modell helyett az egyes tartományokhoz tartozó egyéni nyelvi modell pontosabb lefényképezze. Hasonlóképpen neves hol fog használni az alkalmazást a háttérzaj esetén érdemes az akusztikai modell igazíthatja. Fedezze fel az olyan esetekben, amely alatt a dokumentáció [nyelvi betanítás](how-to-customize-language-model.md) és [akusztikai betanítás](how-to-customize-acoustic-models.md) adjon meg értéket, és látogasson el a [betanítás portál](https://customspeech.ai) indító figyelmezteti a a modell-létrehozási folyamatának. Hasonló a kiindulási modelleket, egyéni modellek keresztül nevezzük az [beszéd SDK](speech-sdk.md) és követni a megfelelő [minták](quickstart-csharp-dotnet-windows.md).

## <a name="transcribe-call-center-audio-calls"></a>Hívás center hang hívások alapuló átírás

Telefonos ügyfélszolgálatok hang nagy mennyiségű összeadódhatnak. Rejtett azokat az elérhető azonban beszédátírási hangfájlok webszolgáltatáson értéken belül. A hívás időtartama a róluk szóló véleményeket, az ügyfelek és az általános a hívást a hívó számára megadott érték számára könnyen megtalálhatók legyenek hívás átiratok beszerzése.

A legjobb kiindulási pontja a [Batch API beszédátírási](batch-transcription.md) együtt kapcsolódó [minta](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

Először szerezze be kell egy [Azure-előfizetési kulcs](https://azure.microsoft.com/try/cognitive-services/) és kell tekintenie a [dokumentáció]([Batch transcription API](batch-transcription.md)).

### <a name="transcribe-call-center-audio-calls-with-baseline-models"></a>Hívás center hang hívások kiindulási modelleket alkalmazhatnak alapuló átírás

A döntést kell tenni, akkor e használatával a belső kiindulási modelleket alkalmazhatnak az átírási végez, a nyelv vagy az akusztikai modell, vagy mindkettő. Alapkonfiguráció modellek használata az API-t csak az API-kulcs szükséges. Belsőleg az API meghívása a legjobb modellt az adatok és alkalmazkodáshoz.

### <a name="transcribe-call-center-audio-calls-with-custom-models"></a>Hívás center hang hívások egyéni modellek alapuló átírás

Ha azt tervezi, egyéni modellt, majd kell, hogy modelljét, és az API-kulcs azonosítója. A Modellazonosító kérhető le a [betanítás portál](https://customspeech.ai). Ez nem talált a "Végpont részletei" nézeten Hálózativégpont-azonosító, de a Modellazonosító, amely ehhez a modellhez "Részletek" kattintva kérheti.

## <a name="voice-bots"></a>Hangalapú robotok

Fejlesztői teheti hatékonyabbá a hangalapú kimeneti az alkalmazását. A beszédfelismerési szolgáltatás is számos synthetize speech [nyelvek](supported-languages.md) és biztosítja a [végpontok](rest-apis.md) elérésére, és ezt a képességet hozzáadása az alkalmazáshoz.

Emellett a felhasználók számára, hogy több személy és egyediség hozzáadásához robotjaikat, a Speech Service segítségével a fejlesztők egyedi hangtípusú testreszabásához. Hasonló beszédfelismerési modellek hangtípust igényelnek felhasználói adatok testreszabása. Fejlesztők feltöltése az adatokat a [hangalapú betanítás portál](https://customspeech.ai) , és kezdje el a márkára robotja hang. Részletes leírásuk [Itt](how-to-text-to-speech.md) , valamint a [– gyakori kérdések](faq-text-to-speech.md) oldalak 

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Első lépések a beszédfelismerő SDK](speech-sdk.md)
