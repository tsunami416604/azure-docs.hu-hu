---
title: A Text Analytics API v2-es verziójának áttelepítési útmutatója
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan helyezheti át alkalmazásait a Text Analytics API 3. verziójának használatára.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 2fd6c908aa95ef22cbf7ea0b62b925d84d66cd28
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84143270"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Migrálás a Text Analytics API 3. x verziójára

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

Ha a Text Analytics API 2,1-es verzióját használja, ez a cikk segítséget nyújt az alkalmazás frissítéséhez a 3. x verzió használatára. Az 3,0-es verzió általánosan elérhető, és olyan új funkciókat vezet be, mint az [elnevezett entitás-felismerés](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) és a [modell verziószámozása](concepts/model-versioning.md). A v 3.1 (v 3.1-preview. x) előzetes verziója is elérhető, amely olyan funkciókat is tartalmaz, mint a [vélemény bányászata](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features). A v2-ben használt modellek nem kapják meg a jövőbeli frissítéseket. 

#### <a name="sentiment-analysis"></a>[Hangulat elemzése](#tab/sentiment-analysis)

## <a name="feature-changes"></a>Szolgáltatások módosításai 

Az 2,1-es verzióban található Hangulatelemzés az API-nak elküldett minden dokumentum esetében 0 és 1 értékkel tér vissza. A 3. verzió Ehelyett visszaadja az érzelmek címkéit (például "pozitív" vagy "negatív") mind a mondatok, mind a dokumentum egészének, valamint a hozzájuk kapcsolódó megbízhatósági pontszámoknak. 

## <a name="steps-to-migrate"></a>Az áttelepíteni kívánt lépések

### <a name="rest-api"></a>REST API

Ha az alkalmazás a REST API használja, frissítse a kérelem végpontját a v3-végpontra az érzelmek elemzéséhez. Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` . Emellett frissítenie kell az alkalmazást a [JSON-válaszban](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results)visszaadott hangulati címkék használatára. 

### <a name="client-libraries"></a>Ügyfélkódtárak

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="ner-and-entity-linking"></a>[Az entitások összekapcsolása](#tab/named-entity-recognition)

## <a name="feature-changes"></a>Szolgáltatások módosításai

> [!NOTE] 
> Jelenleg a [v3 entitások kategóriái](named-entity-types.md) csak angol nyelvű szövegben lesznek visszaadva. Az API az 2,1-es verziót adja vissza más nyelveken található kérelmek esetében, amennyiben azok a 2,1-es verzióban támogatottak.

Az 2,1-es verzióban a Text Analytics API egy végpontot használ az elnevezett entitások felismeréséhez és az entitások összekapcsolásához. A 3. verzió kiterjesztett névvel ellátott entitások észlelését teszi lehetővé, és külön végpontokat használ a meghívásos és az entitások összekapcsolására A 3.1-es verziótól kezdődően (előzetes verzió: 1) a személyes `pii` és az egészségügyi információk is észlelhetők `phi` . 

## <a name="steps-to-migrate"></a>Az áttelepíteni kívánt lépések

### <a name="rest-api"></a>REST API

Ha az alkalmazás a REST API használja, frissítse a kérelem végpontját a meghívásos és/vagy az entitás összekapcsolására szolgáló v3-végpontokra.

Entitáskapcsolás
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

Emellett frissítenie kell az alkalmazást a [JSON-válaszban](how-tos/text-analytics-how-to-entity-linking.md#view-results)visszaadott [entitás-kategóriák](named-entity-types.md) használatára.

### <a name="client-libraries"></a>Ügyfélkódtárak

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="language-detection"></a>[Nyelvfelismerés](#tab/language-detection)

## <a name="feature-changes"></a>Szolgáltatások módosításai 

A nyelvi észlelési funkció nem módosult a v3 verzión kívül, de a JSON-válasz a `ConfidenceScore` helyett fog szerepelni `score` . A v3 is csak egyetlen nyelvet ad vissza a kimenetben. 

## <a name="steps-to-migrate"></a>Az áttelepíteni kívánt lépések

### <a name="rest-api"></a>REST API

Ha az alkalmazás a REST API használja, frissítse a kérelem végpontját a v3 végpontra a nyelvfelismerés érdekében. Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` . Emellett frissítenie kell a `ConfidenceScore` `score` [JSON-válasz](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results)helyett használni kívánt alkalmazást. 

### <a name="client-libraries"></a>Ügyfélkódtárak

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="key-phrase-extraction"></a>[Fő kifejezés kibontása](#tab/key-phrase-extraction)

## <a name="feature-changes"></a>Szolgáltatások módosításai 

A Key kifejezés kibontása szolgáltatás nem módosult a v3 verzión kívül.

## <a name="steps-to-migrate"></a>Az áttelepíteni kívánt lépések

### <a name="rest-api"></a>REST API

Ha az alkalmazás a REST API használja, frissítse a kérelem végpontját a v3 végpontra a fő kifejezés kinyeréséhez. Például:`https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

### <a name="client-libraries"></a>Ügyfélkódtárak

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---


## <a name="see-also"></a>Lásd még

* [Text Analytics API v2 – dokumentáció](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/)
* [Mi a Text Analytics API](overview.md)
* [Nyelvi támogatás](language-support.md)
* [Modell verziószámozása](concepts/model-versioning.md)