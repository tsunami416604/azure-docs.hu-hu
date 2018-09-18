---
title: Key kifejezés kinyerése kognitív keresés szakértelem (Azure Search) |} A Microsoft Docs
description: Kiértékeli a strukturálatlan szöveg, és az egyes rekordok listáját adja vissza a kulcskifejezéseket Azure Search-felderítési bővítést folyamatban.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 694271115626c652523be34160ad6a07053f6387
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45735798"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Kulcs kifejezések kinyerése kognitív szakértelem

A **kulcs kulcsszókeresés** szakértelem strukturálatlan szöveges kiértékeli, és az egyes rekordok listáját adja vissza, kulcskifejezéseket.

Ez a funkció akkor hasznos, ha az a rekord található fő beszédtémákat gyorsan azonosítani kell. Ha például adott bemeneti szöveg "az élelmiszer delicious volt, és izgalommal személyzet történt", a szolgáltatás a "food" és "izgalommal személyzetet" adja vissza.

> [!NOTE]
> A kognitív keresés nyilvános előzetes verzióban érhető el. Képességcsoport végrehajtási, és a lemezkép kinyerése és a normalizálási jelenleg rendelkezésre állnak az ingyenes. Később az ezen funkciók díjszabásáról jelentjük be. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>A Data korlátai
Egy rekord maximális mérete 50 000 karakter által mért kell lennie `String.Length`. Ha az adatok tördelésével, mielőtt elküldené a kulcskifejezések információkinyerő van szüksége, fontolja meg a [szöveg felosztása szakértelem](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Ismeretek paraméterek

A paraméterei a kis-és nagybetűket.
| Bemenetek                | Leírás |
|---------------------|-------------|
| defaultLanguageCode | (Nem kötelező) A alkalmazni a dokumentumokat, explicit módon nem ad meg nyelvi nyelvkód.  Ha az alapértelmezett nyelvi kód nem megadott, az angol (en) lesz az alapértelmezett nyelvi kódot. <br/> Lásd: [támogatott nyelvek teljes listáját](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Nem kötelező) A kulcskifejezések előállításához maximális számát. |

## <a name="skill-inputs"></a>Ismeretek bemenetek
| Bemenetek     | Leírás |
|--------------------|-------------|
| szöveg | Elemezni kívánt szöveget.|
| languageCode  |  A nyelv, a rekordok jelző karakterlánc. Ha ez a paraméter nincs megadva, az alapértelmezett nyelvi kód a rekordok elemzésére használható. <br/>Lásd: [támogatott nyelvek teljes listáját](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Minta-definíció

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

##  <a name="sample-input"></a>Minta beviteli

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
Egy nem támogatott nyelvi kódot ad meg, ha hiba történik, és a kulcskifejezéseket nem ki kell olvasni.
Ha a szöveg üres, figyelmeztetés előállított.
Ha a szöveg mérete nagyobb, mint 50 000 karakter, csak az első 50 000 karakter elemzik, és megjelenik egy figyelmeztetés.

## <a name="see-also"></a>Lásd még

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)