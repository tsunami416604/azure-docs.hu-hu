---
title: Egyéni képességek a kognitív keresési folyamat (Azure Search) felület |} Microsoft Docs
description: Egyéni adatok kinyerése felület web-api egyéni szakértelem az Azure Search kognitív keresési folyamat.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 8f21a56982189aa13745f27f0fae49310ae55aa0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640319"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Egyéni szakértelem hozzáadása egy kognitív keresési folyamat

Ebből a cikkből megismerheti, hogyan egyéni szakértelem hozzáadása egy kognitív keresési folyamatot. A [kognitív keresési indexelése folyamat](cognitive-search-concept-intro.md) az Azure Search lehet összeállítani a [képességek az előre megadott](cognitive-search-predefined-skills.md) és egyéni képességek, amelyek személyes létrehozása és hozzáadása a folyamatot.

Egyéni szakértelem felépítése akadályozható meg a beszúrandó egyedi, a tartalom átalakításához. Egyéni szakértelem hajtja végre a egymástól függetlenül, bármilyen szükséges dúsító lépés alkalmazása. Például, nem adja meg a mező vonatkozó egyéni entitások, egyéni besorolási modell megkülönböztetni azokat az üzleti és a pénzügyi szerződések és a dokumentumok létrehozása, vagy azokat a megfelelő tartalom hangfájlok mélyebb eléréséhez Beszéd felismerés szakértelem hozzáadása. Részletes példa, lásd: [példa: egyéni szakértelem létrehozása](cognitive-search-create-custom-skill-example.md).

 Bármilyen egyéni funkció van szüksége, egy egyszerű és világos felületet egyéni szakértelem csatlakozik a dúsító folyamatot a többi van. Egyetlen követelménye, hogy egy [skillset](cognitive-search-defining-skillset.md) képessége, hogy fogadja el a bemeneti és a kibocsátás kimenetek, amelyek az egész skillset belül használhatóvá módon. Az ebben a cikkben elsősorban a bemeneti és kimeneti formátumok, amely a dúsító csővezeték igényel.

## <a name="web-api-custom-skill-interface"></a>Webes API-t egyéni szakértelem felület

A csak mechanizmusa implementálására egyéni szakértelem jelenleg egy webes API felületen. A webes API-t kell az ebben a szakaszban leírt követelményeknek kell megfelelniük.

### <a name="1--web-api-input-format"></a>1.  Webes API-bemenet formátuma

A Web API el kell fogadnia a rekordok feldolgozását tömbjét. Minden rekordot tartalmaznia kell egy "tulajdonságcsomagot", amely a webes API megadott bemenetet. 

Tegyük fel, hozzon létre egy egyszerű enricher, amely azonosítja a szerződés szövegét az említett első dátuma. Ebben a példában a készséglefedettség fogad el egy egyetlen bemeneti *contractText* a szerződés szövegként. A szakértelem is rendelkezik egy egyetlen kimeneti, és a dátum a szerződés. Ahhoz, hogy a enricher ennél is érdekesebb megoldást, térjen vissza a *contractDate* többrészes összetett típus alakú.

A Web API fogadásához egy bemeneti rekord kötegelt készen áll. Mindegyik tagja a *értékek* tömb jelöli egy adott bejegyzés bemenete. Minden rekordot szükség van a következő elemeket:

+ A *recordId* , amelynek alapján egy adott bejegyzés egyedi azonosítója. A enricher az eredményeket ad vissza, ha kell átadnia a *recordId* annak érdekében, hogy a hívó a a bemeneti rekord eredmények kereséséhez.

+ A *adatok* tagot, amely lényegében az egyes rekordokhoz beviteli mezők összessége.

Kell több konkrét, a fenti példában egy a Web API kell látnia ilyen kérelmeket:

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
A valóságban meghívva előfordulhat, hogy a szolgáltatás több száz vagy ezer bejegyzés helyett csak az itt látható három.

### <a name="2-web-api-output-format"></a>2. Webes API kimeneti formátum

A kimeneti formátuma tartalmazó rekordok készlete egy *recordId*, és egy tulajdonságcsomagot 

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

Ebben a példában csak egy kimenettel rendelkezik, de több mint egy tulajdonság sikerült kimeneti. 

### <a name="errors-and-warning"></a>Hiba és figyelmeztetés

Ahogy az előző példában is látható, lehetséges, hogy a hibaüzenetek és figyelmeztető üzenetek az egyes rekordokhoz visszaadása.

## <a name="consuming-custom-skills-from-skillset"></a>Egyéni képességek a skillset felhasználása

Amikor létrehoz egy webes API enricher, a kérelem részeként HTTP-fejlécek és paraméterek is ismerteti. Az alábbi kódrészletben láthatja, hogyan a kérelemben szereplő paraméterek és a HTTP-fejlécek írhatók le a skillset definíciójának részeként.

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

+ [Példa: Egyéni szakértelem létrehozása a fordítás szöveg API](cognitive-search-create-custom-skill-example.md)
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)
+ [Hozzon létre Skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Hogyan bővített mezők](cognitive-search-output-field-mapping.md)
