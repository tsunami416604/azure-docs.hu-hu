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
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 058b6c979346d9dcce36940432d0e222e919dba9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540827"
---
#   <a name="shaper-cognitive-skill"></a>Shaper cognitive szakértelem

A **Shaper** szakértelem összesíti azokat több bemenet egy [komplex típus](search-howto-complex-data-types.md) , amely később a Adatbővítés folyamatban lehet hivatkozni. A **Shaper** szakértelem lehetővé teszi, hogy lényegében-struktúra létrehozása, határozza meg, hogy a struktúra tagjai nevét és minden tagjának értéket rendelni. Összevont mezők keresési forgatókönyvekben bizonyulhat hasznosnak, például a vezetéknevet és az utónevet kombinálása egy egyszeres szerkezet, város és a egy egyszeres szerkezet, vagy a név állapotot és egyedi identitása létrehozásához egyetlen struktúrába születési dátumot.

Az API-verzió határozza meg, megfelelő átalakítását, mélysége érheti el. 

| API-verzió | Alakításra viselkedések | 
|-------------|-------------------|
| A REST API-t a 2019-05-06-preview verzióját (a .NET SDK-t nem támogatott) | Összetett objektumok több, a mély egy szinttel **Shaper** szakértelem definíciója. |
| a 2019-05-06 ** (általánosan elérhető), 2017. 11. 11 – előzetes verzió| Összetett objektumok egy szintnél mélyebb. Egy többszintes alakzat szükséges együtt láncolási shaper számos lépést.|

Által biztosított módon `api-version=2019-05-06-Preview`, a **Shaper** szakértelem szemlélteti az [3. forgatókönyv](#nested-complex-types) hozzáad egy új nem kötelező *sourceContext* a bemeneti tulajdonság. A *forrás* és *sourceContext* a tulajdonságok akkor kölcsönösen kizárják egymást. Ha a bemeneti képzettség a környezetben, egyszerűen felhasználhatja *forrás*. Ha a bemenet egy *különböző* környezet szakértelem összefüggésben használja, mint a *sourceContext*. A *sourceContext* kell megadni egy beágyazott bemeneti javítása folyamatban, mint a forrás az adott elemhez. 

Minden API-verzióhoz, a válaszban a kimeneti név mindig "kimeneti". Belsőleg, a folyamat leképezhet egy másik nevet, például a "analyzedText", az alábbi példákban szemléltetett módon, de a **Shaper** szakértelem magát a választ adja vissza "kimeneti". Ez lehet fontos Ha képi elemekben gazdag dokumentumok hibakeresést, és figyelje meg, hogy az elnevezési eltérés, vagy ha egyéni műveleteket hozhat létre, és vannak strukturálja a válasz saját magának.

> [!NOTE]
> A **Shaper** szakértelem nincs kötve a Cognitive Services API-t, és nem terheli útmutatójához. Továbbra is ajánlott [Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md), azonban felül a **ingyenes** erőforrás beállítás, amely korlátozza, hogy naponta napi végrehajtott információbeolvasás kis számú.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>1\. forgatókönyv: komplexní typy

Példaként vegyünk egy forgatókönyvet, ahol szeretné létrehozni a szerkezetet *analyzedText* , amely két tagja van: *szöveg* és *vélemények*, illetve. Az Azure Search-index többrészes kereshető mező neve egy *komplex típus* és gyakran létrehozott, ha a forrásadatok rendelkezik egy megfelelő összetett szerkezet, amely hozzá van leképezve.

Azonban egy másik alapuló megközelítéssel hoznak létre a komplex típusok keresztül történik a **Shaper** ismeretek. Egy indexmezők szakértelem ügyféllé indexmezők feldolgozása közben a memóriabeli műveletek a beágyazott struktúrák, majd lehet rendelni egy összetett típus az index minden adathierarchizálásban küldhetnek kimenetet. 

A következő példa szakértelem definíciót nevek biztosít a tag bemeneteként. 


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

### <a name="sample-index"></a>Mintakód-index

A képességek alkalmazási lehetőségét az indexelő hív, és az indexelő igényel az index. Az index egy összetett ábrázolás az alábbi példához hasonlóan nézhet ki. 

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

### <a name="skill-input"></a>Bemeneti szakértelem

Egy bejövő JSON-dokumentumok, a használható bemenet **Shaper** szakértelem lehet:

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


### <a name="skill-output"></a>Kimeneti szakértelem

A **Shaper** szakértelem hoz létre egy új elem nevű *analyzedText* a kombinált elemeinek *szöveg* és *vélemények*. Ez a kimenet az indexséma megfelel-e. Ez fog importált és indexelése az Azure Search-index.

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

## <a name="scenario-2-input-consolidation"></a>2\. forgatókönyv: a bemeneti összevonása

Egy másik példa, hogy a feldolgozási folyamat különböző szakaszaiban kicsomagolta a könyv különböző oldalain fejezet címek és egy könyv címe imagine. Most már létrehozhat egy egyszeres szerkezet mikroszolgáltatásokból álló, ezeket a különféle bemeneteket.

A **Shaper** szakértelem definíció ebben a forgatókönyvben az alábbi példához hasonlóan nézhet ki:

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

### <a name="skill-output"></a>Kimeneti szakértelem
Ebben az esetben a **Shaper** lapossá teszi az összes fejezet címek hozzon létre egy egyetlen olyan tömböt. 

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>3\. forgatókönyv: a bemeneti összevonása a beágyazott környezetekben

> [!NOTE]
> A támogatott struktúrák beágyazott a [REST API verzióját 2019-05-06-Preview](search-api-preview.md) is használható egy [Tudásbázis store](knowledge-store-concept-intro.md) vagy az Azure Search-index.

Tegyük fel, a cím, a fejezetek és a egy könyv tartalmát, és futtatta entitások felismerése és a kulcs kifejezések tartalma és az most kell az eredményeket összesítheti a különböző képességek a fejezet nevét, az entitások és a kulcsfontosságú kifejezéseket, így az egyetlen alakzattá.

A **Shaper** szakértelem definíció ebben a forgatókönyvben az alábbi példához hasonlóan nézhet ki:

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

### <a name="skill-output"></a>Kimeneti szakértelem
Ebben az esetben a **Shaper** összetett típus. Ez a struktúra létezik a memóriában. Ha szeretné menteni a Tudásbázis-áruházban, a képességek alkalmazási lehetőségét, amely meghatározza a tároló jellemzőit a kivetítés kell létrehoznia.

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

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Összetett típusok használata](search-howto-complex-data-types.md)
+ [Tudásbázis store áttekintése](knowledge-store-concept-intro.md)
+ [Hogyan kell a Tudásbázis store használatának első lépései](knowledge-store-howto.md)