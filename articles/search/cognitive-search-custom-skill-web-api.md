---
title: Egyéni webes API-képesség a szakértelmével-ben
titleSuffix: Azure Cognitive Search
description: Kiterjesztheti az Azure Cognitive Search szakértelmével képességeit a webes API-k meghívásával. Egyéni kód integrálásához használja az egyéni webes API-képességet.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 29928d78c2cfc2f21def363341f8383c4efa89d2
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484116"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Egyéni webes API-képesség egy Azure Cognitive Search alkoholtartalom-növelési folyamatban

Az **egyéni webes API** -képesség lehetővé teszi, hogy kiterjessze az AI-bővítést úgy, hogy az egyéni műveleteket biztosító webes API-végpontot hívja meg. A beépített képességekhez hasonlóan egy **egyéni webes API** -képesség is tartalmaz bemeneteket és kimeneteket. A bemenettől függően a webes API egy JSON-adattartalmat kap, amikor az indexelő fut, és válaszként egy JSON-adattartalmat ad eredményként, valamint egy sikerességi állapotkódot. A válasz várhatóan az egyéni szakértelem által megadott kimeneteket adja meg. Minden más válasz hibát jelez, és nem végezhető el a dúsítás.

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

| Paraméter neve     | Leírás |
|--------------------|-------------|
| URI | Annak a webes API-nak az URI-ja, amelybe a _JSON_ -tartalom el lesz küldve. Csak **https** URI-séma engedélyezett |
| httpMethod | A hasznos adatok küldésekor használandó metódus. Az engedélyezett módszerek `PUT` vagy `POST` |
| httpHeaders | Olyan kulcs-érték párok gyűjteménye, amelyekben a kulcsok fejléc-és értéknek felelnek meg, a webes API-ra és a hasznos adattartalommal együtt küldendő fejléc-értékeket. A következő fejlécek nem engedélyezettek ebben a gyűjteményben: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| timeout | Választható Ha meg van adva, az API-hívást készítő http-ügyfél időtúllépését jelzi. A fájlnak XSD "dayTimeDuration" értéknek kell lennie (az [ISO 8601 időtartam](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) értékének korlátozott részhalmaza). `PT60S` például 60 másodpercig. Ha nincs beállítva, a rendszer egy alapértelmezett 30 másodperces értéket választ. Az időtúllépés legfeljebb 230 másodpercig és legalább 1 másodpercig állítható be. |
| batchSize | Választható Azt jelzi, hogy hány "adatrekord" (lásd az alábbi _JSON_ -adattartalom-szerkezetet) egy API-hívás alapján lesz elküldve. Ha nincs beállítva, a rendszer alapértelmezett 1000-as értéket választ. Javasoljuk, hogy használja ezt a paramétert, hogy megfelelő kompromisszumot érjen el az indexelési teljesítmény és az API terhelése között |
| degreeOfParallelism | Választható Ha meg van adva, az indexelő által az Ön által megadott végponttal párhuzamosan kezdeményezett hívások számát jelzi. Csökkentheti ezt az értéket, ha a végpont nem a kérelem terhelése alatt túl magas, vagy ha a végpont több kérést is képes fogadni, és az indexelő teljesítményének növelését szeretné.  Ha nincs beállítva, a rendszer az alapértelmezett 5 értéket használja. A Analyticsunits legfeljebb 10 és legalább 1 értékre állítható be. |

## <a name="skill-inputs"></a>Szaktudás bemenetei

Ehhez a képzettséghez nincsenek "előre definiált" bemenetek. Kiválaszthat egy vagy több olyan mezőt, amely a szaktudás végrehajtásának időpontjában már elérhető lesz, és a webes API-hoz továbbított _JSON_ -adattartalom különböző mezőket tartalmaz.

## <a name="skill-outputs"></a>Szaktudás kimenetei

Ehhez a szakértelemhöz nem tartoznak "előre definiált" kimenetek. Attól függően, hogy a webes API milyen választ ad vissza, adja hozzá a kimeneti mezőket, hogy azok a _JSON_ -válaszból is felvehetők legyenek.


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
## <a name="sample-input-json-structure"></a>Minta bemeneti JSON-struktúra

Ez a _JSON_ -struktúra a webes API-nak küldendő adattartalmat jelöli.
Mindig a következő korlátozásokat fogja követni:

* A legfelső szintű entitás neve `values`, és az objektumok tömbje lesz. Az ilyen objektumok száma a `batchSize`
* A `values` tömb minden objektuma tartalmazni fogja a
    * Egy `recordId` tulajdonság, amely egy **egyedi** karakterlánc, amely a rekord azonosítására szolgál.
    * Egy olyan `data` tulajdonság, amely _JSON_ -objektum. A `data` tulajdonság mezői megfelelnek a szaktudás definíciójának `inputs` szakaszában megadott "nevek" értéknek. Ezeknek a mezőknek az értéke ezeknek a mezőknek a `source` származik (amelyek a dokumentum egy mezőjéből származnak, vagy esetleg más képességtől).

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

## <a name="sample-output-json-structure"></a>Minta kimenet JSON-szerkezete

A "kimenet" a webes API által visszaadott válasznak felel meg. A webes API-nak csak _JSON_ -adattartalmat kell visszaadnia (a `Content-Type` válasz fejlécének ellenőrzésével ellenőrizve), és meg kell felelnie a következő megkötéseknek:

* Egy `values` nevű legfelső szintű entitásnak kell lennie, amely az objektumok tömbje lehet.
* A tömbben lévő objektumok számának meg kell egyeznie a webes API-nak eljuttatott objektumok számával.
* Minden objektumnak a következőket kell tartalmaznia:
   * Egy `recordId` tulajdonság
   * Egy `data` tulajdonság, amely egy olyan objektum, amelyben a mezők olyan dúsítások, amelyek megfelelnek a `output` "nevek" elemének, és amelynek értéke a dúsítás.
   * Egy `errors` tulajdonság, amely az indexelő végrehajtási előzményeibe kerülő hibák listáját tartalmazza. Ez a tulajdonság kötelező, de `null` érték is lehet.
   * Egy `warnings` tulajdonság, amely felsorolja az összes olyan figyelmeztetést, amely az indexelő végrehajtási előzményeihez lesz hozzáadva. Ez a tulajdonság kötelező, de `null` érték is lehet.
* A `values` tömb objektumainak nem kell ugyanabban a sorrendben lenniük, mint a webes API-nak kérésként elküldett `values` tömbben lévő objektumok. A `recordId` azonban a korrelációhoz használja, így a válaszban szereplő olyan rekordokat, amelyek nem részei a webes API-nak az eredeti kérelemnek, a rendszer elveti a `recordId`.

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

## <a name="error-cases"></a>Hibák esetei
Amellett, hogy a webes API nem érhető el, vagy a nem sikeres állapotkódot küldi el, a következők hibás eseteknek minősülnek:

* Ha a webes API sikeres állapotkódot ad vissza, de a válasz azt jelzi, hogy nem `application/json`, akkor a válasz érvénytelennek minősül, és a rendszer nem hajtja végre a dúsítást.
* Ha **érvénytelenek** (`recordId` nem az eredeti kérelemben, vagy duplikált értékekkel) a válasz `values` tömbben lévő rekordokat, a rendszer nem hajtja végre **a rekordok** dúsítását.

Azokban az esetekben, amikor a webes API nem érhető el vagy HTTP-hibát ad vissza, a HTTP-hibával kapcsolatos összes rendelkezésre álló adathoz tartozó rövid hiba lesz hozzáadva az indexelő végrehajtási előzményeihez.

## <a name="see-also"></a>Lásd még

+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Egyéni képesség hozzáadása egy mesterséges intelligencia-bővítési folyamathoz](cognitive-search-custom-skill-interface.md)
+ [Példa: egyéni képesség létrehozása AI-bővítéshez](cognitive-search-create-custom-skill-example.md)
