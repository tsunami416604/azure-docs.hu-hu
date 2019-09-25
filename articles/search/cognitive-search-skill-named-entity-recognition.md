---
title: Nevesített entitások felismerése – kognitív keresési képességek – Azure Search
description: Megnevezett entitások kinyerése a személy, hely és szervezet számára egy Azure Search kognitív keresési folyamat szövege alapján.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: b152d5a48d49e78818602e7f66574937bebce2ac
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265765"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Elnevezett entitások felismerése – kognitív képesség

Az **elnevezett entitás-felismerési** képességek nevű entitások szövegből való kibontása. Az elérhető entitások közé `person`tartoznak `location` a `organization`típusok és a.

> [!IMPORTANT]
> Az elnevezett entitás-felismerési képesség már megszűnt a [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)helyett. A támogatás 2019. február 15-én megszűnt, és az API-t a 2019. május 2-án eltávolították a termékből. Kövesse az [elavult kognitív keresési ismeretekben](cognitive-search-skill-deprecated.md) szereplő javaslatokat a támogatott szaktudásra való áttéréshez.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást](cognitive-search-attach-cognitive-services.md)kell csatolnia. Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedési szakaszának részeként Azure Search. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását a [Azure Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Adatkorlátok
A rekordok maximális méretének 50 000 karakternek kell lennie, a [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)következőképpen mérve:. Ha meg kell szakítania az adatokat, mielőtt elküldené a kivonó kifejezést, érdemes lehet a [szöveg felosztása képességet](cognitive-search-skill-textsplit.md)használni.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| categories    | A kinyerni kívánt kategóriák tömbje.  Lehetséges kategóriájú típusok: `"Person"`, `"Location"`, `"Organization"`. Ha nincs megadva kategória, a rendszer az összes típust adja vissza.|
|defaultLanguageCode |  A bemeneti szöveg nyelvi kódja A következő nyelvek támogatottak:`de, en, es, fr, it`|
| minimumPrecision  | 0 és 1 közötti szám. Ha a pontosság ennél az értéknél kisebb, az entitás nem lesz visszaadva. Az alapértelmezett érték a 0.|

## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemenet neve      | Leírás                   |
|---------------|-------------------------------|
| languageCode  | Nem kötelező. Az alapértelmezett szint a `"en"`.  |
| text          | Az elemezni kívánt szöveg.          |

## <a name="skill-outputs"></a>Szaktudás kimenetei

| Kimenet neve     | Leírás                   |
|---------------|-------------------------------|
| személyek      | Karakterláncok tömbje, amelyben minden sztring egy személy nevét jelöli. |
| locations  | Karakterláncok tömbje, amelyben minden sztring egy helyet jelöl. |
| organizations  | Karakterláncok tömbje, amelyben minden sztring egy szervezetet jelöl. |
| Szervezetek | Összetett típusok tömbje. Minden összetett típus a következő mezőket tartalmazza: <ul><li>Kategória (`"person"`, `"organization"`, vagy `"location"`)</li> <li>érték (a tényleges entitás neve)</li><li>eltolás (az a hely, ahol a szöveg található)</li><li>megbízhatóság (0 és 1 közötti érték, amely azt jelzi, hogy az érték tényleges entitás)</li></ul> |

##  <a name="sample-definition"></a>Minta definíciója

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
##  <a name="sample-input"></a>Minta bemenet

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
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


## <a name="error-cases"></a>Hibák esetei
Ha a dokumentumhoz tartozó nyelvi kód nem támogatott, a rendszer hibát ad vissza, és egyetlen entitás sincs kibontva.

## <a name="see-also"></a>Lásd még

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Entitás-felismerési szakértelem](cognitive-search-skill-entity-recognition.md)
