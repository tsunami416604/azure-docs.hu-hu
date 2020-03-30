---
title: Egyéni webes API-szakértelem a skillsets
titleSuffix: Azure Cognitive Search
description: Bővítse az Azure Cognitive Search-képességek képességeit webes API-k hívásával. Az egyéni webes API-szakértelem segítségével integrálhatja az egyéni kódot.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 29928d78c2cfc2f21def363341f8383c4efa89d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74484116"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Egyéni webes API-szakértelem az Azure Cognitive Search bővítési folyamatában

Az **egyéni webes API-szakértelem** lehetővé teszi az AI-bővítés kiterjesztését azáltal, hogy egyéni műveleteket biztosító webes API-végpontra hív. A beépített képességekhez hasonlóan az **egyéni webes API-szakértelem** bemenetekkel és kimenetekkel rendelkezik. A bemenetektől függően a webes API JSON-tartalommal rendelkezik az indexelő futtatásakor, és válaszként egy JSON-hasznos adatot, valamint egy sikeres állapotkódot ad ki. A válasz várhatóan az egyéni szakértelem által megadott kimenetek. Minden más válasz hibának minősül, és nem végez dúsítást.

A JSON-hasznos teher szerkezetét ebben a dokumentumban lejjebb írjuk le.

> [!NOTE]
> Az indexelő kétszer próbálja meg újra a webes API-ból visszaadott egyes szabványos HTTP-állapotkódokat. Ezek a HTTP-állapotkódok a következők: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| Uri | Annak a webes API-nak az URI-ja, amelyre a _JSON-tartalom_ lesz elküldve. Csak **https** URI séma engedélyezett |
| httpMódszer | A hasznos adat küldése közben használandó módszer. Az engedélyezett `PUT` módszerek`POST` |
| httpFejlécek | Kulcs-érték párok gyűjteménye, ahol a kulcsok fejlécneveket és értékeket jelölnek a webes API-nak a tartalommal együtt küldött fejlécértékek. Ebben a gyűjteményben tilos a következő `Accept`fejlécek szerepeljenek: `TE` `Upgrade`, `Accept-Charset`, `Accept-Encoding` `Content-Length`, `Content-Type`, `Cookie`, `Host`, ,`Via` |
| timeout | (Nem kötelező) Ha meg van adva, azt jelzi, hogy az API-hívást végző http-ügyfél időeltemég mindig elérhető. XSD "dayTimeDuration" értékként kell formázni (az [ISO 8601 időtartam](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) érték korlátozott részhalmaza). Például `PT60S` 60 másodpercig. Ha nincs beállítva, a program 30 másodperces alapértelmezett értéket választ. Az időhosszabbítás legfeljebb 230 másodpercre és legalább 1 másodpercre állítható be. |
| batchSize | (Nem kötelező) Azt jelzi, hogy hány "adatrekord" (lásd az alábbi _JSON_ tartalomstruktúrát) küldi el az API-hívás. Ha nincs beállítva, az alapértelmezett 1000 lesz kiválasztva. Azt javasoljuk, hogy használja ezt a paramétert, hogy megfelelő kompromisszumot érjen el az indexelési átviteli és az API betöltése között. |
| fokOfParallelism | (Nem kötelező) Ha meg van adva, azt jelzi, hogy az indexelő a megadott végpontpal párhuzamosan hány hívást fog kezdeményezni. Ezt az értéket csökkentheti, ha a végpont sikertelen a kérelem terhelése alatt, vagy emelje fel, ha a végpont képes fogadni több kérelmet, és szeretné az indexelő teljesítményének növelését.  Ha nincs beállítva, a rendszer 5-ös alapértelmezett értéket használ. A degreeOfParallelism legfeljebb 10, de legalább 1. |

## <a name="skill-inputs"></a>Szakértelem bemenetei

Ehhez a szakértelemhez nincsenek "előre definiált" bemenetek. Kiválaszthat egy vagy több olyan mezőt, amely már elérhető lenne a szakértelem végrehajtásakor bemenetként, és a Webes API-nak küldött _JSON-tartalom_ különböző mezőkkel fog rendelkezni.

## <a name="skill-outputs"></a>Szakértelem kimenetei

Ehhez a szakértelemhez nincsenek "előre definiált" kimenetek. Attól függően, hogy a válasz a webes API-t ad vissza, adja hozzá a kimeneti mezőket, hogy azok a _JSON-válaszból_ is levethetők.


## <a name="sample-definition"></a>Minta definíciója

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Mintabemeneti JSON-struktúra

Ez a _JSON-struktúra_ a webes API-nak küldött hasznos adat.
Mindig az alábbi korlátozásokat követi:

* A legfelső szintű entitás `values` neve objektumtömb lesz. Az ilyen objektumok száma legbőleg`batchSize`
* A tömb `values` minden objektuma
    * Egyedi `recordId` **karakterlánc,** amely a rekord azonosítására szolgál.
    * `data` _JSON-objektum._ A `data` tulajdonság mezői a szakértelem-definíció `inputs` szakaszában megadott "neveknek" felelnek meg. Ezeknek a mezőknek az `source` értéke e mezők ből származik (amelyek lehetnek a dokumentum egy mezőjéből, vagy potenciálisan más szakértelemből származnak)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Mintakimeneti JSON-struktúra

A "kimenet" a webes API-ból visszaadott válasznak felel meg. A webes API-nak csak _JSON-hasznos_ adatot `Content-Type` kell visszaadnia (a válaszfejléc nézettel ellenőrizve), és meg kell felelnie a következő megkötéseknek:

* Léteznie kell egy legfelső `values` szintű entitásnak, amely objektumok tömbje kell, hogy legyen.
* A tömbben lévő objektumok számának meg kell egyeznie a webes API-ra küldött objektumok számával.
* Minden objektumnak rendelkeznie kell:
   * Egy `recordId` ingatlan
   * Olyan `data` tulajdonság, amely olyan objektum, ahol a mezők a "nevek" és az érték szerinti "nevek" `output` dúsításának megfelelő dúsítást jelentenek.
   * Egy `errors` tulajdonság, egy tömb, amely felsorolja az indexelő végrehajtási előzményeihez hozzáadott hibákat. Ez a tulajdonság kötelező, `null` de lehet értéke.
   * Egy `warnings` tulajdonság, egy tömb, amely felsorolja az indexelő végrehajtási előzményeihez hozzáadott figyelmeztetéseket. Ez a tulajdonság kötelező, `null` de lehet értéke.
* A `values` tömbobjektumnak nem kell ugyanabban a sorrendben `values` lennie, mint a tömbben lévő objektumoknak, amelyeket kérésként küldtek a webes API-nak. A korreláció azonban `recordId` a program szerint a `recordId` webes API-ra irányuló eredeti kérelemnek nem részeként tartalmazó válasz minden rekordját elveti.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Hibaesetek
Amellett, hogy a webes API nem érhető el, vagy nem sikeres állapotkódokat küld ki, a következő esetek hibásnak minősülnek:

* Ha a webes API sikeres állapotkódot ad vissza, `application/json` de a válasz azt jelzi, hogy nem, akkor a válasz érvénytelennek minősül, és nem lesz dúsítás.
* Ha a választömbben `recordId` `values` **érvénytelen** (az eredeti kérelemben nincs vagy ismétlődő értékekkel) rekordok vannak, akkor **ezekhez** a rekordokhoz nem történik dúsítás.

Olyan esetekben, amikor a webes API nem érhető el, vagy http-hibát ad vissza, egy rövid hiba a HTTP-hibával kapcsolatos bármely rendelkezésre álló részletet hozzáadódik az indexelő végrehajtási előzményeihez.

## <a name="see-also"></a>Lásd még

+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Egyéni szakértelem hozzáadása a ai-dúsítási folyamathoz](cognitive-search-custom-skill-interface.md)
+ [Példa: Egyéni szakértelem létrehozása a ai-dúsításhoz](cognitive-search-create-custom-skill-example.md)
