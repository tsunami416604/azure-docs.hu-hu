---
title: Adatváltozás – LUIS
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan adatokat is módosítható előtt előrejelzések a Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: 734389c92ede88d336df60a1a79a738d2abcfa92
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703162"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Az ALTER utterance (kifejezés) adatok előtt vagy közben előrejelzése
A LUIS segítségével kezelheti az utterance (kifejezés) előtt vagy közben az előrejelzési módszert biztosít. Ezek közé tartozik a [helyesírás javítása](luis-tutorial-bing-spellcheck.md), valamint az előre elkészített [datetimeV2](luis-reference-prebuilt-datetimev2.md)időzóna-problémáinak javítása. 

## <a name="correct-spelling-errors-in-utterance"></a>Helyesírási hibák az utterance (kifejezés)
Használja a LUIS [a Bing Spell ellenőrzés API 7-es](../Bing-Spell-Check/overview.md) az utterance (kifejezés) a helyesírási hibákat. A LUIS kell a szolgáltatáshoz tartozó kulcsot. Hozza létre a kulcsot, majd adja hozzá a kulcsot a lekérdezési karakterlánc paraméterként a [végpont](https://go.microsoft.com/fwlink/?linkid=2092356). 

Kijavíthatja a helyesírási hibákat is a **teszt** panel szerint [a kulcs megadása](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). A kulcs, a teszt panel a böngészőben egy munkamenet-változó tárolja. Adja hozzá a kulcsot minden böngésző-munkamenetben helyesírás-ellenőrzés javítani szeretné a teszt panelt. 

A teszt panel és a végpont felszámítja a kulcs használatát a [kulcshasználat](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) kvótát. LUIS valósítja meg a Bing helyesírás-ellenőrzési korlátozások a szöveg hossza. 

A végpont két paraméterei a helyesírási javításokkal működéséhez szükségesek:

|Param|Érték|
|--|--|
|`spellCheck`|logikai|
|`bing-spell-check-subscription-key`|[A Bing Spell ellenőrzés API 7-es](https://azure.microsoft.com/services/cognitive-services/spell-check/) végponti kulcs|

Amikor [a Bing Spell ellenőrzés API 7-es](https://azure.microsoft.com/services/cognitive-services/spell-check/) észleli a hiba, az eredeti utterance (kifejezés) a javított utterance (kifejezés) adja vissza és előrejelzések együtt a végpontról.

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

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Előre összeállított datetimeV2 entitás időzóna módosítása
Ha egy LUIS-alkalmazás az előre elkészített [datetimeV2](luis-reference-prebuilt-datetimev2.md) entitást használja, az előrejelzési válaszban egy datetime érték adható vissza. A kérelem az időzóna visszaadandó megfelelő datetime meghatározására szolgál. Ha a kérelem érkezik egy robot vagy egy másik központi alkalmazás LUIS való elérése előtt, javítsa ki az időzóna, LUIS használja. 

### <a name="endpoint-querystring-parameter"></a>Végpont paramétert
Az időzóna nem kerül kijavításra, a felhasználó időzóna való hozzáadásával a [végpont](https://go.microsoft.com/fwlink/?linkid=2092356) használatával a `timezoneOffset` param. Az érték `timezoneOffset` percek alatt az idő módosítása a pozitív vagy negatív szám lehet.  

|Param|Érték|
|--|--|
|`timezoneOffset`|pozitív vagy negatív szám, percek alatt|

### <a name="daylight-savings-example"></a>Nyári időszámítás – megtakarítási példa
Ha a nyári időszámításhoz módosíthatja a visszaadott előre összeállított datetimeV2 van szüksége, használja a `timezoneOffset` lekérdezési karakterlánc paraméterrel a +/-érték percben a [végpont](https://go.microsoft.com/fwlink/?linkid=2092356) lekérdezés.

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 előrejelzési végpont kérése](#tab/V2)

Adja hozzá a 60 perc: 

https://{Region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=Turn LED? **timezoneOffset = 60**& részletes = {logikai} & helyesírás-ellenőrzés = {logikai} & átmeneti = {logikai} & bing – helyesírás-ellenőrzés-subscription-key = {string} nap & ló = {logikai}

60 perc eltávolítása: 

https://{Region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=Turn LED? **timezoneOffset = 60**& részletes = {logikai} & helyesírás-ellenőrzés = {logikai} & átmeneti = {logikai} & bing – helyesírás-ellenőrzés-subscription-key = {string} nap & ló = {logikai}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 előrejelzési végpont kérése](#tab/V3)

Adja hozzá a 60 perc:

https://{Region}. API. kognitív. microsoft. com/Luis/v 3.0 – Preview/apps/{appId}/Slots/Production/Tippelje? Query = a fények bekapcsolása? **timezoneOffset = 60**& helyesírás = {boolean} & Bing-Spell-Check-előfizetés-Key = {string} & log = {Boolean}

60 perc eltávolítása: 

https://{Region}. API. kognitív. microsoft. com/Luis/v 3.0 – Preview/apps/{appId}/Slots/Production/Tippelje? Query = a fények bekapcsolása? **timezoneOffset =-60**& helyesírás = {boolean} & Bing-Spell-Check-előfizetés-Key = {string} & log = {Boolean}

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#-kód megfelelő timezoneOffset értékét határozza meg
Az alábbi C#-kódot használ a [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) osztályának [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) meghatározni a megfelelő módszer `timezoneOffset` alapján a rendszer pontos ideje:

```CSharp
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
> [Ebben az oktatóanyagban a helyesírási hibák](luis-tutorial-bing-spellcheck.md)
