---
title: A Bing Autosuggest API 5-ös frissítése a v7-re
titleSuffix: Azure Cognitive Services
description: Azonosítja az alkalmazás azon részeit, amelyeket frissítenie kell a 7-es verzió használatához.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 5249a3a1f51eea2ecd0999d71c6b08fdacf37a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68405413"
---
# <a name="autosuggest-api-upgrade-guide"></a>Automatikus api-frissítési útmutató

Ez a frissítési útmutató azonosítja a Bing Autosuggest API 5-ös és 7-es verziója közötti változásokat. Ebben az útmutatóban frissítheti az alkalmazást a 7-es verzióhasználatára.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószáma v5-ről v7-re változott. Például https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/Javaslatok.

### <a name="error-response-objects-and-error-codes"></a>Hibaválasz-objektumok és hibakódok

- Minden sikertelen kérelemnek tartalmaznia kell egy `ErrorResponse` objektumot a választörzsben.

- A következő mezőket `Error` adta hozzá az objektumhoz.  
  - `subCode`&mdash;A hibakódot különálló gyűjtőkre válaszolja, ha lehetséges
  - `moreDetails`&mdash;További információ a `message` mezőben leírt hibáról

- A v5 hibakódokat a `code` következő `subCode` lehetséges és értékekre cserélte.

|Kód|Alkód|Leírás
|-|-|-
|Kiszolgálóhiba|Váratlan hiba<br/>Erőforráshiba<br/>Nincs megvalósítva|A Bing a ServerError-ot adja vissza, ha az alkód feltételek bármelyike bekövetkezik. A válasz tartalmazza ezeket a hibákat, ha a HTTP-állapotkód 500.
|Érvénytelen kérelem|Paraméter hiányzik<br/>ParameterInvalidValue érték<br/>HttpNem engedélyezett<br/>Blokkolva|A Bing az InvalidRequest függvényt adja vissza, ha a kérés bármely része érvénytelen. Például egy szükséges paraméter hiányzik, vagy egy paraméterérték érvénytelen.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a HTTP-állapotkód 400.<br/><br/>Ha a hiba HttpNotAllowed, a HTTP-állapotkód 410.
|RateLimitExceeded||A Bing visszaadja a RateLimitExceeded értéket, ha túllépi a lekérdezések másodpercenkénti (QPS) vagy lekérdezések havonta (QPM) kvótát.<br/><br/>A Bing a 429-es HTTP-állapotkódot adja vissza, ha túllépte a QPS-t, és a 403-at, ha túllépte a QPM protokollt.
|InvalidAuthorization (Érvénytelen engedélyezés)|Engedély hiányzik<br/>EngedélyezésRedundancy|A Bing invalidAuthorization értéket ad vissza, ha a Bing nem tudja hitelesíteni a hívót. Például a `Ocp-Apim-Subscription-Key` fejléc hiányzik, vagy az előfizetési kulcs érvénytelen.<br/><br/>A redundancia akkor következik be, ha egynél több hitelesítési módszert ad meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód 401.
|Elégtelen engedélyezés|Engedélyezésletiltva<br/>Engedélyezés Lejárt|A Bing nem licencet ad vissza, ha a hívónak nincs engedélye az erőforrás eléréséhez. Ez akkor fordulhat elő, ha az előfizetési kulcs le van tiltva, vagy lejárt. <br/><br/>Ha a hiba insufficientAuthorization, a HTTP-állapotkód 403.

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Felhasználási és megjelenítési követelmények](./UseAndDisplayRequirements.md)
