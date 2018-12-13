---
title: Shaper cognitive search szakértelem – Azure Search
description: Metaadatok és strukturált információt kinyerése strukturálatlan adatok, és vegyen részt Ön az Azure Search-felderítési bővítést folyamatban összetett típus.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 627c53f7339dbc35d822a0bf6038ca0f1ea5e653
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313836"
---
#   <a name="shaper-cognitive-skill"></a>Shaper cognitive szakértelem

A **Shaper** szakértelem hoz létre összetett összetett mezők (más néven többrészes mezők) támogatásához. A komplex típus mező több részből áll, de úgy viselkedik, mint az Azure Search-index egyetlen elemet. Összevont mezők keresési forgatókönyvekben bizonyulhat hasznosnak, például a vezetéknevet és az utónevet összefűzhet egy mezőjéhez, város és állam egyetlen mezőt, vagy nevét és a születési dátumát egyedi identitása létrehozásához egyetlen mezőbe.

A Shaper szakértelem lehetővé teszi, hogy lényegében-struktúra létrehozása, határozza meg, hogy a struktúra tagjai nevét, és rendelje hozzá az értékeket minden egyes tagjára.

Alapértelmezés szerint ez a módszer támogatja az objektumok, amely egy szintnél mélyebb. Az összetett objektumokat láncolhatja össze több Shaper lépést.

A válaszban a kimeneti név mindig "kimeneti". Belsőleg a folyamat leképezhet egy másik nevet, például a "kimeneti", de a Shaper a következő példa "analyzedText" magát szakértelem adja vissza "kimeneti" a válaszban. Ez lehet fontos Ha képi elemekben gazdag dokumentumok hibakeresést, és figyelje meg, hogy az elnevezési eltérés, vagy ha egyéni műveleteket hozhat létre, és vannak strukturálja a válasz saját magának.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>1. példa: összetett típusok

Példaként vegyünk egy forgatókönyvet, ahol szeretné létrehozni a szerkezetet *analyzedText* , amely két tagja van: *szöveg* és *vélemények*, illetve. Az Azure Search szolgáltatásban a többrészes kereshető mező neve egy *komplex típus*, és a beépített a rendszer még nem támogatott. Ebben az előzetes verzióban Shaper szakértelem egy összetett típusú mezők az index létrehozására használható. 

Az alábbi példa neveket tartalmaz a tag bemeneteként. A kimeneti struktúra (az Azure Search szolgáltatásban az összetett mező) van megadva a *targetName*. 


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

### <a name="sample-input"></a>Minta beviteli
JSON-dokumentumok biztosítása használható bemeneti Shaper szakértelem lehet:

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
Shaper szakértelem hoz létre egy új elem nevű *analyzedText* a kombinált elemeinek *szöveg* és *vélemények*. 

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

Egy másik példa, hogy a feldolgozási folyamat különböző szakaszaiban kicsomagolta a könyv különböző oldalain fejezet címek és egy könyv címe imagine. Most már létrehozhat egy egyszeres szerkezet mikroszolgáltatásokból álló, ezeket a különféle bemeneteket.

Ebben a forgatókönyvben Shaper szakértelem definíciója az alábbi példához hasonlóan nézhet ki:

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
Ebben az esetben a Shaper lapossá teszi az összes fejezet címek hozzon létre egy egyetlen olyan tömböt. 

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

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)

