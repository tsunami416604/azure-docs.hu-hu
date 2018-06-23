---
title: Frissítse a Bing helyesírás-ellenőrzés API v5 való v7 |} Microsoft Docs
description: Azonosítja a 7-es verzió használatához frissítenie kell az alkalmazás részei.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7DC8FB29-4732-47D8-824B-CF2D7AEBA07B
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: article
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: 1b0406641053fac8a4b3f4721728ad3b6c313ba1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346919"
---
# <a name="spell-check-api-upgrade-guide"></a>Helyesírás ellenőrizze API frissítési útmutató

Az frissítési útmutató meghatározza azokat a változtatásokat, 5-ös verzió és a Bing helyesírás-ellenőrzése API 7-es verziója között. Ez az útmutató segítségével azonosíthatja a 7-es verzió használatához frissítenie kell az alkalmazás részei.

## <a name="breaking-changes"></a>Meghibásodást okozó változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószám v7 v5 változik. Például https://api.cognitive.microsoft.com/bing/\ * \*v7.0 ** / a helyesírás-ellenőrzési.

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Használja ki és jelenítheti meg a követelményeknek](./UseAndDisplayRequirements.md)
