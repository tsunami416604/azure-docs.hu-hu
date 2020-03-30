---
title: Formázó kognitív képesség
titleSuffix: Azure Cognitive Search
description: Metaadatok és strukturált adatok kinyerése strukturálatlan adatokból, és összetett típusként alakíthatja ki őket az Azure Cognitive Search AI-dúsítási folyamatában.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 384b79037bb30656934c5e4b596dac2b776593b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754114"
---
# <a name="shaper-cognitive-skill"></a>Formázó kognitív képesség

A **Shaper** szakértelem több bemenetet egyesít egy [összetett típusba,](search-howto-complex-data-types.md) amely a dúsítási folyamat későbbi részében hivatkozhat. A **Shaper** szakértelem lehetővé teszi, hogy lényegében hozzon létre egy struktúrát, meghatározza a struktúra tagjainak nevét, és értékeket rendeljen az egyes tagokhoz. A keresési forgatókönyvekben hasznos konszolidált mezők közé tartozik például a vezeték- és keresztnév egyetlen struktúrába, városba és államba történő egyesítése egyetlen struktúrában, vagy név és születési dátum egyetlen struktúrába az egyedi identitás létrehozásához.

Emellett a **Shaper** szakértelem illusztrált [forgatókönyv 3](#nested-complex-types) hozzáad egy opcionális *sourceContext* tulajdonság a bemenethez. A *forrás* és *a sourceContext* tulajdonságai kölcsönösen kizárják egymást. Ha a bemenet a szakértelem kontextusában van, egyszerűen használja a *forrást.* Ha a bemenet *más* környezetben van, mint a szakértelem környezetében, használja a *sourceContext*. A *sourceContext* megköveteli, hogy definiáljon egy beágyazott bemeneti az adott elem címezve, mint a forrás. 

A kimenet neve mindig "kimenet". Belsőleg a folyamat más nevet is leképezhet, például "analyzedText", ahogy az alábbi példákban látható, de maga a **Shaper** szakértelem "kimenetet" ad vissza a válaszban. Ez akkor lehet fontos, ha bővített dokumentumokat vesz fel, és észleli az elnevezési eltérést, vagy ha egyéni szakként hoz létre, és saját maga strukturálja a választ.

> [!NOTE]
> A **Shaper** szakértelem nem kötődik a Cognitive Services API-t, és nem kell fizetnie a használata. Továbbra is [csatolja a Cognitive Services-erőforrás,](cognitive-search-attach-cognitive-services.md)azonban az **ingyenes** erőforrás beállítás, amely korlátozza, hogy egy kis számú napi dúsítások naponta.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>1. forgatókönyv: összetett típusok

Vegyünk egy olyan forgatókönyvet, amelyben egy *analyzedText* nevű struktúrát szeretne létrehozni, amelynek két tagja van: *szöveg* és *hangulat*. Az indexben a többrészes kereshető mezőt *összetett típusnak* nevezzük, és gyakran akkor jön létre, ha a forrásadatok megfelelő összetett struktúrával rendelkeznek, amely hozzá van rendelve.

Az összetett típusok létrehozásának egy másik megközelítése azonban a **Shaper** szakértelem. Ha ezt a képzettséget egy skillset, a memórián belüli műveletek során skillset feldolgozás a beágyazott struktúrákkal rendelkező adatalakzatok kimenetelét, amelyek ezután leképezhetők egy összetett típus az indexben. 

A következő példa szakértelem-definíció a tagneveket adja meg bemenetként. 


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

### <a name="sample-index"></a>Mintaindex

A skillset egy indexelő által meghívott, és egy indexelő indexelő igényel indexet. Az indexben lévő összetett mezőábrázolás a következő példához hasonló lehet. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Szakértelem bevitele

A **Shaper-szakértelemhez** használható bemenetet biztosító bejövő JSON-dokumentum a következő lehet:

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


### <a name="skill-output"></a>Szakértelem kimenete

A **Shaper** szakértelem létrehoz egy új elemet, az úgynevezett *analyzedText* a *szöveg* és a *hangulat*együttes elemeit. Ez a kimenet megfelel az indexsémának. A program importálja és indexeli az Azure Cognitive Search indexben.

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

## <a name="scenario-2-input-consolidation"></a>2. forgatókönyv: input konszolidáció

Egy másik példában képzelje el, hogy a folyamatfeldolgozás különböző szakaszaiban kibszerezte egy könyv címét és a könyv különböző oldalain lévő fejezetcímeket. Most már létrehozhat egy egyetlen struktúrát, amely ezekből a különböző bemenetekből áll.

A **Shaper** szakértelem-definíció ebben a forgatókönyvben a következő példához hasonlóan nézhet ki:

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
            "source": "/document/content/pages/*/chapterTitles/*/title"
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

### <a name="skill-output"></a>Szakértelem kimenete
Ebben az esetben a **Shaper** összeolvasztja az összes fejezetcímet, hogy egyetlen tömböt hozzon létre. 

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

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>3. forgatókönyv: bemeneti konszolidáció beágyazott környezetekből

Képzelje el, hogy rendelkezik egy könyv címével, fejezeteivel és tartalmával, és entitásfelismerést és kulcskifejezéseket futtat a tartalomon, és most a különböző készségek eredményeit egyetlen alakzatba kell összesítenie a fejezet nevével, entitásaival és kulcskifejezéseivel.

A **Shaper** szakértelem-definíció ebben a forgatókönyvben a következő példához hasonlóan nézhet ki:

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
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
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

### <a name="skill-output"></a>Szakértelem kimenete
Ebben az esetben az **alakzatalakzat** összetett típust hoz létre. Ez a struktúra a memóriában létezik. Ha [tudástárolóba](knowledge-store-concept-intro.md)szeretné menteni, hozzon létre egy vetítést a skillsetben, amely meghatározza a tárolási jellemzőket.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Összetett típusok használata](search-howto-complex-data-types.md)
+ [Tudástároló (előzetes verzió)](knowledge-store-concept-intro.md)
+ [Tudástároló létrehozása a REST-ben](knowledge-store-create-rest.md)