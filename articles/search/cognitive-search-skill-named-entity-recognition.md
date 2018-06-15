---
title: Entitás felismerés kognitív keresési szakértelem (Azure Search) nevű |} Microsoft Docs
description: Bontsa ki a személy, a hely és a szervezet elnevezett entitások a szöveg egy Azure Search kognitív keresési folyamat.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 73ffcf5e2ced63fddaf0f5ef2ca7e72a7d94b966
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33791041"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Nevesített entitás felismerés kognitív szakértelem

A **nevű entitás felismerés** szakértelem elnevezett entitások kiolvassa a szöveget. Elérhető entitások közé tartoznak a típusok `person`, `location`, és `organization`.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="skill-parameters"></a>Szakértelem paraméterek

A paraméterei a kis-és nagybetűket.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| kategóriák    | A tömb kategóriák, hogy ki kell nyerni.  Lehetséges kategória típusok: `"Person"`, `"Location"`, `"Organization"`. Ha nincs kategória áll rendelkezésre, visszaadja a minden típus.|
|defaultLanguageCode |  A bemeneti szöveg nyelvkódot. A következő nyelveket támogatja: `ar, cs, da, de, en, es, fi, fr, he, hu, it, ko, pt-br, pt`|
| minimumPrecision  | 0 és 1 közötti szám. A pontosság nem éri el ezt az értéket, ha az entitás nem jelennek meg. Az alapértelmezett érték 0.|

## <a name="skill-inputs"></a>Szakértelem bemenetek

| Bemeneti név      | Leírás                   |
|---------------|-------------------------------|
| languageCode  | Választható. Az alapértelmezett szint a `"en"`.  |
| Szöveg          | A szöveg elemzésére.          |

## <a name="skill-outputs"></a>Szakértelem kimenetek

| Kimeneti neve     | Leírás                   |
|---------------|-------------------------------|
| személyek      | Ha minden karakterlánc egy személy nevét jelöli karakterláncokból álló tömb. |
| Helyek  | Ha minden karakterlánc egy helynek felel karakterláncokból álló tömb. |
| A szervezetek  | Ahol az egyes karakterlánc jelenti egy szervezet karakterláncokból álló tömb. |
| entitás | Összetett típusok egy tömbjét. Minden egyes összetett típus a következő mezőket tartalmazza: <ul><li>kategória (`"person"`, `"organization"`, vagy `"location"`)</li> <li>érték (a tényleges entitás neve)</li><li>Az offset (a hely hol található az a szöveg)</li><li>abban, hogy (0 és 1, amely közötti értéket jelöli, hogy abban, hogy, hogy az érték egy tényleges entitás)</li></ul> |

##  <a name="sample-definition"></a>A minta meghatározása

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
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
        "data":
           {
             "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Példa kimenet

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Seattle"],
        "organizations":["Microsoft Corporation"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 45,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 59,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Seattle",
            "offset": 5,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft Corporation",
            "offset": 120,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Hibák esetén
Ha megad egy nem támogatott nyelvi kódot, vagy ha a tartalom nem egyezik a megadott nyelven, visszatérési hiba, és nem entitások ki kell olvasni.

## <a name="see-also"></a>Lásd még

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)