---
title: Szöveg kinyerése képekből
titleSuffix: Azure Cognitive Search
description: Dolgozza fel és bontsa ki a szöveget és egyéb információkat a képekből az Azure Cognitive Search folyamataiban.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98054060210f55803d6e2811e1f494fd3ff00e48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76838258"
---
# <a name="how-to-process-and-extract-information-from-images-in-ai-enrichment-scenarios"></a>Képek feldolgozásának és kinyerésének a ai-dúsítási forgatókönyvekben

Az Azure Cognitive Search számos funkcióval rendelkezik a képek és a képfájlok használatával. A dokumentum feltörése során az *imageAction* paraméterrel szöveget nyerhet ki a fényképekből vagy az alfanumerikus szöveget tartalmazó képekből, például a "STOP" szót stoptáblában. Más esetek közé tartozik a kép szöveges ábrázolása, például a pitypang fényképének "pitypang" vagy a "sárga" szín. A kép metaadatait is kinyerheti, például a méretét.

Ez a cikk részletesebben ismerteti a képfeldolgozást, és útmutatást nyújt az AI-dúsítási folyamat ban lévő lemezképek hez.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Normalizált képek beszereznie

A dokumentumfeltörés részeként új indexelő konfigurációs paraméterek állnak fenn a fájlokba ágyazott képfájlok vagy képek kezelésére. Ezek a paraméterek a képek további későbbi feldolgozásra való normalizálására szolgálnak. A képek normalizálása egységesebbé teszi őket. A nagyképek maximális magasságba és szélességre lesznek átméretezve, hogy fogyókulni legyenek. A tájolással metaadatokat biztosító képek esetében a kép elforgatása a függőleges betöltéshez van beállítva. A metaadat-korrekciókat az egyes képekhez létrehozott összetett típusrögzíti. 

A kép normalizálását nem lehet kikapcsolni. A képeken felüli képességek normalizált képeket várnak. A kép normalizálásának engedélyezése egy indexelőn megköveteli, hogy az indexelőhez egy skillset legyen csatolva.

| Konfigurációs paraméter | Leírás |
|--------------------|-------------|
| imageAction   | Állítsa "nincs" beállításra, ha beágyazott képek vagy képfájlok esetén nem kell műveletet végrehajtani. <br/>Állítsa a "generateNormalizedImages" beállításra, hogy a dokumentum feltörésének részeként normalizált képek tömbjét hozza létre.<br/>Állítsa be a "generateNormalizedImagePerPage" beállításhoz, hogy normalizált képek tömbjét hozza létre, ahol az adatforrásban lévő PDF-fájlok esetében minden oldal egy kimeneti lemezképbe kerül.  A funkció megegyezik a nem PDF-fájltípusok "generateNormalizedImages" funkciójával.<br/>Minden olyan lehetőség, amely nem "nincs", a képek lesznek kitéve a *normalized_images* mezőben. <br/>Az alapértelmezett érték a "nincs". Ez a konfiguráció csak a blob adatforrásokhoz tartozik, ha az "dataToExtract" "contentAndMetadata" lesz. <br/>Egy adott dokumentumból legfeljebb 1000 kép lesz kinyerhető. Ha egy dokumentumban több mint 1000 kép található, az első 1000 kibontásra kerül, és figyelmeztetés jön létre. |
|  normalizedImageMaxWidth | A létrehozott normalizált képek maximális szélessége (képpontban). Az alapértelmezett érték 2000. A megengedett maximális érték 10000. | 
|  normalizáltImageMaxHeight | A létrehozott normalizált képek maximális magassága (képpontban). Az alapértelmezett érték 2000. A megengedett maximális érték 10000.|

> [!NOTE]
> Ha az *imageAction* tulajdonságot nem "nincs"-re állítja be, akkor a *parsingMode* tulajdonságot nem állíthatja másra, mint az "alapértelmezett".  Az indexelő konfigurációjában csak a két tulajdonság egyikét állíthatja be nem alapértelmezett értékre.

Állítsa be a **parsingMode** paramétert `json` (az egyes `jsonArray` blobok egyetlen dokumentumként történő indexelésére) vagy (ha a blobok JSON-tömböket tartalmaznak, és egy tömb minden elemét külön dokumentumként kell kezelni).

A normalizált képek maximális szélessége és magassága esetén az alapértelmezett 2000 képpont az [OCR-szakértelem](cognitive-search-skill-ocr.md) és a [képelemzési készség](cognitive-search-skill-image-analysis.md)által támogatott maximális méreteken alapul. Az [OCR szakértelem](cognitive-search-skill-ocr.md) a nem angol nyelvek esetében 4200, angol nyelven pedig 10000 maximális szélességet és magasságot támogat.  Ha növeli a maximális korlátokat, a feldolgozás sikertelen lehet a nagyobb képeken a skillset definíciója és a dokumentumok nyelve alapján. 

Az imageAction értéket az [indexelő definíciójában](https://docs.microsoft.com/rest/api/searchservice/create-indexer) a következőképpen adhatja meg:

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

Ha az *imageAction* értéke más, mint "nincs", az új *normalized_images* mező egy színtömbnyi képet fog tartalmazni. Minden kép egy összetett típus, amely a következő tagokkal rendelkezik:

| Képtag       | Leírás                             |
|--------------------|-----------------------------------------|
| data               | A normalizált kép BASE64 kódolású karakterlánca JPEG formátumban.   |
| szélesség              | A normalizált kép szélessége képpontban. |
| magasság             | A normalizált kép magassága képpontban. |
| eredeti szélesség      | A kép eredeti szélessége a normalizálás előtt. |
| eredetiMagasság      | A kép eredeti magassága a normalizálás előtt. |
| rotációFromOriginal |  Az óramutató járásával ellentétes irányú elforgatás fokokban, amelyek a normalizált kép létrehozásához fordultak elő. 0 és 360 fok közötti érték. Ez a lépés beolvassa a metaadatokat a kamera vagy képolvasó által létrehozott képből. Általában 90 fokos többszöröse. |
| contentOffset | A karaktereltolás azon a tartalommezőn belül, amelyből a képet kinyerték. Ez a mező csak beágyazott képekkel rendelkező fájlokra vonatkozik. |
| lapSzáma | Ha a képet pdf-dokumentumból nyerték ki vagy renderelték ki, ez a mező az 1-től kezdődően a kinyert vagy kiolvasztott PDF-dokumentum oldalszámát tartalmazza.  Ha a kép nem PDF-dokumentumból származik, akkor ez a mező 0 lesz.  |

 A *normalized_images*mintaértéke:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>Képpel kapcsolatos készségek

Két beépített kognitív készségek, hogy a képeket, mint egy bemenet: [OCR](cognitive-search-skill-ocr.md) és [képelemzés](cognitive-search-skill-image-analysis.md). 

Jelenleg ezek a képességek csak a dokumentum repedési lépéséből létrehozott képekkel működnek. Mint ilyen, az egyetlen `"/document/normalized_images"`támogatott bemenet .

### <a name="image-analysis-skill"></a>Képelemzési készség

A [képelemzési szakértelem](cognitive-search-skill-image-analysis.md) a képtartalom alapján számos vizuális funkciót kinyer. Létrehozhat például egy képaláírást egy képből, címkéket hozhat létre, vagy hírességeket és tájékozódási pontokat azonosíthat.

### <a name="ocr-skill"></a>OCR-készség

Az [OCR szakértelem](cognitive-search-skill-ocr.md) szöveget bont ki képfájlokból, például JPG-ekből, PNG-ekből és bitképekből. Ez kivonat szöveget, valamint az elrendezés információkat. Az elrendezési adatok határolókereteket tartalmaznak az egyes azonosított karakterláncokhoz.

## <a name="embedded-image-scenario"></a>Beágyazott képforgatókönyv

Gyakori forgatókönyv, hogy a következő lépések végrehajtásával egyetlen karakterláncot hoz létre, amely tartalmazza az összes fájltartalmat, mind a szöveget, mind a kép-eredetű szöveget:  

1. [Normalized_images kibontása](#get-normalized-images)
1. Az OCR-szakértelem `"/document/normalized_images"` futtatása bemenetként használva
1. Egyesítse a képek szöveges ábrázolását a fájlból kinyert nyers szöveggel. A [Szövegegyesítés](cognitive-search-skill-textmerger.md) szakértelem segítségével mindkét szöveges adattömböt egyetlen nagy karakterláncba egyesítheti.

A következő példa skillset létrehoz egy *merged_text* mező, amely a dokumentum szöveges tartalmát. Ez is beleértve a OCRed szöveg -ból mindegyik -ból beágyazott kép. 

#### <a name="request-body-syntax"></a>Kérelem törzsének szintaxisa
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

Most, hogy rendelkezik egy merged_text mezővel, leképezheti kereshető mezőként az indexelő definíciójában. A fájlok teljes tartalma, beleértve a képek szövegét is, kereshető lesz.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Kibontott szöveg határolókereteinek megjelenítése

Egy másik gyakori forgatókönyv a keresési eredmények elrendezési adatainak megjelenítése. Előfordulhat például, hogy a keresési eredmények részeként ki szeretné emelni, hogy hol található egy szöveg egy képben.

Mivel az OCR lépés a normalizált képeken történik, az elrendezéskoordinátái a normalizált képtérben vannak. A normalizált kép megjelenítésekor a koordináták jelenléte általában nem jelent problémát, de bizonyos esetekben érdemes lehet megjeleníteni az eredeti képet. Ebben az esetben konvertálja az elrendezés minden egyes koordinátapontját az eredeti képkoordináta-rendszerré. 

Segítőként, ha a normalizált koordinátákat az eredeti koordinátatérre kell átalakítania, a következő algoritmust használhatja:

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
+ [Indexelő létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Képelemzési készség](cognitive-search-skill-image-analysis.md)
+ [OCR-készség](cognitive-search-skill-ocr.md)
+ [Szövegegyesítési szakértelem](cognitive-search-skill-textmerger.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Bővített mezők leképezése](cognitive-search-output-field-mapping.md)
