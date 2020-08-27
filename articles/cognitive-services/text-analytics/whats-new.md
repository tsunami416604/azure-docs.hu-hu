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
ms.date: 08/26/2020
ms.author: aahi
ms.openlocfilehash: f017960e304df04148c318b5098f384e6140de9a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930910"
---
# <a name="whats-new-in-the-text-analytics-api"></a>A Text Analytics API újdonságai.

A Text Analytics API frissítése folyamatosan történik. Ha naprakészen szeretne maradni a legutóbbi fejleményekkel, ez a cikk az új kiadásokkal és szolgáltatásokkal kapcsolatos információkat tartalmaz.

## <a name="august-2020"></a>2020. augusztus

### <a name="general-api-updates"></a>Általános API-frissítések

* Modell verziója `2020-07-01` a v3 `/keyphrases` és a `/pii` `/languages` végpontok számára, amely a következőket adja:
    * További kormányzati és ország-specifikus [entitások kategóriái](named-entity-types.md?tabs=personal) a nevesített entitások felismeréséhez.
    * Norvég és török támogatás a Hangulatelemzés v3-as verzióban.
* A rendszer mostantól HTTP 400-es hibát ad vissza az olyan V3 API-kérelmek esetében, amelyek túllépik a közzétett [adatkorlátot](concepts/data-limits.md). 
* Az eltolást visszaadó végpontok támogatják a választható `stringIndexType` paramétert, amely a visszaadott `offset` és az értékeket úgy állítja be, `length` hogy az megfeleljen egy támogatott [karakterlánc-indexelési sémának](concepts/text-offsets.md).

### <a name="text-analytics-for-health-container-august-updates"></a>Az állapot-tárolók augusztus Text Analytics frissítései

A következő frissítések csak az Text Analytics az állapotfigyelő tárolóra vonatkozó augusztusi kiadására vonatkoznak.

* Új modell – a Text Analytics for Health verziója: `2020-07-24`
* Új URL-cím az Text Analytics küldéséhez az állapotadatok kéréséhez: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (vegye figyelembe, hogy a böngésző gyorsítótárának törlésére lesz szükség az új tároló-rendszerképben található bemutató webalkalmazás használatához)

A JSON-válasz következő tulajdonságai módosultak:

* A `type` új nevet kapott: `category` 
* A `score` új nevet kapott: `confidenceScore`
* `category`A JSON-kimenet mezőjében szereplő entitások mostantól Pascal-ügyben vannak. A következő entitások lettek átnevezve:
    * `EXAMINATION_RELATION` átnevezve: `RelationalOperator` .
    * `EXAMINATION_UNIT` átnevezve: `MeasurementUnit` .
    * `EXAMINATION_VALUE` átnevezve: `MeasurementValue` .
    * `ROUTE_OR_MODE` átnevezve `MedicationRoute` .
    * A relációs entitás `ROUTE_OR_MODE_OF_MEDICATION` át lett nevezve a következőre: `RouteOfMedication` .

A következő entitások lettek hozzáadva:

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* Kapcsolatok kibontása
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [További információ a Text Analytics az állapotfigyelő tárolóról](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>2020. július 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Text Analytics az állapotfigyelő szolgáltatáshoz – nyilvánosan kezdeményezett előzetes verzió

A Text Analytics for Health Container mostantól nyilvános, megnyitható előzetes verzióban érhető el, amely lehetővé teszi az adatok kinyerését a nem strukturált angol nyelvű szövegből olyan klinikai dokumentumokban, mint például a betegek bevitele, az orvos megjegyzései, a kutatási dokumentumok és a mentesítési összefoglalók. Jelenleg nem számítunk fel díjat a Text Analytics állapotának az állapotfigyelő tároló használata esetén.

A tároló a következő funkciókat kínálja:

* Megnevezett entitások felismerése
* Kapcsolatok kibontása
* Entitás összekapcsolása
* Negáció

## <a name="may-2020"></a>2020. május

### <a name="text-analytics-api-v3-general-availability"></a>A Text Analytics API v3 általánosan elérhető

A Text Analysis API v3 mostantól általánosan elérhető a következő frissítésekkel:

* Modell verziója `2020-04-01`
* Új [adatkorlátok](concepts/data-limits.md) az egyes szolgáltatásokhoz
* Frissítve a [Hangulatelemzés (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md) [nyelvi támogatása](language-support.md)
* Különálló végpont az entitás összekapcsolásához 
* Új "címe" entitásának kategóriája [elnevezett entitás-felismerési () v3](how-tos/text-analytics-how-to-entity-linking.md).
* Új alkategóriák:
   * Földrajzi hely
   * Hely – szerkezeti
   * Szervezet – Értéktőzsde
   * Szervezet – orvosi
   * Szervezet – sportok
   * Esemény – kulturális
   * Esemény – természetes
   * Esemény – sport

A JSON-válaszban a következő tulajdonságok lettek hozzáadva:
   * `SentenceText` Hangulatelemzés
   * `Warnings` minden dokumentumhoz 

A JSON-válaszban a következő tulajdonságok nevei módosultak, ahol lehetséges:

* A `score` új nevet kapott: `confidenceScore`
    * `confidenceScore` két tizedesjegy pontossággal rendelkezik. 
* A `type` új nevet kapott: `category`
* A `subtype` új nevet kapott: `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [További információ a Text Analytics API v3-ról](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Text Analytics API v 3.1 nyilvános előzetes verzió
   * Új Hangulatelemzés funkció – [vélemény bányászata](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Új [személyes ( `PII` ) tartományi szűrő](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) a védett állapotra vonatkozó információkhoz ( `PHI` ).

> [!div class="nextstepaction"]
> [További információ a Text Analytics API v 3.1 előzetes verziójáról](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>2020. február

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK-támogatás a Text Analytics API v3 nyilvános előzetes verziójához

Az [egységes Azure SDK kiadás](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)részeként a Text Analytics API v3 SDK mostantól elérhető nyilvános előzetes verzióként a következő programozási nyelvekhez:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [További információ a Text Analytics API v3 SDK-ról](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Elnevezett entitás-felismerés v3 nyilvános előzetes verziója

További entitás-típusok mostantól elérhetők az elnevezett entitás-felismerési (megnevezett) v3 nyilvános előzetes verzióban, mivel kiterjesztjük a szövegben található általános és személyes információk entitások észlelését. Ez a frissítés bevezeti a [modell verzióját](concepts/model-versioning.md) `2020-02-01` , amely a következőket tartalmazza:

* A következő általános entitás-típusok felismerése (csak angol nyelven):
    * PersonType
    * Termék
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

* [Modell verziója](concepts/model-versioning.md) `2019-10-01` , amely a következőket tartalmazza:
    * A szövegben talált entitások kibővített észlelése és kategorizálása. 
    * A következő új entitások típusának felismerése:
        * Telefonszám
        * IP-cím

Az entitások csatolása az angol és a spanyol nyelvet támogatja. A nyelv támogatása az entitás típusától függően változik.

#### <a name="sentiment-analysis-v3-public-preview"></a>Hangulatelemzés v3 nyilvános előzetes verzió

* [Új végpont](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) az érzelmek elemzéséhez.
* [Modell verziója](concepts/model-versioning.md) `2019-10-01` , amely a következőket tartalmazza:

    * Az API szövegének és pontozásának pontosságának és részletességének jelentős javulása.
    * Automatikus címkézés a különböző érzelemekhez a szövegben.
    * A dokumentumok és a mondatok szintjének elemzése és kimenete. 

Támogatja az angol ( `en` ), a japán (), a `ja` kínai egyszerűsített ( `zh-Hans` ), a kínai hagyományos (), a `zh-Hant` francia (), az `fr` olasz () `it` , a spanyol (), a `es` holland () `nl` , a portugál ( `pt` ) és a német () nyelveket `de` , és a következő régiókban érhető el: `Australia East` `Central Canada` `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` ,,,,,,,,,, és. 

> [!div class="nextstepaction"]
> [További információ a Hangulatelemzés v3-ról](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>További lépések

* [Mi a Text Analytics API?](overview.md)  
* [Példa felhasználói forgatókönyvek](text-analytics-user-scenarios.md)
* [Hangulat elemzése](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Nyelvfelismerés](how-tos/text-analytics-how-to-language-detection.md)
* [Entitások felismerése](how-tos/text-analytics-how-to-entity-linking.md)
* [Fő kifejezés kibontása](how-tos/text-analytics-how-to-keyword-extraction.md)
