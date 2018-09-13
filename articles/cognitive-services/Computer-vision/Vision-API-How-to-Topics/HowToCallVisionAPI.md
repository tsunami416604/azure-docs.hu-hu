---
title: A Computer Vision API meghívása |} A Microsoft Docs
description: Ismerje meg, hogyan hívhat meg a Computer Vision API REST a Cognitive Services használatával.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: 34705681e665b57a89c43f31ca695e0acb45ae3f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35760231"
---
# <a name="how-to-call-the-computer-vision-api"></a>A Computer Vision API meghívása

Ez az útmutató ismerteti, hogyan hívhat meg Computer Vision API REST használatával. A minták egyaránt a C# a Computer Vision API ügyféloldali kódtár használatával, és a HTTP POST vagy GET-hívásokként készültek. Fogunk dolgozni:

-   Hogyan "Címkék", "Leírás" és "Kategóriák".
-   Hogyan kérhet le "Tartomány-specifikus" információkat (hírességek).

### <a name="Prerequisites">Előfeltételek</a> 
Kép URL-címe vagy helyileg tárolt képet elérési útját.
  * Támogatott bemeneti módszerek: bináris egy application/octet-stream vagy URL-formájában nyers kép
  * Támogatott képformátum: JPEG, PNG, GIF és BMP
  * Fájl Képméret: legfeljebb 4MB
  * Lemezkép-dimenzió: nagyobb, mint 50 x 50 képpont
  
Az alábbi példákban találja meg a következő funkciókat:

1. Kép elemzése és a címkék és a egy leírás tömbjét adja vissza.
2. Tartomány-specifikus modellek (pontosabban a "hírességek" modell) egy olyan rendszerképre elemzése és a megfelelő első eredményez a JSON-beruházására.

Funkciók a bontásban:

  * **1. lehetőség:** lapszintű elemzése – csak egy adott modell elemzése
  * **2. lehetőség:** bővített elemzése – további információkat a elemzése [86-kategóriák besorolás](../Category-Taxonomy.md)
  
### <a name="Step1">1. lépés: Engedélyezze az API-hívás</a> 
A Computer Vision API minden meghívásához előfizetési kulcs szükséges. Ezt a kulcsot kell átadni a lekérdezési sztring paramétereként, vagy a kérelem fejlécében megadott. 

Szerezzen be egy előfizetési kulcsot, lásd: [előfizetői azonosítók beszerzése hogyan](../Vision-API-How-to-Topics/HowToSubscribe.md
).

**1.** A Computer Vision API példaként átadása az előfizetési kulcsot a lekérdezési karakterlánc keresztül, az alábbiakban megtekintheti:

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

**2.** Az előfizetési kulcs átadásával is adható meg a HTTP-kérelem fejléce:

```ocp-apim-subscription-key: <Your subscription key>```

**3.** Ha az ügyféloldali kódtár használatával, az előfizetési kulcs átadott VisionServiceClient konstruktorának keresztül:

```var visionClient = new VisionServiceClient(“Your subscriptionKey”);```

### <a name="Step2">2. lépés: A Computer Vision API szolgáltatásba töltsön fel egy képet, majd az címkéket, leírásokat és hírességek</a>
Az alapszintű hajtsa végre a Computer Vision API-hívás módja közvetlen kép feltöltésével. Ez történik, hogy az application/octet-stream és az adatok olvasása a lemezképből tartalomtípus "POST" kérelmet küld. A "Címkék" és "Description" a feltöltési módszer a Computer Vision API-hívásokhoz azonos lesz. Az egyetlen különbség a lekérdezési paraméterek, a felhasználó adja meg lesz. 

Itt látható egy adott rendszerképhez "Címkék" és "Description" beszerzése:

**1. lehetőség:** "Címkék" listája és a egy "Description"
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```
```
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
**Két lehetőség** listájának beolvasása "Címkék" csak vagy a "Leírás" csak listája:

###### <a name="tags-only"></a>Csak a címkék:
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
### <a name="here-is-how-to-get-domain-specific-analysis-in-our-case-for-celebrities"></a>Itt, hogyan szerezhet be a tartomány-specifikus elemzését (esetünkben a hírességek).

**1. lehetőség:** lapszintű elemzése – csak egy adott modell elemzése
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```
E beállítás használata esetén más lekérdezési paraméterek {visualFeatures, részletek:} nem érvényesek. Ha meg szeretné tekinteni a minden támogatott modellek, használja: 
```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```
**2. lehetőség:** bővített elemzése – további információkat a elemzése [86-kategóriák besorolás](../Category-Taxonomy.md)

Alkalmazások, ahol szeretné általános kép elemzési adatok mellett kérhet egy vagy több tartomány-specifikus modelleket hogy az v1 API-t a modellek lekérdezési paraméter terjeszthetők ki.
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```
Ez a metódus meghívásakor 86-kategória osztályozó először fog nevezzük. Ha kategóriába sem felel meg, amely egy ismert/megfelelő modell, a második fázis az osztályozó által igénybe vett indítások történik. Például ha a "Részletek = all", vagy "details" tartalmazza a 'hírességek', 86-kategória osztályozó nevezzük, és az eredmény tartalmazza a kategória személy után lesz a hírességek modell nevezzük. Ez növeli a késés az érdeklődő felhasználók a hírességek, a beállítás egy képest.

Az összes v1 lekérdezési paraméterek lesz úgy viselkedik, ebben az esetben.  Ha visualFeatures kategóriák = nincs megadva, akkor implicit módon engedélyezve lesz.

### <a name="Step3">3. lépés: A JSON-kimenetet elemzése és visualFeatures ismertetése és beolvashatók a címkék, leírás =</a>

Például:
```
  {
    “tags”: [
    {
    "name": "outdoor",
        "score": 0.976
    },
    {
    "name": "bird",
        "score": 0.95
    }
            ],
    “description”: 
    {
    "tags": [
    "outdoor",
    "bird"
            ],
    "captions": [
    {
    "text”: “partridge in a pear tree”,
            “confidence”: 0.96
    }
                ]
    }
  }
```
Mező   | Típus  | Tartalom
------|------|------|
Címkék    | objektum    | Címkék tömbjét legfelső objektuma
[] címkék. név | sztring    | A címkék osztályozó kulcsszó
[] címkék. Pontszám    | szám    | Megbízhatósági pontszám, 0 és 1 között.
leírás  | objektum   | Legfelső szintű objektum leírása.
Description.tags] |    sztring  | Címkék listája.  Ha ott nem elegendő bizalom információkat hozhatnak létre egy feliratot, a címkék talán a csak a hívó számára elérhető lehetővé teszi a.
Description.captions[].Text | sztring    | Egy kifejezés, a kép leírását.
Description.captions[].confidence   | szám    | Bizalom a kifejezést.

### <a name="Step4">4. lépés: Beolvasása és a tartomány-specifikus modelleket JSON-kimenetét ismertetése</a>

**1. lehetőség:** lapszintű elemzése – csak egy adott modell elemzése

A kimenet címkék tömbjét lesz, például alábbi példához hasonló lesz:
```
  { 
    "result": [ 
      { 
    "name": "golden retriever", 
    "score": 0.98
    },
    { 
    "name": "Labrador retriever", 
    "score": 0.78
    }
              ]
  }
```

**2. lehetőség:** bővített elemzése – további részleteket biztosít 86-kategóriák besorolás elemzése

Tartomány-specifikus modellek használatával két lehetőség (Enhanced elemzés) a kategóriák vissza típusú ki van bővítve. Például a következőképpen:
```
  {
    "requestId": "87e44580-925a-49c8-b661-d1c54d1b83b5",
    "metadata":     {
      "width": 640,
      "height": 430,
      "format": "Jpeg"
                    },
    "result": {
      "celebrities": 
      [
        {
        "name": "Richard Nixon",
        "faceRectangle": {
          "left": 107,
          "top": 98,
          "width": 165,
          "height": 165
                         },
        "confidence": 0.9999827
        }
      ]
  }
```

A kategóriák mező egy vagy több listáját a [86-kategóriák](../Category-Taxonomy.md) az eredeti besorolás. Fontos megjegyezni, hogy a kategóriák, egy alulhúzásból végződése egyezni fog, kategória és annak gyermekeihez (például people_, valamint people_group hírességek modell).

Mező   | Típus  | Tartalom
------|------|------|
kategóriák | objektum | Legfelső szintű objektum
[] .név kategóriák    | sztring   | Nevezze el a 86-kategóriaelnevezési rendszer
[] .score kategóriák  | szám    | Megbízhatósági pontszám, 0 és 1 közötti
[] .detail kategóriák  | objektum?      | Nem kötelező részletes objektum

Vegye figyelembe, hogy ha több kategória felel meg (például 86-kategória osztályozó adja vissza egy pontszám people_ és amikor a modell people_young hírességek =), a részleteket is csatlakozik a legtöbb általános szintű match (a példában people_.)

### <a name="Errors">Hibák válaszok</a>
Ezek a további hibával NotSupportedModel hiba (HTTP 400), amely beállítást egy, mind a két lehetőség forgatókönyvekben visszaadott vision.analyze, azonos. A beállítás két (Enhanced elemzés), a bármely, a részletek megadott modellek nem felismerhető, ha az API-t adja vissza egy NotSupportedModel akkor is, ha egy vagy több érvényes.  Felhasználói listModels megtudhatja, milyen modellek támogatottak hívhatja.

### <a name="Summary">Összefoglalás</a>

Ezek az alapvető funkciói a Computer Vision API: hogyan tölthet fel képeket és értékes metaadatok lekérése cserébe.

A REST API-t használ, lépjen a [számítógép Vision API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
 
