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
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: b8cc69c45332d0779c6e57b5d74145ee1f5140cd
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391008"
---
#   <a name="shaper-cognitive-skill"></a>Shaper cognitive szakértelem

A **Shaper** szakértelem hoz létre összetett összetett mezők (más néven többrészes mezők) támogatásához. A komplex típus mező több részből áll, de úgy viselkedik, mint az Azure Search-index egyetlen elemet. Összevont mezők keresési forgatókönyvekben bizonyulhat hasznosnak, például a vezetéknevet és az utónevet összefűzhet egy mezőjéhez, város és állam egyetlen mezőt, vagy nevét és a születési dátumát egyedi identitása létrehozásához egyetlen mezőbe.

A **Shaper** szakértelem lehetővé teszi, hogy lényegében-struktúra létrehozása, határozza meg, hogy a struktúra tagjai nevét és minden tagjának értéket rendelni.

Alapértelmezés szerint ez a módszer támogatja az objektumok, amely egy szintnél mélyebb. Az összetettebb objektumok láncolhatja össze több **Shaper** lépéseket.

A válaszban a kimeneti név mindig "kimeneti". Belsőleg, a folyamat leképezhet egy másik nevet, például a "analyzedText" a "kimeneti", az alábbi példák, de a **Shaper** szakértelem magát a választ adja vissza "kimeneti". Ez lehet fontos Ha képi elemekben gazdag dokumentumok hibakeresést, és figyelje meg, hogy az elnevezési eltérés, vagy ha egyéni műveleteket hozhat létre, és vannak strukturálja a válasz saját magának.

> [!NOTE]
> Szakértelem nincs kötve a Cognitive Services API-t, és nincsenek szakértelem nélkül díjak. Azonban meg kell [Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md) ennek ellenére a szabad erőforrás beállítás felülbírálása, amely korlátozza, hogy naponta végrehajtott információbeolvasás kis számú.

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
      "targetName": analyzedText"
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

