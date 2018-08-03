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
ms.openlocfilehash: 0cd6d984ac9329112aa388e8d8ee808d4c3e6227
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445634"
---
# <a name="what-is-the-speech-service-preview"></a>Mi az a Speech service (előzetes verzió)?

A beszédfelismerési szolgáltatás működteti, más Microsoft-termékek, köztük a Cortana és a Microsoft Office technológiák.  Ez ugyanazt a szolgáltatást kognitív szolgáltatás minden olyan vevői rendelkezésére áll. 

> [!NOTE]
> A beszédfelismerési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. Térjen vissza ide rendszeresen frissítéseket dokumentáció, Kódminták további és egyéb.

Egy előfizetéssel rendelkező a beszédfelismerési szolgáltatás biztosítja a fejlesztők számára egyszerű módszert biztosítanak az alkalmazások hatékony beszédfeldolgozó funkcióit. Az alkalmazások a is most Hangvezérlés, beszédátírási, a Diktálás, beszédszintézishez és fordítási szolgáltatás.

## <a name="speech-service-features"></a>Beszédszolgáltatások szolgáltatás

|Függvény|Leírás|
|-|-|
|[Hang-szöveg](speech-to-text.md)| Transcribes audiostreamek lejátszásával, a szöveg, amely bemenetként az alkalmazás fogadhat. Együttműködik a [hangfelismerési szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) felhasználói szándékot származtassa kimondott szöveg.|
|[Szöveg-hang transzformációs](text-to-speech.md)| Egyszerű szöveges konvertálja természetes hangzó beszéddé, az alkalmazás a hangfájl lett elküldve. Több beszédhangot nemek vagy hangsúlyos, különböző számos nyelven érhetők el. |
|[Tolmácsolás –](speech-translation.md)| Lefordítja a közel valós idejű streamelési hang vagy egy rögzített beszédfelismerő feldolgozására használható. |
|[Beszéd eszközök SDK](speech-devices-sdk.md)| Az egyesített beszédszolgáltatás bevezetésével a Microsoft és partnerei ajánlat egy integrált hardver/szoftver fejlesztéséhez speech-kompatibilis eszközök optimalizált platform |

## <a name="using-the-speech-service"></a>A Speech szolgáltatással

A Speech service kétféle módon elérhetővé. [Az SDK](speech-sdk.md) kódbázis hálózati protokollok részletei. A [REST API-val](rest-apis.md) minden programozási nyelvet együttműködik, de nem vonatkozik az SDK által nyújtott összes funkció áttekintésével.

|<br>Módszer|Beszéd<br>a szöveg|A szöveg<br>Beszéd|Beszéd<br>Fordítás|<br>Leírás|
|-|-|-|-|-|
|[SDK-k](speech-sdk.md)|Igen|Nem|Igen|Szalagtárak adott programozási nyelvet, amely leegyszerűsítheti a fejlesztést.|
|[REST](rest-apis.md)|Igen|Igen|Nem|Egy egyszerű HTTP-alapú API, amely megkönnyíti a speech hozzáadása az alkalmazáshoz.|

## <a name="next-steps"></a>További lépések

A beszédfelismerési szolgáltatás ingyenes próba-előfizetését kulcs lekérése.

> [!div class="nextstepaction"]
> [A beszédfelismerési szolgáltatás ingyenes kipróbálása](get-started.md)
