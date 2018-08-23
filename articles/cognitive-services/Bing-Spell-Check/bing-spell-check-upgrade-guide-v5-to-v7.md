---
title: Frissítse a Bing Spell Check API v5 7-es verziója a |} A Microsoft Docs
description: Azonosítja, hogy 7-es verzió használatához frissítenie kell az alkalmazás részei.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7DC8FB29-4732-47D8-824B-CF2D7AEBA07B
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: article
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: 305139e45ee93614eab17c5798cb1105e3e8f8cb
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "41987577"
---
# <a name="spell-check-api-upgrade-guide"></a>Spell Check API frissítési útmutató

A frissítési útmutató 5-ös verzió és a Bing Spell Check API 7-es verziója közötti különbségek azonosítja. Ez az útmutató segítségével azonosíthatja, hogy 7-es verzió használatához frissítenie kell az alkalmazás részei.

## <a name="breaking-changes"></a>Meghibásodást okozó változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószám V5-ös verziója megváltozott a 7-es verziója. Például: `https://api.cognitive.microsoft.com/bing/v7.0/spellcheck`.

### <a name="error-response-objects-and-error-codes"></a>Hiba történt a válasz objektumok és hibakódok

- Most már tartalmazza az összes sikertelen kérelem egy `ErrorResponse` objektum a válasz törzsében.

- A következő mezőket hozzáadni a `Error` objektum.  
  - `subCode`&mdash;Hibakód particionálja be különálló gyűjtők, ha lehetséges
  - `moreDetails`&mdash;A leírt hibával kapcsolatos további információkat a `message` mező
   

- Az 5-ös verziójának hibakódok cseréli a következő lehetséges `code` és `subCode` értékeket.  
  
|Kód|Alkód|Leírás
|-|-|-
|Kiszolgálóhibái|UnexpectedError<br/>ResourceError<br/>NotImplemented|A Bing Kiszolgálóhibái adja vissza, minden alkalommal, amikor a alkód feltételek bármelyike teljesül. A válasz tartalmazza ezeket a hibákat, ha a HTTP-állapotkód: 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Letiltva|A Bing InvalidRequest adja vissza, ha bármelyik részét a kérés nem érvényes. Például egy kötelező paraméter hiányzik, vagy egy paraméter értéke nem érvényes.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a a HTTP-állapotkód: 400.<br/><br/>Ha a hiba HttpNotAllowed, 410-es HTTP-állapotkódot.
|RateLimitExceeded||Minden alkalommal, amikor a lekérdezések másodpercenkénti (lekérdezési QPS) és a lekérdezések száma (QPM) havi kvótát túllépi a Bing RateLimitExceeded adja vissza.<br/><br/>A Bing 429-es HTTP-állapotkód: adja vissza, ha túllépte QPS és a 403-as, QPM túllépése.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|A Bing InvalidAuthorization adja vissza, ha a Bing a hívó nem tudja hitelesíteni. Ha például a `Ocp-Apim-Subscription-Key` fejléc hiányzik, vagy az előfizetési kulcs nem érvényes.<br/><br/>A redundancia akkor fordul elő, ha egynél több hitelesítési módszer adja meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód: a 401-es.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|A Bing InsufficientAuthorization adja vissza, ha a hívónak nincs engedélye az erőforrás eléréséhez. Ez akkor fordulhat elő, ha az előfizetési kulcs le lett tiltva, vagy lejárt. <br/><br/>Ha a hiba InsufficientAuthorization, a HTTP-állapotkód: a 403-as.

- A következő képez le az előző hibakódok új kódokkal. Ha függőséget az 5-ös verziójának hibakódok készített, ennek megfelelően frissülnek a kódot.  
  
|5-ös verzió kód|7-es verzió code.subCode
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
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Letiltva|InvalidRequest.Blocked

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Használati és megjelenítési követelményeinek](./UseAndDisplayRequirements.md)
