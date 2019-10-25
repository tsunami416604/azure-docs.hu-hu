---
title: Entitás-felismerés – kognitív képesség
titleSuffix: Azure Cognitive Search
description: Különböző típusú entitások kinyerése egy alkoholtartalom-növelési folyamatból az Azure Cognitive Searchban.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 08e9656e3b899cbb6d4de733696175e8f31b0e66
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792015"
---
#   <a name="entity-recognition-cognitive-skill"></a>Entitás-felismerés – kognitív képesség

Az **entitás-felismerési** képesség Kinyeri a szövegből származó különböző típusú entitásokat. Ez a képesség a Cognitive Services [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. EntityRecognitionSkill

## <a name="data-limits"></a>Adatkorlátok
A rekordok maximális méretének 50 000 karakternek kell lennie [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)alapján mérve. Ha meg kell szakítania az adatokat, mielőtt elküldené a kivonó kifejezést, érdemes lehet a [szöveg felosztása képességet](cognitive-search-skill-textsplit.md)használni.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket, és mindegyik nem kötelező.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| kategóriák    | A kinyerni kívánt kategóriák tömbje.  Lehetséges kategóriájú típusok: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Ha nincs megadva kategória, a rendszer az összes típust adja vissza.|
|defaultLanguageCode |  A bemeneti szöveg nyelvi kódja A következő nyelvek támogatottak: `de, en, es, fr, it`|
|minimumPrecision | Nem használt. Későbbi használatra fenntartva. |
|includeTypelessEntities | Ha a értéke TRUE (igaz), ha a szöveg jól ismert entitást tartalmaz, de az egyik támogatott kategóriába nem kategorizálható, a rendszer a `"entities"` összetett kimeneti mező részeként adja vissza. 
Ezek olyan entitások, amelyek jól ismertek, de nem tartoznak a jelenleg támogatott "kategóriák" részébe. A "Windows 10" például jól ismert entitás (termék), de a "termékek" nem szerepelnek a jelenleg támogatott kategóriákban. Az alapértelmezett érték `false` |


## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemeneti név      | Leírás                   |
|---------------|-------------------------------|
| languageCode  | Választható. Az alapértelmezett érték a `"en"`.  |
| szöveg          | Az elemezni kívánt szöveg.          |

## <a name="skill-outputs"></a>Szaktudás kimenetei

> [!NOTE]
> Az entitások összes kategóriája nem támogatott az összes nyelv esetében. Csak az _en_, _es_ támogatja a `"Quantity"`, `"Datetime"`, `"URL"`és `"Email"` típusok kinyerését.

| Kimenet neve     | Leírás                   |
|---------------|-------------------------------|
| személyek      | Karakterláncok tömbje, amelyben minden sztring egy személy nevét jelöli. |
| Helyek  | Karakterláncok tömbje, amelyben minden sztring egy helyet jelöl. |
| organizations  | Karakterláncok tömbje, amelyben minden sztring egy szervezetet jelöl. |
| mennyiségek  | Karakterláncok tömbje, amelyben minden sztring egy mennyiséget jelöl. |
| Dátum  | Karakterláncok tömbje, amelyben minden karakterlánc egy DateTime értéket jelöl (ahogy a szövegben jelenik meg). |
| URLs | Karakterláncok tömbje, amelyben minden sztring URL-címet jelöl |
| e-mailek | Karakterláncok tömbje, amelyben minden karakterlánc egy e-mailt jelöl |
| namedEntities | Összetett típusok tömbje, amely a következő mezőket tartalmazza: <ul><li>category</li> <li>érték (a tényleges entitás neve)</li><li>eltolás (az a hely, ahol a szöveg található)</li><li>megbízhatóság (jelenleg nem használatos. A-1 értékre lesz állítva</li></ul> |
| szervezetek | Összetett típusok tömbje, amely részletes információkat tartalmaz a szövegből kinyert entitásokról a következő mezőkkel <ul><li> név (a tényleges entitás neve. Ez a "normalizált" formát jelenti.</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (az entitáshoz tartozó wikipedia oldalára mutató hivatkozás)</li><li>bingId</li><li>típus (az elismert entitás kategóriája)</li><li>altípus (csak bizonyos kategóriák esetében érhető el, ez az entitás típusának részletesebb megjelenítését teszi lehetővé)</li><li> egyezések (egy összetett gyűjtemény, amely tartalmazza)<ul><li>szöveg (az entitás nyers szövege)</li><li>eltolás (a hely hol található)</li><li>Hossz (a nyers entitás hosszának hossza)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Minta definíciója

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
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
##  <a name="sample-input"></a>Minta bemenet

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

##  <a name="sample-output"></a>Példa kimenet

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
            "confidence": -1
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


## <a name="error-cases"></a>Hibák esetei
Ha a dokumentumhoz tartozó nyelvi kód nem támogatott, a rendszer hibát ad vissza, és egyetlen entitás sincs kibontva.

## <a name="see-also"></a>Lásd még:

+ [Beépített szaktudás](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
