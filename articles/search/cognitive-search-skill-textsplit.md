---
title: Szöveg osztott kognitív készség
titleSuffix: Azure Cognitive Search
description: A szöveget adattömbökre vagy szövegoldalakra bonthatja az Azure Cognitive Search AI-dúsítási folyamatának hossza alapján.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3f80169808b1e6420f04b786d2bb06bde9c96231
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73479655"
---
# <a name="text-split-cognitive-skill"></a>Szöveg osztott kognitív készség

A **Szövegfelosztás** szakértelem szövegrészekre bontja a szöveget. Megadhatja, hogy a szöveget mondatokra vagy adott hosszúságú oldalakra szeretné-e bontani. Ez a szakértelem különösen akkor hasznos, ha a szöveg hosszára vonatkozó követelmények más későbbi képességekben vannak. 

> [!NOTE]
> Ez a szakértelem nem kötődik a Cognitive Services API-t, és nem kell fizetnie a használata. Továbbra is [csatolja a Cognitive Services-erőforrás,](cognitive-search-attach-cognitive-services.md)azonban az **ingyenes** erőforrás beállítás, amely korlátozza, hogy egy kis számú napi dúsítások naponta.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| textSplitMode      | "oldalak" vagy "mondatok" | 
| maximumOldaloldalhossz | Ha a textSplitMode beállítása "oldalak", akkor ez a . `String.Length` A minimális érték 100.  Ha a textSplitMode "oldalak" értékre van állítva, az algoritmus megpróbálja a szöveget olyan adattömbökre osztani, amelyek legfeljebb "maximumPageLength" méretűek. Ebben az esetben az algoritmus mindent megtesz, hogy megtörje a mondatot egy mondat határán, így az adattömb mérete valamivel kisebb lehet, mint a "maximumPageLength". | 
| defaultLanguageCode   | (nem kötelező) A következő nyelvkódok `da, de, en, es, fi, fr, it, ko, pt`egyike: . Az alapértelmezett az angol (en). Kevés dolog, hogy fontolja meg:<ul><li>Ha nyelvkód-országkód formátumot ad meg, a program csak a formátum languagecode részét használja.</li><li>Ha a nyelv nem szerepel az előző listában, a felosztott szakértelem megtöri a szöveget a karakterhatároknál.</li><li>A nyelvkód megadása akkor hasznos, ha nem vághat félbe egy szót a nem szóvilágú nyelvek , például a kínai, a japán és a koreai nyelv esetében.</li><li>Ha nem ismeri a nyelvet (azaz fel kell osztania a szöveget a [LanguageDetectionSkill-be](cognitive-search-skill-language-detection.md)történő bevitelhez), az angol (en) alapértelmezett beállításelegendő. </li></ul>  |


## <a name="skill-inputs"></a>Szakértelem bemenetei

| Paraméter neve       | Leírás      |
|----------------------|------------------|
| szöveg  | A karakterlánc-részhalmazra felosztandó szöveg. |
| languageCode  | (Nem kötelező) A dokumentum nyelvkódja. Ha nem ismeri a nyelvet (azaz fel kell osztania a szöveget a [LanguageDetectionSkill-be](cognitive-search-skill-language-detection.md)való bevitelhez), akkor biztonságosan eltávolíthatja ezt a bemenetet.  |

## <a name="skill-outputs"></a>Szakértelem kimenetei 

| Paraméter neve     | Leírás |
|--------------------|-------------|
| textItems (szövegelemek) | Kivont karakterláncok tömbje. |


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

##  <a name="sample-input"></a>Mintabemenet

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

##  <a name="sample-output"></a>Kimenetpélda

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

## <a name="error-cases"></a>Hibaesetek
Ha egy nyelv nem támogatott, a rendszer figyelmeztetést hoz létre, és a szöveget karakterhatárokon osztja fel.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
