---
title: Frissítse a Bing keresési API v5 v7 a kép |} Microsoft Docs
description: Azonosítja a 7-es verzió használatához frissítenie kell az alkalmazás részei.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b4d785eafe9ced6fb12e2dac3282dfd286849eb6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347170"
---
# <a name="image-search-api-upgrade-guide"></a>Kép Search API frissítési útmutató

Az frissítési útmutató meghatározza azokat a változtatásokat, 5-ös verzió és a lemezkép Bing keresési API 7-es verziója között. Ez az útmutató segítségével azonosíthatja a 7-es verzió használatához frissítenie kell az alkalmazás részei.

## <a name="breaking-changes"></a>Meghibásodást okozó változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószám v7 v5 változik. Például https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/images/search.

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

- Átnevezi a `modulesRequested` lekérdezésparaméter a [modulok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules).  
  
- Átnevezi a jegyzetek címkék. Lásd: [modulok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules) lekérdezésparaméter címkékre.  

- En-US csak listáját támogatott ShoppingSources szűrő értéke módosult. Lásd: [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype).  
 

### <a name="image-insights-changes"></a>Kép insights módosítások
  
- Átnevezi a `annotations` mezőjében [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) való `imageTags`.  
  
- Átnevezi a `AnnotationModule` objektum [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule).  
  
- Átnevezi a `Annotation` objektum [címke](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag), és eltávolítja a `confidence` mező.  
  
- Átnevezi a `insightsSourcesSummary` mezőjét a [kép](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objektum `insightsMetadata`.  
  
- Átnevezi a `InsightsSourcesSummary` objektum [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata).  
  
- Hozzáadva a `https://api.cognitive.microsoft.com/bing/v7.0/images/details` végpont. Használni ezt a végpontot, kérelem kép insights/képek/keresési végpont helyett. Lásd: [Insights kép](./image-insights.md). 
  
- A következő lekérdezés paraméterei most már csak érvényes a `/images/details` végpont.  
  
    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [modulok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [CAB-fájl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [CAL](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [autó](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [ki](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  
  
- Átnevezi a `ImageInsightsResponse` objektum [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  
  
- A következő mezők adattípusát módosítani a [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights) objektum.  
  
    -   Milyen típusú megváltozott a `relatedCollections` mezőjét a `ImageGallery[]` való [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule).  
  
    -   Milyen típusú megváltozott a `pagesIncluding` mezőjét a `Image[]` való [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Milyen típusú megváltozott a `relatedSearches` mezőjét a `Query[]` való [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule).  
  
    -   Milyen típusú megváltozott a `recipes` mezőjét a `Recipe[]` való [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule).  
  
    -   Milyen típusú megváltozott a `visuallySimilarImages` mezőjét a `Image[]` való [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Milyen típusú megváltozott a `visuallySimilarProducts` mezőjét a `ProductSummaryImage[]` való [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Eltávolítja a `ProductSummaryImage` objektumra, és a termékkel kapcsolatos mezők áthelyezése a [kép](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objektum. A `Image` objektum a termékkel kapcsolatos mezőket tartalmazza, csak akkor, ha a kép egy kép insight válaszul vizuálisan hasonló termékek részét.  
  
    -   Milyen típusú megváltozott a `recognizedEntityGroups` mezőjét a `RecognizedEntityGroup[]` való [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule).  
  
-   Átnevezi a `categoryClassification` mezőjében [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) való `annotations`, és megváltozott a típus `AnnotationsModule`.  

### <a name="images-answer"></a>Lemezképeket fogadja a hívást

-   Eltávolítja a displayShoppingSourcesBadges és displayRecipeSourcesBadges mezők [képek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images).  
  
-   Átnevezi a `nextOffsetAddCount` mezőjében [képek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) való `nextOffset`. Az eltolás a módon is megváltozott. Korábban, beállíthatja a [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) lekérdezési paramétert a `nextOffsetAddCount` értéke plusz a korábbi eltolási értéke plusz a lemezképek az eredményben számát. Most, hogy állítja `offset` való a `nextOffset` érték.  
    
  
## <a name="non-breaking-changes"></a>Nem-jelentős változásokat

### <a name="query-parameters"></a>Lekérdezési paraméterek
  
- A lehető átlátszó hozzáadott [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) szűrő értéke. A transzparens szűrő csak a képek átlátszó háttérrel adja vissza.

- Hozzáadott bármely, a lehető [licenc](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) szűrő értéke. Az összes szűrő csak a licenc képek adja vissza.
  
- Hozzáadva a [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) és [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) lekérdezési paramétert. Ezek a szűrők segítségével számos különböző méretűek képek vissza.  
  
- Hozzáadva a [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth), [MinWidth értékénél](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth) lekérdezési paramétert. Ezek a szűrők használatával visszaállíthatja a képek magassága és szélessége tartományon belül.  

### <a name="object-changes"></a>Objektummódosítások
  
- Hozzáadva a `description` és `lastUpdated` a mezők a [kínálnak](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer) objektum.  
  
- Hozzáadva a `name` mezőről az [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery) objektum.  
  
- Hozzáadott `similarTerms` számára a [képek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objektum. Ez a mező a jelentése van a felhasználó lekérdezési karakterlánc hasonló feltételek listáját tartalmazza.  
