---
title: Egyéni kognitív keresési képességek – Azure Search
description: A kognitív keresési szakértelmével képességeinek kiterjesztése a webes API-k meghívásával
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: a01518158e063d68734b1230f11cae78f461faac
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183453"
---
# <a name="custom-web-api-skill"></a>Egyéni webes API-képesség

Az **egyéni webes API** -képesség lehetővé teszi a kognitív keresés kiterjesztését, ha az egyéni műveleteket biztosító webes API-végpontot hívja meg. A beépített képességekhez hasonlóan egy **egyéni webes API** -képesség is tartalmaz bemeneteket és kimeneteket. A bemenettől függően a webes API egy JSON-adattartalmat kap, amikor az indexelő fut, és válaszként egy JSON-adattartalmat ad eredményként, valamint egy sikerességi állapotkódot. A válasz várhatóan az egyéni szakértelem által megadott kimeneteket adja meg. Minden más válasz hibát jelez, és nem végezhető el a dúsítás.

A JSON-adattartalom szerkezetét részletesebben ismertetjük ebben a dokumentumban.

> [!NOTE]
> Az indexelő kétszer próbálkozik a webes API által visszaadott szabványos HTTP-állapotkódok esetében. Ezek a HTTP-állapotkódok a következők: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket.

| Paraméternév     | Leírás |
|--------------------|-------------|
| uri azonosító | Annak a webes API-nak az URI-ja, amelybe a _JSON_ -tartalom el lesz küldve. Csak **https** URI-séma engedélyezett |
| httpMethod | A hasznos adatok küldésekor használandó metódus. Engedélyezett módszerek `PUT` vagy`POST` |
| httpHeaders | Olyan kulcs-érték párok gyűjteménye, amelyekben a kulcsok fejléc-és értéknek felelnek meg, a webes API-ra és a hasznos adattartalommal együtt küldendő fejléc-értékeket. A következő fejlécek nem engedélyezettek ebben a gyűjteményben: `Accept` `Accept-Encoding`, `Accept-Charset` `Cookie` `Content-Type` `Content-Length` `Host` ,,,,,,,,`TE` `Upgrade``Via` |
| timeout | Választható Ha meg van adva, az API-hívást készítő http-ügyfél időtúllépését jelzi. A fájlnak XSD "dayTimeDuration" értéknek kell lennie (az [ISO 8601 időtartam](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) értékének korlátozott részhalmaza). Például `PT60S` 60 másodpercig. Ha nincs beállítva, a rendszer egy alapértelmezett 30 másodperces értéket választ. Az időtúllépés legfeljebb 230 másodpercig és legalább 1 másodpercig állítható be. |
| batchSize | Választható Azt jelzi, hogy hány "adatrekord" (lásd az alábbi _JSON_ -adattartalom-szerkezetet) egy API-hívás alapján lesz elküldve. Ha nincs beállítva, a rendszer alapértelmezett 1000-as értéket választ. Javasoljuk, hogy használja ezt a paramétert, hogy megfelelő kompromisszumot érjen el az indexelési teljesítmény és az API terhelése között |

## <a name="skill-inputs"></a>Szaktudás bemenetei

Ehhez a képzettséghez nincsenek "előre definiált" bemenetek. Kiválaszthat egy vagy több olyan mezőt, amely a szaktudás végrehajtásának időpontjában már elérhető lesz, és a webes API-hoz továbbított _JSON_ -adattartalom különböző mezőket tartalmaz.

## <a name="skill-outputs"></a>Szaktudás kimenetei

Ehhez a szakértelemhöz nem tartoznak "előre definiált" kimenetek. Attól függően, hogy a webes API milyen választ ad vissza, adja hozzá a kimeneti mezőket, hogy azok a _JSON_ -válaszból is felvehetők legyenek.


## <a name="sample-definition"></a>Minta definíciója

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can count the number of words or characters or lines in text",
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
            "name": "countOf",
            "source": "/document/propertyToCount"
          }
        ],
        "outputs": [
          {
            "name": "count",
            "targetName": "countOfThings"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Minta bemeneti JSON-struktúra

Ez a _JSON_ -struktúra a webes API-nak küldendő adattartalmat jelöli.
Mindig a következő korlátozásokat fogja követni:

* A legfelső szintű entitás neve `values` , és az objektumok tömbje lesz. Az ilyen objektumok száma a következő lesz:`batchSize`
* A tömb minden objektuma tartalmazni fogja a `values`
    * Olyan `recordId` tulajdonság, amely **egyedi** karakterlánc, amely a rekord azonosítására szolgál.
    * Egy `data` olyan tulajdonság, amely egy _JSON_ -objektum. A `data` tulajdonság mezői a szaktudás definíciójának `inputs` szakaszában megadott "nevek" értékkel fognak egyezni. Ezeknek a mezőknek az értéke az adott mezőkből származik `source` (amelyek a dokumentum egy mezőjéből származnak, vagy esetleg egy másik képességből)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "countOf": "words"
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "countOf": "characters"
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world \r\n Hi World",
             "language": "en",
             "countOf": "lines"
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "countOf": null
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Minta kimenet JSON-szerkezete

A "kimenet" a webes API által visszaadott válasznak felel meg. A webes API-nak csak _JSON_ -adattartalomot kell visszaadnia `Content-Type` (a válasz fejlécének ellenőrzésével ellenőrizve), és meg kell felelnie a következő megkötéseknek:

* Egy olyan legfelső szintű entitásnak `values` kell lennie, amely az objektumok tömbje lehet.
* A tömbben lévő objektumok számának meg kell egyeznie a webes API-nak eljuttatott objektumok számával.
* Minden objektumnak a következőket kell tartalmaznia:
   * Egy `recordId` tulajdonság
   * Egy `data` tulajdonság, amely egy olyan objektum, amelyben a mezők olyan dúsítások, amelyek megfelelnek a "nevek `output` " elemnek, és amelynek értéke a dúsításnak minősül.
   * Egy `errors` tulajdonság, egy tömb, amely felsorolja az indexelő végrehajtási előzményeihez hozzáadott hibákat. Ez a tulajdonság kötelező, de `null` értéke is lehet.
   * Egy `warnings` tulajdonság, amely felsorolja az összes olyan figyelmeztetést, amely az indexelő végrehajtási előzményeihez lesz hozzáadva. Ez a tulajdonság kötelező, de `null` értéke is lehet.
* A `values` tömb objektumainak nem kell ugyanabban a sorrendben lennie, mint a webes API `values` -nak kérésként elküldett tömb objektumainak. Azonban a a `recordId` korrelációt veszi fel, így a válaszban `recordId` szereplő olyan rekordokat, amelyek nem részei a webes API-nak, a rendszer elveti.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "Cannot understand what needs to be counted"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "count": 2
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "count": 6
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "count": 11
            },
            "errors": null,
            "warnings": null
        },
    ]
}

```

## <a name="error-cases"></a>Hibák esetei
Amellett, hogy a webes API nem érhető el, vagy a nem sikeres állapotkódot küldi el, a következők hibás eseteknek minősülnek:

* Ha a webes API sikeres állapotkódot ad vissza, de a válasz azt jelzi, hogy `application/json` a válasz érvénytelennek minősül, és a rendszer nem hajtja végre a dúsítást.
* Ha **érvénytelen** ( `recordId` nem az eredeti kérelemben vagy ismétlődő értékekkel) rekord szerepel a válasz `values` tömbben, a rendszer nem hajtja végre a rekordok dúsítását.

Azokban az esetekben, amikor a webes API nem érhető el vagy HTTP-hibát ad vissza, a HTTP-hibával kapcsolatos összes rendelkezésre álló adathoz tartozó rövid hiba lesz hozzáadva az indexelő végrehajtási előzményeihez.

## <a name="see-also"></a>Lásd még

+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Egyéni szakértelem hozzáadása a kognitív kereséshez](cognitive-search-custom-skill-interface.md)
+ [Példa: Egyéni képesség létrehozása a kognitív kereséshez](cognitive-search-create-custom-skill-example.md)