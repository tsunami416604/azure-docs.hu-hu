---
title: A Computer Vision API meghívása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hívhatja meg a Computer Vision API-t a REST API használatával az Azure Cognitive Servicesben.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 298228eedb73298f00654f4f72c201d9ed671090
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72177061"
---
# <a name="call-the-computer-vision-api"></a>A Computer Vision API meghívása

Ez a cikk bemutatja, hogyan hívja meg a Computer Vision API a REST API használatával. A minták a Computer Vision API ügyfélkódtár használatával c# vagy http post vagy GET hívásként írva vannak. A cikk a következőkre összpontosít:

- Címkék, leírás és kategóriák beszerzése
- Tartományspecifikus információk vagy "hírességek" beszerzése

## <a name="prerequisites"></a>Előfeltételek

- Kép URL-címe vagy helyileg tárolt kép elérési útja
- Támogatott beviteli módszerek: egy nyers kép bináris formájában egy alkalmazás / oktett-stream, vagy egy kép URL-címét
- Támogatott képfájlformátumok: JPEG, PNG, GIF és BMP
- Képfájl mérete: legbősz: 4 MB vagy kevesebb
- Képméretek: 50 &times; 50 képpont vagy nagyobb
  
A cikkben szereplő példák a következő szolgáltatásokat mutatják be:

* Kép elemzése címkék és leírás visszaadására
* Egy kép elemzése tartományspecifikus modellel (pontosabban a "hírességek" modellel) a JSON megfelelő eredményének visszaadására

A funkciók a következő lehetőségeket kínálják:

- **1. lehetőség**: Hatókörrel végzett elemzés – Csak egy adott modell elemzése
- **2. lehetőség**: Továbbfejlesztett elemzés - Elemzés további részletek biztosításához [86 kategóriás taxonómia](../Category-Taxonomy.md) használatával
  
## <a name="authorize-the-api-call"></a>Az API-hívás engedélyezése

A Computer Vision API minden meghívásához előfizetési kulcs szükséges. Ezt a kulcsot vagy át kell adni egy lekérdezési karakterlánc-paraméteren, vagy meg kell adni a kérelem fejlécében.

Az ingyenes próbakulcs beszerezéséhez tegye a következők egyikét:
* Nyissa meg a [Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) lapot. 
* A [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lapon előfizethet a Computer Vision szolgáltatásra.

Az előfizetési kulcsot az alábbi módon adhatja át:

* Adja át egy lekérdezési karakterláncon keresztül, mint ebben a Computer Vision API-példában:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Adja meg a HTTP-kérelem fejlécében:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* Az ügyfélkódtár használatakor adja át a kulcsot a ComputerVisionClient konstruktorán keresztül, és adja meg az ügyfél egyik tulajdonságának régióját:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Kép feltöltése a Computer Vision API szolgáltatásba

A Computer Vision API-hívás végrehajtásának alapvető módja egy kép feltöltése közvetlenül a címkék, a leírás és a hírességek visszaadására. Ehhez küld egy "POST" kérelmet a bináris képet a HTTP-törzs együtt a képből beolvasott adatokkal együtt. A feltöltési módszer megegyezik az összes Computer Vision API-hívásesetén. Az egyetlen különbség a megadott lekérdezési paraméterek. 

Adott kép esetén címkéket és leírást kaphat az alábbi lehetőségek egyikével:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>1. lehetőség: Címkék listájának és leírásának beszerezni

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>2. lehetőség: Csak a címkék listájának vagy csak leírásnak a leválasztása

Csak címkék esetén futtassa a következőket:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Csak leírás esetén futtassa a következőket:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Tartományspecifikus elemzés beszereznie (hírességek)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>1. lehetőség: Hatókörrel végzett elemzés – Csak egy adott modell elemzése
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

E beállítás használata esetén semmi más lekérdezési paraméter {visualFeatures, részletek} nem érvényes. Ha meg szeretné tekinteni valamennyi támogatott modellt, használja a következőt:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>2. lehetőség: Továbbfejlesztett elemzés - Elemzés további részletek biztosításához 86 kategóriás taxonómia használatával

Olyan alkalmazások esetében, ahol egy vagy több tartományspecifikus modell részletei mellett általános képelemzést szeretne kapni, bővítse ki a v1 API-t a modellek lekérdezési paraméter használatával.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Ha meghívja ezt a metódust, először hívja meg a [86-kategóriás](../Category-Taxonomy.md) osztályozó. Ha a kategóriák bármelyike megegyezik egy ismert vagy egyező modell éval, az osztályozó meghívása második lépés következik be. Ha például a "details=all" vagy a "details" magában foglalja a "hírességeket", akkor a 86-os osztályozó hívása után a hírességek modelljét hívja fel. Az eredmény magában foglalja a kategória személy. Az 1.

Ebben az esetben az összes v1 lekérdezési paraméter ugyanúgy viselkedik. Ha nem adja meg a visualFeatures=categories-t, implicit módon engedélyezve van.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>A JSON-kimenet beolvasása és megértése elemzéshez

Például:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Mező | Típus | Tartalom
------|------|------|
Címkék  | `object` | Címkék tömbjének legfelső szintű objektuma.
tags[].Name | `string`  | A kulcsszó a címkék osztályozó.
tags[].Score    | `number`  | A megbízhatósági pontszám 0 és 1 között.
leírás  | `object` | A leírás legfelső szintű objektuma.
description.tags[] |    `string`    | A címkék listája.  Ha nem bízik a felirat készítésének képességében, akkor lehet, hogy a címkék az egyetlen elérhető információ a hívó számára.
description.captions[].text | `string`  | A képet leíró kifejezés.
description.captions[].confidence   | `number`  | A kifejezés önbizalmi pontszáma.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Tartományspecifikus modellek JSON-kimenetének beolvasása és megértése

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>1. lehetőség: Hatókörrel végzett elemzés – Csak egy adott modell elemzése

A kimenet címkék tömbje, ahogy az a következő példában látható:

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>2. lehetőség: Továbbfejlesztett elemzés - Elemzés további részletek megadásához a "86-kategóriák" rendszerhasználatával

A 2.

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

A kategóriák mező az eredeti [rendszerbesorolás 86 kategóriájából](../Category-Taxonomy.md) egy vagy több listáját tartalmazza. Az aláhúzásjelben végződő kategóriák megfelelnek az adott kategóriának és gyermekeinek (például "people_" vagy "people_group" a hírességek modellje esetében).

Mező   | Típus  | Tartalom
------|------|------|
kategóriák | `object`   | A legfelső szintű objektum.
categories[].name    | `string` | A 86 kategóriás taxonómiai listából származó név.
categories[].score  | `number`  | A megbízhatósági pontszám 0 és 1 között.
categories[].detail  | `object?`      | (Nem kötelező) A részletobjektum.

Ha több kategória egyezik (például a 86-os osztályozó a "people_" és a "people_young" pontszámát adja vissza, ha a modell=hírességek), a részletek a legáltalánosabb szintű egyezéshez kapcsolódnak ("people_") ebben a példában).

## <a name="error-responses"></a>Hibaválaszok

Ezek a hibák megegyeznek a vision.analyze, a további NotSupportedModel hiba (HTTP 400), amely visszaadható mind az 1. A 2. Hogy megtudja, milyen modellek támogatottak, hívhatlistModels.

## <a name="next-steps"></a>További lépések

A REST API használatához lásd: [Computer Vision API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
