---
title: Frissítés a Bing keresési API v5 v7 a webes |} Microsoft Docs
description: Azonosítja a 7-es verzió használatához frissítenie kell az alkalmazás részei.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 01/15/2017
ms.author: scottwhi
ms.openlocfilehash: 155297f230c0ee02d6fa49d6d35eb24d9941f29b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348971"
---
# <a name="web-search-api-upgrade-guide"></a>Webalkalmazás keresési API-frissítési útmutató

Az frissítési útmutató meghatározza azokat a változtatásokat, 5-ös verzió és a webes Bing keresési API 7-es verziója között. Ez az útmutató segítségével azonosíthatja a 7-es verzió használatához frissítenie kell az alkalmazás részei.

## <a name="breaking-changes"></a>Meghibásodást okozó változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószám v7 v5 változik. Például a https:\/\/api.cognitive.microsoft.com/bing/**v7.0**  /keresni.

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
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing InsufficientAuthorization adja vissza, ha a hívó nem jogosult az erőforrás elérésére. Ez a hiba akkor fordulhat elő, ha az Előfizetés kulcs le van tiltva, vagy lejárt. <br/><br/>A hiba InsufficientAuthorization, a HTTP-állapotkód akkor 403-as.

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


## <a name="non-breaking-changes"></a>Nem-jelentős változásokat  

### <a name="headers"></a>Fejlécek

- A választható hozzáadott [Pragma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#pragma) kérelemfejlécet. Alapértelmezés szerint a Bing gyorsítótárazott tartalom visszaadása, ha elérhető. Gyorsítótárazott tartalom visszaküldésével a Bing megakadályozása érdekében állítsa a Pragma fejléc no-cache (például Pragma: no-cache).

### <a name="query-parameters"></a>Lekérdezési paraméterek

- Hozzáadva a [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) lekérdezési paraméter. Ez a paraméter használatával adja meg a választ, a válasz felvenni kívánt számát. A kérdésekre adott válaszokat rangsorolási alapján választják ki. Ha ez a paraméter értéke például három (3), a válasz a felső három rangsorolt kérdésekre adott válaszokat tartalmazza.  
  
- Hozzáadva a [előléptetni](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) lekérdezési paraméter. Ezzel a paraméterrel együtt `answerCount` egy vagy több válasz típust, függetlenül azok rangsorolási explicit módon felvenni. Például a lemezképeket és videók támogatására be a válasz állíthat előléptetni *videók, képek*. Szemben nem tartoznak bele az előléptetni kívánt válaszokat listáját a `answerCount` korlátot. Például ha `answerCount` 2 és `promote` értéke *videók, képek*, a válasz weblapjait, híreket, videók és lemezképek tartalmazhat.

### <a name="object-changes"></a>Objektummódosítások

- Hozzáadva a `someResultsRemoved` mezőről az [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) objektum. A mezőben egy logikai érték, amely jelzi, hogy a válasz kizárását egyes eredményeket a Webes válasz.  

