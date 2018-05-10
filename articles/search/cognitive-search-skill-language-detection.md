---
title: Nyelvi észlelési kognitív keresési szakértelem (Azure Search) |} Microsoft Docs
description: Strukturálatlan szöveg kiértékeli, és az egyes rekordokhoz, ad vissza egy nyelvi azonosító egy Azure Search dúsító folyamat elemzés erősségével jelző pontszámot.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 338d89b47ea451efcf8300d4ac016a6946a95259
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
#   <a name="language-detection-cognitive-skill"></a>Nyelvi észlelési kognitív szakértelem

Legfeljebb 120 nyelvek esetén a **nyelvi észlelési** szakértelem észleli a bemeneti szöveg nyelvét és jelentések minden dokumentumhoz, a kérés küldése egy egyetlen nyelvi kódot. A nyelvi kódot párosított az elemzés erősségével jelző pontszámot.

Ez a funkció akkor különösen akkor hasznos, ha meg kell adnia a szöveg nyelvének más képességek bemeneti (például a [véleményeket Snalysis szakértelem](cognitive-search-skill-sentiment.md) vagy [szöveges szakértelem](cognitive-search-skill-textsplit.md)).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Adatok korlátok
A maximális mérete egy olyan rekordot kell mért 50 000 karakternél `String.Length`. Ha feloszthatja az adatokat, mielőtt elküldené a céggel kapcsolatos véleményeket analyzer van szüksége, használhatja a [szöveges szakértelem](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Szakértelem bemenetek

A paraméterei a kis-és nagybetűket.

| Bemenetek     | Leírás |
|--------------------|-------------|
| Szöveg | A szöveg elemzése.|

## <a name="skill-outputs"></a>Szakértelem kimenetek

| Kimeneti neve    | Leírás |
|--------------------|-------------|
| languageCode | Az ISO-6391 nyelvi kódját azonosított nyelven. Például "hu". |
| LanguageName | Nyelv neve. Például "angol." |
| Pontszám | 0 és 1 közötti értéket. A valószínűsége, hogy a nyelvi helyesen azonosítani. A pontszám 1-nél kisebb lehet, ha a mondatok rendelkezik vegyes nyelveket.  |

##  <a name="sample-definition"></a>A minta meghatározása

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

##  <a name="sample-input"></a>A minta bemenet

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
Ha a szöveg egy nem támogatott nyelven van kifejezve, hiba történik, és nincs nyelvi azonosító adja vissza.

## <a name="see-also"></a>Lásd még

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)