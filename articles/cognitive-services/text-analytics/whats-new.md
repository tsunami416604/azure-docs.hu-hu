---
title: A Text Analytics API újdonságai
titleSuffix: Text Analytics - Azure Cognitive Services
description: Ez a cikk az Azure Cognitive Services szövegelemzésúj kiadásaival és funkcióival kapcsolatos információkat tartalmaz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77188798"
---
# <a name="whats-new-in-the-text-analytics-api"></a>A Text Analytics API újdonságai.

A Text Analytics API folyamatosan frissül. Ahhoz, hogy naprakész legyen a legújabb fejleményekkel, ez a cikk tájékoztatást nyújt az új kiadásokról és funkciókról.

## <a name="february-2020"></a>2020. február

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK-támogatás a Text Analytics API v3 nyilvános előzetes verziójához

Az egyesített [Azure SDK-kiadás](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)részeként a Text Analytics API v3 SDK már elérhető nyilvános előzetes verzióként a következő programozási nyelvekhez:
   * [C #](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [További információ a Text Analytics API v3 SDK-ról](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Elnevezett entitásfelismerés v3 nyilvános előzetes verzió

További entitástípusok már elérhetők a Named Entity Recognition (NER) v3 nyilvános előzetes verziószolgáltatásban, ahogy bővítjük a szövegben található általános és személyes adatok entitásainak észlelését. Ez a frissítés bemutatja [a modellverziót,](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`amely a következőket tartalmazza:

* A következő általános entitástípusok elismerése (csak angol nyelven):
    * Személytípus
    * Product
    * Esemény
    * Geopolitikai entitás (GPE) altípusként a Helymeghatározás
    * Ügyességi

* A következő személyes információs entitástípusok felismerése (csak angol nyelven):
    * Személy
    * Szervezet
    * Életkor altípusként a Mennyiség csoportban
    * Dátum altípusként a DateTime alatt
    * E-mail 
    * Telefonszám (csak az Egyesült Államokban)
    * URL-cím
    * IP-cím

> [!div class="nextstepaction"]
> [További információ a Névvel ellátott entitásfelismerésről a 3.](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>2019. október

#### <a name="named-entity-recognition-ner"></a>Elnevezett entitásfelismerés (NER)

* [Új végpont](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) a személyes adatok felismeréséhez entitástípusok (csak angol nyelven)

* Különálló végpontok a [gazdálkodó egység kimutatásához](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) és [a gazdálkodó egység összekapcsolásához.](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)

* [Modell verzió ,](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`amely tartalmazza:
    * A szövegben található entitások kiterjesztett észlelése és kategorizálása. 
    * A következő új entitástípusok felismerése:
        * Telefonszám
        * IP-cím

Az összekapcsolási entitás támogatja az angolt és a spanyolt. A NER nyelvi támogatása entitástípusonként változik.

#### <a name="sentiment-analysis-v3-public-preview"></a>Hangulatelemzés v3 nyilvános előzetes verzió

* Új [végpont](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) az érzelmek elemzéséhez.
* [Modell verzió ,](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`amely tartalmazza:

    * Az API szövegének kategorizálásának és pontozásának pontossága és részletessége jelentős javulást jelent.
    * Automatikus címkézés a szöveg különböző érzelmeihez.
    * Hangulatelemzés és kimenet egy dokumentum- és mondatszinten. 

Támogatja az angol`en`(`ja`), japán (`zh-Hans`), kínai`zh-Hant`egyszerűsített`fr`( ),`it`kínai`es`hagyományos (`nl`),`pt`francia (`de`), olasz ( ), spanyol `Australia East` `Central Canada`( `Central US` `East Asia`), `East US` `East US 2`holland `North Europe` `Southeast Asia`( `South Central US` `UK South`), `West Europe`portugál `West US 2`( ), és rendelkezésre áll a következő régiókban: , , , , , , , , , , , , , , , , és . 

> [!div class="nextstepaction"]
> [További információ a hangulatelemzésről a 3- as v.-ről](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>További lépések

* [Mi a Text Analytics API?](overview.md)  
* [Példa felhasználói forgatókönyvek](text-analytics-user-scenarios.md)
* [Hangulatelemzés](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Nyelvfelismerés](how-tos/text-analytics-how-to-language-detection.md)
* [Entitások felismerése](how-tos/text-analytics-how-to-entity-linking.md)
* [Kulcskifejezés kinyerése](how-tos/text-analytics-how-to-keyword-extraction.md)
