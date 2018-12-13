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
ms.date: 11/27/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: e63354152f8821c5ce975563639c8b87fb332bd4
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313989"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Nevesített entitások felismerése cognitive szakértelem

A **megnevezett entitások felismerése** szakértelem nevesített entitásokhoz kigyűjti a szöveget. Elérhető entitások közé tartozik a típusok `person`, `location` és `organization`.

> [!NOTE]
> <ul>
> <li>
> December 21, 2018-as, lesz egy Cognitive Services-erőforrás társítása egy Azure Search-képességek alkalmazási lehetőségét. Ez lehetővé teszi indexmezők végrehajtási díjszabási elindításához. Ezen a napon is megkezdjük a dokumentumfeltörést fázis részeként a lemezkép kinyerési díjszabási. A szövegek dokumentumokból való kinyerése továbbra is ingyenesen használható.>
> A végrehajtás beépített képességek díjat számítunk fel a meglévő [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services/) . Kép kinyerési díjszabás az előzetes verziók díjszabása díjat számítunk fel, és a leírt a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Ismerje meg, [további](cognitive-search-attach-cognitive-services.md).</li>
> <li> Nevesített entitások felismerése szakértelem "elavult" minősülnek, és nem hivatalosan támogatja től február 15., a 2019. Kövesse a felsorolt javaslatokra <a href="cognitive-search-skill-deprecated.md">elavult kognitív keresés</a> migrálása a támogatott műveleteket lap</li>

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

| Bemeneti név      | Leírás                   |
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
