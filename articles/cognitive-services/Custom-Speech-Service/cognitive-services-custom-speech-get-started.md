---
title: Első lépések az Azure egyéni beszéd szolgáltatással |} Microsoft Docs
description: Az egyéni beszéd előfizet, majd kapcsolja a szolgáltatás Azure-előfizetés a modellek betanítása és telepítésére.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: fe7a140f5ba2d712014f03663a88d516958d188e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347114"
---
# <a name="get-started-with-custom-speech-service"></a>Az egyéni beszéd Service

Az egyéni beszéd szolgáltatás fő funkcióinak vizsgálatát, és megtudhatja, hogyan létrehozásához, telepítéséhez és akusztikus és nyelvi modellek használata az alkalmazás igényeinek. További részletes dokumentációt és részletes útmutatás található az egyéni beszéd szolgáltatások Portal a regisztrációt követően.

## <a name="samples"></a>Példák  
Egy jó mintáját, az első is, amelyek az általunk biztosított [Itt](https://github.com/Microsoft/Cognitive-Custom-Speech-Service).

## <a name="prerequisites"></a>Előfeltételek  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>Egyéni beszéd előfizet és előfizetés-kulcs beszerzése
Előtt játszik és a fenti példában kell egyéni beszéd előfizet és lekérése egy előfizetés, tekintse meg [előfizetések](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech) vagy hajtsa végre a magyarázatokat [Itt](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md). Ebben az oktatóanyagban mind az elsődleges és másodlagos kulcsot is használható. Ügyeljen arra, hogy kövesse a bevált gyakorlatokat, hogy az API-kulcs titkos kulcsot, és biztonságáról.

### <a name="get-the-client-library-and-example"></a>Az ügyfél első könyvtár és példa
Egy ügyféloldali kódtár és példa segítségével letöltheti [SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk). A letöltött zip-fájl szükséges a kiválasztott mappába kinyerni, sok felhasználó válassza ki a Visual Studio 2015 mappát.

## <a name="creating-a-custom-acoustic-model"></a>Egy egyéni akusztikus modell létrehozása
Egy adott tartományhoz akusztikus modell testreszabásához beszéd adatok gyűjteménye szükség. A gyűjtemény a beszédfelismerés adatok hang fájlokat, és hang fájlnévkiterjesztésének transcriptions szövegfájlba áll. A hangadatok kell modelleznie, ahol szeretné használni a felismerő forgatókönyv

Példa: Ha szeretné jobban ismeri fel a beszédfelismerés zajos gyári környezetben, a hangfájlok zajos gyárból beszéd személyek kell állnia.
Ha érdekli az egyetlen hangalapú teljesítményének optimalizálása, pl. szeretné átírni összes FDR tartozó Fireside csevegés, majd a hangfájlok csak adott hangalapú számos példát kell állnia.

Egy egyéni akusztikus modell létrehozása a részletes leírást talál [Itt](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md).

## <a name="creating-a-custom-language-model"></a>Egy egyéni nyelvi modell létrehozása
Az eljárás olyan egyéni nyelvi modell létrehozásához egy akusztikus modell létrehozása kivételével nincs elérhető hang adat, csak szöveg hasonlít. A szöveges lekérdezések számos példát kell állnia, vagy mondja ki a felhasználók utterances várt felhasználók vannak bejelentkezve, vagy az alkalmazás arról (vagy írja be).

Egy egyéni nyelvi modell létrehozása a részletes leírást talál [Itt](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md).

## <a name="creating-a-custom-speech-to-text-endpoint"></a>Egy egyéni beszéd-szöveg-végpont létrehozása
Egyéni akusztikus modellek és/vagy nyelvi modellek létrehozása után, akkor egy egyéni beszéd-szöveg végpont is telepíthető. Új egyéni végpont létrehozásához kattintson a "Központi" a "CRIS" menüben, az oldal tetején. Ezzel megnyitná a jelenlegi egyéni végpontok "központi" nevű tábla. Ha még nem hozott létre végpontok, a táblázat üres lesz. Az aktuális területi beállítása a táblázat címsorában megjelenik. Ha azt szeretné, más nyelven telepítést hozhat létre, kattintson a "Módosítás területi beállítás". További információ a támogatott nyelvek módosítása területi című szakaszban található.

Részletes leírás található egyéni beszéd-szöveg-végpont létrehozása [Itt](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md).

## <a name="using-a-custom-speech-endpoint"></a>Egy egyéni beszéd végpont használatával
Kérelmek lehet küldeni egy CRIS beszéd-szöveg végpont nagyon hasonló módon az alapértelmezett Microsoft kognitív szolgáltatások beszéd végpontként. Ne feledje, hogy ezeket a végpontokat a hang transzformációs API az alapértelmezett végpontok gyakorlatilag azonos. Ebből kifolyólag a ügyféloldali kódtár vagy a hang transzformációs API REST API-n keresztül elérhető ugyanezeket a funkciókat érhető el a a egyéni végponthoz.

Részletes leírást talál egy egyéni beszéd-szöveg endpoint használatáról [Itt](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md).


Vegye figyelembe, hogy a végpontok CRIS keresztül képes-e a különböző számú egyidejű kérelmek attól függően, hogy a réteg az előfizetés társítva. Ha további elfpgads, mint a kért, hibakód: 429 visszaadható (túl sok kérelem). További információt talál a [árakról](https://www.microsoft.com/cognitive-services/en-us/pricing). Emellett nincs a havi kvóta az ingyenes szint kérelmek. Azokban az esetekben, hozzáférhet a végpont felett a havi kvóta a szolgáltatás ingyenes szint adja vissza a 403-as hibakód tiltott.

A szolgáltatás azt feltételezi, hogy a valós idejű átkerülnek a hang. Ha gyorsabb továbbítja, a kérelem akkor veszi figyelembe működését, amíg a valós idejű időtartama megfelelt.

* [Áttekintés](cognitive-services-custom-speech-home.md)
* [GYIK](cognitive-services-custom-speech-faq.md)
* [Szószedet](cognitive-services-custom-speech-glossary.md)
