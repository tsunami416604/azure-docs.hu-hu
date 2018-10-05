---
title: Mi a Custom Speech Service?
titlesuffix: Azure Cognitive Services
description: A Custom Speech Service egy felhőalapú szolgáltatás, amellyel a felhasználók testre szabhatják a beszédmodelleket a diktálások átírásához.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: overview
ms.date: 02/07/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: f90fc40a42806cfb002da2d9943eaa41736df4d7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222743"
---
# <a name="what-is-custom-speech-service"></a>Mi a Custom Speech Service?

A Custom Speech Service egy felhőalapú szolgáltatás, amellyel a felhasználók testre szabhatják a beszédmodelleket a diktálások átírásához.
A Custom Speech Service használatához tekintse meg a [Custom Speech Service portálját](https://cris.ai).

A Custom Speech Service-szel alkalmazására és felhasználóira szabott nyelvi modelleket és akusztikai modelleket hozhat létre. Az adott beszéd- és/vagy szövegadatok a Custom Speech Service-be történő feltöltésével létrehozhat olyan egyéni modelleket, amelyeket együtt használhat a Microsoft meglévő fejlett beszédmodelljeivel.

Ha például beszédinterakciót ad egy mobiltelefonos, táblagépes vagy számítógépes alkalmazáshoz, létrehozhat egy egyéni nyelvi modellt, amely kombinálható a Microsoft akusztikai modelljével, így létrehozhat egy, az Ön alkalmazására szabott diktálási végpontot. Ezzel a szolgáltatással akkor is létrehozhat és üzembe helyezhet egy egyéni akusztikai modellt, ha alkalmazása egy adott környezetben vagy adott felhasználók általi használatra készült.


## <a name="how-do-speech-recognition-systems-work"></a>Hogyan működnek a beszédfelismerő rendszerek?
A beszédfelismerő rendszerek számos együttműködő összetevőből állnak. Két különösen fontos összetevő az akusztikai modell és a nyelvi modell.

Az akusztikai modell egy besoroló, amely rövid hangrészleteket egy bizonyos számú fonéma vagy hangegység közül eggyel felcímkéz egy adott nyelven. A „speech” szó például a következő négy fonémából áll: „s p iy ch”. Ezek a besorolások másodpercenként nagyjából százszor történnek meg.

A nyelvi modell szósorozatok valószínűségi eloszlása. A nyelvi modell segít a rendszernek megkülönböztetni hasonlóan hangzó szósorozatokat a szósorozatok valószínűsége alapján. A „recognize speech” és a „wreck a nice beach” például hasonlóan hangzik, de az első kifejezés sokkal nagyobb valószínűséggel fordul elő, ezért a nyelvi modell egy magasabb pontszámot rendel hozzá.

Az akusztikai és a nyelvi modell is egy betanítási adatokból tanított statisztikai modell. Ebből az következik, hogy akkor teljesítenek a legjobban, ha az alkalmazásokban előforduló beszéd hasonlít a betanítás során megfigyelt adatokhoz. A Microsoft beszédfelismerő motorjának akusztikai és nyelvi modelljét egy hatalmas beszéd- és szöveggyűjteményen tanították be, emellett a modellek fejlett teljesítményt nyújtanak a leggyakoribb használati forgatókönyvekben, mint amikor Cortanával kommunikál az okostelefonján, táblagépén vagy számítógépén, hangalapú internetes keresést hajt végre, illetve szöveges üzeneteket diktál egy barátjának.

## <a name="why-use-the-custom-speech-service"></a>Miért érdemes a Custom Speech Service szolgáltatást használni?
Bár a Microsoft beszédfelismerő motorja világszínvonalú, mégis inkább a fentebb említett forgatókönyveket célozza meg. Ha azonban azt szeretné, hogy alkalmazásában a hangalapú lekérdezés tartalmazzon egy adott szókincset, például termékneveket vagy a mindennapi beszédben ritkán előforduló szakzsargont, valószínű, hogy a nyelvi modell testreszabásával nagyobb teljesítményt érhet el.

Ha például egy olyan alkalmazást fejleszt, amely az MSDN-ben hajt végre hangalapú keresést, valószínű, hogy olyan kifejezések, mint „object-oriented”, a „namespace” vagy a „dot net” gyakrabban fognak előfordulni, mint a mindennapi beszéd elemei. A nyelvi modell testreszabása által a rendszer ezt megtanulja.

## <a name="next-steps"></a>További lépések

További információkért a Custom Speech Service használatával kapcsolatban lásd a [Custom Speech Service portálját] (https://cris.ai).

* [Első lépések](cognitive-services-custom-speech-get-started.md)
* [GYIK](cognitive-services-custom-speech-faq.md)
* [Szószedet](cognitive-services-custom-speech-glossary.md)
