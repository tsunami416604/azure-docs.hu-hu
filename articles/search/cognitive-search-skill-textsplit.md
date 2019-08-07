---
title: A kognitív keresési képességek szövegének felosztása – Azure Search
description: Szöveg felosztása darabokra vagy szöveges lapokra egy Azure Search alkoholtartalom-növelési folyamaton alapuló hossz alapján.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 8fb7ff51507212dfb162c09fcee469d6f154f3c3
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840898"
---
#   <a name="text-split-cognitive-skill"></a>Szöveg felosztása kognitív képességgel

A **szöveg felosztása** készséggel szövegrészek szövegét bontja. Megadhatja, hogy a szöveget mondatokra vagy adott hosszúságú lapokra kívánja-e bontani. Ez a képesség különösen akkor hasznos, ha az egyéb szakismeretekben legfeljebb egy szöveges hosszra vonatkozó követelmény van. 

> [!NOTE]
> Ez a képesség nem kötődik Cognitive Services API-hoz, és nem kell fizetnie a használatért. Továbbra is [csatlakoztatnia kell egy Cognitive Services](cognitive-search-attach-cognitive-services.md)-erőforrást, hogy felülírja az **ingyenes** erőforrás-beállítást, amely naponta csak kis mennyiségű napi dúsítást korlátozza.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket.

| Paraméternév     | Leírás |
|--------------------|-------------|
| textSplitMode      | Vagy "Pages" vagy "mondatok" | 
| maximumPageLength | Ha a textSplitMode "Pages" (lapok `String.Length`) értékre van állítva, ez az oldal maximális hosszára vonatkozik. A minimális érték 100.  Ha a textSplitMode "Pages" értékre van állítva, az algoritmus megpróbálja felosztani a szöveget a legtöbb "maximumPageLength" méretű darabokra. Ebben az esetben az algoritmus elvégzi a legjobbat, hogy a mondatot egy mondat határán megtörje, így a tömb mérete valamivel kisebb lehet, mint a "maximumPageLength". | 
| defaultLanguageCode   | választható A következő nyelvi kódok egyike: `da, de, en, es, fi, fr, it, ko, pt`. Az alapértelmezett érték az angol (en). Néhány megfontolandó szempont:<ul><li>Ha languagecode-országhívószám formátumot továbbít, a rendszer csak a formátum languagecode-részét használja.</li><li>Ha a nyelv nem szerepel az előző listán, a felosztott képesség megszakítja a szöveget a karakterek határain.</li><li>A nyelvi kód megadásával elkerülhető, hogy a szó ne legyen több, mint a kínai, Japán és koreai nyelveken.</li></ul>  |


## <a name="skill-inputs"></a>Szaktudás bemenetei

| Paraméternév       | Leírás      |
|----------------------|------------------|
| text  | Az alsztringbe feldarabolt szöveg |
| languageCode  | Választható A dokumentumhoz tartozó nyelvi kód.  |

## <a name="skill-outputs"></a>Szaktudás kimenetei 

| Paraméternév     | Leírás |
|--------------------|-------------|
| textItems | A kinyert alsztringek tömbje. |


##  <a name="sample-definition"></a>Minta definíciója

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

##  <a name="sample-input"></a>Minta bemenet

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
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

##  <a name="sample-output"></a>Minta kimenete

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

## <a name="error-cases"></a>Hibák esetei
Ha a nyelv nem támogatott, a rendszer figyelmeztetést generál, és a szöveg a karakter határain belülre van bontva.

## <a name="see-also"></a>Lásd még

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
