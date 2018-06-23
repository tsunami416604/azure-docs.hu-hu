---
title: Frissítse a Bing v7 a videó API v5 |} Microsoft Docs
description: Azonosítja a 7-es verzió használatához frissítenie kell az alkalmazás részei.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: FA7DDF07-97AC-4EBE-8C50-A9737D43B38E
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 62646d026e141d0549c68e18f9318fa32d3e00df
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347043"
---
# <a name="video-search-api-upgrade-guide"></a>Videó Search API frissítési útmutató

Az frissítési útmutató meghatározza azokat a változtatásokat, 5-ös verzió és a videó Bing keresési API 7-es verziója között. Ez az útmutató segítségével azonosíthatja a 7-es verzió használatához frissítenie kell az alkalmazás részei.

## <a name="breaking-changes"></a>Meghibásodást okozó változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószám v7 v5 változik. Például https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/videos/search.

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

### <a name="query-parameters"></a>Lekérdezési paraméterek

- Átnevezi a `modulesRequested` lekérdezésparaméter a [modulok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Objektummódosítások

- Átnevezi a `nextOffsetAddCount` mezőjében [videók](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) való `nextOffset`. Az eltolás a módon is megváltozott. Korábban, akkor kell állítania a [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) lekérdezési paramétert a `nextOffset` értéke plusz a korábbi eltolási értéke plusz az eredményben videók száma. Most, hogy egyszerűen állítsa be a `offset` lekérdezési paramétert a `nextOffset` érték.  
  
- Adattípusát módosítani a `relatedVideos` mezőjét a `Video[]` való [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videosmodule) (lásd: [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videodetails)).

