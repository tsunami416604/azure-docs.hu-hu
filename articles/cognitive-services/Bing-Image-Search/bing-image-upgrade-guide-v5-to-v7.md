---
title: Frissítés Bing Image Search API V5-ről a v7-re
titleSuffix: Azure Cognitive Services
description: Ez a frissítési útmutató a Bing Image Search API 5. és 7. verziójának változásait ismerteti. Ez az útmutató segítséget nyújt az alkalmazás azon részeinek azonosításához, amelyeket frissítenie kell a 7-es verzió használatához.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "68883494"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Bing Image Search API v7 frissítési útmutató

Ez a frissítési útmutató a Bing Image Search API 5. és 7. verziójának változásait azonosítja. Ez az útmutató segítséget nyújt az alkalmazás azon részeinek azonosításához, amelyeket frissítenie kell a 7-es verzió használatához.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószáma a V5 verzióról a v7-re változott. Például https: \/ /API.Cognitive.microsoft.com/Bing/ \* \* v 7.0 * */images/Search.

### <a name="error-response-objects-and-error-codes"></a>Hibás objektumok és hibakódok

- Az összes sikertelen kérelemnek tartalmaznia kell egy `ErrorResponse` objektumot a válasz törzsében.

- A következő mezőket adta hozzá az `Error` objektumhoz.  
  - `subCode`&mdash;A hibakódot különálló gyűjtőbe particionálja, ha lehetséges
  - `moreDetails`&mdash;További információ a mezőben leírt hibáról `message`


- Az V5-hibakódokat a következő lehetséges `code` és értékekkel cserélte le `subCode` .

|Code|Alkód|Leírás
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Nincs implementálva|A Bing visszaadja a ServerError, ha az alkódok bármely feltétele teljesül. A válasz tartalmazza ezeket a hibákat, ha a HTTP-állapotkód 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokkolva|A Bing visszaadja a InvalidRequest, ha a kérelem bármely része érvénytelen. Például hiányzik egy kötelező paraméter, vagy a paraméter értéke érvénytelen.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a HTTP-állapotkód 400.<br/><br/>Ha a hiba HttpNotAllowed, a 410-es HTTP-állapotkód.
|RateLimitExceeded||A Bing visszaadja a RateLimitExceeded, amikor a másodpercenkénti lekérdezések (QPS) vagy a havi lekérdezés (QPM) kvóta meghaladja a kvótát.<br/><br/>A Bing a 429-as HTTP-állapotkódot adja vissza, ha túllépte a QPS és a 403 értéket, ha túllépte a QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|A Bing visszaadja a InvalidAuthorization, ha a Bing nem tudja hitelesíteni a hívót. Például `Ocp-Apim-Subscription-Key` hiányzik a fejléc, vagy az előfizetési kulcs érvénytelen.<br/><br/>A redundancia akkor fordul elő, ha egynél több hitelesítési módszert ad meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|A Bing visszaadja a InsufficientAuthorization, ha a hívónak nincs engedélye az erőforrás elérésére. Ez akkor fordulhat elő, ha az előfizetési kulcs le van tiltva vagy lejárt. <br/><br/>Ha a hiba InsufficientAuthorization, a HTTP-állapotkód 403.

- A következő leképezi az előző hibakódokat az új kódokra. Ha az V5-hibakódok függőségét vette igénybe, frissítse a kódját ennek megfelelően.

|5. verzió kódja|7-es verzió kódja. alkód
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Disabled (Letiltva)|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError. UnexpectedError
DataSourceErrors|ServerError. ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Nincs implementálva|ServerError. nincs implementálva
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blokkolva|InvalidRequest. Blocked



### <a name="query-parameters"></a>Lekérdezési paraméterek

- Átnevezte a `modulesRequested` lekérdezési paramétert a [modulokra](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Átnevezte a megjegyzéseket a címkékre. Lásd: [modulok](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) lekérdezési paramétere címkékre.  

- Módosította a ShoppingSources szűrő értékének támogatott piacainak listáját csak en-US értékre. Lásd: [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Képelemzési változások

- Átnevezte a `annotations` [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) mezőjét a következőre: `imageTags` .  

- Átnevezte a `AnnotationModule` [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule)objektumot.  

- Átnevezte az `Annotation` objektumot a [címkére](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag), és eltávolította a `confidence` mezőt.  

- Átnevezte a `insightsSourcesSummary` [rendszerkép](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) objektum mezőjét a következőre: `insightsMetadata` .  

- Átnevezte a `InsightsSourcesSummary` [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata)objektumot.  

- A végpont hozzáadva `https://api.cognitive.microsoft.com/bing/v7.0/images/details` . Ezzel a végponttal az/images/Search végpont helyett képelemzéseket kérhet. Lásd: [Képelemzések](./image-insights.md).

- A következő lekérdezési paraméterek mostantól csak a `/images/details` végpontnál érvényesek.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [modulok](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [Cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [Cat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [az](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Átnevezte a `ImageInsightsResponse` [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights)objektumot.  

- Módosította a következő mezők adattípusait a [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) objektumban.  

    -   Módosította a mező típusát a `relatedCollections` `ImageGallery[]` [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule)értékre.  

    -   Módosította a mező típusát a `pagesIncluding` `Image[]` [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)értékre.  

    -   Módosította a mező típusát a `relatedSearches` `Query[]` [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule)értékre.  

    -   Módosította a mező típusát a `recipes` `Recipe[]` [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule)értékre.  

    -   Módosította a mező típusát a `visuallySimilarImages` `Image[]` [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)értékre.  

    -   Módosította a mező típusát a `visuallySimilarProducts` `ProductSummaryImage[]` [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)értékre.  

    -   Eltávolította az `ProductSummaryImage` objektumot, és áthelyezte a termékkel kapcsolatos mezőket a [rendszerkép](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) objektumba. Az `Image` objektum csak akkor tartalmazza a termékkel kapcsolatos mezőket, ha a képet egy képelemzési válaszban szereplő, hasonló termékek részeként jeleníti meg.  

    -   Módosította a mező típusát a `recognizedEntityGroups` `RecognizedEntityGroup[]` [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule)értékre.  

-   Átnevezte a `categoryClassification` [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) mezőjét `annotations` , és módosította a típusát `AnnotationsModule` .  

### <a name="images-answer"></a>A képek válasza

-   Eltávolította a displayShoppingSourcesBadges és a displayRecipeSourcesBadges mezőket a [képekből](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   Átnevezte a `nextOffsetAddCount` [képek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) mezőjét a következőre: `nextOffset` . Az eltolás használatának módja is megváltozott. Korábban az [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) lekérdezési paraméter `nextOffsetAddCount` értékét az előző eltolási értékre és az eredményben szereplő rendszerképek számára kell beállítani. Most `offset` az értékre kell állítani `nextOffset` .  


## <a name="non-breaking-changes"></a>Nem törhető változások

### <a name="query-parameters"></a>Lekérdezési paraméterek

- A rendszer transzparensként adja hozzá a lehetséges [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) -szűrő értékét. Az átlátszó szűrő csak az átlátszó háttérrel rendelkező képeket adja vissza.

- Hozzáadta a bármely lehetséges [licenckulcs](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) -értéket. A bármely szűrő csak a licenccel rendelkező lemezképeket adja vissza.

- Hozzáadta a [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) és a [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) lekérdezési paramétereket. Ezekkel a szűrőkkel a képfájlok egy tartományán belüli képeket adhatnak vissza.  

- Hozzáadta a [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), a [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), a [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth)és a [MinWidth értékénél](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) lekérdezési paramétereket. Ezekkel a szűrőkkel a képek a magasság és a szélesség tartományán belül adhatók vissza.  

### <a name="object-changes"></a>Objektum változásai

- Hozzáadta a `description` és a `lastUpdated` mezőket az [ajánlat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) objektumhoz.  

- Hozzáadta a `name` mezőt a [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery) objektumhoz.  

- Hozzáadva `similarTerms` a [képek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) objektumhoz. Ez a mező a felhasználó lekérdezési karakterláncához hasonló kifejezések listáját tartalmazza.  
