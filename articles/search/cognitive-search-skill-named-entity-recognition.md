---
title: Megnevezett entitások felismerése cognitive search szakértelem – Azure Search
description: Nevesített entitások személy, helyen és szervezeten kigyűjtése a szöveg Azure Search-kognitív keresés folyamatban.
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
ms.openlocfilehash: 7c462971f7ac02e35ab8df5b651da7d0a3f39e1f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65022263"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Nevesített entitások felismerése cognitive szakértelem

A **megnevezett entitások felismerése** szakértelem nevesített entitásokhoz kigyűjti a szöveget. Elérhető entitások közé tartozik a típusok `person`, `location` és `organization`.

> [!IMPORTANT]
> Nevesített entitások felismerése szakértelem megszűnt helyébe [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Támogatási leállította 2019. február 15., és az API el lett távolítva a termékből, 2019. május 2. Kövesse a [elavult kognitív keresés](cognitive-search-skill-deprecated.md) migrálása a támogatott műveleteket.

> [!NOTE]
> Bontsa ki a hatókört által a feldolgozás, gyakoriságának növelése további dokumentumok hozzáadása, vagy adja hozzá a további AI-algoritmusokat, kell [számlázható Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md). A díjakat API-k hívásakor, a Cognitive Services, valamint a lemezkép kinyerése a az Azure Search-dokumentumfeltörést fázis részeként. Nem számítunk fel díjat a szövegkinyerés dokumentumok közül.
>
> Végrehajtási beépített képességek a meglévő díjakat [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services/). A kép kinyerési díjszabás leírása a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Adatkorlátok
Egy rekord maximális mérete 50 000 karakter által mért kell lennie `String.Length`. Ha az adatok tördelésével, mielőtt elküldené a kulcskifejezések információkinyerő van szüksége, fontolja meg a [szöveg felosztása szakértelem](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Ismeretek paraméterek

A paraméterei a kis-és nagybetűket.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| kategóriák    | Ki kell nyerni kategóriákat tömbje.  Lehetséges kategória típusok: `"Person"`, `"Location"`, `"Organization"`. Ha nincs kategória áll rendelkezésre, a rendszer minden adja vissza.|
|defaultLanguageCode |  A bemeneti szöveg nyelvkódja. A következő nyelvek támogatottak: `de, en, es, fr, it`|
| minimumPrecision  | Egy 0 és 1 közötti szám. Ha a pontosság kisebb, mint ezt az értéket, az entitás nem jelennek meg. Az alapértelmezett érték a 0.|

## <a name="skill-inputs"></a>Ismeretek bemenetek

| Bemenet neve      | Leírás                   |
|---------------|-------------------------------|
| languageCode  | Választható. Az alapértelmezett szint a `"en"`.  |
| szöveg          | Az elemzendő szöveg.          |

## <a name="skill-outputs"></a>Ismeretek kimenetek

| Kimeneti név     | Leírás                   |
|---------------|-------------------------------|
| személyek      | Egy karakterlánctömb, ahol minden egyes karakterlánc egy személy nevét jelöli. |
| helyek  | Egy karakterlánctömb, ahol az egyes sztringek jelenti egy helyre. |
| organizations  | Ahol az egyes sztringek jelenti egy szervezet karakterláncok tömbje. |
| entitás | Összetett típusok egy tömbjét. Minden egyes komplex típus a következő mezőket tartalmazza: <ul><li>kategória (`"person"`, `"organization"`, vagy `"location"`)</li> <li>érték (a tényleges entitás neve)</li><li>eltolás (a hely hol található a szövegben)</li><li>(0, 1, amely közötti értéket jelöli, hogy, hogy az érték egy tényleges entitás megbízhatósági) magabiztosan</li></ul> |

##  <a name="sample-definition"></a>Minta-definíció

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
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
##  <a name="sample-input"></a>Minta beviteli

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "This is the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
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
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Hibák esetén
A dokumentum a nyelvkód nem támogatott, ha hibát ad vissza, és nincsenek entitások ki kell olvasni.

## <a name="see-also"></a>Lásd még

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Entitások felismerése szakértelem](cognitive-search-skill-entity-recognition.md)
