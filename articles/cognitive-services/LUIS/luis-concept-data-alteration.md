---
title: Adatváltozás – LUIS
description: Ismerje meg, hogyan adatokat is módosítható előtt előrejelzések a Language Understanding (LUIS)
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 5547724a6333d248a7ba4e9aeecaaa8f331feb7d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361130"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Az ALTER utterance (kifejezés) adatok előtt vagy közben előrejelzése
A LUIS segítségével kezelheti az utterance (kifejezés) előtt vagy közben az előrejelzési módszert biztosít. Ezek közé tartozik a [helyesírás javítása](luis-tutorial-bing-spellcheck.md), valamint az előre elkészített [datetimeV2](luis-reference-prebuilt-datetimev2.md)időzóna-problémáinak javítása.

## <a name="correct-spelling-errors-in-utterance"></a>Helyesírási hibák az utterance (kifejezés)


### <a name="v3-runtime"></a>V3 futtatókörnyezet

Szöveg előfeldolgozása helyesírás-helyesbítésekhez, mielőtt elküldi a kiírást a LUIS-nek. Használjon például hosszúságú kimondott szöveg a megfelelő helyesírással, így biztosítva a megfelelő előrejelzések megszerzését.

A [Bing Spell Check](../bing-spell-check/overview.md) használatával javítsa a szöveget, mielőtt ELKÜLDENÉ a Luis-nek.

### <a name="prior-to-v3-runtime"></a>A v3 futtatókörnyezet előtt

LUIS [Bing Spell Check API v7](../Bing-Spell-Check/overview.md) használatával javítsa ki a helyesírási hibákat a teljes szövegben. A LUIS kell a szolgáltatáshoz tartozó kulcsot. Hozza létre a kulcsot, majd adja hozzá a kulcsot querystring paraméterként a [végpontnál](https://go.microsoft.com/fwlink/?linkid=2092356).

A végpont két paraméterei a helyesírási javításokkal működéséhez szükségesek:

|Param|Érték|
|--|--|
|`spellCheck`|logikai|
|`bing-spell-check-subscription-key`|[Bing Spell Check API v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) -végpont kulcsa|

Ha [Bing Spell Check API v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) hibát észlel, az eredeti kimaradás és a korrigált Kimondás a végponton lévő előrejelzések mellett történik.

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>Engedélyezett szavak listája
A LUIS-ben használt Bing Spell Check API nem támogatja a helyesírás-ellenőrzési változtatások során figyelmen kívül hagyó szavak listáját. Ha engedélyezni szeretné a szavak vagy rövidítések listáját, akkor a Kimondás előtt dolgozza fel a kilépést az ügyfélalkalmazás számára, mielőtt elküldi a megfogalmazást a LUIS for szándék előrejelzésére.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Előre összeállított datetimeV2 entitás időzóna módosítása
Ha egy LUIS-alkalmazás az előre elkészített [datetimeV2](luis-reference-prebuilt-datetimev2.md) entitást használja, az előrejelzési válaszban egy datetime érték adható vissza. A kérelem az időzóna visszaadandó megfelelő datetime meghatározására szolgál. Ha a kérelem érkezik egy robot vagy egy másik központi alkalmazás LUIS való elérése előtt, javítsa ki az időzóna, LUIS használja.

### <a name="endpoint-querystring-parameter"></a>Végpont paramétert
Az időzóna kijavítása úgy történik, hogy a felhasználó időzónáját hozzáadja a [végponthoz](https://go.microsoft.com/fwlink/?linkid=2092356) a `timezoneOffset` param használatával. Az idő módosításához a `timezoneOffset` értékének pozitív vagy negatív számnak kell lennie percben.

|Param|Érték|
|--|--|
|`timezoneOffset`|pozitív vagy negatív szám, percek alatt|

### <a name="daylight-savings-example"></a>Nyári időszámítás – megtakarítási példa
Ha a visszaadott előre elkészített datetimeV2 szeretné beállítani a nyári időszámítást, akkor a querystring paramétert a [végpont](https://go.microsoft.com/fwlink/?linkid=2092356) lekérdezéséhez percben kell `timezoneOffset` használni.

#### <a name="v2-prediction-endpoint-request"></a>[V2 előrejelzési végpont kérése](#tab/V2)

Adja hozzá a 60 perc:

https://{Region}. API. kognitív. microsoft. com/Luis/v 2.0/apps/{appId}? q = a fények bekapcsolása? **timezoneOffset = 60**& verbose = {boolean} & helyesírás = {boolean} &-előkészítés = {boolean} & Bing-Spell-Check-előfizetés-Key = {string} & log = {Boolean}

60 perc eltávolítása:

https://{Region}. API. kognitív. microsoft. com/Luis/v 2.0/apps/{appId}? q = a fények bekapcsolása? **timezoneOffset =-60**& részletesen = {boolean} & helyesírás = {boolean} &-előkészítés = {boolean} & Bing-Spell-Check-előfizetés-Key = {string} & log = {Boolean}

#### <a name="v3-prediction-endpoint-request"></a>[V3 előrejelzési végpont kérése](#tab/V3)

Adja hozzá a 60 perc:

https://{Region}. API. kognitív. microsoft. com/Luis/v 3.0 – Preview/apps/{appId}/Slots/Production/Tippelje? Query = a fények bekapcsolása? **timezoneOffset = 60**& helyesírás = {boolean} & Bing-Spell-Check-előfizetés-Key = {string} & log = {Boolean}

60 perc eltávolítása:

https://{Region}. API. kognitív. microsoft. com/Luis/v 3.0 – Preview/apps/{appId}/Slots/Production/Tippelje? Query = a fények bekapcsolása? **timezoneOffset =-60**& helyesírás = {boolean} & Bing-Spell-Check-előfizetés-Key = {string} & log = {Boolean}

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * *

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#-kód megfelelő timezoneOffset értékét határozza meg
A következő C# kód a [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) osztály [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) metódusát használja a megfelelő `timezoneOffset` meghatározásához a rendszeridő alapján:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Helyesírási hibák javítása ezzel az Oktatóanyaggal](luis-tutorial-bing-spellcheck.md)
