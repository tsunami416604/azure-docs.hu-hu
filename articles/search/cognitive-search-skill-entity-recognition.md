---
title: Az Entity Cognition kognitív keresési képességei – Azure Search
description: Különböző típusú entitások kinyerése szövegből egy Azure Search kognitív keresési folyamatban.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: eb196b9ff72201358982764f227ab7935489f05d
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183402"
---
#    <a name="entity-recognition-cognitive-skill"></a>Entitás-felismerés – kognitív képesség

Az **entitás** -felismerési képesség Kinyeri a szövegből származó különböző típusú entitásokat. Ez a képesség a Cognitive Services [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást](cognitive-search-attach-cognitive-services.md)kell csatolnia. Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedési szakaszának részeként Azure Search. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását a [Azure Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Adatkorlátok
A rekordok maximális méretének 50 000 karakternek kell lennie, a [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)következőképpen mérve:. Ha meg kell szakítania az adatokat, mielőtt elküldené a kivonó kifejezést, érdemes lehet a [szöveg felosztása képességet](cognitive-search-skill-textsplit.md)használni.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket, és mindegyik nem kötelező.

| Paraméternév     | Leírás |
|--------------------|-------------|
| categories    | A kinyerni kívánt kategóriák tömbje.  Lehetséges kategóriájú típusok: `"Person"` `"Location"` `"Organization"`, `"Quantity"` ,,`"URL"`,, ,`"Email"`. `"Datetime"` Ha nincs megadva kategória, a rendszer az összes típust adja vissza.|
|defaultLanguageCode |  A bemeneti szöveg nyelvi kódja A következő nyelvek támogatottak:`de, en, es, fr, it`|
|minimumPrecision | Nem használt. Későbbi használatra fenntartva. |
|includeTypelessEntities | Ha a értéke TRUE (igaz), ha a szöveg jól ismert entitást tartalmaz, de az egyik támogatott kategóriába nem kategorizálható, akkor a rendszer a `"entities"` komplex kimenet mező részeként adja vissza. 
Ezek olyan entitások, amelyek jól ismertek, de nem tartoznak a jelenleg támogatott "kategóriák" részébe. A "Windows 10" például jól ismert entitás (termék), de a "termékek" nem szerepelnek a jelenleg támogatott kategóriákban. Alapértelmezett érték`false` |


## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemenet neve      | Leírás                   |
|---------------|-------------------------------|
| languageCode  | Nem kötelező. Az alapértelmezett szint a `"en"`.  |
| text          | Az elemezni kívánt szöveg.          |

## <a name="skill-outputs"></a>Szaktudás kimenetei

> [!NOTE]
> Az entitások összes kategóriája nem támogatott az összes nyelv esetében. Csak _en_, _es_ `"Quantity"` `"Datetime"`támogatja a`"URL"` ,,,típusok`"Email"` kinyerését.

| Kimenet neve     | Leírás                   |
|---------------|-------------------------------|
| személyek      | Karakterláncok tömbje, amelyben minden sztring egy személy nevét jelöli. |
| locations  | Karakterláncok tömbje, amelyben minden sztring egy helyet jelöl. |
| organizations  | Karakterláncok tömbje, amelyben minden sztring egy szervezetet jelöl. |
| mennyiségek  | Karakterláncok tömbje, amelyben minden sztring egy mennyiséget jelöl. |
| Dátum  | Karakterláncok tömbje, amelyben minden karakterlánc egy DateTime értéket jelöl (ahogy a szövegben jelenik meg). |
| URLs | Karakterláncok tömbje, amelyben minden sztring URL-címet jelöl |
| e-mailek | Karakterláncok tömbje, amelyben minden karakterlánc egy e-mailt jelöl |
| namedEntities | Összetett típusok tömbje, amely a következő mezőket tartalmazza: <ul><li>category</li> <li>érték (a tényleges entitás neve)</li><li>eltolás (az a hely, ahol a szöveg található)</li><li>megbízhatóság (jelenleg nem használatos. A-1 értékre lesz állítva</li></ul> |
| entitás | Összetett típusok tömbje, amely részletes információkat tartalmaz a szövegből kinyert entitásokról a következő mezőkkel <ul><li> név (a tényleges entitás neve. Ez a "normalizált" formát jelenti.</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (az entitáshoz tartozó wikipedia oldalára mutató hivatkozás)</li><li>bingId</li><li>típus (az elismert entitás kategóriája)</li><li>altípus (csak bizonyos kategóriák esetében érhető el, ez az entitás típusának részletesebb megjelenítését teszi lehetővé)</li><li> egyezések (egy összetett gyűjtemény, amely tartalmazza)<ul><li>szöveg (az entitás nyers szövege)</li><li>eltolás (a hely hol található)</li><li>Hossz (a nyers entitás hosszának hossza)</li></ul></li></ul> |

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

## <a name="see-also"></a>Lásd még

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
