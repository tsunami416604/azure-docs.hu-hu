---
title: Egyéni cognitive search szakértelem – Azure Search
description: Kognitív keresés szakértelmével funkcióinak kiterjesztése meghívásával webes API-k
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: luisca
ms.custom: seojan2018
ms.openlocfilehash: 1fcb12fc2cfae98376210e1924a670cce444f4f2
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757652"
---
# <a name="custom-web-api-skill"></a>Egyéni webes API-ismeretek

A **egyéni Web API** szakértelem lehetővé teszi, hogy terjessze ki a kognitív keresés meghívásával egy webes API-végpont egyéni műveleteket biztosít. Beépített képességeit, hasonlóan egy **egyéni Web API-t** szakértelem rendelkezik bemeneteit és kimeneteit. A bemenetek függően a a webes API egy JSON-adattartalom kapja, amikor az indexelő futása, és kiírja a JSON-hasznosadatokat adott válaszként sikeres állapotkódot együtt. A válasz kellene rendelkeznie az egyéni ismeretek által megadott kimeneteire. Semmilyen más választ számít a hibát, és nem végrehajtott információbeolvasás lesz végrehajtva.

A JSON-adattartalmat szerkezete olyan további le ebben a dokumentumban.

> [!NOTE]
> Az indexelő kétszer az egyes szabványos HTTP-állapotkódok a webes API által visszaadott próbálkozik újra. A HTTP-állapotkódok a következők: 
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Ismeretek paraméterek

A paraméterei a kis-és nagybetűket.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| uri azonosító | Az URI a webes API-t, amelyhez a _JSON_ adattartalom küld. Csak **https** URI-séma használata engedélyezett. |
| httpMethod | A használt módszer a tartalom küldése közben. Módszerek a következők engedélyezett `PUT` vagy `POST` |
| httpHeaders | Ahol a kulcsok felel meg a fejléc nevét, és küld a webes API-t a hasznos együtt fejléc értékeket jelölik kulcs-érték párok gyűjteménye. A következő fejlécek nem használhatják fel az ebben a gyűjteményben: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| timeout | (Nem kötelező) Megadása esetén a http ügyfél, így az API-hívás azt jelzi, az időkorlátot. Egy XSD "nyelv szerinti dayTimeDuration" értékként kell formázni (korlátozott részhalmaza olyan [ISO 8601 időtartama](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) érték). Ha például `PT60S` 60 másodpercig. Ha nincs beállítva, 30 másodperces alapértelmezett értéke van kiválasztva. Az időtúllépés állítható legfeljebb 90 másodperc alatt és a egy legalább 1 másodperc. |
| batchSize | (Nem kötelező) Azt jelzi, hogy hány "adatfelderítési rekordok" (lásd: _JSON_ adattartalom struktúra az alábbi) küld egy API-hívás. Ha nem a csoportot, alapértelmezés szerint 1000 van kiválasztva. Azt javasoljuk, hogy a paraméter használatával érhet el az indexelési teljesítmény és az API-t terhelése megfelelő magával |

## <a name="skill-inputs"></a>Ismeretek bemenetek

Nincsenek a szakértelem "előre meghatározott" adatbevitel nélkül. Választhat egy vagy több mező, amely már elérhető a képzettségi végrehajtási bemenetként időpontjában, és a _JSON_ a webes API-nak küldött hasznos fog rendelkezni a különböző mezők.

## <a name="skill-outputs"></a>Ismeretek kimenetek

Nincsenek sem "előre meghatározott" szakértelem kimeneteit. Attól függően, a webes API visszaadja a választ, adja hozzá az táblakimeneti mezők, így azok is lehet felülettől a _JSON_ választ.


## <a name="sample-definition"></a>Minta-definíció

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
## <a name="sample-input-json-structure"></a>Példa a bemeneti JSON struktúrában

Ez _JSON_ struktúra küld a webes API adattartalom jelöli.
Ezek a korlátozások mindig távolságban követi:

* A legfelső szintű entitás neve `values` és a egy objektumokból álló tömböt lesz. Az ilyen objektumok száma lesz legfeljebb a `batchSize`
* Az egyes objektumok a `values` tömböt fog rendelkezni.
    * A `recordId` tulajdonság, amely egy **egyedi** rekord azonosításához használt karakterlánc.
    * A `data` tulajdonság, amely egy _JSON_ objektum. Mezőit a `data` tulajdonságot a "név" megadott fognak megfelelni a `inputs` az szakértelem definíció szakasza. Ezek a mezők értékét lesznek a a `source` (amely lehet egy mezőt a dokumentum, vagy esetleg egy másik szakértelem) mezők

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

## <a name="sample-output-json-structure"></a>Minta kimenet JSON struktúrában

A "kimeneti" felel meg a választ adott vissza a webes API-t. A webes API-t adhat vissza egy _JSON_ adattartalom (megtekintésével ellenőrizni a `Content-Type` válaszfejléc) és -eleget kell tenniük a következő korlátozásokkal:

* Meg kell adni egy legfelső szintű entitás nevű `values` kell lennie, amely objektumok egy tömbjét.
* A tömb objektumainak számát legyen ugyanaz, mint a webes küldött objektumot API-t.
* Az egyes objektumok kell rendelkeznie:
   * A `recordId` tulajdonság
   * A `data` tulajdonság, amely egy olyan objektum, ahol a mezők a következők végrehajtott információbeolvasás egyeztetést "neve" a `output` , és amelynek értéke a Adatbővítés számít.
   * Egy `errors` tulajdonság, egy tömb, listázás hibákat észlelt, amely megjelenik az indexelő futtatási előzményei. Ez a tulajdonság megadása kötelező, de rendelkezhetnek egy `null` értéket.
   * A `warnings` tulajdonság, egy tömb, bármilyen figyelmeztetés található, amely megjelenik az indexelő futtatási előzményei listázása. Ez a tulajdonság megadása kötelező, de rendelkezhetnek egy `null` értéket.
* Az objektumok a `values` tömb nem kell ugyanabban a sorrendben, mint az objektumokat a `values` egy kérést a webes API-nak küldött tömb. Azonban a `recordId` korrelációs szolgál, így bármely rögzítse a válaszban tartalmazó egy `recordId` amely azonban nem része a webes API-nak küldött eredeti kéréssel elvesznek.

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

## <a name="error-cases"></a>Hibák esetén
A webes API nem érhető el, vagy ki nem sikeres állapotú kódok küldő éppen mellett a következő tekinthetők hibás esetek:

* Ha a webes API sikeres állapotkódot ad vissza, de a válasz azt jelzi, hogy nincs `application/json` akkor számít, hogy a válasz érvénytelen és nem végrehajtott információbeolvasás történik.
* Van-e **érvénytelen** (a `recordId` nem az eredeti kérést, vagy az ismétlődő értékek) rögzíti a válaszban `values` tömb, sem Adatbővítés történik a **ezeket** rögzíti.

Azokra az esetekre, amikor a webes API nem érhető el, vagy egy HTTP-hibát ad vissza egy rövid hibaüzenet bármilyen elérhető HTTP hiba részleteit hozzáadódik az indexelő futtatási előzményei.

## <a name="see-also"></a>Lásd még

+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Egyéni ismeretek hozzáadása a kognitív keresés](cognitive-search-custom-skill-interface.md)
+ [Hozzon létre egy egyéni ismeretek a szöveg lefordítása API-val](cognitive-search-create-custom-skill-example.md)