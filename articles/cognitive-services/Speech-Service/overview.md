---
title: Mi az a Speech Service (előzetes verzió)?
description: 'A Speech service a Microsoft Cognitive Services részét képezi, és több, korábban külön elérhető Azure-beszédszolgáltatást egyesít: a Bing Speech (beszédfelismerés és szövegfelolvasás), a Custom Speech és a beszédfordítási szolgáltatást.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 922320bb0b880e933b27025257e6a533fe257680
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091472"
---
# <a name="what-is-the-speech-service"></a>Mi az a beszédszolgáltatás?

A Speech service azokat az Azure-beszédszolgáltatásokat egyesíti magában, amelyek korábban a [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/speech/home), a [Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), a [Custom Speech](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) és az [egyéni hang](http://customvoice.ai/) szolgáltatáson keresztül voltak elérhetőek. Mostantól egyetlen előfizetéssel hozzáférhet az összes funkcióhoz.

A többi Azure-beszédszolgáltatáshoz hasonlóan a Speech service hátterét is bevált beszédtechnológiák adják, amelyeket többek között a Cortana vagy a Microsoft Office is használ. Számíthat a jó minőségű eredményekre és a megbízható Azure-felhőre.

> [!NOTE]
> A Speech service jelenleg nyilvános előzetes verzióban érhető el. Ha rendszeresen visszatér ide, nyomon követheti a dokumentáció frissítéseit, az új kódmintákat és a további módosításokat.

## <a name="main-speech-service-functions"></a>A Speech service legfontosabb funkciói

A Speech service elsődleges funkciói a diktálás (más néven beszédfelismerés vagy átírás), a szövegfelolvasás (beszédszintézis) és a beszédfordítás.

|Függvény|Szolgáltatások|
|-|-|
|[Diktálás](speech-to-text.md)| <ul><li>Folyamatos, valós idejű beszédet szöveggé alakít.<li>A hangfelvételekről képes egyszerre több beszédforrást is szöveggé alakítani. <li>A szolgáltatás különféle felismerési módokat kínál az interaktív, a párbeszédes és a diktálási használati esetekhez.<li>Támogatja a köztes eredményeket, a beszéd végének észlelését, az automatikus szövegformázást és a profanitás kitakarását. <li>A [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) szolgáltatásra támaszkodva a szöveggé alakított beszédből képes kikövetkeztetni a felhasználói szándékot.\*|
|[Szövegfelolvasás](text-to-speech.md)| <ul><li>Az írott szöveget természetesnek hangzó beszéddé alakítja. <li>Számos támogatott nyelvhez elérhető több különféle nem és/vagy nyelvjárás. <li>Támogatja az egyszerű szöveges bevitelt és az SSML-t (Speech Synthesis Markup Language). |
|[Beszédfordítás](speech-translation.md)| <ul><li>Streamelt hangtartalmakat fordít közel valós időben.<li> Felvett beszédet is képes feldolgozni.<li>Az eredményeket szövegként vagy szintetizált beszédként biztosítja. |

\* *A szándékfelismeréshez LUIS-előfizetés szükséges.*


## <a name="customizing-speech-features"></a>Beszédfunkciók testreszabása

A Speech service-szel saját adatait használhatja a Speech service diktálási és szövegfelolvasási funkcióit működtető modellek betanításához. 

|Szolgáltatás|Modell|Cél|
|-|-|-|
|Diktálás|[Akusztikai modell](how-to-customize-acoustic-models.md)|Adott beszélők és környezetek (például autók vagy gyárak) átírásához használható.|
||[Nyelvi modell](how-to-customize-language-model.md)|Adott szakterülethez tartozó szókincs és nyelvtan (például orvosi vagy informatikai szakzsargon) átírásához használható.|
||[Kiejtési modell](how-to-customize-pronunciation.md)|Rövidítések és mozaikszavak átírásához (például BKK, ami „bé ká ká”). |
|Szövegfelolvasás|[Hangtípusok](how-to-customize-voice-font.md)|Saját hangot biztosíthat az alkalmazásának, ha emberi beszédből vett mintákkal tanítja be a modellt.|

Az egyszer már létrehozott, egyéni modelleket ezután bárhol felhasználhatja, ahol az alkalmazásában a diktálási vagy a szövegfelolvasási funkcióhoz a standard modelleket használná.


## <a name="using-the-speech-service"></a>A Speech service használata

A beszédalapú alkalmazások fejlesztésének megkönnyítése érdekében a Microsoft az új Speech service-hez biztosítja a [Beszéd SDK-t](speech-sdk.md). A Beszéd SDK egységes, natív diktálási és beszédfordítási API-kat biztosít a C#, a C++ és a Java programnyelvekhez. Ha Ön ezen nyelvek valamelyikével fejleszt, a Beszéd SDK megkönnyíti a fejlesztést a hálózati részletek kezelésével.

A Speech service ezen felül egy [REST API](rest-apis.md)-val is rendelkezik, amely bármely, HTTP-kéréseket küldő programnyelvvel képes együttműködni. A REST-felület azonban nem rendelkezik az SDK valós idejű, streamelési funkciójával.

|<br>Módszer|Dik-<br>tálás|Szöveg-<br>felolvasás|Beszéd-<br>fordítás|<br>Leírás|
|-|-|-|-|-|
|[Beszéd SDK](speech-sdk.md)|Igen|Nem|Igen|Fejlesztést megkönnyítő, natív API-k a C#, C++ és Java programnyelvhez.|
|[REST](rest-apis.md)|Igen|Igen|Nem|Egy egyszerű, HTTP-alapú API, amellyel könnyedén adhat beszédet alkalmazásaihoz.|

### <a name="websockets"></a>WebSocketek

A Speech service WebSocket-protokollokkal is rendelkezik a diktálás és a beszédfordítás streameléséhez. A Beszéd SDK-k ezekkel a protokollokkal kommunikálnak a Speech service-szel. A Speech service-szel a Beszéd SDK-t érdemes használni saját WebSocket-kommunikáció implementálása helyett.

Ha azonban rendelkezik olyan kóddal, amely WebSocketeken keresztül használja a Bing Speechet vagy a Translator Speechet, érdemes frissíteni a Speech service használatára. A WebSocket-protokollok kompatibilisek, csak a végpontok eltérőek.

### <a name="speech-devices-sdk"></a>Beszédeszközök SDK

A [Beszédeszközök SDK](speech-devices-sdk.md) egy integrált hardver- és szoftverplatform beszédalapú eszközök fejlesztői számára. Hardverpartnerünk referenciaterveket és fejlesztési egységeket biztosít. A Microsoft egy olyan eszközre optimalizált SDK-t nyújt, amely teljes körűen kihasználja a hardver képességeit.


## <a name="speech-scenarios"></a>Beszédforgatókönyvek

A Speech service használati esetei többek között a következők:

> [!div class="checklist"]
> * Hanggal aktiválható alkalmazások létrehozása
> * Telefonos ügyfélszolgálati felvételek átírása
> * Hangrobotok implementálása

### <a name="voice-user-interface"></a>Hangalapú felhasználói felület

A hangbevitel nagyszerű eszköz arra, hogy egy rugalmas, manuális beavatkozás nélküli, gyorsan használható alkalmazást hozzon létre. A hangalapú alkalmazásokban a felhasználók egyszerűen kérhetik a szükséges információt ahelyett, hogy meg kellene keresniük.

Ha alkalmazását a nyilvánosságnak szánja, használhatja az alapértelmezett beszédfelismerési modelleket. Ezek a modellek számos különféle beszélőt megbízhatóan felismernek a gyakori környezetekben.

Ha alkalmazását egy adott szakterületen kívánja használni (például orvostudományi vagy informatikai), létrehozhat egy [nyelvi modellt](how-to-customize-language-model.md), amellyel megtaníthatja a Speech service-t az alkalmazás által használt speciális terminológiára.

Ha alkalmazását zajos környezetben fogják használni, például gyárban, létrehozhat egy egyéni [akusztikai modellt](how-to-customize-acoustic-models.md), amellyel a Speech service könnyebben meg tudja különböztetni a beszédet a zajoktól.

Ehhez első lépésként egyszerűen töltse le a [Beszéd SDK-t](speech-sdk.md), és kövesse a hozzá tartozó [rövid útmutatót](quickstart-csharp-dotnet-windows.md).

### <a name="call-center-transcription"></a>Telefonos ügyfélszolgálati felvételek átírása

A telefonos ügyfélszolgálati felvételeket gyakran csak akkor hallgatják meg, ha probléma merül fel egy hívással kapcsolatban. A Speech service-szel minden felvétel könnyedén átírható szöveggé. A szövegeket ezután könnyedén indexelheti [teljes szöveges kereséshez](https://docs.microsoft.com/azure/search/search-what-is-azure-search), vagy [szövegelemzést](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) végezhet a hangulat, a nyelv vagy a kulcsszavak meghatározásához.

Ha a telefonos ügyfélszolgálati felvételeiben speciális terminológia fordul elő (például terméknevek vagy informatikai szakzsargon), létrehozhat egy [nyelvi modellt](how-to-customize-language-model.md), amellyel megtaníthatja a Speech service-nek ezt a szókészletet. Az [egyéni akusztikai modellel](how-to-customize-acoustic-models.md) a Speech service képes könnyebben megérteni a nem optimális telefonkapcsolatokat is.

Ha többet szeretne tudni erről a forgatókönyvről, olvassa el a részletes tájékoztatót a Speech service-szel végzett [kötegelt átírásról](batch-transcription.md).

### <a name="voice-bots"></a>Hangrobotok

A [robotok](https://dev.botframework.com/) egyre népszerűbb módot kínálnak, hogy összekössük a felhasználókat az őket érdeklő információkkal, vagy az ügyfeleket kedvenc vállalkozásaikkal. Ha egy webhelyhez vagy alkalmazáshoz hozzáad egy beszélgetésre szánt felhasználói felületet, a funkciói könnyebben megtalálhatóak és gyorsabban elérhetőek lesznek. A Speech service-szel a beszélgetés még gördülékenyebb lehet, mivel a szóbeli kérdésekre ugyanúgy szóban válaszol.

Ha egyedi személyiséget szeretne adni hangalapú robotjának (és ezzel erősíteni a márkát), saját hangot adhat neki. Az egyéni hang készítése kétlépéses folyamat. Első lépésként [készítsen felvételeket](record-custom-voice-samples.md) arról a hangról, amelyet használni szeretne. Ezután [küldje el ezeket a felvételeket](how-to-customize-voice-font.md) (a szöveges átirattal együtt) a Speech service [hangtestreszabási portálja számára](https://cris.ai/Home/CustomVoice), amely elvégzi a többit. Miután létrehozta az egyéni hangot, egyszerűen felhasználhatja az alkalmazásában.

## <a name="next-steps"></a>További lépések

Szerezzen be egy előfizetési kulcsot a Speech service-hez.

> [!div class="nextstepaction"]
> [Próbálja ki ingyenesen a Speech service-t](get-started.md)
