---
title: Felület definíciója egyéni képességekhez
titleSuffix: Azure Cognitive Search
description: Egyéni adatkinyerési felület web-api egyéni szakértelem egy AI dúsítási folyamat az Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 78f5f6eda28bed164668445b5671dad92f8dedd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500270"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Egyéni szakértelem hozzáadása az Azure Cognitive Search dúsítási folyamatához

Az Azure Cognitive Search [gazdagítási folyamata](cognitive-search-concept-intro.md) [beépített kognitív képességekből,](cognitive-search-predefined-skills.md) valamint a folyamathoz személyesen létrehozott és hozzáadott [egyéni képességekből](cognitive-search-custom-skill-web-api.md) is összeszerelhető. Ebből a cikkből megtudhatja, hogyan hozhat létre olyan egyéni képességeket, amelyek lehetővé teszik, hogy az AI-bővítési folyamatba bekerüljön. 

Egyéni szakértelem létrehozása lehetővé teszi a tartalomegyedi átalakításának beszúrását. Az egyéni szakértelem egymástól függetlenül hajtható végre, és bármilyen dúsítási lépést alkalmaz, amire szüksége van. Például meghatározhat mezőspecifikus egyéni entitásokat, egyéni osztályozási modelleket hozhat létre az üzleti és pénzügyi szerződések és dokumentumok megkülönböztetésére, vagy hozzáadhat egy beszédfelismerési készséget, hogy mélyebbre jusson a releváns tartalom hangfájljaiban. Egy lépésről lépésre példa, [lásd: Példa: Egyéni szakértelem létrehozása a ai-bővítés](cognitive-search-create-custom-skill-example.md).

 Bármilyen egyéni képességre van szüksége, van egy egyszerű és egyértelmű felület egy egyéni szakértelem csatlakoztatásához a dúsítási folyamat többi részéhez. A [skillsetbe](cognitive-search-defining-skillset.md) való felvétel egyetlen követelménye a bemenetek fogadása és a kimenetek kibocsátása a skillset egészén belül fogyasztható módon. A cikk középpontjában a bemeneti és kimeneti formátumok, amelyek a dúsítási folyamat igényel.

## <a name="web-api-custom-skill-interface"></a>Webes API egyéni szakértelem-felület

Egyéni WebAPI-szakértelem-végpontok alapértelmezett időtúllépésre, ha nem adnak vissza választ egy 30 másodperces ablakban. Az indexelési folyamat szinkron, és az indexelés időtúllépések hibát eredményez, ha az adott ablakban nem érkezik válasz.  Az időtúllépést legfeljebb 230 másodpercre lehet beállítani az időtúllépésre paraméter beállításával:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Győződjön meg arról, hogy az URI biztonságos (HTTPS).

Jelenleg az egyéni szakértelemmel való interakció egyetlen mechanizmusa egy webes API-felületen keresztül történik. A webes API-nak meg kell felelnie az ebben a szakaszban leírt követelményeknek.

### <a name="1--web-api-input-format"></a>1. Webes API bemeneti formátum

A webes API-nak el kell fogadnia a feldolgozandó rekordok tömbjét. Minden rekordnak tartalmaznia kell egy "tulajdonságtáskát", amely a webes API-hoz megadott bemenet. 

Tegyük fel, hogy egy egyszerű dúsítót szeretne létrehozni, amely azonosítja a szerződés szövegében említett első dátumot. Ebben a példában a szakértelem egyetlen bemeneti *contractText-et* fogad el szerződésszövegként. A szakértelem egyetlen kimenettel is rendelkezik, amely a szerződés dátuma. Ahhoz, hogy a dúsító érdekesebb, vissza ezt a *szerződéstDátum* alakú többrészes komplex típusú.

A webes API-nak készen kell lennie a bemeneti rekordok kötegének fogadására. Az *értékek* tömbjének minden tagja egy adott rekord bemenetét jelöli. Minden rekordnak a következő elemekkel kell rendelkeznie:

+ Egy *rekordazonosító* tag, amely egy adott rekord egyedi azonosítója. Amikor a dúsító visszaadja az eredményeket, meg kell adnia ezt a *recordId* annak érdekében, hogy a hívó egyezik a rekord eredményeit a bemeneti.

+ *Adattag,* amely lényegében egy zsák bemeneti mezők minden rekord.

Ahhoz, hogy konkrétabb legyen, a fenti példában a webes API-nak az ehhez hasonló kérésekre kell számítania:

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
A valóságban, a szolgáltatás kap hívott több száz vagy ezer rekordok helyett csak a három itt látható.

### <a name="2-web-api-output-format"></a>2. Webes API kimeneti formátum

A kimenet formátuma egy *rekordazonosítót*és egy tulajdonságzsákot tartalmazó rekordhalmaz. 

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

Ebben a konkrét példában csak egy kimenet, de több tulajdonság is kimenetele. 

### <a name="errors-and-warning"></a>Hibák és figyelmeztetés

Ahogy az előző példában is látható, minden rekordhoz hibaüzenetet és figyelmeztető üzenetet adhat vissza.

## <a name="consuming-custom-skills-from-skillset"></a>Egyéni készségek fogyasztása a skillset-ből

Amikor létrehoz egy webes API-bővítést, a http-fejléceket és paramétereket a kérelem részeként írhatja le. Az alábbi kódrészlet bemutatja, hogyan lehet a kérelem paramétereit és a *választható* HTTP-fejléceket a skillset definíció részeként leírni. Http-fejlécek nem követelmény, de lehetővé teszik, hogy további konfigurációs képességek hozzáadása a szakértelem, és állítsa be őket a skillset definíció.

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

Ez a cikk az egyéni szakértelem egy skillset integrálásához szükséges felületi követelményeket is lefedte. Kattintson az alábbi linkekre, ha többet szeretne megtudni az egyéni készségekről és a skillset-összetételről.

+ [Power Skills: egyéni készségek tárháza](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Példa: Egyéni szakértelem létrehozása a ai-dúsításhoz](cognitive-search-create-custom-skill-example.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Szakértelemkészlet létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Bővített mezők leképezése](cognitive-search-output-field-mapping.md)
