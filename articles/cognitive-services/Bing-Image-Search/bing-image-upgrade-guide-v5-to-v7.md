---
title: Frissítés a Bing Image Search API v5 7-es verzióját,
titleSuffix: Azure Cognitive Services
description: A frissítési útmutató 5-ös verzió és a Bing Image Search API 7-es verziója között változik. Ez az útmutató segítségével azonosíthatja, hogy 7-es verzió használatához frissítenie kell az alkalmazás részei.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: b3675821cd5565df409fac209b6763845460df1c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163376"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>A Bing Image Search API 7-es verziójával a verziófrissítési útmutató

A frissítési útmutató 5-ös verzió és a Bing Image Search API 7-es verziója közötti különbségek azonosítja. Ez az útmutató segítségével azonosíthatja, hogy 7-es verzió használatához frissítenie kell az alkalmazás részei.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószám V5-ös verziója megváltozott a 7-es verziója. Ha például https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/images/search.

### <a name="error-response-objects-and-error-codes"></a>Hiba történt a válasz objektumok és hibakódok

- Most már tartalmazza az összes sikertelen kérelem egy `ErrorResponse` objektum a válasz törzsében.

- A következő mezőket hozzáadni a `Error` objektum.  
  - `subCode`&mdash;Hibakód particionálja be különálló gyűjtők, ha lehetséges
  - `moreDetails`&mdash;A leírt hibával kapcsolatos további információkat a `message` mező


- Az 5-ös verziójának hibakódok cseréli a következő lehetséges `code` és `subCode` értékeket.

|Kód|SubCode|Leírás
|-|-|-
|Kiszolgálóhibái|UnexpectedError<br/>ResourceError<br/>Nincs implementálva|A Bing Kiszolgálóhibái adja vissza, minden alkalommal, amikor az alárendelt kód feltételek bármelyike teljesül. A válasz tartalmazza ezeket a hibákat, ha a HTTP-állapotkód: 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Letiltva|A Bing InvalidRequest adja vissza, ha bármelyik részét a kérés érvénytelen, nem. Például egy kötelező paraméter hiányzik, vagy egy paraméter értéke nem érvényes.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a a HTTP-állapotkód: 400.<br/><br/>Ha a hiba HttpNotAllowed, 410-es HTTP-állapotkódot.
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
Letiltva|InvalidRequest.Blocked



### <a name="query-parameters"></a>Lekérdezési paraméterek

- Átnevezték a `modulesRequested` lekérdezési paramétert [modulok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).  

- A jegyzetek a címkék neve. Lásd: [modulok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) lekérdezési paraméter, a címkék közé.  

- Támogatott piac ShoppingSources szűrő értéke listája csak en-US módosult. Lásd: [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Kép insights módosítások

- Átnevezték a `annotations` mezőjében [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) való `imageTags`.  

- Átnevezték a `AnnotationModule` az objektum [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule).  

- Átnevezték a `Annotation` az objektum [címke](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag), és eltávolítja a `confidence` mező.  

- Átnevezték a `insightsSourcesSummary` mezőjében a [kép](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) az objektum `insightsMetadata`.  

- Átnevezték a `InsightsSourcesSummary` az objektum [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata).  

- Hozzáadja a `https://api.cognitive.microsoft.com/bing/v7.0/images/details` végpont. Ennek a végpontnak a kérelem hasznos képadatok használja a/képek/search-végpont helyett. Lásd: [Insights kép](./image-insights.md).

- Most már csak érvényes lekérdezési paraméterek a `/images/details` végpont.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [Modulok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [ct](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  

- Átnevezték a `ImageInsightsResponse` az objektum [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  

- A következő mező adattípusát a [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights) objektum.  

    -   Módosított típusától a `relatedCollections` mezőt `ImageGallery[]` való [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Módosított típusától a `pagesIncluding` mezőt `Image[]` való [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Módosított típusától a `relatedSearches` mezőt `Query[]` való [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Módosított típusától a `recipes` mezőt `Recipe[]` való [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule).  

    -   Módosított típusától a `visuallySimilarImages` mezőt `Image[]` való [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Módosított típusától a `visuallySimilarProducts` mezőt `ProductSummaryImage[]` való [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Eltávolítja a `ProductSummaryImage` objektumra, és azokat a termékkel kapcsolatos mezők áthelyezése a [kép](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objektum. A `Image` objektum a termékkel kapcsolatos mezőket tartalmazza, csak akkor, ha a kép egy kép insight válaszul vizuálisan hasonló termékek része.  

    -   Módosított típusától a `recognizedEntityGroups` mezőt `RecognizedEntityGroup[]` való [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Átnevezték a `categoryClassification` mezőjében [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) való `annotations`, és a típus módosítva `AnnotationsModule`.  

### <a name="images-answer"></a>Képek válasz

-   Eltávolítja a displayShoppingSourcesBadges és displayRecipeSourcesBadges mezők [lemezképek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images).  

-   Átnevezték a `nextOffsetAddCount` mezőjében [lemezképek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) való `nextOffset`. Az eltolás módon is megváltozott. Korábban, megadhatja a [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) lekérdezési paraméter, a `nextOffsetAddCount` értéke plusz a korábbi eltolási értéke plusz az eredményben képek számát. Most beállíthat `offset` , a `nextOffset` értéket.  


## <a name="non-breaking-changes"></a>A nem kompatibilitástörő változások

### <a name="query-parameters"></a>Lekérdezési paraméterek

- Csak egy lehet átlátszó hozzáadott [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) szűrése a értékét. A transzparens szűrő csak azokat a rendszerképeket, áttetsző háttérrel adja vissza.

- Hozzáadott bármely, a lehető [licenc](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) szűrése a értékét. Az összes szűrő csak a licenc alatt álló képeket ad vissza.

- Hozzáadja a [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) és [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) lekérdezési paramétereket. Ezek a szűrők segítségével képeket fájlméretek tartományon belül.  

- Hozzáadja a [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth), [MinWidth értékénél](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth) lekérdezési paramétereket. Ezek a szűrők segítségével képeket magasságát és szélességét tartományon belül.  

### <a name="object-changes"></a>Objektum módosítása

- Hozzáadja a `description` és `lastUpdated` a mezők a [ajánlat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer) objektum.  

- Hozzáadja a `name` mezőt a [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery) objektum.  

- Hozzáadott `similarTerms` , a [lemezképek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objektum. Ebben a mezőben a feltételeket a jelentése a felhasználó lekérdezési karakterlánchoz hasonló listáját tartalmazza.  
