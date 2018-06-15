---
title: Kulcs kifejezés kibontási kognitív keresési szakértelem (Azure Search) |} Microsoft Docs
description: Strukturálatlan szöveg kiértékeli, és az egyes rekordokhoz, olyan listát ad vissza a legfontosabb kifejezések egy Azure Search dúsító folyamat.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a12efaa020e626e4a10a0708c9b84d8fe125588c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33791034"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Kulcs kifejezés kibontási kognitív szakértelem

A **kulcs kifejezés kibontási** szakértelem strukturálatlan szöveg kiértékeli, és az egyes rekordokhoz, a legfontosabb kifejezések listáját adja vissza.

Ez a funkció akkor hasznos, ha szüksége gyorsan azonosíthatja a fő beszélő pontok a rekordban. Például adott bemeneti szöveg "az étele delicious volt, és nem találhatók a csodálatos személyzet", a szolgáltatás "étele" és "csodálatos személyzet" adja vissza.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Adatok korlátok
A maximális mérete egy olyan rekordot kell mért 50 000 karakternél `String.Length`. Ha feloszthatja az adatokat, mielőtt elküldené a kulcs kifejezés kivonatoló van szüksége, fontolja meg a [szöveges szakértelem](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Szakértelem paraméterek

A paraméterei a kis-és nagybetűket.
| Bemenetek                | Leírás |
|---------------------|-------------|
| defaultLanguageCode | (Választható) Az explicit módon nem ad meg nyelvi alkalmazza nyelvi kódot.  Ha az alapértelmezett nyelvi kód nem megadott, az angol (en) lesz az alapértelmezett nyelvi kódot. <br/> Lásd: [támogatott nyelvek teljes listáját](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Választható) A legfontosabb kifejezések eredményezett maximális száma. |

## <a name="skill-inputs"></a>Szakértelem bemenetek
| Bemenetek     | Leírás |
|--------------------|-------------|
| Szöveg | A szöveg elemzése.|
| languageCode  |  Egy karakterlánc, amely a nyelvi bejegyzések. Ha ez a paraméter nincs megadva, az alapértelmezett nyelv kódja használandó elemzése a rekordok. <br/>Lásd: [támogatott nyelvek teljes listája](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>A minta meghatározása

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
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
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Hibák és figyelmeztetések
Egy nem támogatott nyelvi kódot adja meg, ha hiba történik, és a legfontosabb kifejezések nem ki kell olvasni.
Ha a szöveg üres, a rendszer figyelmeztetést gyűjtenek.
Ha a szöveg mérete nagyobb, mint 50 000 karakter, csak az első 50 000 karakternél elemzik, és megjelenik egy figyelmeztetés.

## <a name="see-also"></a>Lásd még

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)