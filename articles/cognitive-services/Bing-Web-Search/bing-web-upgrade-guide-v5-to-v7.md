---
title: Frissítés a API V5-ös verziója a 7-es verzióját – a Bing webes keresési API
titleSuffix: Azure Cognitive Services
description: Határozza meg, mely részei az alkalmazás megkövetelése frissíti a Bing Web Search 7-es API-k használata.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: reference
ms.date: 01/15/2017
ms.author: scottwhi
ms.openlocfilehash: eb84c961d13c5abac7a0c9f426f099d21f034f20
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129743"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Frissítés a Bing webes keresési API v5 a 7-es verziója

A frissítési útmutató 5-ös verzió és a Bing Web Search API 7-es verziója közötti különbségek azonosítja. Ez az útmutató segítségével azonosíthatja, hogy 7-es verzió használatához frissítenie kell az alkalmazás részei.

## <a name="breaking-changes"></a>Meghibásodást okozó változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószám V5-ös verziója megváltozott a 7-es verziója. Ha például https:\/\/api.cognitive.microsoft.com/bing/**v7.0**  /keresése.

### <a name="error-response-objects-and-error-codes"></a>Hiba történt a válasz objektumok és hibakódok

- Most már tartalmazza az összes sikertelen kérelem egy `ErrorResponse` objektum a válasz törzsében.

- A következő mezőket hozzáadni a `Error` objektum.  
  - `subCode`&mdash;Hibakód particionálja be különálló gyűjtők, ha lehetséges
  - `moreDetails`&mdash;A leírt hibával kapcsolatos további információkat a `message` mező


- Az 5-ös verziójának hibakódok cseréli a következő lehetséges `code` és `subCode` értékeket.

|Kód|Alkód|Leírás
|-|-|-
|Kiszolgálóhibái|UnexpectedError<br/>ResourceError<br/>Nincs implementálva|A Bing Kiszolgálóhibái adja vissza, minden alkalommal, amikor az alárendelt kód feltételek bármelyike teljesül. A válasz tartalmazza ezeket a hibákat, ha a HTTP-állapotkód: 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Letiltva|A Bing InvalidRequest adja vissza, ha bármelyik részét a kérés érvénytelen, nem. Például egy kötelező paraméter hiányzik, vagy egy paraméter értéke nem érvényes.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a a HTTP-állapotkód: 400.<br/><br/>Ha a hiba HttpNotAllowed, 410-es HTTP-állapotkódot.
|RateLimitExceeded||Minden alkalommal, amikor a lekérdezések másodpercenkénti (lekérdezési QPS) és a lekérdezések száma (QPM) havi kvótát túllépi a Bing RateLimitExceeded adja vissza.<br/><br/>A Bing 429-es HTTP-állapotkód: adja vissza, ha túllépte QPS és a 403-as, QPM túllépése.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|A Bing InvalidAuthorization adja vissza, ha a Bing a hívó nem tudja hitelesíteni. Ha például a `Ocp-Apim-Subscription-Key` fejléc hiányzik, vagy az előfizetési kulcs nem érvényes.<br/><br/>A redundancia akkor fordul elő, ha egynél több hitelesítési módszer adja meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód: a 401-es.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|A Bing InsufficientAuthorization adja vissza, ha a hívó nem rendelkezik engedéllyel az erőforrás eléréséhez. Ez a hiba akkor fordulhat elő, ha az előfizetési kulcs le lett tiltva, vagy lejárt. <br/><br/>Ha a hiba InsufficientAuthorization, a HTTP-állapotkód: a 403-as.

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
Nincs implementálva|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Letiltva|InvalidRequest.Blocked


## <a name="non-breaking-changes"></a>A nem kompatibilitástörő változások  

### <a name="headers"></a>Fejlécek

- A választható hozzáadott [Pragma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#pragma) kérés fejlécéhez. Alapértelmezés szerint a Bing gyorsítótárazott tartalmat vissza, ha elérhető. Gyorsítótárazott tartalom visszaadó megakadályozni a Bing, állítsa be a Pragma fejléc no-cache (például Pragma: no-cache).

### <a name="query-parameters"></a>Lekérdezési paraméterek

- Hozzáadja a [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) lekérdezési paraméter. Ez a paraméter használatával határozza meg a válasz tartalmazza a kívánt válaszokat. A válaszok rangsorolása alapján választják ki. Például, ha a paraméter értéke három (3), a válasz az első három rangsorolt adott válaszokat tartalmazza.  

- Hozzáadja a [előléptetése](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) lekérdezési paraméter. Ezzel a paraméterrel együtt `answerCount` explicit módon felvenni egy vagy több válasz típusa, függetlenül attól, hogy ennek a területnek. Ha például a képek és videók támogatására, a válasz így állíthatja be előléptetés *videók, képek*. Az előléptetni kívánt válaszokat listája nem számítanak bele a `answerCount` korlátot. Például ha `answerCount` 2 és `promote` értékre van állítva *videók, képek*, a válasz tartalmazhat weblapok, híreket, videókat és lemezképek.

### <a name="object-changes"></a>Objektum módosítása

- Hozzáadja a `someResultsRemoved` mezőt a [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) objektum. A mező egy logikai érték, amely azt jelzi-e a válasz zárva néhány eredmények a webes válaszokat tartalmazza.  
