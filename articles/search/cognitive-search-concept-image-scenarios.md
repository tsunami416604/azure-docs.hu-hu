---
title: Feldolgozni, és a szöveg kinyerése az Azure Search képek |} Microsoft Docs
description: Folyamat, és hogyan nyerhet ki szöveg- és egyéb információkat a képek kognitív keresési folyamatok az Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: c58e731f6b8c86a0b7d6f2500d81077904b2f5ef
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Hogyan kell feldolgozni információk kinyerése kognitív keresési forgatókönyvekben lemezképek

Kognitív keresési képek és képfájlok több képességekkel rendelkezik. A dokumentum repedés során használhatja a *imageAction* fotóit vagy alfanumerikus, például a "STOP" szó a stop bejelentkezési szöveget tartalmazó szöveg kinyerése paramétert. Egyéb forgatókönyvek például a létrehozásakor a kép, például egy dandelion, vagy a szín "sárga" fénykép "dandelion" szöveg megjelenítése. A kép mérete metaadatait is nyerhet.

Ez a cikk ismerteti a kép részletesebb feldolgozása, és útmutatást kínál a lemezképek kognitív keresési-feldolgozási folyamat használata.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Normalizált képek beolvasása

Dokumentum repedés részeként nincsenek konfigurációs paraméterei indexelő kezelési képfájlok vagy az fájlokban található beágyazott képek új készletét. Ezek a paraméterek segítségével további alárendelt feldolgozási képek optimalizálására. Lemezképek normalizálása révén azokat egységesebb. Nagy méretű képek átméretezése egy maximális magasságát és szélességét használhatóvá tegye őket. A lemezképek a tájolás biztosít a metaadatok kép elforgatásának függőleges betöltése módosul. Metaadatok beállításai az egyes lemezképek létrehozott összetett típusban lesznek rögzítve. 

Kikapcsolja a normalizálást kép nem kapcsolható. Lemezképek ismétlés készségeikre normalizált képek várt.

| Konfigurációs paraméter | Leírás |
|--------------------|-------------|
| imageAction   | Állítsa be az "none", ha a beágyazott képek vagy képfájlok észlelt kell eljárni semmilyen műveletet. <br/>Állítsa be "generateNormalizedImages" repedés dokumentum részeként bájttömb normalizált lemezképek létrehozásához. Ezeket a lemezképeket elérhetővé tehető a a *normalized_images* mező. <br/>Az alapértelmezett érték a "none." Ez a konfiguráció csak akkor megfelelő blob-adatforrások, ha "dataToExtract" értéke "contentAndMetadata." |
|  normalizedImageMaxWidth | A maximális szélességét (képpontban) normalizált lemezképek jön létre. Az alapértelmezett érték 2000.|
|  normalizedImageMaxHeight | Normalizált generált képek maximális magasságát (képpontban). Az alapértelmezett érték 2000.|

> [!NOTE]
> Ha a *imageAction* tulajdonsága csak "nincs" értékre, csak akkor állíthatja be a *parsingMode* tulajdonság csak az "alapértelmezett" értékre.  Előfordulhat, hogy csak beállítása ezen két tulajdonságok egyikét, nem alapértelmezett értékre az indexelő konfigurációját.

Az alapértelmezett 2000 képpontos normalizált képek maximális szélességét és magasságát által támogatott maximális méret alapján a [OCR szakértelem](cognitive-search-skill-ocr.md) és a [elemzés szakértelem kép](cognitive-search-skill-image-analysis.md). Ha növeli a maximális korlátai, a feldolgozás sikertelen lehet, a nagyobb méretű képek.


Megadja a imageAction a [indexelő definition](ref-create-indexer.md) az alábbiak szerint:

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

Ha a *imageAction* "generateNormalizedImages" értékre van állítva az új *normalized_images* mezőben képek tömbjét. Minden rendszerkép egy összetett típus, amely a következő tagokkal rendelkezik:

| Kép tag       | Leírás                             |
|--------------------|-----------------------------------------|
| adat               | A BASE64 kódolású karakterlánc JPEG formátumú normalizált kép.   |
| Szélessége              | A képpontban megadott nyílméretét normalizált kép szélessége. |
| Magassága             | A képpontban megadott nyílméretét normalizált kép magassága. |
| originalWidth      | Normalizálási előtt a kép eredeti szélessége. |
| originalHeight      | Normalizálási előtt a kép eredeti magasságát. |
| rotationFromOriginal |  Balra Elforgatás (fokban megadva), amely a normalizált lemezkép létrehozása történt. 0 fok és 360 fok közötti értéket. Ez a lépés a metaadatok a lemezképet, a kamera vagy képolvasó által létrehozott olvassa be. Általában többszöröse 90 fok. |
| contentOffset |A karaktereltolás, ahol a kép kinyert tartalom mezőjében. Ez a mező csak csak beágyazott képekhez fájlok. |

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

## <a name="image-related-skills"></a>Kép kapcsolatos ismeretek

Nincsenek a két beépített kognitív képességek, amelyek képek bemenetként: [OCR](cognitive-search-skill-ocr.md) és [kép Analysis](cognitive-search-skill-image-analysis.md). 

Ezek a képességek jelenleg, csak működik együtt a dokumentum jelszófeltörő lépésben létrehozott lemezképek. Így az egyetlen támogatott beviteli van `"/document/normalized_images"`.

### <a name="image-analysis-skill"></a>Kép elemzés szakértelem

A [kép elemzés szakértelem](cognitive-search-skill-image-analysis.md) széles választéka vizuális funkciókat a lemezkép tartalom alapján bontja ki. Például hozzon létre egy felirat kép, címkék létrehozása, vagy celebrities és jellegzetes hely azonosítása.

### <a name="ocr-skill"></a>OCR szakértelem

A [OCR szakértelem](cognitive-search-skill-ocr.md) szöveg kiolvassa a képfájlok például alakíthatóak, PNG vagy bittérképekkel. Azt is kibonthat egy szöveges továbbá az elrendezési adatokat. Az elrendezési adatokat biztosít a határoló mezőkbe az egyes azonosított karakterláncok.

A OCR szakértelem algoritmus felismeri a szöveg a képek használandó kiválasztását teszi lehetővé. Jelenleg támogatja a két algoritmusok, nyomtatott szöveg egyet, majd egy másikat kézzel írt szöveg.

## <a name="embedded-image-scenario"></a>Beágyazott kép forgatókönyv

Egy általános forgatókönyv magában foglalja egy egyetlen karakterláncot tartalmazó összes fájl tartalmát, a szöveg és a lemezkép-eredeti szöveg, a következő lépések elvégzésével:  

1. [Bontsa ki a normalized_images](#get-normalized-images)
1. Futtatja a OCR szakértelem `"/document/normalized_images"` bemenetként
1. Ezeket a képeket szöveges ábrázolását egyesítése a nyers szöveggel kibontani a fájlból. Használhatja a [szöveg egyesítése](cognitive-search-skill-textmerger.md) szakértelem mindkét szöveg adattömbök összevonni egy egyetlen nagy karakterlánccá egyesít.

A következő példa skillset létrehoz egy *merged_text* a dokumentum a szöveges tartalom tartalmazó mezőt. A beágyazott képek mindegyikének OCRed szöveget is tartalmaz. 

#### <a name="request-body-syntax"></a>Törzs szintaxis
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
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
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```

Most, hogy egy merged_text mező, akkor lehetett leképez kereshető mezőt az indexelő-definícióban. A fájlok, a szöveg, képek, beleértve a tartalom lesznek kereshetők.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Határolókeret mezőkbe kibontott szöveg megjelenítése

Egy másik gyakori forgatókönyv van megjelenítése a keresési eredmények elrendezési adatokat. Például előfordulhat, hogy kívánt jelölje ki, ahol szöveg található a kép a keresési eredmények részeként.

A normalizált képek a OCR lépés hajtja végre, mert a elrendezés koordináták százalékosan vannak a normalizált kép területet. A normalizált kép megjelenítésekor a koordináták általában nem probléma, de bizonyos helyzetekben előfordulhat, hogy megjeleníteni kívánt az eredeti kép. Ebben az esetben átalakítása minden koordináta pontra az elrendezésben az eredeti kép koordináta-rendszerére. 

Segítő, mint az eredeti koordináta területre normalizált koordináták átalakításához használhatja a következő algoritmust:

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
+ [Az indexelő (REST) létrehozása](ref-create-indexer.md)
+ [Kép Szakértelem elemzése](cognitive-search-skill-image-analysis.md)
+ [OCR szakértelem](cognitive-search-skill-ocr.md)
+ [Szöveg egyesítési szakértelem](cognitive-search-skill-textmerger.md)
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)
+ [Hogyan bővített mezők](cognitive-search-output-field-mapping.md)
