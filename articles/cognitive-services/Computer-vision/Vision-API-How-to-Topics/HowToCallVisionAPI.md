---
title: 'Példa: A rendszerkép elemzése API meghívása – Computer Vision'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hívhatja meg a Computer Vision API-t a REST használatával az Azure Cognitive Servicesben.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 35e6ad922ab54748165fcf8e273d93ee44bc42cc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564530"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Példa: A Computer Vision API meghívása

Ez az útmutató ismerteti, hogyan hívhatja meg a Computer Vision API-t a REST használatával. A mintákat megadjuk C#-ben a Computer Vision API ügyfélkódtár használatával és HTTP POST/GET hívásokkal is. Amire figyelni fogunk:

- Hogyan kaphatjuk meg a „Címkék”, „Leírás” és „Kategóriák” elemeket.
- Hogyan kaphatjuk meg a „fogalomkör-specifikus” információkat (hírességek).

## <a name="prerequisites"></a>Előfeltételek

- A kép URL-címe vagy a helyileg tárolt kép elérési útja.
- Támogatott bemeneti módszerek: Nyers képbináris fájl alkalmazás/oktett adatfolyam vagy képurl-cím formájában
- Támogatott képformátumok: JPEG, PNG, GIF, BMP
- Képfájl mérete: Kisebb, mint 4MB nál
- Képdimenzió: Nagyobb, mint 50 x 50 képpont
  
Az alábbi példákban a következő funkciókat mutatjuk be:

1. Kép elemzése és a címkék és a leírás visszaadása tömbben.
2. Kép elemzése fogalomkör-specifikus modellel (pontosabban a „hírességek” modellel), a megfelelő eredmények visszaadása JSON formában.

A funkciók lebontása:

- **Egy lehetőség:** Hatókörrel rendelkező elemzés – csak egy adott modell elemzése
- **Második lehetőség:** Továbbfejlesztett elemzés – további részletek a [86-kategóriák besorolásával](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>Az API-hívás engedélyezése

A Computer Vision API minden meghívásához előfizetési kulcs szükséges. Ezt a kulcsot egy lekérdezésisztring-paraméteren keresztül kell továbbítani, vagy a kérelemfejlécben kell megadni.

Az ingyenes próbaverziós kulcs beszerzéséhez tekintse [meg a Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)című témakört. Vagy kövesse a [Cognitive Services fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) az Computer Visionra való előfizetéshez és a kulcs beszerzéséhez című témakör utasításait.

1. Az előfizetési kulcs lekérdezési karakterláncban történő átadásához lásd az alábbi Computer Vision API példát:

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

1. Az előfizetési kulcs átadható a HTTP-kérés fejlécében is:

```ocp-apim-subscription-key: <Your subscription key>```

1. Az ügyfélkódtár használatakor az előfizetési kulcs átadása a VisionServiceClient konstruktorán keresztül történik:

```var visionClient = new VisionServiceClient("Your subscriptionKey");```

## <a name="upload-an-image-to-the-computer-vision-api-service-and-get-back-tags-descriptions-and-celebrities"></a>Rendszerkép feltöltése a Computer Vision API szolgáltatásba és a címkék, leírások és hírességek visszaszerzése

A Computer Vision API-hívás elvégzésének alapvető módja a kép közvetlen feltöltése. Ez „POST” kérés küldését jelenti application/octet-stream tartalomtípussal, a képből kiolvasott adatokkal együtt. A „Címkék” és „Leírás” esetében ez a feltöltési mód minden Computer Vision API-hívásra azonos lesz. Az egyetlen különbség a felhasználó által megadott lekérdezési paraméterben lesz. 

Íme a „Címkék” és „Leírás” lekérése egy adott képre:

**Egy lehetőség:** "Címkék" és egy "Leírás" listájának beolvasása

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```

**Második lehetőség** Csak a „Címkék” listájának vagy csak a „Leírás” listájának lekérése:

###### <a name="tags-only"></a>Csak Címkék:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Csak Leírás:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```

### <a name="get-domain-specific-analysis-celebrities"></a>Tartomány-specifikus elemzés (hírességek) beolvasása

**Egy lehetőség:** Hatókörrel rendelkező elemzés – csak egy adott modell elemzése
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

E beállítás használata esetén semmi más lekérdezési paraméter {visualFeatures, részletek} nem érvényes. Ha meg szeretné tekinteni valamennyi támogatott modellt, használja a következőt:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```

**Második lehetőség:** Továbbfejlesztett elemzés – további részletek a [86-kategóriák besorolásával](../Category-Taxonomy.md)

Olyan alkalmazásokban, ahol a felhasználó az egy vagy több fogalomkör-specifikus modellből származó részleteken kívül általános képelemzést is szeretne, kibővítjük a v1 API-t a modell lekérdező paraméterrel.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```

A metódus hívásakor először meghívjuk a 86-kategóriás osztályozót. Ha a kategóriák bármelyike egyezik egy ismert/megfelelő modellével, akkor sor kerül az osztályozó másodszori meghívására. Például ha a „részletek = all” vagy a „részletek” tartalmazza a „hírességek”-et, akkor a 86-kategóriás osztályozó meghívása után és ha az eredmény tartalmazza a kategória személyt, meghívjuk a hírességek modellt. Ez az első lehetőséghez képest növelni fogja a késést a hírességek iránt érdeklődő felhasználók számára.

Ebben az esetben az összes v1 lekérdezési paraméter ugyanúgy viselkedik.  Ha a visualFeatures=categories megadása elmaradt, implicit módon engedélyezésre kerül.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Az elemzéshez használt JSON-kimenet beolvasása és megismerése

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

Mező | Type | Tartalom
------|------|------|
Tags  | `object` | Felső szintű objektum a címkék tömbjének
tags[].Name | `string`  | Kulcsszó a címke osztályozótól
tags[].Score    | `number`  | Megbízhatósági pontszám, 0 és 1 között.
description  | `object` | Felső szintű objektum a leírásnak.
description.tags[] |    `string`    | Címkék listája.  Ha a felirat létrehozásához nincs elegendően nagy megbízhatóság, akkor a hívó számára egyedül a címkék adnak elérhető információt.
description.captions[].text | `string`  | A képet leíró kifejezés.
description.captions[].confidence   | `number`  | A kifejezéshez tartozó megbízhatóság.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>A tartományi specifikus modellek JSON-kimenetének beolvasása és megértése

**Egy lehetőség:** Hatókörrel rendelkező elemzés – csak egy adott modell elemzése

A kimenet címkék tömbje lesz, például az alábbi példához hasonló lesz:

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

**Második lehetőség:** Továbbfejlesztett elemzés – további részletek a 86-kategóriák besorolásával

A második lehetőséget használó fogalomkör-specifikus modellekre (bővített elemzés) a kategória visszatérési típusa kibővül. Például a következőképpen:

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

A kategóriák mező egy vagy több lista az eredeti [86-kategóriás](../Category-Taxonomy.md) eredeti elnevezésekből. Fontos megjegyezni, hogy az aláhúzással végződő kategóriák a kategóriának és a gyerek kategóriának is megfelelnek (például people_, valamint people_group a hírességek modellnél).

Mező   | Type  | Tartalom
------|------|------|
categories | `object`   | Legfelső szintű objektum
categories[].name    | `string` | Neve a 86-kategóriás rendszerben
categories[].score  | `number`  | Megbízhatósági pontszám, 0 és 1 között
categories[].detail  | `object?`      | Nem kötelező részletes objektum

Vegye figyelembe, hogy ha több kategória is megfelel (például a 86-kategóriás osztályozó ad eredményt a people_ és a people_young esetében is ha model=hírességek), az adatokat az általánosabb szintű megfeleléshez illeszti (a példában people_.)

## <a name="errors-responses"></a>Hibákra adott válaszok

Ezek ugyanazok, mint a vision.analyze esetén, de van egy plusz hiba, a NotSupportedModel (HTTP 400), amely az első és második lehetőségnél is előfordulhat. A második lehetőségnél (bővített elemzés), ha a részletesen megadott modellek bármelyikét nem sikerül felismerni, az API a NotSupportedModel-lel fog visszaténi, akkor is ha van köztük érvényes.  A felhasználók a listModels hívásával deríthetik ki, hogy mely modellek támogatottak.

## <a name="next-steps"></a>További lépések

A REST API használatához lásd: [Computer Vision API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
