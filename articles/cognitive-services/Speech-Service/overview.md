---
title: Mi az a beszédszolgáltatás?
description: 'A Speech service a Microsoft Cognitive Services részét képezi, és több, korábban külön elérhető Azure-beszédszolgáltatást egyesít: a Bing Speech (beszédfelismerés és szövegfelolvasás), a Custom Speech és a beszédfordítási szolgáltatást.'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 09/24/2018
ms.author: v-jerkin
ms.openlocfilehash: b014144dd819aa354596175068bfb2c5905a4377
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857992"
---
# <a name="what-is-the-speech-service"></a>Mi az a beszédszolgáltatás?


A többi Azure-beszédszolgáltatáshoz hasonlóan a Speech service hátterét is azok a beszédtechnológiák adják, amelyeket többek között a Cortana vagy a Microsoft Office is használ.

A Speech service azokat az Azure-beszédszolgáltatásokat egyesíti magában, amelyek korábban a [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/speech/home), a [Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), a [Custom Speech](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) és az [egyéni hang](http://customvoice.ai/) szolgáltatáson keresztül voltak elérhetőek. Mostantól egyetlen előfizetéssel hozzáférhet az összes funkcióhoz.

## <a name="main-speech-service-functions"></a>A Speech service legfontosabb funkciói

A Speech service elsődleges funkciói a diktálás (más néven beszédfelismerés vagy átírás), a szövegfelolvasás (beszédszintézis) és a beszédfordítás.

|Függvény|Szolgáltatások|
|-|-|
|[Diktálás](speech-to-text.md)| <ul><li>Folyamatos, valós idejű beszédet szöveggé alakít.<li>A hangfelvételekről képes egyszerre több beszédforrást is szöveggé alakítani. <li>Támogatja a köztes eredményeket, a beszéd végének észlelését, az automatikus szövegformázást és a profanitás kitakarását. <li>A [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) szolgáltatásra támaszkodva a szöveggé alakított beszédből képes kikövetkeztetni a felhasználói szándékot.\*|
|[Szövegfelolvasás](text-to-speech.md)| <ul><li>Az írott szöveget természetesnek hangzó beszéddé alakítja. <li>Számos támogatott nyelvhez elérhető több különféle nem és/vagy nyelvjárás. <li>Támogatja az egyszerű szöveges bevitelt és az SSML-t (Speech Synthesis Markup Language). |
|[Beszédfordítás](speech-translation.md)| <ul><li>Streamelt hangtartalmakat fordít közel valós időben.<li> Felvett beszédet is képes feldolgozni.<li>Az eredményeket szövegként vagy szintetizált beszédként biztosítja. |

\* *A szándékfelismeréshez LUIS-előfizetés szükséges.*

## <a name="customize-speech-features"></a>Beszédfunkciók testreszabása

Használhatja a saját adatait a Speech Service diktálási és szövegfelolvasási funkcióit működtető modellek betanításához.

|Szolgáltatás|Modell|Cél|
|-|-|-|
|Diktálás|[Akusztikai modell](how-to-customize-acoustic-models.md)|Adott beszélők és környezetek (például autók vagy gyárak) átírásához használható.|
||[Nyelvi modell](how-to-customize-language-model.md)|Adott szakterülethez tartozó szókincs és nyelvtan (például orvosi vagy informatikai szakzsargon) átírásához használható.|
||[Kiejtési modell](how-to-customize-pronunciation.md)|Rövidítések és mozaikszavak átírásához használható (például BKK, ami „bé ká ká”). |
|Szövegfelolvasás|[Hangtípusok](how-to-customize-voice-font.md)|Saját hangot biztosíthat az alkalmazásának, ha emberi beszédből vett mintákkal tanítja be a modellt.|

Az egyéni modelleket bárhol használhatja, ahol az alkalmazás a diktálási vagy a szövegfelolvasási funkcióhoz a standard modelleket használná.

## <a name="use-the-speech-service"></a>A Speech Service használata

A beszédalapú alkalmazások fejlesztésének megkönnyítése céljából a Microsoft a Speech Service-hez biztosítja a [Beszéd SDK](speech-sdk.md) használatát. A Beszéd SDK egységes, natív diktálási és beszédfordítási API-kat biztosít a C#, a C++ és a Java programnyelvekhez. Ha Ön ezen nyelvek valamelyikével fejleszt, a Beszéd SDK megkönnyíti a fejlesztést a hálózati részletek kezelésével.

A Speech service ezen felül egy [REST API](rest-apis.md)-val is rendelkezik, amely bármely, HTTP-kéréseket küldő programnyelvvel képes együttműködni. A REST-felület nem rendelkezik az SDK valós idejű, streamelési funkciójával.

|<br>Módszer|Beszéd<br>tálás|Szöveg-<br>Beszéd|Beszéd<br>Fordítás|<br>Leírás|
|-|-|-|-|-|
|[Beszéd SDK](speech-sdk.md)|Igen|Nem|Igen|Fejlesztést megkönnyítő, natív API-k a C#, C++ és Java programnyelvhez.|
|[REST](rest-apis.md)|Igen|Igen|Nem|Egy egyszerű, HTTP-alapú API, amellyel könnyedén adhat beszédet alkalmazásaihoz.|

### <a name="websockets"></a>WebSocketek

A Speech Service WebSocket-protokollokkal is rendelkezik a diktálás és a beszédfordítás streameléséhez. A Beszéd SDK-k ezekkel a protokollokkal kommunikálnak a Speech service-szel. A Speech Service-szel a Beszéd SDK-t használja, és ne saját WebSocket-kommunikációt próbáljon implementálni.

Ha azonban rendelkezik olyan kóddal, amely WebSocketeken keresztül használja a Bing Speechet vagy a Translator Speechet, frissítheti azt a Speech Service használatára. A WebSocket-protokollok kompatibilisek, csak a végpontok eltérőek.

### <a name="speech-devices-sdk"></a>Beszédeszközök SDK

A [Beszédeszközök SDK](speech-devices-sdk.md) egy integrált hardver- és szoftverplatform beszédalapú eszközök fejlesztői számára. Hardverpartnerünk referenciaterveket és fejlesztési egységeket biztosít. A Microsoft egy olyan eszközre optimalizált SDK-t nyújt, amely teljes körűen kihasználja a hardver képességeit.

## <a name="why-move-to-the-speech-service"></a>Miért érdemes áttérni a beszédfelismerési szolgáltatásra?

A beszédfelismerési szolgáltatás biztosítja a Bing Speech API és három további Azure beszédfelismerési szolgáltatás (Custom Speech, Custom Voice és Translator Speech) összes funkcióját és még sok más lehetőséget. E szolgáltatások használóinak javasoljuk a beszédfelismerési szolgáltatásra való áttérést.

A beszédfelismerési szolgáltatás számos frissítést kínál ezekhez a szolgáltatásokhoz, többek között a következőket:

* Pontosabb beszédfelismerés. A szolgáltatásban használt modelleket rendszeresen fejlesztjük.

* Jobban skálázható. A szolgáltatás alkalmasabb több egyidejű kérés kezelésére, ami csökkenti a késéseket.

* A beszédfelismerési szolgáltatás időalapú díjszabási modellt használ. Részletek: [A Speech Service díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

* A beszédfelismerési szolgáltatás világszerte [több régióban](regions.md) is elérhető az ügyfelek igényeinek megfelelően. Az alkalmazása által használt minden régió esetében rendelkeznie kell [Azure-előfizetéssel](https://docs.microsoft.com/azure/cognitive-services/welcome).

* Egyetlen beszédszolgáltatási előfizetési kulcs a következő funkciókhoz biztosít hozzáférést. Mindegyik funkció forgalmi díját külön állapítjuk meg, így csak a használt funkciókért kell fizetnie.

    * [Beszédfelismerés](speech-to-text.md)
    * [Egyéni beszédfelismerés](https://cris.ai/CustomSpeech)
    * [Szövegfelolvasás](text-to-speech.md)
    * [Egyéni szövegfelolvasási hangok](https://cris.ai/CustomVoice)
    * [Tolmácsolás](speech-translation.md) (nem tartalmaz [szövegfordítást](https://docs.microsoft.com/azure/cognitive-services/translator/translator-info-overview))

* A Speech Service beszédfelismerési funkció a beszélő szándékának felismeréséhez integrálható a [Language Understanding szolgáltatással](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS). A Speech szolgáltatással LUIS végponti kulcs is használható. Részleteket a [szándékfelismerési oktatóanyag](how-to-recognize-intents-from-speech-csharp.md) tartalmaz.

* A beszédfelismerés használatához ezentúl nem kell megadnia a felismerési módot.

* A beszédfelismerési szolgáltatás támogatja a 24 KHz-es beszédhangot a szöveg beszéddé alakításához, ami javítja a hangminőséget. A jelen dokumentum írásakor két ilyen beszédhang áll rendelkezésre (csak amerikai angol) : `Jessa24kRUS` és `Guy24kRUS`.

* A beszédfelismerési szolgáltatás [kötegelt átírási](batch-transcription.md) funkciójával nagy mennyiségű felvett beszédet, például telefonos ügyfélszolgálati felvételeket, lehet hatékonyan szöveggé alakítani, azok könnyen elemezhetők és kereshetők.

* A Beszéd SDK használatakor a hang szöveggé alakításának nincs streamelési időkorlátja.

* A [Beszéd SDK](speech-sdk.md) a beszédfelismerési szolgáltatáshoz egységes API-t biztosít számos programozási nyelven és végrehajtási környezetben (többek között Windows 10, UWP és .NET Core), ami különösen több platform használatakor megkönnyíti a fejlesztést.

* A beszédfelismerési szolgáltatás kompatibilis a más Azure beszédfelismerési szolgáltatások által használt REST API-kkal és WebSockets protokollokkal, ami megkönnyíti a meglévő ügyfélalkalmazások beszédfelismerési szolgáltatásba való migrálását.

## <a name="speech-scenarios"></a>Beszédforgatókönyvek

A Speech service használati esetei többek között a következők:

> [!div class="checklist"]
> * Hanggal aktiválható alkalmazások létrehozása
> * Telefonos ügyfélszolgálati felvételek átírása
> * Hangrobotok implementálása

### <a name="voice-user-interface"></a>Hangalapú felhasználói felület

A hangbevitel nagyszerű eszköz arra, hogy egy rugalmas, manuális beavatkozás nélküli, gyorsan használható alkalmazást hozzon létre. A hangalapú alkalmazásokban a felhasználók egyszerűen rákérdezhetnek a szükséges információkra.

Ha alkalmazását a nyilvánosságnak szánja, használhatja az alapértelmezett beszédfelismerési modelleket. Ezek a modellek számos különféle beszélőt képesek felismerni a gyakori környezetekben.

Ha alkalmazását egy adott szakterületen kívánja használni (például orvostudományi vagy informatikai területen), létrehozhat egy [nyelvi modellt](how-to-customize-language-model.md). A modell használatával megtaníthatja a Speech Service-t az alkalmazás által használt speciális terminológiára.

Ha alkalmazását zajos környezetben, például gyárban fogják használni, létrehozhat egy egyéni [akusztikai modellt](how-to-customize-acoustic-models.md). A modell segítségével a Speech Service könnyebben meg tudja különböztetni a beszédet a zajoktól.

Egyszerűen használatba vehető. Csak töltse le a [Beszéd SDK-t](speech-sdk.md), és kövesse a hozzá tartozó [rövid útmutatót](quickstart-csharp-dotnet-windows.md).

### <a name="call-center-transcription"></a>Telefonos ügyfélszolgálati felvételek átírása

A telefonos ügyfélszolgálati felvételeket gyakran csak akkor hallgatják meg, ha probléma merül fel egy hívással kapcsolatban. A Speech service-szel minden felvétel könnyedén átírható szöveggé. A szöveget ezután könnyedén indexelheti [teljes szöveges kereséshez](https://docs.microsoft.com/azure/search/search-what-is-azure-search), vagy [szövegelemzést](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) végezhet a hangulat, a nyelv vagy a kulcsszavak meghatározásához.

Ha a telefonos ügyfélszolgálati felvételeiben speciális terminológia is előfordul (például terméknevek vagy informatikai szakzsargon), létrehozhat egy [nyelvi modellt](how-to-customize-language-model.md), amellyel megtaníthatja a Speech Service-nek ezt a szókészletet. Az [egyéni akusztikai modellel](how-to-customize-acoustic-models.md) a Speech service képes könnyebben megérteni a nem optimális telefonkapcsolatokat is.

Ha többet szeretne tudni erről a forgatókönyvről, olvassa el a részletes tájékoztatót a Speech service-szel végzett [kötegelt átírásról](batch-transcription.md).

### <a name="voice-bots"></a>Hangrobotok

A [robotok](https://dev.botframework.com/) népszerű módot kínálnak a felhasználók és az őket érdeklő információk, vagy az ügyfelek és kedvenc vállalkozásaik összekötésére. Ha a webhelyét vagy alkalmazását beszélgetésre szánt felhasználói felülettel egészíti ki, a funkciói könnyebben megtalálhatóak és gyorsabban elérhetőek lesznek. A Speech service-szel a beszélgetés még gördülékenyebb lehet, mivel a szóbeli kérdésekre ugyanúgy szóban válaszol.

Ha egyedi személyiséget szeretne adni hangalapú robotjának, saját hangot adhat neki. Az egyéni hang készítése kétlépéses folyamat. Első lépésként [készítsen felvételeket](record-custom-voice-samples.md) arról a hangról, amelyet használni szeretne. Ezután [küldje el ezeket a felvételeket](how-to-customize-voice-font.md) (a szöveges átirattal együtt) a Speech Service [hangtestreszabási portáljára](https://cris.ai/Home/CustomVoice), amely elvégzi a többit. Miután létrehozta az egyéni hangot, egyszerűen használhatja az alkalmazásában.

## <a name="next-steps"></a>További lépések

Szerezzen be egy előfizetési kulcsot a Speech service-hez.

> [!div class="nextstepaction"]
> [Próbálja ki ingyenesen a Speech service-t](get-started.md)
