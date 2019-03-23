---
title: 'Példa: Az elemzés image API - hívás a Computer Vision'
titlesuffix: Azure Cognitive Services
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
ms.openlocfilehash: 4d9ec05b2495ec54657405c00e7dd42ee10911b1
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350912"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Példa: A Computer Vision API meghívása

Ez az útmutató ismerteti, hogyan hívhatja meg a Computer Vision API-t a REST használatával. A mintákat megadjuk C#-ben a Computer Vision API ügyfélkódtár használatával és HTTP POST/GET hívásokkal is. Amire figyelni fogunk:

- Hogyan kaphatjuk meg a „Címkék”, „Leírás” és „Kategóriák” elemeket.
- Hogyan kaphatjuk meg a „fogalomkör-specifikus” információkat (hírességek).

## <a name="prerequisites"></a>Előfeltételek

- A kép URL-címe vagy a helyileg tárolt kép elérési útja.
- Támogatott bemeneti módszerek: Egy application/octet-stream vagy URL-formájában bináris nyers kép
- Képformátum támogatja: JPEG, PNG, GIF, BMP
- Kép mérete: 4MB-nál kevesebb
- Lemezkép-dimenzió: Nagyobb, mint 50 x 50 képpont
  
Az alábbi példákban a következő funkciókat mutatjuk be:

1. Kép elemzése és a címkék és a leírás visszaadása tömbben.
2. Kép elemzése fogalomkör-specifikus modellel (pontosabban a „hírességek” modellel), a megfelelő eredmények visszaadása JSON formában.

A funkciók lebontása:

- **1. lehetőség:** Hatókörön belüli elemzés – csak egy adott modell elemzése
- **2. lehetőség:** Továbbfejlesztett elemzése – további információkat a elemzése [86-kategóriák besorolás](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>Engedélyezze az API-hívás

A Computer Vision API minden meghívásához előfizetési kulcs szükséges. Ezt a kulcsot vagy egy lekérdezési karakterlánc paramétereként kell átadni vagy a kérelem fejlécében lehet megadni.

Előfizetői kulcs beszerzéséhez, lásd [Előfizetői kulcsok beszerzésének módja](../Vision-API-How-to-Topics/HowToSubscribe.md
) című témakört.

1. Az előfizetési kulcs lekérdezési karakterláncban történő átadásához lásd az alábbi Computer Vision API példát:

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

1. Az előfizetési kulcs átadható a HTTP-kérés fejlécében is:

```ocp-apim-subscription-key: <Your subscription key>```

1. Az ügyfélkódtár használatakor az előfizetési kulcs átadása a VisionServiceClient konstruktorán keresztül történik:

```var visionClient = new VisionServiceClient("Your subscriptionKey");```

## <a name="upload-an-image-to-the-computer-vision-api-service-and-get-back-tags-descriptions-and-celebrities"></a>A Computer Vision API szolgáltatásba töltsön fel egy képet, majd az címkéket, leírásokat és hírességek

A Computer Vision API-hívás elvégzésének alapvető módja a kép közvetlen feltöltése. Ez „POST” kérés küldését jelenti application/octet-stream tartalomtípussal, a képből kiolvasott adatokkal együtt. A „Címkék” és „Leírás” esetében ez a feltöltési mód minden Computer Vision API-hívásra azonos lesz. Az egyetlen különbség a felhasználó által megadott lekérdezési paraméterben lesz. 

Íme a „Címkék” és „Leírás” lekérése egy adott képre:

**1. lehetőség:** "Címkék" listája és a egy "Description"

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

###### <a name="tags-only"></a>Csak címkék:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Csak leírása:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```

### <a name="get-domain-specific-analysis-celebrities"></a>Tartomány-specifikus elemzését (hírességek) beolvasása

**1. lehetőség:** Hatókörön belüli elemzés – csak egy adott modell elemzése
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

E beállítás használata esetén semmi más lekérdezési paraméter {visualFeatures, részletek} nem érvényes. Ha meg szeretné tekinteni valamennyi támogatott modellt, használja a következőt:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```

**2. lehetőség:** Továbbfejlesztett elemzése – további információkat a elemzése [86-kategóriák besorolás](../Category-Taxonomy.md)

Olyan alkalmazásokban, ahol a felhasználó az egy vagy több fogalomkör-specifikus modellből származó részleteken kívül általános képelemzést is szeretne, kibővítjük a v1 API-t a modell lekérdező paraméterrel.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```

A metódus hívásakor először meghívjuk a 86-kategóriás osztályozót. Ha a kategóriák bármelyike egyezik egy ismert/megfelelő modellével, akkor sor kerül az osztályozó másodszori meghívására. Például ha a „részletek = all” vagy a „részletek” tartalmazza a „hírességek”-et, akkor a 86-kategóriás osztályozó meghívása után és ha az eredmény tartalmazza a kategória személyt, meghívjuk a hírességek modellt. Ez az első lehetőséghez képest növelni fogja a késést a hírességek iránt érdeklődő felhasználók számára.

Ebben az esetben az összes v1 lekérdezési paraméter ugyanúgy viselkedik.  Ha a visualFeatures=categories megadása elmaradt, implicit módon engedélyezésre kerül.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Beolvasása és elemzése a JSON-kimenetet ismertetése

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

Mező | Typo | Tartalom
------|------|------|
Címkék  | objektum | Felső szintű objektum a címkék tömbjének
tags[].Name | sztring    | Kulcsszó a címke osztályozótól
tags[].Score    | szám    | Megbízhatósági pontszám, 0 és 1 között.
leírás  | objektum   | Felső szintű objektum a leírásnak.
description.tags[] |    sztring  | Címkék listája.  Ha a felirat létrehozásához nincs elegendően nagy megbízhatóság, akkor a hívó számára egyedül a címkék adnak elérhető információt.
description.captions[].text | sztring    | A képet leíró kifejezés.
description.captions[].confidence   | szám    | A kifejezéshez tartozó megbízhatóság.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Lekéréséhez és a tartomány-specifikus modelleket JSON-kimenetét ismertetése

**1. lehetőség:** Hatókörön belüli elemzés – csak egy adott modell elemzése

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

**2. lehetőség:** Továbbfejlesztett elemzése – további részleteket biztosít 86-kategóriák besorolás elemzése

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

Mező   | Typo  | Tartalom
------|------|------|
kategóriák | objektum | Legfelső szintű objektum
categories[].name    | sztring   | Neve a 86-kategóriás rendszerben
categories[].score  | szám    | Megbízhatósági pontszám, 0 és 1 között
categories[].detail  | objektum?      | Nem kötelező részletes objektum

Vegye figyelembe, hogy ha több kategória is megfelel (például a 86-kategóriás osztályozó ad eredményt a people_ és a people_young esetében is ha model=hírességek), az adatokat az általánosabb szintű megfeleléshez illeszti (a példában people_.)

## <a name="errors-responses"></a>Hibák válaszok

Ezek ugyanazok, mint a vision.analyze esetén, de van egy plusz hiba, a NotSupportedModel (HTTP 400), amely az első és második lehetőségnél is előfordulhat. A második lehetőségnél (bővített elemzés), ha a részletesen megadott modellek bármelyikét nem sikerül felismerni, az API a NotSupportedModel-lel fog visszaténi, akkor is ha van köztük érvényes.  A felhasználók a listModels hívásával deríthetik ki, hogy mely modellek támogatottak.

## <a name="next-steps"></a>További lépések

A REST API használatához lásd: [Computer Vision API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).