---
title: Adatváltozás – LUIS
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan változtathatók meg az adatváltozások a Language Understanding (LUIS) előrejelzések előtt
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 1bde70dadbe1e5b8ba9bf90bd9ca2f48a4c65491
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381800"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>A kiírási adatértékek módosítása az előrejelzés előtt vagy közben
LUIS lehetővé teszi a Kimondás kezelését a jóslat előtt vagy közben. Ezek közé tartozik a [helyesírás javítása](luis-tutorial-bing-spellcheck.md), valamint az előre elkészített [datetimeV2](luis-reference-prebuilt-datetimev2.md)időzóna-problémáinak javítása. 

## <a name="correct-spelling-errors-in-utterance"></a>Helyesírási hibák javítása a teljes szövegben

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


LUIS [Bing Spell Check API v7](../Bing-Spell-Check/overview.md) használatával javítsa ki a helyesírási hibákat a teljes szövegben. A LUIS-nek szüksége van a szolgáltatáshoz társított kulcsra. Hozza létre a kulcsot, majd adja hozzá a kulcsot querystring paraméterként a [végpontnál](https://go.microsoft.com/fwlink/?linkid=2092356). 

<!--
You can also correct spelling errors in the **Test** panel by [entering the key](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key is kept as a session variable in the browser for the Test panel. Add the key to the Test panel in each browser session you want spelling corrected. 

Usage of the key in the test panel and at the endpoint count toward the [key usage](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) quota. LUIS implements Bing Spell Check limits for text length. 

-->

A végpontnak két paramétert kell megadnia a helyesírási javítások működéséhez:

|Paraméter|Value (Díj)|
|--|--|
|`spellCheck`|logikai|
|`bing-spell-check-subscription-key`|[Bing Spell Check API v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) -végpont kulcsa|

Ha [Bing Spell Check API v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) hibát észlel, az eredeti kimaradás és a korrigált Kimondás a végponton lévő előrejelzések mellett történik.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)
 
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

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Előre elkészített datetimeV2 entitás időzónájának módosítása
Ha egy LUIS-alkalmazás az előre elkészített [datetimeV2](luis-reference-prebuilt-datetimev2.md) entitást használja, az előrejelzési válaszban egy datetime érték adható vissza. A kérelem időzónája a helyes datetime érték meghatározására szolgál. Ha a kérelem egy robotból vagy egy másik központosított alkalmazásból érkezik, mielőtt elkezdené a LUIS-t, javítsa ki az időzóna LUIS használatát. 

### <a name="endpoint-querystring-parameter"></a>Endpoint querystring paraméter
Az időzóna kijavítása úgy történik, hogy a felhasználó időzónáját hozzáadja a [végponthoz](https://go.microsoft.com/fwlink/?linkid=2092356) a `timezoneOffset` param használatával. Az idő módosításához a `timezoneOffset` értékének pozitív vagy negatív számnak kell lennie percben.  

|Paraméter|Value (Díj)|
|--|--|
|`timezoneOffset`|pozitív vagy negatív szám (percben)|

### <a name="daylight-savings-example"></a>Nyári megtakarítások – példa
Ha a visszaadott előre elkészített datetimeV2 szeretné beállítani a nyári időszámítást, akkor a querystring paramétert a [végpont](https://go.microsoft.com/fwlink/?linkid=2092356) lekérdezéséhez percben kell `timezoneOffset` használni.

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 előrejelzési végpont kérése](#tab/V2)

60 perc hozzáadása: 

https://{Region}. API. kognitív. microsoft. com/Luis/v 2.0/apps/{appId}? q = a fények bekapcsolása? **timezoneOffset = 60**& verbose = {boolean} & helyesírás = {boolean} &-előkészítés = {boolean} & Bing-Spell-Check-előfizetés-Key = {string} & log = {Boolean}

60 perc eltávolítása: 

https://{Region}. API. kognitív. microsoft. com/Luis/v 2.0/apps/{appId}? q = a fények bekapcsolása? **timezoneOffset =-60**& részletesen = {boolean} & helyesírás = {boolean} &-előkészítés = {boolean} & Bing-Spell-Check-előfizetés-Key = {string} & log = {Boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 előrejelzési végpont kérése](#tab/V3)

60 perc hozzáadása:

https://{Region}. API. kognitív. microsoft. com/Luis/v 3.0 – Preview/apps/{appId}/Slots/Production/Tippelje? Query = a fények bekapcsolása? **timezoneOffset = 60**& helyesírás = {boolean} & Bing-Spell-Check-előfizetés-Key = {string} & log = {Boolean}

60 perc eltávolítása: 

https://{Region}. API. kognitív. microsoft. com/Luis/v 3.0 – Preview/apps/{appId}/Slots/Production/Tippelje? Query = a fények bekapcsolása? **timezoneOffset =-60**& helyesírás = {boolean} & Bing-Spell-Check-előfizetés-Key = {string} & log = {Boolean}

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#a kód meghatározza a timezoneOffset helyes értékét
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
