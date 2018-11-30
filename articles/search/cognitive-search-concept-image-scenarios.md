---
title: Dolgozza fel, és a szöveg kinyerése a rendszerképeket az Azure Search szolgáltatásban |} A Microsoft Docs
description: Feldolgozása és kinyerése szöveg- és egyéb információkat cognitive rendszerképekből keresési folyamatok az Azure Search szolgáltatásban.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 5ee821f3d1e81e581f376d4b14b3e8393a8f190c
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444633"
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Hogyan dolgozza fel, és információt nyerhet ki képekből a kognitív keresés forgatókönyvek

Cognitive search, a képeket és képfájlok esetében több képességekkel rendelkezik. Során dokumentumfeltörést, használhatja a *imageAction* szöveg kinyerésére fotóit vagy alfanumerikus szöveget, például a "STOP" szó egy leállítási jelet tartalmazó paraméter. Egyéb forgatókönyvek között megtalálható, kép, például egy fényképet, egy dandelion, vagy a "sárga" szín "dandelion" szöveges ábrázolása létrehozása. Bontsa ki a metaadatokat, például a méret a képen is.

Ez a cikk ismerteti részletesebben képfeldolgozó, és útmutatást kínál a kognitív keresés folyamatban lévő lemezképek használata.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Normalizált képek bekérése

Dokumentumfeltörést részeként nincsenek konfigurációs paraméterei indexelő képfájlok vagy a fájlok a beágyazott képek kezelése új készletét. Ezeket a paramétereket az alárendelt feldolgozáshoz további rendszerképeket normalizálása szolgálnak. Képek normalizálása megkönnyíti a több egységes. Nagy méretű képek átméretezése egy maximális magasságának és szélességének ellenőrizze fogyóeszközök. A lemezképek biztosít a metaadatok a tájolás esetén kép elforgatása a függőleges betöltése módosul. Metaadatok korrekciók létrehozott minden egyes képe egy összetett típus rögzíti a rendszer. 

Nem kapcsolhatja ki rendszerképet normalizálási. Képek ciklustevékenység képességeit normalizált lemezképek várható.

| Konfigurációs paraméter | Leírás |
|--------------------|-------------|
| imageAction   | Ha nem kell műveletet, ha a beágyazott képek vagy képek észlelt állítsa be a "nincs". <br/>Állítsa a "generateNormalizedImages" normalizált lemezképek tömbjét létrehozásához dokumentumleképezési részeként. Ezek a lemezképek a lesz közzétéve a *normalized_images* mező. <br/>Az alapértelmezett érték a "nincs." Ez a konfiguráció csak a tárolására blob az adatforrásokat, ha "dataToExtract" értéke "contentAndMetadata." |
|  normalizedImageMaxWidth | A maximális szélességét (képpontban) normalizált a képeket. Az alapértelmezett érték 2000.|
|  normalizedImageMaxHeight | A maximális magasságát (képpontban) normalizált a képeket. Az alapértelmezett érték 2000.|

> [!NOTE]
> Ha a *imageAction* tulajdonsága "none" értékre, nem tudja beállítani a *parsingMode* tulajdonság "alapértelmezett" értékre.  Előfordulhat, hogy csak beállította a két tulajdonság közül egy nem alapértelmezett érték az indexelő konfigurációban.

Állítsa be a **parsingMode** paramétert `json` (az index minden egyes blob egyetlen dokumentumként) vagy `jsonArray` (Ha a blobok tartalmazzák a JSON-tömbök és egy külön dokumentum kell tekinteni a tömb egyes elemei kell).

Az alapértelmezett 2000 képpontos normalizált képek maximális szélességét és magasságát által támogatott maximális méret alapján a [OCR szakértelem](cognitive-search-skill-ocr.md) és a [elemzési szakértelem kép](cognitive-search-skill-image-analysis.md). Ha növeli a maximális korlát, a feldolgozás sikertelen lehet, a nagyobb képeken.


Azt adja meg a imageAction a a [indexelő definíciója](https://docs.microsoft.com/rest/api/searchservice/create-indexer) módon:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

Ha a *imageAction* "generateNormalizedImages," értékre van állítva az új *normalized_images* mező tartalmazni fog a képek tömbjét. Minden egyes képe egy összetett típus, amely rendelkezik az alábbi tagokat:

| Kép tag       | Leírás                             |
|--------------------|-----------------------------------------|
| adat               | BASE64 kódolású karakterlánc normalizált JPEG formátumú kép.   |
| Szélesség              | Szélessége a normalizált képen (képpontban). |
| Magasság             | Magasságát (képpontban) a normalizált képe. |
| originalWidth      | Normalizálási előtt a kép eredeti szélessége. |
| originalHeight      | Normalizálási előtt a kép eredeti magassága. |
| rotationFromOriginal |  Balra elforgatása a normalizált lemezkép létrehozására történt fokban. 0 fok és 360 fok közötti értéket. Ebben a lépésben a kép, kamera vagy a képolvasó által létrehozott metaadatait olvassa be. Általában többszöröse 90 fok. |
| contentOffset |A znaku není v platném a tartalom mező, ahol a rendszerkép lett kimásolva. Ez a mező csak akkor érvényes, a fájlok a beágyazott képek. |

 Minta értéke *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500  
  }
]
```

## <a name="image-related-skills"></a>Lemezképpel kapcsolatos ismereteit

Nincsenek a két beépített kognitív képességeket, amelyek rendszerképeket bemenetként: [OCR](cognitive-search-skill-ocr.md) és [képelemzés](cognitive-search-skill-image-analysis.md). 

Ezek a képességek jelenleg csak működik a dokumentum krakkolás. lépésben létrehozott képekkel. Mint ilyen, az egyetlen támogatott bemeneti az `"/document/normalized_images"`.

### <a name="image-analysis-skill"></a>Kép elemzése szakértelem

A [képelemzés szakértelem](cognitive-search-skill-image-analysis.md) kinyeri a sokféle kép tartalma alapján vizuális jellemzőket. Például hozzon létre egy felirat kép, címkék létrehozása, vagy hírességek és tereptárgyak felismerése azonosításához.

### <a name="ocr-skill"></a>Optikai Karakterfelismerés szakértelem

A [OCR szakértelem](cognitive-search-skill-ocr.md) szöveges állomány kinyerésére például bitképeket, alakíthatóak és PNG képfájl. Ez lehet szöveg kinyerése és elrendezés információkat tartalmazza. Az elrendezési adatokat az egyes azonosított karakterlánc határoló mezők biztosít.

Az optikai Karakterfelismerés szakértelem az algoritmus felismeri a szöveget a képeken kiválasztását teszi lehetővé. Jelenleg támogatja a két algoritmus, így az nyomtatott szöveg és egy másik írt szöveg felismerése képekből.

## <a name="embedded-image-scenario"></a>Beágyazott kép forgatókönyv

Egy gyakori forgatókönyv áll tartalmazó összes fájl tartalmát, szöveg és a lemezkép-forrás szöveg-, a következő lépések végrehajtásával egyetlen karakterlánc létrehozása:  

1. [Bontsa ki a normalized_images](#get-normalized-images)
1. Az optikai Karakterfelismerés szakértelem használatával fusson `"/document/normalized_images"` bemenetként
1. Egyesítés ezen rendszerképek szöveges ábrázolása a kibontott fájlból a nyers szöveg. Használhatja a [szöveg egyesítése](cognitive-search-skill-textmerger.md) szakértelem konszolidálhatja mindkét szöveg adattömbök egyetlen nagy karakterlánccá.

A következő példa indexmezők létrehoz egy *merged_text* tartalmazó szöveges tartalmát a dokumentumot. A beágyazott képek mindegyike OCRed szöveget is tartalmaz. 

#### <a name="request-body-syntax"></a>Kérelem törzse szintaxis
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```

Most, hogy egy merged_text mező, akkor hozzárendelheti egy kereshető mező, az indexelő-definícióban. A fájlok, a szöveg, képek, beleértve a tartalom mindegyikét kereshető lesz.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Határoló kinyert szöveg mezők megjelenítése

Egy másik gyakori forgatókönyv jelenítenek meg a keresési eredmények elrendezésének adatai. Például érdemes kiemeléséhez, ahol egy adott szöveg található a képet a keresési eredmények részeként.

Az optikai Karakterfelismerés lépést a normalizált képeken, mivel a elrendezés koordináták vannak a normalizált kép terület. A normalizált kép megjelenítésekor koordináták meglétének általában nem probléma, de bizonyos esetekben érdemes az eredeti képet jeleníti meg. Ebben az esetben konvertálja az elrendezés koordináta pontok mindegyike az eredeti kép koordináta-rendszerére. 

A segítő, mint ha átalakítja a normalizált koordináták az eredeti koordináta területre van szüksége használhatja a következő algoritmus:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="see-also"></a>Lásd még
+ [Az indexelő (REST) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Kép Szakértelem elemzése](cognitive-search-skill-image-analysis.md)
+ [Optikai Karakterfelismerés szakértelem](cognitive-search-skill-ocr.md)
+ [Szöveg egyesítési szakértelem](cognitive-search-skill-textmerger.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Hogyan hidaljuk mezők leképezése](cognitive-search-output-field-mapping.md)
