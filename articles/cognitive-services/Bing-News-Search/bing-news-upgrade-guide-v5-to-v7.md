---
title: Bing News Search API V5 frissítése a v7-re
titleSuffix: Azure Cognitive Services
description: Az alkalmazás azon részeit azonosítja, amelyeket frissítenie kell a 7-es verzió használatára.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 1263e93b1e316cab4afb51cd828737a5bd087fed
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423847"
---
# <a name="news-search-api-upgrade-guide"></a>News Search API-frissítési útmutató

Ez a frissítési útmutató a Bing News Search API 5. és 7. verziójának változásait azonosítja. Ez az útmutató segítséget nyújt az alkalmazás azon részeinek azonosításához, amelyeket frissítenie kell a 7-es verzió használatához.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószáma a V5 verzióról a v7-re változott. Például https://api.cognitive.microsoft.com/bing/: **v 7.0**/News/Search.

### <a name="error-response-objects-and-error-codes"></a>Hibás objektumok és hibakódok

- Az összes sikertelen kérelemnek tartalmaznia kell `ErrorResponse` egy objektumot a válasz törzsében.

- A következő mezőket adta hozzá az `Error` objektumhoz.  
  - `subCode`&mdash;A hibakódot különálló gyűjtőbe particionálja, ha lehetséges
  - `moreDetails`&mdash;További információ a `message` mezőben leírt hibáról

- Az V5-hibakódokat a következő lehetséges `code` és `subCode` értékekkel cserélte le.

|Kód|SubCode|Leírás
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Nincs implementálva|A Bing visszaadja a ServerError, ha az alkódok bármely feltétele teljesül. A válasz tartalmazza ezeket a hibákat, ha a HTTP-állapotkód 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokkolt|A Bing visszaadja a InvalidRequest, ha a kérelem bármely része érvénytelen. Például hiányzik egy kötelező paraméter, vagy a paraméter értéke érvénytelen.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a HTTP-állapotkód 400.<br/><br/>Ha a hiba HttpNotAllowed, a 410-es HTTP-állapotkód.
|RateLimitExceeded||A Bing visszaadja a RateLimitExceeded, amikor a másodpercenkénti lekérdezések (QPS) vagy a havi lekérdezés (QPM) kvóta meghaladja a kvótát.<br/><br/>A Bing a 429-as HTTP-állapotkódot adja vissza, ha túllépte a QPS és a 403 értéket, ha túllépte a QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|A Bing visszaadja a InvalidAuthorization, ha a Bing nem tudja hitelesíteni a hívót. Például hiányzik a `Ocp-Apim-Subscription-Key` fejléc, vagy az előfizetési kulcs érvénytelen.<br/><br/>A redundancia akkor fordul elő, ha egynél több hitelesítési módszert ad meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|A Bing visszaadja a InsufficientAuthorization, ha a hívónak nincs engedélye az erőforrás elérésére. Ez akkor fordulhat elő, ha az előfizetési kulcs le van tiltva vagy lejárt. <br/><br/>Ha a hiba InsufficientAuthorization, a HTTP-állapotkód 403.

- A következő leképezi az előző hibakódokat az új kódokra. Ha az V5-hibakódok függőségét vette igénybe, frissítse a kódját ennek megfelelően.

|5\. verzió kódja|7-es verzió kódja. alkód
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Letiltva|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError. ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Nincs implementálva|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blokkolt|InvalidRequest. Blocked

### <a name="object-changes"></a>Objektum változásai

- Hozzáadta `contractualRules` a mezőt a [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) objektumhoz. A `contractualRules` mező tartalmazza a követni kívánt szabályok listáját (például cikk-hozzárendelés). A használata `contractualRules` `provider`helyett a megadott hozzárendelést kell alkalmaznia. A cikk csak `contractualRules` akkor szerepel, ha a [Web Search API](../bing-web-search/search-the-web.md) -válasz hírekre adott választ tartalmaz.

## <a name="non-breaking-changes"></a>Nem törhető változások

### <a name="query-parameters"></a>Lekérdezési paraméterek

- A termékek olyan lehetséges értékként lettek hozzáadva, mint a [Kategória](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) lekérdezési paraméterének beállítása. Tekintse meg [a kategóriákat piacok alapján](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

- Hozzáadta a [sortby](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) lekérdezési paramétert, amely a legutóbb megjelenő dátum szerint rendezve jeleníti meg a trendi témákat.

- A lekérdezési paraméter [óta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) hozzáadott egy, a Bing által a megadott UNIX-időbélyeggel vagy azt követően felderített trendek témakört.

### <a name="object-changes"></a>Objektum változásai

- Hozzáadta `mentions` a mezőt a [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) objektumhoz. A `mentions` mező a cikkben található entitások (személyek vagy helyek) listáját tartalmazza.

- Hozzáadta `video` a mezőt a [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) objektumhoz. A `video` mező a újságcikkhez kapcsolódó videót tartalmaz. A videó vagy egy \<iframe\> , amelyet beágyazhat, vagy egy mozgó miniatűrt is használhat.

- Hozzáadta `sort` a mezőt a [News](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) objektumhoz. A `sort` mező a cikkek rendezési sorrendjét jeleníti meg. A cikkek például a relevancia (alapértelmezett) vagy a dátum szerint vannak rendezve.

- Hozzáadta a [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) objektumot, amely meghatározza a rendezési sorrendet. A `isSelected` mező jelzi, hogy a válasz a rendezési sorrendet használta-e. Ha az **értéke igaz**, a válasz a rendezési sorrendet használta. Ha `isSelected` a értéke **false (hamis**), `url` a mezőben szereplő URL-cím használatával más rendezési sorrendet is igényelhet.
