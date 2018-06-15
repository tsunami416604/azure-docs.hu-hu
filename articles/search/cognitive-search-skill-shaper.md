---
title: Formázóban kognitív keresési szakértelem (Azure Search) |} Microsoft Docs
description: Metaadatok és a strukturált információk kinyerése strukturálatlan adatok, és alakul azt egy Azure Search dúsító folyamat összetett típusként.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 311f4bd67081de567763783a9d86540eda36d9f8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33791006"
---
#   <a name="shaper-cognitive-skill"></a>Formázóban kognitív szakértelem

A **formázóban** szakértelem létrehoz egy összetett típus összetett mezők (más néven többrészes mezők) támogatásához. Egy összetett típus mező több részből áll, de a rendszer csak egy elemet az Azure Search-index. Konszolidált mezők keresési esetekben hasznos, például a első és utolsó nevét kombinálása egy egyetlen mezőben, város és az állapotot a egyetlen mezőben, vagy a név és egyedi identitásának meghatározásához egyetlen mezőbe születési dátumot.

A formázóban szakértelem lehetővé teszi lényegében hozzon létre egy struktúra, struktúra tag nevét adja meg, és értéket hozzárendelni minden tag.

Alapértelmezés szerint ez a módszer támogatja az objektumok, amelyek egy szintnél mélyebb. Az összetett objektumok több formázóban lépéseket láncában találhatók.

A válaszban a kimeneti neve mindig "kimeneti". Belső a feldolgozási sor leképezhet egy másik nevet, például a "kimeneti", de a formázóban a következő példa "analyzedText" szakértelem saját magát adja vissza "kimeneti" a válaszban. Ez lehet fontos Ha bővített dokumentumok hibakeresést, és figyelje meg az elnevezési eltérés, vagy ha egy egyéni szakértelem build és struktúrájának kialakításakor a válasz saját maga.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>1. példa: összetett típusok

Egy olyan esetet, ahol szeretne létrehozni egy struktúra nevű *analyzedText* , amely két tagokkal rendelkezik: *szöveg* és *véleményeket*, illetve. Az Azure Search többrészes kereshető mező neve egy *összetett típus*, és még nem támogatott a kezdő verzióról. Ebben az előzetesben formázóban szakértelem használható összetett típusú mezők létrehozni az indexet. 

Az alábbi példa nevek biztosít a tag a bemeneti adatként. A kimeneti szerkezete (az Azure Search összetett mező) van megadva a *targetName*. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>A minta bemenet
Egy JSON-dokumentum használható bemeneti biztosítva formázóban szakértelem lehet:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="sample-output"></a>Példa kimenet
A formázóban szakértelem állít elő, nevű új elem *analyzedText* kombinált elemei a *szöveg* és *véleményeket*. 

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="sample-2-input-consolidation"></a>2. példa: a bemeneti összevonása

Egy másik Példa képzelhető el, hogy pipeline-feldolgozási különböző szakaszaiban, kicsomagolta könyv címe, és a könyv különböző oldalain fejezet címek. Mostantól létrehozhat egy egyszeres szerkezet származó különböző összetevői.

Ebben a forgatókönyvben formázóban szakértelem definícióját a következő példa látható:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*"
        }
    ],
    "outputs": [
        {
            "output": "titlesAndChapters",
            "targetName": "analyzedText"
        }
    ]
}
```

### <a name="sample-output"></a>Példa kimenet
Ebben az esetben a formázóban simítja egyetlen tömb létrehozásához minden fejezet nevet. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Lásd még

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)

