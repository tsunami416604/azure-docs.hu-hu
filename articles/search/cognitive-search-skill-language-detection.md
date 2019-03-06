---
title: Nyelv észlelése cognitive search szakértelem – Azure Search
description: Kiértékeli a strukturálatlan szöveg, és az egyes rekordokhoz, az Azure Search-felderítési bővítést folyamatban elemzés erőssége jelző pontszámot nyelvi azonosítónak visszaad.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 605f4c639cfc8c0f9732f7347532e1bd7edc055f
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404654"
---
#   <a name="language-detection-cognitive-skill"></a>Nyelv észlelése cognitive szakértelem

A **nyelvfelismerés** szakértelem észleli a bemeneti szöveg nyelvét, és a kérés küldése minden dokumentum egyetlen nyelvkód jelentéseket. A nyelvi kód az elemzés erőssége jelző pontszámot párban áll. Ezen a képzettségi használja a gépi tanulási modellek által biztosított [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) a Cognitive Services.

Ez a funkció akkor különösen hasznos, ha a meg kell adnia a szöveg nyelvét, egyéb képességek bemenetként (például a [Hangulatelemzés szakértelem](cognitive-search-skill-sentiment.md) vagy [szöveg felosztása szakértelem](cognitive-search-skill-textsplit.md)).

Nyelv észlelése kihasználja a Bing a természetes nyelvi feldolgozás könyvtárak, ami meghaladja az értéket a [támogatott nyelvek és régiók](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) szövegelemzési felsorolt. Nyelvek listája pontosan nincs közzétéve, de minden széles körben beszélt nyelv plusz variantní hodnoty, dialektusok és néhány regionális és kulturális nyelv. Ha a kevésbé gyakran használt nyelven tartalommal rendelkezik, [az nyelvi API-t próbálja](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) megtekintheti, ha egy kódot ad vissza. A válasz nem észlelhető nyelvek `unknown`.

> [!NOTE]
> 2018. December 21., kezdési is [Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md) és a egy Azure Search-képességek alkalmazási lehetőségét. Ez lehetővé teszi indexmezők végrehajtási díjszabási elindításához. Ezen a napon azt is már díjszabási kép kinyerése a dokumentumfeltörést fázis részeként. A dokumentumok szövegkinyerés továbbra is ingyenesen kínáljuk.
>
> [Beépített kognitív szakértelem](cognitive-search-predefined-skills.md) végrehajtás díja a [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services), azonos értékelje, ha végrehajtotta a feladat közvetlenül. Kép kinyerése nem egy Azure Search költségekkel, jelenleg az előzetes verzió áron érhető el. További információkért lásd: a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) vagy [számlázás módját works](search-sku-tier.md#how-billing-works).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Adatkorlátok
Egy rekord maximális mérete 50 000 karakter által mért kell lennie `String.Length`. Ha az adatok tördelésével, mielőtt elküldené a róluk szóló véleményeket analyzer van szüksége, használhat a [szöveg felosztása szakértelem](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Ismeretek bemenetek

A paraméterei a kis-és nagybetűket.

| Bemenetek     | Leírás |
|--------------------|-------------|
| szöveg | Elemezni kívánt szöveget.|

## <a name="skill-outputs"></a>Ismeretek kimenetek

| Kimeneti név    | Leírás |
|--------------------|-------------|
| languageCode | Az azonosított nyelv ISO 6391 nyelvi kódot. Például "hu". |
| LanguageName | Nyelv neve. Például "angol". |
| pontszám | 0 és 1 közötti értéket. Annak valószínűsége, hogy nyelvi megfelelően legyen azonosítva. A pontszám 1-nél kisebb lehet, ha a mondat van vegyes nyelveket.  |

##  <a name="sample-definition"></a>Minta-definíció

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>Minta beviteli

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>Példa kimenet

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>Hibák esetén
Szöveg egy nem támogatott nyelven van kifejezve, ha hiba történik, és nincs nyelvi azonosítót adja vissza.

## <a name="see-also"></a>Lásd még

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
