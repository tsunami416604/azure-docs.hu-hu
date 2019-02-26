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
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c55783e9b209a1280a21edca34b75e72481f4cb6
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806988"
---
#   <a name="shaper-cognitive-skill"></a>Shaper cognitive szakértelem

A **Shaper** szakértelem összesíti több bemenet egy összetett típus, amely később a Adatbővítés folyamat lehet hivatkozni. A **Shaper** szakértelem lehetővé teszi, hogy lényegében-struktúra létrehozása, határozza meg, hogy a struktúra tagjai nevét és minden tagjának értéket rendelni. Összevont mezők keresési forgatókönyvekben bizonyulhat hasznosnak, például a vezetéknevet és az utónevet kombinálása egy egyszeres szerkezet, város és a egy egyszeres szerkezet, vagy a név állapotot és egyedi identitása létrehozásához egyetlen struktúrába születési dátumot.

Alapértelmezés szerint ez a módszer támogatja az objektumok, amely egy szintnél mélyebb. Az összetettebb objektumok láncolhatja össze több **Shaper** lépéseket.

A válaszban a kimeneti név mindig "kimeneti". Belsőleg, a folyamat leképezhet egy másik nevet, például a "analyzedText" a "kimeneti", az alábbi példák, de a **Shaper** szakértelem magát a választ adja vissza "kimeneti". Ez lehet fontos Ha képi elemekben gazdag dokumentumok hibakeresést, és figyelje meg, hogy az elnevezési eltérés, vagy ha egyéni műveleteket hozhat létre, és vannak strukturálja a válasz saját magának.

> [!NOTE]
> Szakértelem nincs kötve a Cognitive Services API-t, és nem terheli útmutatójához. Továbbra is ajánlott [Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md), azonban felül a **ingyenes** erőforrás beállítás, amely korlátozza, hogy naponta napi végrehajtott információbeolvasás kis számú.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>1. példa: összetett típusok

Példaként vegyünk egy forgatókönyvet, ahol szeretné létrehozni a szerkezetet *analyzedText* , amely két tagja van: *szöveg* és *vélemények*, illetve. Az Azure Search szolgáltatásban a többrészes kereshető mező neve egy *komplex típus*, és a beépített a rendszer még nem támogatott. Az előzetes verzióhoz egy **Shaper** ismeretek segítségével hozzon létre egy összetett típusú mezők az index. 

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
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>Minta beviteli
A használható bemenet JSON-dokumentumok **Shaper** szakértelem lehet:

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
A **Shaper** szakértelem hoz létre egy új elem nevű *analyzedText* a kombinált elemeinek *szöveg* és *vélemények*. 

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
            "name": "output",
            "targetName": "titlesAndChapters"
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

