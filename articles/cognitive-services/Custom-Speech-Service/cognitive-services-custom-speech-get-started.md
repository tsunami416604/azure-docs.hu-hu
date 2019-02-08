---
title: A Custom Speech Service az Azure-ban – első lépések |} A Microsoft Docs
description: Fizessen elő a Custom Speech service és a szolgáltatás tevékenységek összekapcsolása a modell betanítását és üzembe helyezést az Azure-előfizetés.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: nitinme
ms.openlocfilehash: 66aa2768960ee99184ab12070c78c2148c297b2d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878527"
---
# <a name="get-started-with-custom-speech-service"></a>Ismerkedés a Custom Speech Service

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Ismerkedés a Custom Speech Service fő funkciói, és megtudhatja, hogyan hozhatók létre, telepíthetők és akusztikai és nyelvi modellek használata az alkalmazásnak szüksége van. Több széles körű dokumentációval és részletes útmutatás található a Custom Speech Services portálon a regisztrációt követően.

## <a name="samples"></a>Példák  
Egy jó mintáját találkozása, amelyek az általunk biztosított [Itt](https://github.com/Microsoft/Cognitive-Custom-Speech-Service).

## <a name="prerequisites"></a>Előfeltételek  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>Előfizetés a Custom Speech Service és az előfizetési kulcs lekérése
A fentiekben lejátszás előtt a példában a kell előfizetni a Custom Speech Service és lekérése egy előfizetést, tekintse meg [előfizetések](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech) , vagy kövesse a magyarázatokat [Itt](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md). Ehhez az oktatóanyaghoz az elsődleges és a másodlagos kulcs is használható. Az API-kulcs biztonságban és titokban tartásához mindenképpen kövesse az ajánlott eljárásokat.

### <a name="get-the-client-library-and-example"></a>Az ügyfélkódtár és a példa lekérése
Előfordulhat, hogy töltse le a egy ügyféloldali kódtár és egy példa keresztül [SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk). A letöltött zip-fájlt ki kell csomagolni egy tetszőleges mappába. Számos felhasználó a Visual Studio 2015 mappát választja.

## <a name="creating-a-custom-acoustic-model"></a>Egy egyéni akusztikai modell létrehozása
Egy adott tartományhoz tartozó akusztikai modell testreszabásához beszédadatok gyűjteményére van szükség. A gyűjtemény beszédadatokat tartalmazó hangfájlokból, valamint az ezek mindegyikének szöveges átiratát tartalmazó szövegfájlból áll. A hívásaiból kell modelleznie, amelyben a felismerő használni kívánt forgatókönyv

Példa: Ha jobb beszédfelismerést szeretne elérni egy zajos gyári környezetben, a hangfájloknak zajos gyárban felvett beszédmintákat kell tartalmazniuk.
Önt érdeklő egyetlen beszélő teljesítményének optimalizálása, például szeretné lefényképezze összes FDR a Fireside csevegés, akkor a példákat, hogy a beszélő csak hangfájlt kell állnia.

Egy egyéni akusztikai modell létrehozása a részletes leírást talál [Itt](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md).

## <a name="creating-a-custom-language-model"></a>Egyéni nyelvi modell létrehozása
Az eljárás egyéni nyelvi modell létrehozásához nagyon hasonlít az akusztikai modell, azzal a különbséggel nem szerepel megjeleníthető hang adat, csak szöveg. A szöveg állhat számos példa lekérdezéseket vagy utterances várt tegyük fel, hogy a felhasználók vagy a felhasználók bejelentkezés az alkalmazás közli, hogy (vagy írja be).

Egyéni nyelvi modell létrehozásáról részletes leírást talál [Itt](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md).

## <a name="creating-a-custom-speech-to-text-endpoint"></a>Egy egyéni hang-szöveg transzformációs-végpont létrehozása
Testreszabott akusztikai modell és/vagy nyelvi modellek létrehozása után, akkor a hang-szöveg transzformációs egyéni végpontok is telepíthető. Új egyéni végpont létrehozásához kattintson a "Központi" elemre a lap tetején a "CRIS" menüből. Ekkor megjelenik egy "Telepítés" aktuális egyéni végpontok nevű táblát. Ha még nem hozott olyan végpontok, a tábla üres lesz. A tábla címe az aktuális területi beállítást mutatja. Ha szeretné más nyelven üzembe helyezése, kattintson a "Change területi beállítása". További információ a támogatott nyelvek módosítása területi beállítás című szakaszban található.

Egy egyéni hang-szöveg transzformációs végpont létrehozása a részletes leírást talál [Itt](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md).

## <a name="using-a-custom-speech-endpoint"></a>Egy egyéni beszéd-végpont használatával
Kérelmek lehet küldeni a CRIS hang-szöveg transzformációs végpont nagyon hasonló módon az alapértelmezett Microsoft Cognitive Services beszédfelismerési végpontként. Vegye figyelembe, hogy ezeket a végpontokat az alapértelmezett végpontok a beszédfelismerő API funkcionálisan azonos. Így az ügyféloldali kódtár vagy a beszédfelismerő API REST API-val keresztül elérhető ugyanazokat a funkciókat is a érhető el az egyéni végpont.

Részletes leírást talál a hang-szöveg transzformációs egyéni végpont használatát [Itt](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md).


Vegye figyelembe, hogy a CRIS segítségével létrehozott végpontok a csomagtól függően egyidejű kérelmek, az előfizetés társítva a különböző számú tud feldolgozni. Ha több, mint a felismerés kérnek, a 429-es hibakód visszaadja (túl sok kérés). További információt a [díjszabási információk](https://www.microsoft.com/cognitive-services/en-us/pricing). Emellett nincs kérések az ingyenes egy havi kvótáját. Azokban az esetekben érhetők el az ingyenes szint feletti havi kvótájának a szolgáltatás az a végpont adja vissza a 403-as hibakód tiltott.

A szolgáltatás feltételezi, hogy a valós idejű hang továbbított. Ha gyorsabban továbbítja, a kérelem akkor minősül, amíg a valós idejű időtartam letelte.

* [Áttekintés](cognitive-services-custom-speech-home.md)
* [Gyakori kérdések](cognitive-services-custom-speech-faq.md)
* [Szószedet](cognitive-services-custom-speech-glossary.md)
