---
title: Entitásfelismerés kognitív készsége
titleSuffix: Azure Cognitive Search
description: Különböző típusú entitások kinyerése egy gazdagítási folyamat szövegéből az Azure Cognitive Search-ben.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6ef5952b6413563b2c2e16ff2218f709b414fb84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297811"
---
#    <a name="entity-recognition-cognitive-skill"></a>Entitásfelismerés kognitív készsége

Az **Entity Recognition** szakértelem különböző típusú entitásokat nyer ki a szövegből. Ez a szakértelem a Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

> [!NOTE]
> A hogy a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti a hatókört, [egy számlázható Cognitive Services-erőforrást kell csatolnia.](cognitive-search-attach-cognitive-services.md) A díjak akkor keletkeznek, amikor API-kat hívnak a Cognitive Servicesben, és az Azure Cognitive Search dokumentumfeltörési szakaszának részeként képkinyerést végeznek. A dokumentumokból történő szövegkinyerésért nem kell díjat fizetni.
>
> A beépített képességek végrehajtása a meglévő [Cognitive Services díja int.](https://azure.microsoft.com/pricing/details/cognitive-services/) A képkinyerésdíj szabása az [Azure Cognitive Search díjszabási lapján található.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Adatkorlátok
A rekord maximális méretének 50 000 karakternek [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)kell lennie a szerint mérve. Ha meg kell szakítania az adatokat, mielőtt elküldi azokat a kulcskifejezés-kiválasztónak, fontolja meg a [Szövegfelosztás szakértelem](cognitive-search-skill-textsplit.md)használatát.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek ben a kis- és nagybetűket nem lehet kiválasztható.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| kategóriák    | Kibontandó kategóriák tömbje.  Lehetséges `"Person"`kategóriatípusok: `"Location"` `"Organization"`, `"Quantity"` `"Datetime"`, `"URL"` `"Email"`, , , , . Ha nincs megadva kategória, a rendszer minden típust visszaad.|
|defaultLanguageCode |    A bemeneti szöveg nyelvkódja. A következő nyelvek `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans`támogatottak: . Nem minden entitáskategória támogatott minden nyelven; lásd az alábbi megjegyzést.|
|minimumPontosság | 0 és 1 közötti érték. Ha a megbízhatósági `namedEntities` pontszám (a kimenetben) alacsonyabb, mint ez az érték, az entitás nem adja vissza. Az alapértelmezett érték a 0. |
|includeTypelessEntities | Állítsa `true` be, ha fel szeretné ismerni azokat a jól ismert entitásokat, amelyek nem felelnek meg az aktuális kategóriáknak. Az elismert entitások `entities` a komplex kimeneti mezőben kerülnek visszaadásra. Például a "Windows 10" egy jól ismert entitás (termék), de mivel a "Termékek" nem támogatott kategória, ez az entitás szerepelni fog az entitások kimeneti mezőjében. Az alapértelmezett érték`false` |


## <a name="skill-inputs"></a>Szakértelem bemenetei

| Bemenet neve      | Leírás                   |
|---------------|-------------------------------|
| languageCode    | Választható. Az alapértelmezett szint a `"en"`.  |
| szöveg          | Az elemző szöveg.          |

## <a name="skill-outputs"></a>Szakértelem kimenetei

> [!NOTE]
> Nem minden entitáskategória támogatott minden nyelven. A `"Person"` `"Location"`, `"Organization"` és az entitás kategóriatípusok a fenti nyelvek teljes listájához támogatottak. Csak _de,_ _en_, _es_, _fr_és `"Quantity"` `"Datetime"` _zh-hans_ támogatja a kivonását, `"URL"`, és `"Email"` típusait. További információ: [Language and region support for the Text Analytics API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support).  

| Kimenet neve      | Leírás                   |
|---------------|-------------------------------|
| Személyek       | Karakterláncok tömbje, ahol minden karakterlánc egy személy nevét jelöli. |
| Helyek  | Karakterláncok tömbje, ahol minden karakterlánc egy helyet jelöl. |
| organizations  | Karakterláncok tömbje, ahol minden karakterlánc egy szervezetet jelöl. |
| Mennyiségek  | Karakterláncok tömbje, ahol minden karakterlánc mennyiséget jelöl. |
| dateTimes  | Karakterláncok tömbje, ahol minden karakterlánc datetime értéket jelöl (ahogy az a szövegben megjelenik). |
| Urls | Karakterláncok tömbje, ahol minden karakterlánc URL-címet jelöl |
| E-mail | Karakterláncok tömbje, ahol minden karakterlánc egy e-mailt jelöl |
| namedEntities (elnevezett entitások) | Összetett típusok tömbje, amely a következő mezőket tartalmazza: <ul><li>category</li> <li>érték (A tényleges entitás neve)</li><li>eltolás (Az a hely, ahol a szövegben található)</li><li>bizalom (A magasabb érték azt jelenti, hogy inkább valódi entitás)</li></ul> |
| Entitások | Összetett típusok tömbje, amely gazdag információkat tartalmaz a szövegből kinyert entitásokról, a következő mezőkkel <ul><li> név (a tényleges entitás neve. Ez egy "normalizált" űrlapot jelent)</li><li> wikipediaId</li><li>wikipediaNyelv</li><li>wikipediaUrl (egy link a Wikipédia oldalára a szervezet számára)</li><li>bingId</li><li>típus (a felismert entitás kategóriája)</li><li>altípus (csak bizonyos kategóriákesetében érhető el, ez részletesebb nézetet ad az entitástípusról)</li><li> egyezik (egy összetett gyűjtemény, amely tartalmazza)<ul><li>szöveg (az entitás nyers szövege)</li><li>eltolás (az a hely, ahol megtalálták)</li><li>hossz (a nyers entitásszöveg hossza)</li></ul></li></ul> |

##    <a name="sample-definition"></a>Minta definíciója

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
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
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##    <a name="sample-input"></a>Mintabevitel

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Példa kimenet

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```

Vegye figyelembe, hogy a szakértelem kimenetében lévő entitások által visszaadott eltolások közvetlenül a [Text Analytics API-ból](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)származnak, ami azt jelenti, hogy ha az okat az eredeti karakterláncba indexeli, a [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) osztályt kell használnia a .NET-ben a megfelelő tartalom kinyeréséhez.  [További részletek itt találhatók.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-cases"></a>Hibaesetek
Ha a dokumentum nyelvi kódja nem támogatott, a rendszer hibát ad vissza, és nem bont ki entitásokat.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
