---
title: 'Oktatóanyag: a rendszerkép részleteinek kinyerése a REST API és a C# – Bing Image Search'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban olyan C#-alkalmazást hozhat létre, amely a Bing Image Search API használatával Kinyeri a képek részleteit.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 12/06/2019
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: 2b51fb8dcc456d895d5c4032bd16e1502ee86f0f
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407081"
---
# <a name="tutorial-extract-image-details-using-the-bing-image-search-api-and-c"></a>Oktatóanyag: Képadatok kinyerése a Bing Image Search API és a C# használatával

A Bing Image Search API-n keresztül több [végpont](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint) is elérhető. A `/details` végpont egy képpel együtt érkező POST-kérelmet fogad el, és különböző adatokat képes visszaadni a képről. A C#-alkalmazás az API használatával elküld egy képet, és megjeleníti a Bing által visszaadott adatokat JSON-objektumok formájában, mint amilyen például a következő:

![[JSON-találatok]](media/cognitive-services-bing-images-api/jsonResult.jpg)

Ez az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Az Image Search `/details` végpont használata `POST` kérelemben
> * A kérelem fejléceinek megadása
> * URL-paraméterek használata eredmények megadásához
> * Képadatok feltöltése és `POST`-kérelem küldése
> * JSON-találatok megjelenítése a konzolban

A minta forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingGetSimilarImages.cs).

## <a name="prerequisites"></a>Előfeltételek

* A [Visual studio 2017 vagy újabb](https://visualstudio.microsoft.com/downloads/)verziójának bármely kiadása.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="construct-an-image-details-search-request"></a>Kérelem létrehozása képek részleteinek kereséséhez

Lentebb látható a `/details` végpont, amely olyan POST-kérelmet fogad el, amelynek a törzse képadatokat tartalmaz. Használhatja az alábbi globális végpontot, vagy az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../cognitive-services/cognitive-services-custom-subdomains.md) végpontot.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```

Egy keresési kérelem URL-címének létrehozásánál a `modules` paraméter a fenti végpontot követi, és megadja, hogy a találatok milyen típusú adatokat fognak tartalmazni:

* `modules=All`
* `modules=RecognizedEntities` (a képen látható személyek és helyek)

Adja meg a `modules=All` paramétert a POST-kérelemben, hogy a kapott JSON-szöveg tartalmazza a következőket:

* `bestRepresentativeQuery` – Bing-lekérdezés, amely a feltöltött képhez hasonló képeket ad vissza
* `detectedObjects` – a képen látható tárgyak
* `image` – a kép metaadatai
* `imageInsightsToken` – token későbbi GET-kérelmekhez, amelyek a `RecognizedEntities` (a képen látható személyek vagy helyek) típusú adatokat nyernek ki a képről.
* `imageTags` – a kép címkéi
* `pagesIncluding` – a képet tartalmazó weblapok
* `relatedSearches` – a kép részletei alapján indított keresések.
* `visuallySimilarImages` – hasonló képek a weben.

Adja meg a `modules=RecognizedEntities` paramétert a POST-kérelemben, hogy csak az `imageInsightsToken` típusú adatokat kapja meg, amelyeket további GET-kérelmekben használhat fel a képen szereplő személyek vagy helyek azonosításához.

## <a name="create-a-webclient-object-and-set-headers-for-the-api-request"></a>WebClient-objektum létrehozása és az API-kérelem fejléceinek beállítása

Hozzon létre egy `WebClient` objektumot, és állítsa be a fejléceket. A Bing Search API-ra irányuló minden kérelemben szerepelnie kell egy `Ocp-Apim-Subscription-Key` fejlécnek. Egy képet feltöltő `POST`-kérelemben emellett meg kell adni a `ContentType: multipart/form-data` fejlécet is.

```javascript
WebClient client = new WebClient();
client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
client.Headers["ContentType"] = "multipart/form-data";
```

## <a name="upload-the-image-and-display-the-results"></a>Kép feltöltése és találatok megjelenítése

A `WebClient` osztály `UpLoadFile()` metódusa formázza meg a `POST`-kérelem adatait, beleértve a `RequestStream` osztály formázását, és a `HttpWebRequest` osztály meghívását.

Hívja meg a `WebClient.UpLoadFile()` metódust a `/details` végponttal és feltöltendő képfájllal. Használja a JSON-választ a `SearchResult` struktúra egy példányának inicializálásához, majd tárolja a választ.

```javascript        
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";
// The image to upload. Replace with your file and path.
const string imageFile = "your-image.jpg";
byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
var json = System.Text.Encoding.Default.GetString(resp);
// Create result object for return
var searchResult = new SearchResult()
{
    jsonResult = json,
    relevantHeaders = new Dictionary<String, String>()
};
```
Ezt a JSON-választ ezután megjelenítheti a konzolon.

## <a name="use-an-image-insights-token-in-a-request"></a>Képelemzés-token használata kérelemben

Ha egy `POST`-kérelem eredményeivel visszaadott `ImageInsightsToken` tokent szeretne használni, hozzáadhatja azt egy `GET`-kérelemhez. Például:

```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```

Ha azonosítható személyek vagy helyek szerepelnek a képen, a kérelem rájuk vonatkozó információkat fog visszaadni.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Képek és keresési beállítások megjelenítése egyoldalas webalkalmazásban](tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>További információ

* [Bing Image Search API – referencia](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
