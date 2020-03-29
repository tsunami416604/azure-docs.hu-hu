---
title: Adatmódosítás - LUIS
description: Ismerje meg, hogyan módosíthatók az adatok a nyelvi ismeretek (LUIS) előrejelzései előtt
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: b3b36351a64a4e1a0bd13d5785a4e0609a80901d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292059"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Az utterance (kifejezés) adatainak módosítása az előrejelzés előtt vagy alatt
A LUIS lehetőséget biztosít az utterance (kifejezés) kezelésére az előrejelzés előtt vagy alatt. Ezek közé tartozik [a helyesírás-javítás](luis-tutorial-bing-spellcheck.md), és az előre összeállított [datetimeV2](luis-reference-prebuilt-datetimev2.md)időzóna-problémáinak javítása .

## <a name="correct-spelling-errors-in-utterance"></a>Helyesírási hibák kijavítása az utterance (kifejezés) kifejezésben


### <a name="v3-runtime"></a>V3-as futásidő

Az utterance (kifejezés) skimondott szöveg a LUIS-nak való elküldése előtt a helyesírási javítások előfeldolgozása. Használja a helyes helyesírású példakimondott szöveget, hogy biztosítsa a megfelelő előrejelzéseket.

A [Bing helyesírás-ellenőrzése](../bing-spell-check/overview.md) segítségével javítsa ki a szöveget, mielőtt elküldi azt a LUIS.Use Bing Spell Check to correct text before sending it to LUIS.

### <a name="prior-to-v3-runtime"></a>V3-as futáselőtt

A LUIS [a Bing Helyesírás-ellenőrző API V7-et](../Bing-Spell-Check/overview.md) használja az utterance (kifejezés) helyesírási hibák kijavítására. A LUIS-nak szüksége van a szolgáltatáshoz társított kulcsra. Hozza létre a kulcsot, majd adja hozzá a kulcsot lekérdezési karakterlánc-paraméterként a [végponthoz.](https://go.microsoft.com/fwlink/?linkid=2092356)

A végpont működéséhez két params szükséges a helyesírási javításokhoz:

|Param|Érték|
|--|--|
|`spellCheck`|logikai|
|`bing-spell-check-subscription-key`|[Bing helyesírás-ellenőrző API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) végpontkulcs|

Amikor [a Bing Helyesírás-ellenőrző API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) hibát észlel, az eredeti utterance (kifejezés) és a javított utterance (kifejezés) a végpontról származó előrejelzésekkel együtt jelenik meg.

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)

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

### <a name="list-of-allowed-words"></a>Az engedélyezett szavak listája
A Bing helyesírás-ellenőrző API-t a LUIS nem támogatja a szavak listáját figyelmen kívül hagyni a helyesírás-ellenőrzés módosításai során. Ha engedélyeznie kell a szavak vagy rövidítések listáját, dolgozza fel az utterance (kifejezés) az ügyfélalkalmazásban, mielőtt elküldi az utterance (kifejezés) a LUIS szándék előrejelzésére.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Előre összeállított datetimeV2 entitás időzónájának módosítása
Ha egy LUIS-alkalmazás az előre összeállított [datetimeV2](luis-reference-prebuilt-datetimev2.md) entitást használja, egy datetime érték adható vissza az előrejelzési válaszban. A kérelem időzónája határozza meg a visszaküldés helyes dátumidejét. Ha a kérelem egy robotvagy egy másik központi alkalmazás, mielőtt a LUIS, javítsa ki az időzónát LUIS használ.

### <a name="endpoint-querystring-parameter"></a>Végpont lekérdezési karakterláncának paramétere
Az időzóna javítása a felhasználó időzónájának a [végponthoz](https://go.microsoft.com/fwlink/?linkid=2092356) való hozzáadásával történik a `timezoneOffset` param használatával. Az érték `timezoneOffset` nek az idő megváltoztatásához percben meg kell adni a pozitív vagy negatív számot.

|Param|Érték|
|--|--|
|`timezoneOffset`|pozitív vagy negatív szám, percben|

### <a name="daylight-savings-example"></a>Példa nyári időszámításra
Ha a visszaadott előre összeállított datetimeV2-t a nyári `timezoneOffset` időszámításhoz kell igazítania, a [végpontlekérdezéshez](https://go.microsoft.com/fwlink/?linkid=2092356) a lekérdezési karakterlánc paramétert kell használnia a +/- értékkel percben.

#### <a name="v2-prediction-endpoint-request"></a>[V2 előrejelzési végpontra vonatkozó kérés](#tab/V2)

60 perc hozzáadása:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

60 perc eltávolítása:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v3-prediction-endpoint-request"></a>[V3 előrejelzési végpontra vonatkozó kérés](#tab/V3)

60 perc hozzáadása:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

60 perc eltávolítása:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

* * *

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>A C# kód meghatározza az időzóna helyes értékétOffset
A következő C# kód a [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) osztály [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) `timezoneOffset` metódusát használja a rendszeridő alapján a helyes meghatározásához:

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Helyesírási hibák kijavítása az oktatóanyaggal](luis-tutorial-bing-spellcheck.md)
