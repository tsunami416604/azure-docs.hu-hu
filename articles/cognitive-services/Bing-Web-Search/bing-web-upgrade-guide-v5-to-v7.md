---
title: Frissítés API-ról v5-ről v7-re – Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Határozza meg, hogy az alkalmazás mely részeiigényelnek frissítéseket a Bing Web Search v7 API-k használatához.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 2133cd59c524112ae8a77c0a20cbce1d1336a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881306"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Frissítés a Bing Web Search API 5-ös v5-ről v7-re

Ez a frissítési útmutató azonosítja a Bing Web Search API 5-ös és 7-es verziója közötti változásokat. Ebben az útmutatóban azonosíthatja az alkalmazás azon részeit, amelyeket frissítenie kell a 7-es verzió használatához.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószáma v5-ről v7-re változott. Például https:\/\/api.cognitive.microsoft.com/bing/**v7.0**/search.

### <a name="error-response-objects-and-error-codes"></a>Hibaválasz-objektumok és hibakódok

- Minden sikertelen kérelemnek tartalmaznia kell egy `ErrorResponse` objektumot a választörzsben.

- A következő mezőket `Error` adta hozzá az objektumhoz.  
  - `subCode`&mdash;A hibakódot különálló gyűjtőkre válaszolja, ha lehetséges
  - `moreDetails`&mdash;További információ a `message` mezőben leírt hibáról


- A v5 hibakódokat a `code` következő `subCode` lehetséges és értékekre cserélte.

|Kód|Alkód|Leírás
|-|-|-
|Kiszolgálóhiba|Váratlan hiba<br/>Erőforráshiba<br/>Nincs megvalósítva|A Bing a ServerError-ot adja vissza, ha az alkód feltételek bármelyike bekövetkezik. A válasz ezeket a hibákat tartalmazza, ha a HTTP-állapotkód 500.
|Érvénytelen kérelem|Paraméter hiányzik<br/>ParameterInvalidValue érték<br/>HttpNem engedélyezett<br/>Blokkolva|A Bing az InvalidRequest függvényt adja vissza, ha a kérés bármely része érvénytelen. Például egy szükséges paraméter hiányzik, vagy egy paraméterérték érvénytelen.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a HTTP-állapotkód 400.<br/><br/>Ha a hiba HttpNotAllowed, a HTTP-állapotkód 410.
|RateLimitExceeded||A Bing visszaadja a RateLimitExceeded értéket, ha túllépi a lekérdezések másodpercenkénti (QPS) vagy lekérdezések havonta (QPM) kvótát.<br/><br/>A Bing a 429-es HTTP-állapotkódot adja vissza, ha túllépte a QPS-t, és a 403-at, ha túllépte a QPM protokollt.
|InvalidAuthorization (Érvénytelen engedélyezés)|Engedély hiányzik<br/>EngedélyezésRedundancy|A Bing invalidAuthorization értéket ad vissza, ha a Bing nem tudja hitelesíteni a hívót. Például a `Ocp-Apim-Subscription-Key` fejléc hiányzik, vagy az előfizetési kulcs érvénytelen.<br/><br/>A redundancia akkor következik be, ha egynél több hitelesítési módszert ad meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód 401.
|Elégtelen engedélyezés|Engedélyezésletiltva<br/>Engedélyezés Lejárt|A Bing nem licencet ad vissza, ha a hívónak nincs engedélye az erőforrás eléréséhez. Ez a hiba akkor fordulhat elő, ha az előfizetési kulcs le van tiltva, vagy lejárt. <br/><br/>Ha a hiba insufficientAuthorization, a HTTP-állapotkód 403.

- Az alábbiakban az előző hibakódokat az új kódokhoz rendelik. Ha a v5-ös hibakódoktól függ, ennek megfelelően frissítse a kódot.

|5-ös verzió kódja|7-es verziójú kód.alkód
|-|-
|RequestParameterMissing (RequestParameterMissing)|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccess Megtagadva|Elégtelen engedélyezés
Túllépvea kötet|RateLimitExceeded
TúllépveqpsLimit|RateLimitExceeded
Letiltva|InsufficientAuthorization.AuthorizationDisabled
Váratlan hiba|ServerError.UnexpectedError
DataSourceErrors (Adatforráshibák)|ServerError.ResourceError
Engedély hiányzik|InvalidAuthorization.AuthorizationMissing
HttpNem engedélyezett|InvalidRequest.HttpNotallowed
UserAgentMissing|InvalidRequest.ParameterMissing
Nincs megvalósítva|ServerError.NotImplemented
InvalidAuthorization (Érvénytelen engedélyezés)|InvalidAuthorization (Érvénytelen engedélyezés)
InvalidAuthorizationMetódus|InvalidAuthorization (Érvénytelen engedélyezés)
MultipleAuthorizationMetódus|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope (Elégtelen hatókör)|Elégtelen engedélyezés
Blokkolva|ÉrvénytelenKérelem.Letiltva


## <a name="non-breaking-changes"></a>Nem törik módosítások  

### <a name="headers"></a>Fejlécek

- Hozzáadva a választható [Pragma](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) kérelemfejléc. Alapértelmezés szerint a Bing gyorsítótárazott tartalmat ad vissza, ha van ilyen. Ha nem szeretné, hogy a Bing gyorsítótárazott tartalmat adjon vissza, állítsa a Pragma fejlécet no-cache értékre (például Pragma: no-cache).

### <a name="query-parameters"></a>Lekérdezési paraméterek

- Hozzáadva az [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) lekérdezési paraméter. Ezzel a paraméterrel megadhatja, hogy a válaszok száma hány választ tartalmazzon. A válaszokat a rangsorolás alapján választjuk ki. Ha például ezt a paramétert háromra (3) állítja, a válasz az első három rangsorolt választ tartalmazza.  

- Hozzáadva az [előléptetési](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) lekérdezési paraméter. Ezzel a paraméterrel `answerCount` együtt explicit módon egy vagy több választípust is megad, függetlenül azok rangsorolásától. Ha például videókat és képeket szeretne népszerűsíteni a válaszban, akkor *a videókra, képekre*állítja be a népszerűsítést. Az előléptetni kívánt válaszok listája nem `answerCount` számít bele a korlátba. Ha például `answerCount` 2, `promote` és *videókra, képekre*van beállítva, a válasz tartalmazhat weblapokat, híreket, videókat és képeket.

### <a name="object-changes"></a>Objektummódosítások

- Hozzáadva `someResultsRemoved` a mezőt a [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) objektumhoz. A mező logikai értéket tartalmaz, amely azt jelzi, hogy a válasz kizárt-e néhány eredményt a webes válaszból.  
