---
title: Frissítés a Bing Image Search API-ról az 5-ös v5-ről a v7-re
titleSuffix: Azure Cognitive Services
description: Ez a frissítési útmutató a Bing Image Search API 5-ös és 7-es verziója közötti változásokat ismerteti. Ebben az útmutatóban azonosíthatja az alkalmazás azon részeit, amelyeket frissítenie kell a 7-es verzió használatához.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: c4c6b95996206cfb38ea3f77b89c3ebe3c2c0026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68883494"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Bing Image Search API v7 frissítési útmutató

Ez a frissítési útmutató azonosítja a Bing Image Search API 5-ös és 7-es verziója közötti változásokat. Ebben az útmutatóban azonosíthatja az alkalmazás azon részeit, amelyeket frissítenie kell a 7-es verzió használatához.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószáma v5-ről v7-re változott. Például https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search.

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

- A `modulesRequested` lekérdezési paramétert [átnevezték modulokra.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  

- A Jegyzetek átnevezése Címkékre. Lásd: [modulok](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) lekérdezési paraméter címkék.  

- A ShoppingSources szűrőértékének listáját csak en-US-ra változtatta. Lásd [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>A képelemzés változásai

- Az `annotations` [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) mezőátnevezése a névre. `imageTags`  

- Az `AnnotationModule` objektumot [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule)névre keresztelték át.  

- Az `Annotation` objektumot [átneveztük Tag](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag) `confidence` névre, és eltávolítottuk a mezőt.  

- A [Kép](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) objektum `insightsMetadata` `insightsSourcesSummary` mezőjének átnevezése a programra.  

- Az objektum `InsightsSourcesSummary` átnevezése [InsightsMetadata névre.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata)  

- Hozzáadva `https://api.cognitive.microsoft.com/bing/v7.0/images/details` a végpont. Ezzel a végpontkal a /images/search endpoint helyett képelemzéseket kérhet. Lásd: [Képelemzések](./image-insights.md).

- A következő lekérdezési paraméterek most `/images/details` már csak a végpontmal érvényesek.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [Modulok](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [Cab](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [Cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [Macska](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [Ct](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Az `ImageInsightsResponse` objektumot [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights)névre keresztelték át.  

- Módosította az [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) objektum következő mezőinek adattípusait.  

    -   A `relatedCollections` mező típusát `ImageGallery[]` [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule)-ra változtatta.  

    -   A `pagesIncluding` mező típusát `Image[]` [Képmodulra](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)változtatta.  

    -   A `relatedSearches` mező típusát `Query[]` [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule)-ra változtatta.  

    -   A `recipes` mező típusát `Recipe[]` [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule)-ra változtatta.  

    -   A `visuallySimilarImages` mező típusát `Image[]` [Képmodulra](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)változtatta.  

    -   A `visuallySimilarProducts` mező típusát `ProductSummaryImage[]` [Képmodulra](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)változtatta.  

    -   Eltávolította az `ProductSummaryImage` objektumot, és áthelyezte a termékkel kapcsolatos mezőket a [Kép](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) objektumba. Az `Image` objektum csak akkor tartalmazza a termékkel kapcsolatos mezőket, ha a kép vizuálisan hasonló termékek részeként szerepel a képbetekintési válaszban.  

    -   A `recognizedEntityGroups` mező típusát `RecognizedEntityGroup[]` [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule)-ra változtatta.  

-   Az `categoryClassification` [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) mezőjét `annotations`átnevezték a programra, és a típusát a-ra `AnnotationsModule`változtatták.  

### <a name="images-answer"></a>Képek válasz

-   Eltávolította a displayShoppingSourcesBadges és displayRecipeSourcesBadges mezők [képek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   A `nextOffsetAddCount` [Képek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) mező átnevezése a névre. `nextOffset` Az eltolás használatának módja is megváltozott. Korábban az [eltolási](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) `nextOffsetAddCount` lekérdezésparamétert az előző eltolási értékkel és az eredményben lévő képek számával növelt értékre állította be. Most állítsa `offset` be `nextOffset` az értéket.  


## <a name="non-breaking-changes"></a>Nem törik módosítások

### <a name="query-parameters"></a>Lekérdezési paraméterek

- Hozzáadott Átlátszó, mint egy lehetséges [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) szűrő értéke. Az Átlátszó szűrő csak átlátszó háttérrel rendelkező képeket ad vissza.

- Hozzáadva az Any, mint lehetséges [licencszűrő](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) értékét. A Minden szűrő csak licenc alatt álló képeket ad vissza.

- Hozzáadva a [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) és [a minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) lekérdezési paraméterek. Ezekkel a szűrőkkel több fájlméreten belül is visszaküldheti a képeket.  

- Hozzáadva a [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth), [minWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) lekérdezési paraméterek. Ezekkel a szűrőkkel a képeket a magasságok és szélességek tartományán belül küldheti vissza.  

### <a name="object-changes"></a>Objektummódosítások

- Hozzáadva `description` `lastUpdated` a és mezőket az [Ajánlat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) objektumhoz.  

- Hozzáadta `name` a mezőt az [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery) objektumhoz.  

- Hozzáadva `similarTerms` a [Képek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) objektumhoz. Ez a mező a felhasználó lekérdezési karakterláncához hasonló kifejezések listáját tartalmazza.  
