---
title: Mi az a Speech service (előzetes verzió)?
description: 'A Speech service, a Microsoft Cognitive Services részeként több egység számos Azure beszédszolgáltatások külön-külön korábban elérhető volt: (amely magában foglalja, beszédfelismerés, és a szöveg-beszéd átalakítás), a Bing Speech, egyéni beszédfelismerési és beszédalapú fordítási.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 60ff2f71766a14af17ebb1cb9d20825976471296
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "41988770"
---
# <a name="what-is-the-speech-service-preview"></a>Mi az a Speech service (előzetes verzió)?

Egy előfizetéssel rendelkező a beszédfelismerési szolgáltatás biztosítja a fejlesztők számára egyszerű módszert beszédfeldolgozó hatékony funkciók hozzáadása alkalmazásokhoz. Az alkalmazások a is most Hangvezérlés, beszédátírási, a Diktálás, beszédszintézishez és beszédalapú fordítási szolgáltatás.

A beszédfelismerési szolgáltatás működteti, más Microsoft-termékek, köztük a Cortana és a Microsoft Office technológiák.

> [!NOTE]
> A beszédfelismerési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. Térjen vissza ide rendszeresen frissítéseket dokumentáció, Kódminták további és egyéb.

## <a name="speech-service-features"></a>Beszédszolgáltatások szolgáltatás

|Függvény|Leírás|
|-|-|
|[Hang-szöveg](speech-to-text.md)| Transcribes audiostreamek lejátszásával, a szöveg, amely bemenetként az alkalmazás fogadhat. Együttműködik a [hangfelismerési szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) felhasználói szándékot származtassa kimondott szöveg.|
|[Szöveg-hang transzformációs](text-to-speech.md)| Egyszerű szöveges konvertálja természetes hangzó beszéddé, az alkalmazás a hangfájl lett elküldve. Több beszédhangot nemek vagy hangsúlyos, különböző számos nyelven érhetők el. |
|[Tolmácsolás –](speech-translation.md)| Lefordítja a közel valós idejű streamelési hang vagy egy rögzített beszédfelismerő feldolgozására használható. |
|Egyéni hang-szöveg|Hang-szöveg transzformációs hoz létre a saját testre szabható [akusztikai](how-to-customize-acoustic-models.md) és [nyelvi](how-to-customize-language-model.md) modelljei és megadásával egyéni [írásmódja](how-to-customize-pronunciation.md) szabályokat. |
|[Egyéni Szövegfelolvasás](how-to-customize-voice-font.md)|A saját beszédhangot az szöveg-hang transzformációs is létrehozhat.|
|[Beszéd eszközök SDK](speech-devices-sdk.md)| Az egyesített beszédszolgáltatás bevezetésével a Microsoft és partnerei ajánlat egy integrált hardver/szoftver fejlesztéséhez speech-kompatibilis eszközök optimalizált platform |

## <a name="access-to-the-speech-service"></a>A Beszédfelismerés szolgáltatáshoz való hozzáférést

A Speech service kétféle módon elérhetővé. [Az SDK](speech-sdk.md) kódbázis egyszerűbben fejlesztési a támogatott platformokon a hálózati protokollok részletei. A [REST API-val](rest-apis.md) minden programozási nyelvet együttműködik, de nem vonatkozik az SDK által nyújtott összes funkció áttekintésével.

|<br>Módszer|Beszéd<br>a szöveg|A szöveg<br>Beszéd|Beszéd<br>Fordítás|<br>Leírás|
|-|-|-|-|-|
|[SDK-k](speech-sdk.md)|Igen|Nem|Igen|Adott programozási nyelvek, kódtárak, amely leegyszerűsítheti a fejlesztést Websocket-alapú szűrődémon használatára.|
|[REST](rest-apis.md)|Igen|Igen|Nem|Egy egyszerű HTTP-alapú API, amely megkönnyíti a speech hozzáadása az alkalmazáshoz.|

## <a name="speech-scenarios"></a>Beszéd forgatókönyvek

Néhány gyakori használati technológia röviden az alábbiak ismertetik. A [beszéd SDK](speech-sdk.md) ezekben a forgatókönyvekben a legtöbb központi.

> [!div class="checklist"]
> * Hang-eseményindítóval aktivált alkalmazások létrehozása
> * Hívás center felvételek alapuló átírás
> * Beszédfelismerési robotokat megvalósítása

### <a name="voice-triggered-apps"></a>Hangalapú indított alkalmazások

Beszédbemenetet kiválóan alkalmas, hogy az alkalmazás rugalmas, beavatkozás nélküli, és gyors használatára. Egy hang-kompatibilis alkalmazások a felhasználók csak kérhet ikonra kattintva vagy koppintva váltson magasabb helyett a szükséges információk.

Ha az alkalmazás használatra szánt a nagyközönség, használhatja a speech recognition modell a beszédfelismerési szolgáltatás által biztosított. Egy tipikus környezetekben előadói számos FELISMERVE jó feladata hajtja végre.

Ha az alkalmazás fogja használni egy adott tartományban (például orvosi vagy informatikai), létrehozhat egy [nyelvi modell](how-to-customize-language-model.md) , akik az információ a speciális, az alkalmazás által használt terminológia a Speech service.

Ha az alkalmazás fogja használni egy zajos környezetben, például egy gyári hozhat létre egyéni [akusztikai modell](how-to-customize-acoustic-models.md) jobban engedélyezéséhez a Speech service speech megkülönböztetni a háttérzaj.

Sajátítsa el megírásához letöltése a [beszéd SDK](speech-sdk.md) és a egy megfelelő a következő [rövid](quickstart-csharp-dotnet-windows.md) cikk.

### <a name="transcribe-call-center-recordings"></a>Hívás center felvételek alapuló átírás

Gyakran call center felvételek, ha probléma merül fel a hívással kizárólag konzultációt. A Speech szolgáltatással egyszerűen lefényképezze minden felvétel szöveg. Szöveg-, amint egyszerűen indexelésére használhatja őket a [teljes szöveges keresés](https://docs.microsoft.com/azure/search/search-what-is-azure-search) vagy a alkalmazni [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) véleményeket, nyelv és kulcskifejezések észlelése.

Ha a hívás center felvételek gyakran tartalmaznak (például termékneveket vagy informatikai szakkifejezéseivel) speciális terminológiája, létrehozhat egy [nyelvi modell](how-to-customize-language-model.md) , akik a beszédfelismerési szolgáltatás az adott szókincsből eredőket. Egyéni [akusztikai modell](how-to-customize-acoustic-models.md) súgó a Speech service képes megérteni kevésbé optimális telefonos kapcsolatok.

További információ erről a forgatókönyvről, tudjon meg többet [beszédátírási batch](batch-transcription.md) a Speech szolgáltatással.

### <a name="voice-bots"></a>Hangalapú robotok

[A robotok](https://dev.botframework.com/) egy egyre népszerű lehetőség a felhasználók összekapcsolása az adatokkal szeretnének, és a vállalkozások számára rendelkező ügyfelek használhatják kedvenc. Természetes nyelvi felhasználói felületet a webhely vagy alkalmazás hozzáadása lehetővé teszi a működését, található egyszerűbb és gyorsabb a eléréséhez. A Speech szolgáltatással ennél a beszélgetésnél veszi fluency új dimenziót által ténylegesen válaszol a szintetizált kimondott lekérdezéseket.

A voice-kompatibilis robot ad hozzá egy egyedi személyiségelemző (és erősítse márkáját) adhat egy hang saját. Egy egyéni beszédfelismerési létrehozása két lépésből áll. Első, akkor [győződjön meg a felvételt](record-custom-voice-samples.md) a beszéd létrehozásához használni szeretne. Akkor [küldje el e felvételek](how-to-customize-voice-font.md) (valamint a szöveget szöveges) a beszédfelismerési szolgáltatás [hangalapú testreszabási portál](https://cris.ai/Home/CustomVoice), amely szolgáltatás végzi. Miután létrehozta a egyéni beszédfelismerési, akkor magától értetődő, használhatja az alkalmazásban.

## <a name="next-steps"></a>További lépések

A beszédfelismerési szolgáltatás előfizetési kulcs lekérése.

> [!div class="nextstepaction"]
> [A beszédfelismerési szolgáltatás ingyenes kipróbálása](get-started.md)
