---
title: Dokumentum-extrakciós kognitív képesség (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: A tartalom kibontása egy fájlból a dúsítási folyamaton belül. Ez a képesség jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837731"
---
# <a name="document-extraction-cognitive-skill"></a>Dokumentum-extrakciós kognitív képesség

> [!IMPORTANT] 
> Ez a képesség jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Jelenleg nincs portál vagy .NET SDK-támogatás.

A **dokumentum-kivonási** képesség Kinyeri a tartalmat egy fájlból a dúsítási folyamaton belül. Ez lehetővé teszi, hogy kihasználja az olyan dokumentum-kivonási lépést, amely általában a készségkészlet végrehajtása előtt történik, amelyet más szakismeretek is létrehozhatnak.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum részeként, az indexelés során. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kinyerésének díjszabását a [díjszabási oldalon](https://go.microsoft.com/fwlink/?linkid=2042400)találja.
## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. util. DocumentExtractionSkill

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket.

| Bemenetek            | Megengedett értékek | Leírás |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | A nem tiszta szöveg-vagy JSON-fájlokból kinyert dokumentumok kinyeréséhez `default`. Az egyszerű szövegfájlok teljesítményének növeléséhez állítsa `text` értékre. Állítsa be `json`re a strukturált tartalom JSON-fájlokból való kinyeréséhez. Ha `parsingMode` nincs explicit módon definiálva, akkor a rendszer `default`ra állítja be. |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | A `contentAndMetadata` értékre állítva kinyerheti az összes metaadatot és szöveges tartalmat az egyes fájlokból. A `allMetadata` értékre állítva kinyerheti csak a [tartalomtípus-specifikus metaadatokat](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (például a csak a. png fájlokra jellemző metaadatokat). Ha `dataToExtract` nincs explicit módon definiálva, akkor a rendszer `contentAndMetadata`ra állítja be. |
| `configuration` | Lásd alább. | A dokumentumok kinyerésének módját módosító választható paraméterek szótára. A támogatott konfigurációs tulajdonságok leírását az alábbi táblázat tartalmazza. |

| Konfigurációs paraméter   | Megengedett értékek | Leírás |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Állítsa `none` értékre, hogy figyelmen kívül hagyja az adatkészletben lévő beágyazott képeket vagy képfájlokat. Ez az alapértelmezett érték. <br/>Ha a [képelemzést kognitív képességekkel szeretné használni](cognitive-search-concept-image-scenarios.md), állítsa be `generateNormalizedImages`, hogy a készség a dokumentum repedésének részeként normalizált lemezképek tömbjét hozza létre. Ehhez a művelethez a `parsingMode` `default`re van beállítva, és a `dataToExtract` `contentAndMetadata`értékre van állítva. A normalizált kép olyan további feldolgozásra utal, amely egységes képkimenetet, méretet és elforgatást eredményez, így a konzisztens renderelés elősegítése, ha képeket tartalmaz a vizuális keresési eredményekben (például egy gráf vezérlőelemben a [JFK bemutatóban](https://github.com/Microsoft/AzureSearch_JFK_Files)látható azonos méretű fényképeket). Ez az információ minden rendszerképhez létrejön, ha ezt a beállítást használja.  <br/>Ha úgy állítja be a `generateNormalizedImagePerPage`, hogy a PDF-fájlokat a rendszer a beágyazott képek kibontása helyett eltérően kezeli, az egyes lapok képként jelennek meg, és ennek megfelelően normalizálva lesznek.  A nem PDF típusú fájltípusok ugyanúgy lesznek kezelve, mint a `generateNormalizedImages` beállításakor.
| `normalizedImageMaxWidth` | 50-10000 közötti egész szám | A generált normalizált képek maximális szélessége (képpontban). Az alapértelmezett érték a 2000. | 
| `normalizedImageMaxHeight` | 50-10000 közötti egész szám | A generált normalizált képek maximális magassága (képpontban). Az alapértelmezett érték a 2000. |

> [!NOTE]
> A normalizált képek maximális szélességének és magasságának 2000 képpont alapértéke az [OCR-képesség](cognitive-search-skill-ocr.md) és a [képelemzési képesség](cognitive-search-skill-image-analysis.md)által támogatott maximális méretektől függ. Az [OCR-képesség](cognitive-search-skill-ocr.md) legfeljebb 4200-es szélességet és magasságot támogat a nem angol nyelvű és a 10000 angol nyelv esetén.  Ha csökkenti a maximális korlátot, a feldolgozás a készségkészlet-definíciótól és a dokumentumok nyelvétől függően meghiúsulhat a nagyobb rendszerképeken. 
## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemeneti név     | Leírás |
|--------------------|-------------|
| file_data | Az a fájl, amelyből a tartalmat ki kell olvasni. |

A "file_data" bemenetnek az alábbiak szerint definiált objektumnak kell lennie:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

A fájl hivatkozási objektuma háromféleképpen hozható létre:

 - A `allowSkillsetToReadFileData` paraméter beállítása az indexelő definíciójában a "true" értékre.  Ezzel létrehoz egy elérési utat `/document/file_data`, amely a blob-adatforrásból letöltött eredeti adatfájlt jelképező objektum. Ez a paraméter csak a blob Storage-ban lévő adatértékekre vonatkozik.

 - Az indexelő definíciójában a `imageAction` paramétert a `none`tól eltérő értékre kell beállítani.  Ez létrehoz egy olyan rendszerképekből álló tömböt, amely a szükséges konvenciót követi, ha a rendszer külön (például `/document/normalized_images/*`) adta át ezt a képességet.

 - Az egyéni képességek egy JSON-objektumot adnak vissza, amely pontosan a fentiek szerint van meghatározva.  A `$type` paramétert pontosan `file` értékre kell beállítani, és a `data` paraméternek a fájl tartalmának alap 64 kódolású bájtos tömb adatának kell lennie.

## <a name="skill-outputs"></a>Szaktudás kimenetei

| Kimenet neve    | Leírás |
|--------------|-------------|
| content | A dokumentum szöveges tartalma. |
| normalized_images | Ha a `imageAction` egy másik értékre van állítva, `none`az új *normalized_images* mező képek tömbjét fogja tartalmazni. Az egyes rendszerképek kimeneti formátumával kapcsolatos további részletekért tekintse meg [a képek kinyerésének dokumentációját](cognitive-search-concept-image-scenarios.md) . |

##  <a name="sample-definition"></a>Minta definíciója

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>Minta bemenet

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>Példa kimenet

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Lásd még:

+ [Beépített szaktudás](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Információk feldolgozása és kinyerése a rendszerképekből a kognitív keresési helyzetekben](cognitive-search-concept-image-scenarios.md)