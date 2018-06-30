---
title: A LUIS - Azure módosítása – fogalmak megismerése |} Microsoft Docs
description: Ismerje meg, hogyan adatok előrejelzéseket a nyelvi ismertetése (LUIS) előtt módosíthatók
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/26/2018
ms.author: v-geberr
ms.openlocfilehash: ab9b9fe5c34c334fd65971f9040c8e43467aa242
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112150"
---
# <a name="data-alterations"></a>Adatok változások
LUIS segítségével kezelheti a utterance előtt vagy közben az előrejelzés módszereket biztosítja. 

## <a name="correct-spelling-errors-in-utterance"></a>Megfelelő helyesírási hibáinak utterance
LUIS használ [Bing helyesírás ellenőrizze API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) a utterance helyesírási hibáinak javítása. LUIS kell a szolgáltatáshoz tartozó kulcs. A kulcs létrehozásához, és vegye fel a kulcs a lekérdezési karakterlánc paraméterként a [végpont](https://aka.ms/luis-endpoint-apis). 

Kijavíthatja a hibát nem mutatja a **teszt** által panel [a kulcs megadása](interactive-test.md#view-bing-spell-check-corrections-in-test-panel). A kulcs a böngészőben a teszt panel munkamenet változóként maradnak. A teszt panel minden böngésző-munkamenet azt szeretné, hogy helyesen adta-e javítani a kulcs hozzáadása. 

A teszt panelen, és a végpont rétegbe felé a kulcs használatát a [kulcshasználat](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) kvótát. LUIS valósítja meg a szöveg hosszúsága korlátai Bing helyesírás-ellenőrzés. 

A végponthoz két paraméterei helyesírás korrekciók működéséhez szükséges:

|Param|Érték|
|--|--|
|`spellCheck`|logikai|
|`bing-spell-check-subscription-key`|[Bing helyesírás ellenőrizze API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) végpontkulcs|

Ha [Bing helyesírás ellenőrizze API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) észleli a hibát, az eredeti utterance és a javított utterance a rendszer adja vissza előrejelzéseket együtt a végpont.

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
 
## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Előre elkészített datetimeV2 entitás időzónájának módosítása
Amikor egy LUIS alkalmazást használja az előre elkészített datetimeV2 entitást, datetime értéket az előrejelzés válaszként adhatók vissza. A kérelem az időzóna segítségével határozza meg a helyes dátum és idő való visszatéréshez. Ha a kérelem érkezik a bot vagy egy másik központi alkalmazás LUIS elérése előtt, javítsa ki az időzóna LUIS használja. 

### <a name="endpoint-querystring-parameter"></a>Végpont lekérdezési karakterlánc paraméter
Az időzóna kijavításáig a felhasználó időzónája való hozzáadásával a [végpont](https://aka.ms/luis-endpoint-apis) használatával a `timezoneOffset` param. Értékének `timezoneOffset` pozitív vagy negatív szám kell megváltoztatni az idő percben.  

|Param|Érték|
|--|--|
|`timezoneOffset`|pozitív vagy negatív szám, percben|

### <a name="daylight-savings-example"></a>Nyári megtakarítások – példa
Ha úgy, hogy a nyári időszámításhoz visszaadott előre elkészített datetimeV2 van szüksége, használja a `timezoneOffset` lekérdezési karakterlánc paramétert egy érték percben +/-a [végpont](https://aka.ms/luis-endpoint-apis) lekérdezés.

60 perc hozzáadása: 

https://{Region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=Turn LED? **timezoneOffset = 60**& részletes = {logikai} & a helyesírás-ellenőrzési = {logikai} & átmeneti = {logikai} & bing helyesírás-ellenőrzés-előfizetés-kulcs = {karakterlánc} & jelentkezzen = {logikai}

60 perc eltávolítása: 

https://{Region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=Turn LED? **timezoneOffset = 60**& részletes = {logikai} & a helyesírás-ellenőrzési = {logikai} & átmeneti = {logikai} & bing helyesírás-ellenőrzés-előfizetés-kulcs = {karakterlánc} & jelentkezzen = {logikai}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#-kódban meghatározza, hogy megfelelő értéket a timezoneOffset
Az alábbi C# használ a [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo?view=netframework-4.7.1) osztályának [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid?view=netframework-4.7.1#examples) módszerrel határozható meg a megfelelő `timezoneOffset` alapján a rendszer pontos ideje:

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
> [Az oktatóanyaghoz helyesen hibák](luis-tutorial-bing-spellcheck.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions