---
title: Mi az a Speech service (előzetes verzió)? | Microsoft Docs
description: 'A Speech service, a Microsoft Cognitive Services részeként több egység számos Azure beszédszolgáltatások külön-külön korábban elérhető volt: (amely magában foglalja, beszédfelismerés, és a szöveg-beszéd átalakítás), a Bing Speech, egyéni beszédfelismerési és beszédalapú fordítási.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: ccdcdeeaf4ac8730be4f9e3ee648dc41c2a02641
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345158"
---
# <a name="what-is-the-speech-service-preview"></a>Mi az a Speech service (előzetes verzió)?

A Speech service, a Microsoft Cognitive Services részeként több egység számos Azure beszédszolgáltatások külön-külön korábban elérhető volt: (amely magában foglalja, beszédfelismerés, és a szöveg-beszéd átalakítás), a Bing Speech, egyéni beszédfelismerési és beszédalapú fordítási. Prekurzorainak, például a beszédfelismerési szolgáltatás működteti más Microsoft-termékek, köztük a Cortana és a Microsoft Office technológiák.

> [!NOTE]
> A beszédfelismerési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. Térjen vissza ide rendszeresen frissítéseket dokumentáció, Kódminták további és egyéb.

Egy előfizetéssel rendelkező az egyesített beszédszolgáltatás biztosítja a fejlesztők számára egyszerű módszert biztosítanak az alkalmazások hatékony beszédfeldolgozó funkcióit. Az alkalmazások a is most Hangvezérlés, beszédátírási, a Diktálás, beszédszintézishez és fordítási szolgáltatás.

|Függvény|Leírás|
|-|-|
|Diktálás|Folyamatos emberi speech alakítja a szöveg, amely az alkalmazás bemeneteként használható. Integrálható a [hangfelismerési szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) felhasználói szándékot származtassa kimondott szöveg.|
|Szövegfelolvasás|Hangfájlok természetes hangzó szintetizált beszéd szöveges alakítja.|
|Beszéd&nbsp;fordítása|Adja meg a fordítása más nyelvekre, beszédfelismerés, a szöveg és beszéd eredményéről.|

## <a name="using-the-speech-service"></a>A Speech szolgáltatással

A Speech service kétféle módon elérhetővé. [Az SDK](speech-sdk.md) kódbázis hálózati protokollok részletei. A [REST API-val](rest-apis.md) minden programozási nyelvet együttműködik, de nem vonatkozik az SDK által nyújtott összes funkció áttekintésével.

|<br>Módszer|Beszéd<br>a szöveg|A szöveg<br>Beszéd|Beszéd<br>Fordítás|<br>Leírás|
|-|-|-|-|-|
|[SDK-k](speech-sdk.md)|Igen|Nem|Igen|Szalagtárak adott programozási nyelvet, amely leegyszerűsítheti a fejlesztést.|
|[REST](rest-apis.md)|Igen|Igen|Nem|Egy egyszerű HTTP-alapú API, amely megkönnyíti a speech hozzáadása az alkalmazáshoz.|

## <a name="speech-to-text"></a>Diktálás

A [Speech to Text](speech-to-text.md) (STT), vagy a beszédfelismerés, API-t transcribes audiostreamek lejátszásával, a szöveg, amely bemenetként az alkalmazás fogadhat. Az alkalmazás ezután például beírhatja a szöveget egy dokumentumba vagy parancsként alkalmazhatja.

Beszédfelismerés külön-külön optimalizált interaktív, a beszélgetésnél, és a Diktálás forgatókönyvek. Az alábbiakban gyakori alkalmazási esetei a Speech to Text API. 

* Ismeri fel az utterance (kifejezés egy rövid), például egy parancsot, közbenső eredmények nélkül
* Egy hosszú, a korábban rögzített utterance (kifejezés), például egy hangposta-üzenet alapuló átírás
* A streamelési speech lefényképezze valós idejű, a részleges eredményeket, a Diktálás
* Határozza meg a felhasználók igényeit ehhez használja a beszélt természetes nyelvi kérés alapján

A Speech to Text API támogatja az interaktív lejegyzés valós idejű folyamatos felismerése és a köztes eredményeket. Szintén támogatja a beszéd végének észlelését, választható lehetőségként a kis- és nagybetűk közötti automatikus váltást és központozást, a profanitás kitakarását és a szövegnormalizálást.

Testre szabható a Speech to Text akusztikai és nyelvi modelleket speciális szószedet, zajos környezetben és különféle különböző módszert is.

## <a name="text-to-speech"></a>Szövegfelolvasás

A [szöveg-beszéd átalakítás](text-to-speech.md) (Szövegfelolvasás), vagy a beszédszintézishez, API-t konvertálja egyszerű szöveges természetes hangzó beszéddé, az alkalmazás a hangfájl lett elküldve. Több beszédhangot nemek vagy hangsúlyos, különböző számos nyelven érhetők el.

Az API támogatja a Speech összefoglaló Markup Language (SSML) címkéket, így megadhatja, hogy pontos nevének fonetikus írásmódja feladatátvételnek szavak. Az SSML emellett a beszéd jellemzőit (beleértve a hangsúlyt, a sebességet, a hangerőt, a beszélő nemét és a hangmagasságot) közvetlenül a szövegben is képes jelölni.

Az alábbiakban gyakori alkalmazási esetei a Text to Speech API.

* Beszéd kimenet egy másik képernyő kimenetként a gyengén látó felhasználók számára
* Hangalapú kérő üzenet autós navigációs alkalmazásokhoz
* Természetes nyelvi felhasználói felületet a Speech to Text API-sel

Ha egy nem támogatott dialect kell, vagy csak szeretné, az alkalmazás egyéni, a Text to Speech API támogatja a egyéni beszédfelismerési modelleket.

## <a name="speech-translation"></a>Beszédfordítás

A [beszédalapú fordítási](speech-translation.md) lefordítani a közel valós idejű streamelési hang vagy feldolgozni a rögzített beszédfelismerő API használható. Streamelési fordítását, a szolgáltatás ideiglenes fordítási folyamat előrehaladását jelzik, hogy a felhasználó számára megjelenő eredményeket adja vissza. Az eredmények szövegként vagy hangként is visszaadhatók.

A következők: beszédalapú fordítási alkalmazási helyzetei

* A utazók "természetes nyelvi" fordítási mobilalkalmazással vagy eszköz megvalósítása 
* Automatikus fordítások a feliratozás, hang- és videofelvételeket

## <a name="speech-devices-sdk"></a>Beszédeszközök SDK

Az egyesített beszédszolgáltatás bevezetésével a Microsoft és partnerei ajánlat egy beszéd-kompatibilis eszközök fejlesztésének optimalizált integrált hardver/szoftver platform: a [Speech Devices SDK-val](speech-devices-sdk.md). Ez az SDK alkalmas intelligens beszédeszközök fejlesztésére az összes alkalmazástípushoz.

A beszéd eszközök SDK lehetővé teszi, a saját testre szabott ébresztési szó, a környezeti eszközök felépítését úgy, hogy a köteg, amely elindítja a rendszerhang rögzítése a márka egyedi. Az SDK továbbá kiváló audiofeldolgozást is biztosít többcsatornás forrásokból a pontosabb beszédfelismerés érdekében, amely magában foglalja például a zajszűrést, a távoli hangot és a nyalábformálást.

Az SDK-t a 443-as portot használja a websockets alapul.

## <a name="next-steps"></a>További lépések

A beszédfelismerési szolgáltatás ingyenes próba-előfizetését kulcs lekérése.

> [!div class="nextstepaction"]
> [A beszédfelismerési szolgáltatás ingyenes kipróbálása](get-started.md)
