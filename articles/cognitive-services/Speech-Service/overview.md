---
title: Mi az a Speech Service?
titleSuffix: Azure Cognitive Services
description: 'A Speech Service, Azure Cognitive Services részeként több beszédszolgáltatások, amely korábban elérhető volt külön-külön több egység: Bing Speech (amely magában foglalja, beszédfelismerés, és a szöveg-beszéd átalakítás), egyéni beszédfelismerési és beszédalapú fordítási.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: d60e5f881e44f397090a3ba5e467c08f20137d72
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858831"
---
# <a name="what-is-speech-services"></a>Mi az beszédszolgáltatások?

Hasonlóan a többi Azure speech services beszédszolgáltatások beszédfelismerési technológiák, például a Cortana és a Microsoft Office-termékekben használt vannak működteti.

A beszédszolgáltatások korábban keresztül elérhető Azure beszédszolgáltatások egyesítése a [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/speech/home), [Beszédfordító](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), [egyéni beszédfelismerési](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home), és [egyéni Beszédfelismerési](http://customvoice.ai/) szolgáltatások. Mostantól egyetlen előfizetéssel hozzáférhet az összes funkcióhoz.

## <a name="main-speech-services-functions"></a>Beszédszolgáltatások fő funkciók

A beszédszolgáltatások elsődleges funkciói a következők: hang-szöveg transzformációs (is nevezik beszédfelismerés vagy beszédátírási), a szöveg-hang transzformációs (beszédszintézishez) és a beszédalapú fordítási.

|Függvény|Szolgáltatások|
|-|-|
|[Beszédfelismerés](speech-to-text.md)| <li>Folyamatos, valós idejű beszédet szöveggé alakít.<li>A hangfelvételekről képes egyszerre több beszédforrást is szöveggé alakítani. <li>Támogatja a köztes eredményeket, a beszéd végének észlelését, az automatikus szövegformázást és a profanitás kitakarását. <li>A [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) szolgáltatásra támaszkodva a szöveggé alakított beszédből képes kikövetkeztetni a felhasználói szándékot.\*|
|[Text-to-Speech](text-to-speech.md)| <li>**ÚJ**: Itt Neurális szöveg-hang transzformációs beszédhangot szinte megkülönböztetni emberi speech (angol nyelven). <li>Az írott szöveget természetesnek hangzó beszéddé alakítja. <li>Számos támogatott nyelvhez elérhető több különféle nem és/vagy nyelvjárás. <li>Támogatja az egyszerű szöveges bevitelt és az SSML-t (Speech Synthesis Markup Language). |
|[Tolmácsolás](speech-translation.md)| <li>Streamelt hangtartalmakat fordít közel valós időben.<li> Felvett beszédet is képes feldolgozni.<li>Az eredményeket szövegként vagy szintetizált beszédként biztosítja. |


## <a name="customize-speech-features"></a>Beszédfunkciók testreszabása

Használhatja a saját adatait a Speech Service diktálási és szövegfelolvasási funkcióit működtető modellek betanításához.

|Funkció|Modell|Cél|
|-|-|-|
|Speech-to-text|[Akusztikai modell](how-to-customize-acoustic-models.md)|Adott beszélők és környezetek (például autók vagy gyárak) átírásához használható.|
||[Nyelvi modell](how-to-customize-language-model.md)|Adott szakterülethez tartozó szókincs és nyelvtan (például orvosi vagy informatikai szakzsargon) átírásához használható.|
||[Kiejtési modell](how-to-customize-pronunciation.md)|Rövidítések és mozaikszavak átírásához használható (például BKK, ami „bé ká ká”). |
|Szövegfelolvasás|[Hangtípusok](how-to-customize-voice-font.md)|Saját hangot biztosíthat az alkalmazásának, ha emberi beszédből vett mintákkal tanítja be a modellt.|

Az egyéni modelleket bárhol használhatja, ahol az alkalmazás a diktálási vagy a szövegfelolvasási funkcióhoz a standard modelleket használná.

## <a name="use-the-speech-service"></a>A Speech Service használata

A beszédalapú alkalmazások fejlesztésének megkönnyítése céljából a Microsoft a Speech Service-hez biztosítja a [Beszéd SDK](speech-sdk.md) használatát. A Beszéd SDK egységes, natív diktálási és beszédfordítási API-kat biztosít a C#, a C++ és a Java programnyelvekhez. Ha Ön ezen nyelvek valamelyikével fejleszt, a Beszéd SDK megkönnyíti a fejlesztést a hálózati részletek kezelésével.

A beszédfelismerési szolgáltatás is rendelkezik egy [REST API-val](rest-apis.md) , amely együttműködik minden programozási nyelvet használhat, amelyek HTTP-kérelmekre. A REST-felület nem rendelkezik az SDK valós idejű, streamelési funkciójával.

|<br>Módszer|Beszéd<br>tálás|Szöveg-<br>Beszéd|Beszéd<br>Fordítás|<br>Leírás|
|-|-|-|-|-|
|[Beszéd SDK](speech-sdk.md)|Igen|Nem|Igen|Fejlesztést megkönnyítő, natív API-k a C#, C++ és Java programnyelvhez.|
|[REST API-k](rest-apis.md)|Igen|Igen|Nem|Egy egyszerű, HTTP-alapú API, amellyel könnyedén adhat beszédet alkalmazásaihoz.|

### <a name="websockets"></a>WebSocketek

A beszédszolgáltatások is protokoly WebSocket streamelési hang-szöveg transzformációs és beszédalapú fordítási. A Beszéd SDK-k ezekkel a protokollokkal kommunikálnak a Speech service-szel. A Speech Service-szel a Beszéd SDK-t használja, és ne saját WebSocket-kommunikációt próbáljon implementálni.

Ha már rendelkezik kódokat, amelyek a Bing Speech vagy Websocketek használatával Translator Speech, frissítheti úgy, hogy a beszédszolgáltatások használja. A WebSocket protokoll kompatibilisek, azonban a végpontok különböző.

### <a name="speech-devices-sdk"></a>Beszédeszközök SDK

A [Beszédeszközök SDK](speech-devices-sdk.md) egy integrált hardver- és szoftverplatform beszédalapú eszközök fejlesztői számára. Hardverpartnerünk referenciaterveket és fejlesztési egységeket biztosít. A Microsoft egy olyan eszközre optimalizált SDK-t nyújt, amely teljes körűen kihasználja a hardver képességeit.


## <a name="speech-scenarios"></a>Beszédforgatókönyvek

Beszédszolgáltatások alkalmazási helyzeteit a következők:

> [!div class="checklist"]
> * Hanggal aktiválható alkalmazások létrehozása
> * Telefonos ügyfélszolgálati felvételek átírása
> * Hangrobotok implementálása

### <a name="voice-user-interface"></a>Hangalapú felhasználói felület

A hangbevitel nagyszerű eszköz arra, hogy egy rugalmas, manuális beavatkozás nélküli, gyorsan használható alkalmazást hozzon létre. A hangalapú alkalmazásokban a felhasználók egyszerűen rákérdezhetnek a szükséges információkra.

Ha alkalmazását a nyilvánosságnak szánja, használhatja az alapértelmezett beszédfelismerési modelleket. Ezek a modellek számos különféle beszélőt képesek felismerni a gyakori környezetekben.

Ha alkalmazását egy adott szakterületen kívánja használni (például orvostudományi vagy informatikai területen), létrehozhat egy [nyelvi modellt](how-to-customize-language-model.md). Ez a modell, akik az információ a speciális, az alkalmazás által használt terminológia beszédszolgáltatások használhatja.

Ha alkalmazását zajos környezetben, például gyárban fogják használni, létrehozhat egy egyéni [akusztikai modellt](how-to-customize-acoustic-models.md). Ez a modell segítségével az beszédszolgáltatások beszédfelismerési megkülönböztetni a háttérzaj.

### <a name="call-center-transcription"></a>Telefonos ügyfélszolgálati felvételek átírása

A telefonos ügyfélszolgálati felvételeket gyakran csak akkor hallgatják meg, ha probléma merül fel egy hívással kapcsolatban. A Speech service-szel minden felvétel könnyedén átírható szöveggé. A szöveget ezután könnyedén indexelheti [teljes szöveges kereséshez](https://docs.microsoft.com/azure/search/search-what-is-azure-search), vagy [szövegelemzést](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) végezhet a hangulat, a nyelv vagy a kulcsszavak meghatározásához.

Ha a hívás center felvételek olyan speciális terminológiát, például termékneveket vagy informatikai beszédben, létrehozhat egy [nyelvi modell](how-to-customize-language-model.md) , akik a beszédszolgáltatások az a szöveg szóhasználati. Egyéni [akusztikai modell](how-to-customize-acoustic-models.md) is segítséget a beszédszolgáltatások kevésbé optimális telefonos kapcsolatok ismertetése.

Ha többet szeretne tudni erről a forgatókönyvről, olvassa el a részletes tájékoztatót a Speech service-szel végzett [kötegelt átírásról](batch-transcription.md).

### <a name="voice-bots"></a>Hangrobotok

A [robotok](https://dev.botframework.com/) népszerű módot kínálnak a felhasználók és az őket érdeklő információk, vagy az ügyfelek és kedvenc vállalkozásaik összekötésére. Ha a webhelyét vagy alkalmazását beszélgetésre szánt felhasználói felülettel egészíti ki, a funkciói könnyebben megtalálhatóak és gyorsabban elérhetőek lesznek. A Speech service-szel a beszélgetés még gördülékenyebb lehet, mivel a szóbeli kérdésekre ugyanúgy szóban válaszol.

Ha egyedi személyiséget szeretne adni hangalapú robotjának, saját hangot adhat neki. Az egyéni hang készítése kétlépéses folyamat. Első lépésként [készítsen felvételeket](record-custom-voice-samples.md) arról a hangról, amelyet használni szeretne. Ezután [küldje el ezeket a felvételeket](how-to-customize-voice-font.md) (a szöveges átirattal együtt) a Speech Service [hangtestreszabási portáljára](https://cris.ai/Home/CustomVoice), amely elvégzi a többit. Miután létrehozta az egyéni hangot, egyszerűen használhatja az alkalmazásában.

## <a name="next-steps"></a>További lépések

Szerezze be a beszédszolgáltatások egy előfizetési kulcsot.

> [!div class="nextstepaction"]
> [A Speech Services ingyenes kipróbálása](get-started.md)
