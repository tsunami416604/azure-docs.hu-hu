---
title: Adatok módosítása
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ismerje meg, hogyan adatokat is módosítható előtt előrejelzések a Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 9e0d5970ff07c5fd2ef35033a9ccb52feccf3ecb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219345"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Az ALTER utterance (kifejezés) adatok előtt vagy közben előrejelzése
A LUIS segítségével kezelheti az utterance (kifejezés) előtt vagy közben az előrejelzési módszert biztosít. Ezek közé tartozik a helyesírás-ellenőrzés és kijavítása prebuild datetimeV2 időzóna problémák kijavítása. 

## <a name="correct-spelling-errors-in-utterance"></a>Helyesírási hibák az utterance (kifejezés)
Használja a LUIS [a Bing Spell ellenőrzés API 7-es](https://azure.microsoft.com/services/cognitive-services/spell-check/) az utterance (kifejezés) a helyesírási hibákat. A LUIS kell a szolgáltatáshoz tartozó kulcsot. Hozza létre a kulcsot, majd adja hozzá a kulcsot a lekérdezési karakterlánc paraméterként a [végpont](https://aka.ms/luis-endpoint-apis). 

Kijavíthatja a helyesírási hibákat is a **teszt** panel szerint [a kulcs megadása](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). A kulcs, a teszt panel a böngészőben egy munkamenet-változó tárolja. Adja hozzá a kulcsot minden böngésző-munkamenetben helyesírás-ellenőrzés javítani szeretné a teszt panelt. 

A teszt panel és a végpont felszámítja a kulcs használatát a [kulcshasználat](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) kvótát. LUIS valósítja meg a Bing helyesírás-ellenőrzési korlátozások a szöveg hossza. 

A végpont két paraméterei a helyesírási javításokkal működéséhez szükségesek:

|Param|Érték|
|--|--|
|`spellCheck`|logikai|
|`bing-spell-check-subscription-key`|[A Bing Spell ellenőrzés API 7-es](https://azure.microsoft.com/services/cognitive-services/spell-check/) végponti kulcs|

Amikor [a Bing Spell ellenőrzés API 7-es](https://azure.microsoft.com/services/cognitive-services/spell-check/) észleli a hiba, az eredeti utterance (kifejezés) a javított utterance (kifejezés) adja vissza és előrejelzések együtt a végpontról.

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
 
### <a name="whitelist-words"></a>Engedélyezési lista szavakat
A Bing spell check API-val a LUIS nem támogatja a fehér-szavak listáját a spell során figyelmen kívül ellenőrizze a módosításokat. Ha engedélyezési listára szavakat vagy betűszavakat van szüksége, dolgozza fel az ügyfélalkalmazásban, engedélyezési listáját, mielőtt elküldené az utterance (kifejezés) LUIS szándék előrejelzéshez utterance (kifejezés).

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Előre összeállított datetimeV2 entitás időzóna módosítása
A LUIS-alkalmazás az előre összeállított datetimeV2 entitást használja, amikor egy datetime értéket a előrejelzési válaszban adhatók vissza. A kérelem az időzóna visszaadandó megfelelő datetime meghatározására szolgál. Ha a kérelem érkezik egy robot vagy egy másik központi alkalmazás LUIS való elérése előtt, javítsa ki az időzóna, LUIS használja. 

### <a name="endpoint-querystring-parameter"></a>Végpont paramétert
Az időzóna nem kerül kijavításra, a felhasználó időzóna való hozzáadásával a [végpont](https://aka.ms/luis-endpoint-apis) használatával a `timezoneOffset` param. Az érték `timezoneOffset` percek alatt az idő módosítása a pozitív vagy negatív szám lehet.  

|Param|Érték|
|--|--|
|`timezoneOffset`|pozitív vagy negatív szám, percek alatt|

### <a name="daylight-savings-example"></a>Nyári időszámítás – megtakarítási példa
Ha a nyári időszámításhoz módosíthatja a visszaadott előre összeállított datetimeV2 van szüksége, használja a `timezoneOffset` lekérdezési karakterlánc paraméterrel a +/-érték percben a [végpont](https://aka.ms/luis-endpoint-apis) lekérdezés.

Adja hozzá a 60 perc: 

https://{Region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=Turn LED? **timezoneOffset = 60**& részletes = {logikai} & helyesírás-ellenőrzés = {logikai} & átmeneti = {logikai} & bing – helyesírás-ellenőrzés-subscription-key = {string} nap & ló = {logikai}

60 perc eltávolítása: 

https://{Region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=Turn LED? **timezoneOffset = 60**& részletes = {logikai} & helyesírás-ellenőrzés = {logikai} & átmeneti = {logikai} & bing – helyesírás-ellenőrzés-subscription-key = {string} nap & ló = {logikai}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#-kód megfelelő timezoneOffset értékét határozza meg
Az alábbi C#-kódot használ a [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo?view=netframework-4.7.1) osztályának [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid?view=netframework-4.7.1#examples) meghatározni a megfelelő módszer `timezoneOffset` alapján a rendszer pontos ideje:

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
