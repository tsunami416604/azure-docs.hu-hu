---
title: Bing hírek keresés API frissítése a v7 v5 |} Microsoft Docs
description: Azonosítja a 7-es verzió használatához frissítenie kell az alkalmazás részei.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 5334C475-4841-4736-A66E-DC1E07CBCEC9
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: baed6f0091ddad40b4802c0fb52dc2ca1818cd03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347126"
---
# <a name="news-search-api-upgrade-guide"></a>Hírek Search API frissítési útmutató

Az frissítési útmutató meghatározza azokat a változtatásokat, 5-ös verzió és a Bing hírek keresési API-JÁNAK 7-es verziója között. Ez az útmutató segítségével azonosíthatja a 7-es verzió használatához frissítenie kell az alkalmazás részei.

## <a name="breaking-changes"></a>Meghibásodást okozó változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószám v7 v5 változik. Például https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/news/search.

### <a name="error-response-objects-and-error-codes"></a>Hiba válasz objektumok és hibakódok

- Összes sikertelen kérelem most tartalmaznia kell egy `ErrorResponse` az adott válasz törzsének objektumban.

- A következő mezőket hozzáadni a `Error` objektum.  
  - `subCode`&mdash;A hiba kódja particionálja be különálló gyűjtők, ha lehetséges
  - `moreDetails`&mdash;A leírt hibával kapcsolatban további információt a `message` mező
   

- A következő lehetséges v5 hibakódok helyett `code` és `subCode` értékeket.

|Kód|Alkód|Leírás
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Nincs implementálva|Bing ServerError adja vissza, ha az alárendelt kód feltételek bármelyike teljesül. A válasz tartalmazza ezeket a hibákat, ha a HTTP-állapotkód: 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Letiltva|Bing InvalidRequest adja vissza, ha valamely része a kérelem érvénytelen. Például egy kötelező paraméter hiányzik, vagy egy paraméter értéke érvénytelen.<br/><br/>A hiba ParameterMissing vagy ParameterInvalidValue, a HTTP-állapotkód akkor 400.<br/><br/>Ha a hiba HttpNotAllowed, a HTTP-állapotkód 410.
|RateLimitExceeded||Bing RateLimitExceeded adja vissza, ha az túllépi a lekérdezések / másodperc (QPS) vagy a lekérdezések száma (QPM) havi kvóta.<br/><br/>Bing HTTP-állapotkód 429 adja vissza, ha túllépte QPS és 403 Ha QPM túllépte.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing InvalidAuthorization adja vissza, ha a Bing a hívó nem tudja hitelesíteni. Például a `Ocp-Apim-Subscription-Key` fejléc hiányzik, vagy az Előfizetés kulcs nem érvényes.<br/><br/>Redundancia akkor fordul elő, ha egynél több hitelesítési módszer adja meg.<br/><br/>A hiba InvalidAuthorization, a HTTP-állapotkód akkor 401-es.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing InsufficientAuthorization adja vissza, ha a hívó nem jogosult az erőforrás elérésére. Ez akkor fordulhat elő, ha az Előfizetés kulcs le van tiltva, vagy lejárt. <br/><br/>A hiba InsufficientAuthorization, a HTTP-állapotkód akkor 403-as.

- A következő van leképezve az előző hibakódok új kódokkal. Ha egy függőséget már végzett v5 hibakódok, ennek megfelelően frissítse a kódot.

|5-ös verzió kódot|7-es verzió code.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Letiltva|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Nincs implementálva|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Letiltva|InvalidRequest.Blocked

### <a name="object-changes"></a>Objektummódosítások

- Hozzáadva a `contractualRules` mezőről az [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objektum. A `contractualRules` mezőben (például a cikk attribútumára) létrehozásához szükséges szabályok listáját. A megadott attribútumára kell alkalmaznia `contractualRules` helyett `provider`. A cikkből `contractualRules` csak akkor, ha a [webes keresés API](../bing-web-search/search-the-web.md) válasz egy hírek választ tartalmaz.

## <a name="non-breaking-changes"></a>Nem törhető változások

### <a name="query-parameters"></a>Lekérdezési paraméterek

- Termékek fel, mert egy lehetséges értéke, amely előfordulhat, hogy a [kategória](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) lekérdezési paramétert. Lásd: [kategóriák szerint piacok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#categories-by-market).  
    
- Hozzáadva a [SortBy](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortby) lekérdezési paraméter, amely trendekkel kapcsolatos témakörök a legutóbbi első dátum szerint rendezve adja vissza.  
  
- Hozzáadva a [óta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#since) lekérdezési paraméter, amely trendekkel kapcsolatos témakörök felfedezett által a Bing, a megadott Unix epoch Timestamp típusú a következőnél adja vissza.

### <a name="object-changes"></a>Objektummódosítások

- Hozzáadva a `mentions` mezőről az [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objektum. A `mentions` mező entitások (személyek vagy helyek) című cikkben találhatók listáját tartalmazza.  
  
- Hozzáadva a `video` mezőről az [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objektum. A `video` mezőben a hírek cikk kapcsolódó videó. A videó esik, vagy egy \<iframe\> , amely beágyazása vagy mozgásérzékelési miniatűr.   
  
- Hozzáadva a `sort` mezőről az [hírek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news) objektum. A `sort` mezőben látható, a cikkek rendezési sorrendjét. Például a cikk relevanciájának (alapértelmezés) vagy dátum alapján rendezi a.  
  
- Hozzáadva a [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortvalue) objektum, amely meghatározza a rendezési sorrend. A `isSelected` mező azt jelzi, hogy a válasz használja-e a rendezési sorrend. Ha **igaz**, a válasz a rendezési sorrendet használja. Ha `isSelected` van **hamis**, az URL-címet is használhat a `url` mező eltérő rendezési sorrend kéréséhez.
