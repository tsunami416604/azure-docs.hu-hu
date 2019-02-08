---
title: Frissítse a Bing News Search API V5-ös verziója a 7-es verziója
titlesuffix: Azure Cognitive Services
description: Azonosítja, hogy 7-es verzió használatához frissítenie kell az alkalmazás részei.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 04c457fba5cb32cc1312ffac2c2f7c1470b5a46b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878442"
---
# <a name="news-search-api-upgrade-guide"></a>A verziófrissítési útmutató News Search API

A frissítési útmutató 5-ös verzió és a Bing News Search API 7-es verziója közötti különbségek azonosítja. Ez az útmutató segítségével azonosíthatja, hogy 7-es verzió használatához frissítenie kell az alkalmazás részei.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószám V5-ös verziója megváltozott a 7-es verziója. Ha például https://api.cognitive.microsoft.com/bing/ **v7.0**  /news/keresés.

### <a name="error-response-objects-and-error-codes"></a>Hiba történt a válasz objektumok és hibakódok

- Most már tartalmazza az összes sikertelen kérelem egy `ErrorResponse` objektum a válasz törzsében.

- A következő mezőket hozzáadni a `Error` objektum.  
  - `subCode`&mdash;Hibakód particionálja be különálló gyűjtők, ha lehetséges
  - `moreDetails`&mdash;A leírt hibával kapcsolatos további információkat a `message` mező

- Az 5-ös verziójának hibakódok cseréli a következő lehetséges `code` és `subCode` értékeket.

|Kód|SubCode|Leírás
|-|-|-
|Kiszolgálóhibái|UnexpectedError<br/>ResourceError<br/>Nincs implementálva|A Bing Kiszolgálóhibái adja vissza, minden alkalommal, amikor az alárendelt kód feltételek bármelyike teljesül. A válasz tartalmazza ezeket a hibákat, ha a HTTP-állapotkód: 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokkolt|A Bing InvalidRequest adja vissza, ha bármelyik részét a kérés érvénytelen, nem. Például egy kötelező paraméter hiányzik, vagy egy paraméter értéke nem érvényes.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a a HTTP-állapotkód: 400.<br/><br/>Ha a hiba HttpNotAllowed, 410-es HTTP-állapotkódot.
|RateLimitExceeded||Minden alkalommal, amikor a lekérdezések másodpercenkénti (lekérdezési QPS) és a lekérdezések száma (QPM) havi kvótát túllépi a Bing RateLimitExceeded adja vissza.<br/><br/>A Bing 429-es HTTP-állapotkód: adja vissza, ha túllépte QPS és a 403-as, QPM túllépése.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|A Bing InvalidAuthorization adja vissza, ha a Bing a hívó nem tudja hitelesíteni. Ha például a `Ocp-Apim-Subscription-Key` fejléc hiányzik, vagy az előfizetési kulcs nem érvényes.<br/><br/>A redundancia akkor fordul elő, ha egynél több hitelesítési módszer adja meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód: a 401-es.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|A Bing InsufficientAuthorization adja vissza, ha a hívó nem rendelkezik engedéllyel az erőforrás eléréséhez. Ez akkor fordulhat elő, ha az előfizetési kulcs le lett tiltva, vagy lejárt. <br/><br/>Ha a hiba InsufficientAuthorization, a HTTP-állapotkód: a 403-as.

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
Blokkolt|InvalidRequest.Blocked

### <a name="object-changes"></a>Objektum módosítása

- Hozzáadja a `contractualRules` mezőt a [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objektum. A `contractualRules` mezőt kell követnie (például a cikk tesznek elérhetővé; ilyenek) szabályok listáját tartalmazza. Telepítenie kell a megadott attribution `contractualRules` használata helyett `provider`. A cikk tartalmazza `contractualRules` csak akkor, ha a [Web Search API](../bing-web-search/search-the-web.md) válasz tartalmaz egy News választ.

## <a name="non-breaking-changes"></a>Nem kompatibilitástörő változások

### <a name="query-parameters"></a>Lekérdezési paraméterek

- Termékek hozzáadva, amely adhatja meg a lehetséges értéke a [kategória](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) lekérdezési paramétert. Lásd: [piacok szerint kategóriák](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).

- Hozzáadja a [rendezési szempontjainak helyi](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortby) lekérdezési paraméter, amely népszerű témakörök a legutóbbi van legelöl a dátum szerint rendezve adja vissza.

- Hozzáadja a [óta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#since) lekérdezési paraméter, amely népszerű témakörök, vagy azt követően a megadott Unix alapidőpont időbélyeg Bing által felderített adja vissza.

### <a name="object-changes"></a>Objektum módosítása

- Hozzáadja a `mentions` mezőt a [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objektum. A `mentions` mező tartalmazza azokat az entitásokat (azok a személyek vagy helyek), a cikkben található.

- Hozzáadja a `video` mezőt a [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objektum. A `video` mező tartalmaz egy videót, amely a hír kapcsolódik. A videó kisebb, mint egy \<iframe\> is beágyazható, vagy a mozgásban lévő adatoknak egyaránt miniatűr.

- Hozzáadja a `sort` mezőt a [hírek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news) objektum. A `sort` mezőt jeleníti meg a cikk a rendezési sorrend. Például a cikkeket a relevancia alapján végzett (alapértelmezés) vagy a dátum szerint vannak rendezve.

- Hozzáadja a [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortvalue) objektum, amely a rendezési sorrend határozza meg. A `isSelected` mező jelzi, hogy használható-e a válasz a rendezési sorrendet. Ha **igaz**, a válasz használni a rendezési sorrendet. Ha `isSelected` van **hamis**, az URL-címet is használhatja a `url` mező eltérő rendezési sorrend kéréséhez.
