---
title: Szöveg felosztása kognitív keresési szakértelem (Azure Search) |} Microsoft Docs
description: Szöveg felosztása adattömbök vagy a szöveget egy Azure Search dúsító folyamat hossza alapján.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: dbb9261cfce0a8437cfe76121fa16aa87c4b3393
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
#   <a name="text-split-cognitive-skill"></a>Ossza fel a kognitív szakértelem szöveg

A **szöveges** szakértelem szöveg bontja szövegrészeket. Megadhatja, hogy szeretné-e a mondatok vagy lapok adott hosszúságú bonthatja. A szakértelem, különösen akkor hasznos, ha szöveg maximális hossz követelményeinek után más képességek. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Szakértelem paraméterek

A paraméterei a kis-és nagybetűket.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| textSplitMode      | "Lap" vagy "mondat" | 
| maximumPageLength | Ha textSplitMode "lap" van megadva, ez vonatkozik a maximális hossz mért `String.Length`. A minimális érték 100. | 
| defaultLanguageCode   | (választható) A következő nyelvi kódok: `da, de, en, es, fi, fr, it, ko, pt`. Alapértelmezés szerint angol (en). Néhány dolgot figyelembe kell venni:<ul><li>Egy languagecode-országhívószám formátumban adja meg, ha a formátum csak a languagecode része használható.</li><li>A nyelvi nincs a fenti listán szereplő, ha a felosztás szakértelem megsérti a szöveg karakter határokat.</li><li>Biztosít egy nyelvi kódot akkor hasznos, a word kivágja fele, például a kínai, japán és koreai szóköztől nyelvek esetén a elkerülése érdekében.</li></ul>  |


## <a name="skill-inputs"></a>Szakértelem bemenetek

| Paraméter neve       | Leírás      |
|----------------------|------------------|
| Szöveg  | A szöveg felosztása karakterláncrészletet. |
| languageCode  | (Választható) A dokumentum nyelvi kódot.  |

## <a name="skill-outputs"></a>Szakértelem kimenetek 

| Paraméter neve     | Leírás |
|--------------------|-------------|
| textItems | A tömb karakterláncrészletet tartalmazza, amelyek könyvtárban találhatók. |


##  <a name="sample-definition"></a>A minta meghatározása

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
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
            "data": {
                "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Minta kimenet

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Hibák esetén
A nyelv nem támogatott, ha generál a rendszer figyelmeztetést, és a szöveg van szétosztva: karakter határokat.

## <a name="see-also"></a>Lásd még

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)
