---
title: Nyelv észlelése cognitive search szakértelem (Azure Search) |} A Microsoft Docs
description: Kiértékeli a strukturálatlan szöveg, és az egyes rekordokhoz, az Azure Search-felderítési bővítést folyamatban elemzés erőssége jelző pontszámot nyelvi azonosítónak visszaad.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 443ac895085053b7c4c876c3deecaa1943c9f506
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171058"
---
#   <a name="language-detection-cognitive-skill"></a>Nyelv észlelése cognitive szakértelem

Legfeljebb 120 nyelv esetén a **nyelvfelismerés** szakértelem észleli a bemeneti szöveg nyelvét, és a kérés küldése minden dokumentum egyetlen nyelvkód jelentéseket. A nyelvi kód az elemzés erőssége jelző pontszámot párban áll.

Ez a funkció akkor különösen hasznos, ha a meg kell adnia a szöveg nyelvét, egyéb képességek bemenetként (például a [Hangulatelemzés szakértelem](cognitive-search-skill-sentiment.md) vagy [szöveg felosztása szakértelem](cognitive-search-skill-textsplit.md)).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>A Data korlátai
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
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill ",
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
