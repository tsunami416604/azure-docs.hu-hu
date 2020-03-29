---
title: Dokumentumkivonáskognitív képesség (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Tartalmat bont ki egy fájlból a dúsítási folyamaton belül. Ez a szakértelem jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76837731"
---
# <a name="document-extraction-cognitive-skill"></a>Dokumentum kitermelése kognitív készség

> [!IMPORTANT] 
> Ez a szakértelem jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Jelenleg nincs portál- vagy .NET SDK-támogatás.

A **dokumentum kivonása** szakértelem kinyeri a tartalmat egy fájlból a dúsítási folyamaton belül. Ez lehetővé teszi, hogy kihasználják a dokumentum kinyerési lépés, amely általában akkor történik, mielőtt a skillset végrehajtása a fájlokat, amelyek más képességek által generált.

> [!NOTE]
> A hogy a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti a hatókört, [egy számlázható Cognitive Services-erőforrást kell csatolnia.](cognitive-search-attach-cognitive-services.md) A díjak a Cognitive Services API-k hívásakor és a dokumentumfeltörési szakasz részeként az indexelés részeként keletkeznek. A dokumentumokból történő szövegkinyerésért nem kell díjat fizetni.
>
> A beépített képességek végrehajtása a meglévő [Cognitive Services díja int.](https://azure.microsoft.com/pricing/details/cognitive-services/) A képkinyerési árképzést az [árképzési oldalon ismerteti.](https://go.microsoft.com/fwlink/?linkid=2042400)
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Bemenetek            | Megengedett értékek | Leírás |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | `default` A nem tiszta szövegű vagy json formátumú fájlok dokumentumkinyerésének beállítása. Úgy `text` van beállítva, hogy javítsa az egyszerű szöveges fájlok teljesítményét. Állítsa `json` be, hogy kibontsa a strukturált tartalmat a json fájlokból. Ha `parsingMode` nincs megadva kifejezetten, akkor `default`a . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Állítsa `contentAndMetadata` be, hogy minden metaadatot és szöveges tartalmat kibontson az egyes fájlokból. Úgy `allMetadata` van beállítva, hogy csak a [tartalomtípus-specifikus metaadatokat bontsa](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) ki (például a metaadatok csak .png fájlokra jellemzőek). Ha `dataToExtract` nincs megadva kifejezetten, akkor `contentAndMetadata`a . |
| `configuration` | Lásd alább. | A dokumentum kinyerésének módját módosító választható paraméterek szótára. A támogatott konfigurációs tulajdonságok leírását az alábbi táblázat tartalmazza. |

| Konfigurációs paraméter   | Megengedett értékek | Leírás |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Állítsa `none` be, hogy figyelmen kívül hagyja az adatkészletben lévő beágyazott képeket vagy képfájlokat. Ez az alapértelmezett beállítás. <br/>A [képelemzés segítségével kognitív](cognitive-search-concept-image-scenarios.md) `generateNormalizedImages` képességek , állítsa be, hogy a készség hozzon létre egy sor normalizált képek részeként dokumentum repedés. `parsingMode` Ehhez a művelethez `default` a `dataToExtract` beállítás `contentAndMetadata`a és a beállítására van állítva. A normalizált kép olyan további feldolgozásra utal, amely egységes képkimenetet eredményez, méretezhető és elforgatva elősegíti a konzisztens renderelést, ha a képeket a vizuális keresési eredmények között szerepel (például azonos méretű fényképeket egy grafikonvezérlőben, ahogy az a [JFK demóban](https://github.com/Microsoft/AzureSearch_JFK_Files)látható). Ez az információ minden lemezképhez létrejön, ha ezt a beállítást használja.  <br/>Ha a `generateNormalizedImagePerPage`beállítás , PDF fájlokat kell kezelni másképp, hogy ahelyett, hogy kibontása beágyazott képek, minden oldal jelenik meg, mint egy kép, és ennek megfelelően normalizálódik.  A nem PDF formátumú fájltípusokat `generateNormalizedImages` ugyanúgy kezeli a program, mintha be lett volna állítva.
| `normalizedImageMaxWidth` | Bármely egész szám 50-10000 között | A létrehozott normalizált képek maximális szélessége (képpontban). Az alapértelmezett érték 2000. | 
| `normalizedImageMaxHeight` | Bármely egész szám 50-10000 között | A létrehozott normalizált képek maximális magassága (képpontban). Az alapértelmezett érték 2000. |

> [!NOTE]
> A normalizált képek maximális szélessége és magassága esetén az alapértelmezett 2000 képpont az [OCR-szakértelem](cognitive-search-skill-ocr.md) és a [képelemzési készség](cognitive-search-skill-image-analysis.md)által támogatott maximális méreteken alapul. Az [OCR szakértelem](cognitive-search-skill-ocr.md) a nem angol nyelvek esetében 4200, angol nyelven pedig 10000 maximális szélességet és magasságot támogat.  Ha növeli a maximális korlátokat, a feldolgozás sikertelen lehet a nagyobb képeken a skillset definíciója és a dokumentumok nyelve alapján. 
## <a name="skill-inputs"></a>Szakértelem bemenetei

| Bemenet neve     | Leírás |
|--------------------|-------------|
| file_data | Az a fájl, amelyből a tartalmat ki kell nyerni. |

A "file_data" bemenetnek a következőképpen meghatározott objektumnak kell lennie:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Ez a fájlreferencia-objektum a következő három módszer közül választhat:

 - Az `allowSkillsetToReadFileData` indexelő definíciójának paraméterének beállítása "true" értékre.  Ez létrehoz egy `/document/file_data` elérési utat, amely egy objektum, amely a blob adatforrásból letöltött eredeti fájladatokat jelöli. Ez a paraméter csak a Blob storage-ban lévő adatokra vonatkozik.

 - Az `imageAction` indexelő definíciójának paraméterének beállítása `none`nem a.  Ez egy sor képet hoz létre, amely követi a szükséges konvenciót a `/document/normalized_images/*`bemeneti ezt a készséget, ha át egyenként (azaz ).

 - Az egyéni szakértelem pontosan a fentiek szerint definiált json-objektumot ad vissza.  A `$type` paramétert pontosan `file` kell `data` beállítani, és a paraméternek a fájltartalom 64 kódolású bájttömbjének kell lennie.

## <a name="skill-outputs"></a>Szakértelem kimenetei

| Kimenet neve    | Leírás |
|--------------|-------------|
| content | A dokumentum szöveges tartalma. |
| normalized_images | Ha `imageAction` a értéke más `none`értékre van beállítva, az új *normalized_images* mező egy sor képet fog tartalmazni. Az egyes képek kimeneti formátumával kapcsolatos további részleteket [a képkinyerésdokumentációjában](cognitive-search-concept-image-scenarios.md) talál. |

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

##  <a name="sample-input"></a>Mintabevitel

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

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Az információk feldolgozása és kinyerése a képekből kognitív keresési forgatókönyvekben](cognitive-search-concept-image-scenarios.md)