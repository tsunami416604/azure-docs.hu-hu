---
title: A kognitív keresés folyamat – Azure Search egyéni képesség illesztőjének definícióját
description: Egyéni adatok kinyerése felület webes-API-t egyéni ismeretek a kognitív keresés folyamatban, az Azure Search szolgáltatásban.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 08/14/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 1df2a565b1c9e8bd2bea75ce78abc84608c89eea
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314652"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Egy egyéni ismeretek hozzáadása a cognitive search folyamat

A [kognitív Keresés indexelése folyamat](cognitive-search-concept-intro.md) az Azure Search szolgáltatásban is összeállítani a [képességek az előre meghatározott](cognitive-search-predefined-skills.md) és egyéni képesség, amely a személyes létrehozása és hozzáadása a folyamatban. Ebből a cikkből megtudhatja, hogyan hozhat létre egy egyéni ismeretek által elérhetővé tett egy felületet, így lehetővé teszi a kognitív keresés folyamat szerepelnek. 

Egy egyéni ismeretek létrehozása lehetőséget nyújt a átalakítások egyedi legyen a tartalom beszúrása. Egyéni műveleteket hajt végre egymástól függetlenül, bármilyen szükséges Adatbővítés lépés alkalmazása. Például, sikerült határozza meg az egyéni entitások mezőt-specifikus, üzleti és a pénzügyi szerződések és a dokumentumok megkülönböztetéséhez egyéni képbesorolási modellek létrehozása vagy hangfájlokat, a kapcsolódó tartalom mélyebb közvetítőn speech recognition szakértelem hozzáadása. Egy részletes példa: [példa: egyéni műveleteket létrehozása](cognitive-search-create-custom-skill-example.md).

 Bármilyen egyéni képesség van szüksége, van egy egyéni ismeretek csatlakozik a többi felderítési bővítést folyamat egy egyszerű és egyértelműen felületet. Egyetlen követelménye, hogy egy [indexmezők](cognitive-search-defining-skillset.md) képessége, fogadja el a bemenetek és kimenetek, amelyek teljes készségeitől belül értelmezhető módon küldik. Ez a cikk célja a bemeneti és kimeneti formátumok a Adatbővítés folyamat szükséges a.

## <a name="web-api-custom-skill-interface"></a>Webes API-k egyéni ismeretek felület

Egyéni WebAPI szakértelem végpontok 5 perces időtartamon belül választ kell visszaadnia. Az indexelési folyamat szinkron és indexelő állítja elő időtúllépési hiba, ha a válasz nem érkezik meg ezt az ablakot."

Az egyéni ismeretek folytatott interakcióra szolgáló csak mechanizmus jelenleg egy webes API-felületen keresztül. A webes API van szüksége az ebben a szakaszban leírt követelményeknek kell megfelelnie.

### <a name="1--web-api-input-format"></a>1.  Webes API bemeneti formátum

A webes API el kell fogadnia egy feldolgozandó rekordokból álló tömbbel. Minden rekord tartalmaznia kell egy "tulajdonságcsomagot", amely a megadott információ a webes API-nak. 

Tegyük fel, hogy hozzon létre egy egyszerű enricher, amely azonosítja az első, a szerződés szövegét az említett dátum. Ebben a példában a szakértelem fogad egy egyetlen bemeneti *contractText* , mint a szerződés szöveg. A szakértelem is rendelkezik egy egyetlen kimeneti, amely a dátumot a szerződés. Ahhoz, hogy a enricher érdekesebb, vissza ez *contractDate* többrészes komplex típus alakú.

Lehet, hogy a webes API készen állnak a kötegelt bemeneti rekordok fogadására. Minden egyes tagja a *értékek* tömb a bemenet egy adott rekord jelöli. Minden rekord kell rendelkeznie a következő elemeket:

+ A *rekordazonosító* tag, amely egy adott rekord egyedi azonosítója. A enricher az eredményeket adja vissza, ha kell átadnia a *rekordazonosító* annak érdekében, hogy megfelelően a bemeneti rekord az eredményeket a hívónak.

+ A *adatok* tag, amely lényegében az egyes rekordok beviteli mezőket összessége.

Kell több konkrét, a fenti példa szerint a webes API kell látnia, hogy ilyen kéréseket:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
A valóságban ez a szolgáltatás első nevű akár több száz vagy ezer helyett csak az itt látható három rekordot.

### <a name="2-web-api-output-format"></a>2. Webes API kimeneti formátum

A kimenet formátuma a tartalmazó rekordok készlete egy *rekordazonosító*, és a egy tulajdonságcsomagot 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

Ebben a példában csak egy kimeneti rendelkezik, de több tulajdonság kimeneti sikerült. 

### <a name="errors-and-warning"></a>Hiba és figyelmeztetés

Mivel az előző példában is látható, előfordulhat, hogy a hibaüzenetek és figyelmeztető üzenetek az egyes rekordok visszaadása.

## <a name="consuming-custom-skills-from-skillset"></a>Képességcsoport a fogyasztó egyéni képesség

Amikor létrehoz egy webes API-t enricher, a kérelem részeként HTTP-fejlécek és paramétereket is ismerteti. Az alábbi kódrészlet bemutatja, hogyan kérelem paramétereit, és HTTP-fejlécek írhatók le a képességek alkalmazási lehetőségét definíciójának részeként.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>További lépések

+ [Példa: A fordítás API hoz létre egy egyéni szakértelem](cognitive-search-create-custom-skill-example.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Képességcsoport (REST) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Hogyan hidaljuk mezők leképezése](cognitive-search-output-field-mapping.md)
