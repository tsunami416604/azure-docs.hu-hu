---
title: A Text Analytics API újdonságai
titleSuffix: Text Analytics - Azure Cognitive Services
description: Ez a cikk az Azure Cognitive Services Text Analytics új kiadásairól és funkcióiról nyújt információkat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: e3fbff7a5919d4d43656f3112cb24f2017191348
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086787"
---
# <a name="whats-new-in-the-text-analytics-api"></a>A Text Analytics API újdonságai.

A Text Analytics API frissítése folyamatosan történik. Ha naprakészen szeretne maradni a legutóbbi fejleményekkel, ez a cikk az új kiadásokkal és szolgáltatásokkal kapcsolatos információkat tartalmaz.

## <a name="named-entity-recognition-v3-public-preview---february-2020"></a>Elnevezett Entity Recognition v3 Public Preview – február 2020

További entitás-típusok mostantól elérhetők az elnevezett entitás-felismerési (megnevezett) v3 nyilvános előzetes verzióban, mivel kiterjesztjük a szövegben található általános és személyes információk entitások észlelését. Ez a frissítés bevezeti a [modell verzióját](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`, amely a következőket tartalmazza:

* A következő általános entitás-típusok felismerése (csak angol nyelven):
    * PersonType
    * Product
    * Esemény
    * Geopolitikai entitás (GPE) a hely alatt altípusként
    * Ügyességi

* A személyes adatok következő típusú entitás-típusainak felismerése (csak angol nyelven):
    * Személy
    * Szervezet
    * A mennyiség alatti altípusok kora
    * Dátum a DateTime alatt altípusként
    * E-mail 
    * Telefonszám (csak US)
    * URL-cím
    * IP-cím

> [!div class="nextstepaction"]
> [További információ az elnevezett Entity Recognition v3-ról](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>2019. október

#### <a name="named-entity-recognition-ner"></a>Elnevezett entitások felismerése

* Egy [új végpont](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) a személyes információk entitás-típusai felismeréséhez (csak angol nyelven)

* Különálló végpontok az [entitások felismeréséhez](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) és az [entitások összekapcsolásához](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Modell verziója](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`, amely a következőket tartalmazza:
    * A szövegben talált entitások kibővített észlelése és kategorizálása. 
    * A következő új entitások típusának felismerése:
        * Telefonszám
        * IP-cím

Az entitások csatolása az angol és a spanyol nyelvet támogatja. A nyelv támogatása az entitás típusától függően változik.

#### <a name="sentiment-analysis-v3-public-preview"></a>Hangulatelemzés v3 nyilvános előzetes verzió

* [Új végpont](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) az érzelmek elemzéséhez.
* [Modell verziója](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`, amely a következőket tartalmazza:

    * Az API szövegének és pontozásának pontosságának és részletességének jelentős javulása.
    * Automatikus címkézés a különböző érzelemekhez a szövegben.
    * A dokumentumok és a mondatok szintjének elemzése és kimenete. 

Támogatja az angol (`en`), a japán (`ja`), egyszerűsített kínai (`zh-Hans`), hagyományos kínai (`zh-Hant`), francia (`fr`), olasz (`it`), spanyol (`es`), holland (`nl`), Portugál (`pt`) és német (`de`), és a következő régiókban érhető el: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`és `West US 2`. 

> [!div class="nextstepaction"]
> [További információ a Hangulatelemzés v3-ról](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Következő lépések

* [Mi a Text Analytics API?](overview.md)  
* [Példa felhasználói forgatókönyvekre](text-analytics-user-scenarios.md)
* [Hangulat elemzése](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Nyelvfelismerés](how-tos/text-analytics-how-to-language-detection.md)
* [Entitások felismerése](how-tos/text-analytics-how-to-entity-linking.md)
* [Fő kifejezés kibontása](how-tos/text-analytics-how-to-keyword-extraction.md)
