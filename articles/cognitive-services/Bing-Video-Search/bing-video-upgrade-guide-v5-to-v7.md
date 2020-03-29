---
title: A Bing Video Search API 5-ös frissítése a v7-re
titleSuffix: Azure Cognitive Services
description: Azonosítja az alkalmazás azon részeit, amelyeket frissítenie kell a 7-es verzió használatához.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 5dc4c870ae8dbe9f082456d738836aced1271732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68500734"
---
# <a name="video-search-api-upgrade-guide"></a>Videokeresési API frissítési útmutatója

Ez a frissítési útmutató azonosítja a Bing Video Search API 5-ös és 7-es verziója közötti változásokat. Ebben az útmutatóban azonosíthatja az alkalmazás azon részeit, amelyeket frissítenie kell a 7-es verzió használatához.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószáma v5-ről v7-re változott. Például: `https://api.cognitive.microsoft.com/bing/v7.0/videos/search`.

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

### <a name="query-parameters"></a>Lekérdezési paraméterek

- A `modulesRequested` lekérdezési paramétert [átnevezték modulokra.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested)  

### <a name="object-changes"></a>Objektummódosítások

- A `nextOffsetAddCount` [Videók](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) mezőátnevezése a névre. `nextOffset` Az eltolás használatának módja is megváltozott. Korábban az [eltolási](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) lekérdezésparamétert az értékre és az `nextOffset` előző eltolási értékre és az eredményben szereplő videók számára állította be. Most egyszerűen állítsa `offset` be a `nextOffset` lekérdezési paramétert az értékre.  
  
- A `relatedVideos` mező adattípusát `Video[]` [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videosmodule) értékre módosította (lásd: [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails)).

