---
title: Elnevezett entitásfelismerési kognitív képesség
titleSuffix: Azure Cognitive Search
description: Névvel ellátott entitások kinyerése a személy, a hely és a szervezet számára az Azure Cognitive Search AI-dúsítási folyamatszövegéből.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 127155e492b556ce1ce02b67cf0b0846b99ebcd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791935"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Elnevezett entitásfelismerési kognitív képesség

A **named entity felismerés** szakértelem kivonatok nevű entitások a szövegből. A rendelkezésre álló `person` `location` entitások közé tartoznak a típusok és `organization`a .

> [!IMPORTANT]
> A névvel ellátott entitásfelismerési szakértelem mostantól megszűnik a [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). A támogatás 2019. február 15-én leállt, és az API-t 2019. május 2-án eltávolították a termékből. Kövesse az [Elavult kognitív keresési képességek](cognitive-search-skill-deprecated.md) ben található javaslatokat, hogy egy támogatott szakértelemre térjen át.

> [!NOTE]
> A hogy a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti a hatókört, [egy számlázható Cognitive Services-erőforrást kell csatolnia.](cognitive-search-attach-cognitive-services.md) A díjak akkor keletkeznek, amikor API-kat hívnak a Cognitive Servicesben, és az Azure Cognitive Search dokumentumfeltörési szakaszának részeként képkinyerést végeznek. A dokumentumokból történő szövegkinyerésért nem kell díjat fizetni.
>
> A beépített képességek végrehajtása a meglévő [Cognitive Services díja int.](https://azure.microsoft.com/pricing/details/cognitive-services/) A képkinyerésdíj szabása az [Azure Cognitive Search díjszabási lapján található.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Adatkorlátok
A rekord maximális méretének 50 000 karakternek [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)kell lennie a szerint mérve. Ha meg kell szakítania az adatokat, mielőtt elküldi azokat a kulcskifejezés-kiválasztónak, fontolja meg a [Szövegfelosztás szakértelem](cognitive-search-skill-textsplit.md)használatát.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| kategóriák    | Kibontandó kategóriák tömbje.  Lehetséges `"Person"`kategóriatípusok: `"Location"` `"Organization"`, , . Ha nincs megadva kategória, a rendszer minden típust visszaad.|
|defaultLanguageCode |  A bemeneti szöveg nyelvkódja. A következő nyelvek támogatottak:`de, en, es, fr, it`|
| minimumPontosság  | Egy 0 és 1 közötti szám. Ha a pontosság alacsonyabb ennél az értéknél, az entitás nem kerül visszaadásra. Az alapértelmezett érték a 0.|

## <a name="skill-inputs"></a>Szakértelem bemenetei

| Bemenet neve      | Leírás                   |
|---------------|-------------------------------|
| languageCode  | Választható. Az alapértelmezett szint a `"en"`.  |
| szöveg          | Az elemző szöveg.          |

## <a name="skill-outputs"></a>Szakértelem kimenetei

| Kimenet neve     | Leírás                   |
|---------------|-------------------------------|
| Személyek      | Karakterláncok tömbje, ahol minden karakterlánc egy személy nevét jelöli. |
| Helyek  | Karakterláncok tömbje, ahol minden karakterlánc egy helyet jelöl. |
| organizations  | Karakterláncok tömbje, ahol minden karakterlánc egy szervezetet jelöl. |
| Entitások | Összetett típusok tömbje. Minden összetett típus a következő mezőket tartalmazza: <ul><li>kategória`"person"`( `"organization"`, `"location"`, vagy )</li> <li>érték (a tényleges entitás neve)</li><li>eltolás (Az a hely, ahol a szövegben található)</li><li>megbízhatóság (0 és 1 közötti érték, amely azt a bizalmat jelöli, hogy az érték egy tényleges entitás)</li></ul> |

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
##  <a name="sample-input"></a>Mintabevitel

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


## <a name="error-cases"></a>Hibaesetek
Ha a dokumentum nyelvi kódja nem támogatott, a rendszer hibát ad vissza, és nem bont ki entitásokat.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Entitás-kivezetési szakértelem](cognitive-search-skill-entity-recognition.md)
