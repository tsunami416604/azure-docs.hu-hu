---
title: Fordító szöveg API-észlelési módszer
titleSuffix: Azure Cognitive Services
description: Azonosítsa egy szövegnyelvét az Azure Cognitive Services Translator Text API Detect metódussal.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 370f3b14c12fc05f181d6497b7069bbf1cf3c9cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837298"
---
# <a name="translator-text-api-30-detect"></a>Fordító szöveg API 3.0: Észlelés

Egy szövegnyelv nyelvét azonosítja.

## <a name="request-url"></a>Kérés URL-címe

Kérés `POST` küldése a következő knek:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Kérelem paraméterei

A lekérdezési karakterláncon átadott kérelemparaméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>api-verzió</td>
    <td>*Kötelező paraméter*.<br/>Az ügyfél által kért API verziója. Az értéknek kell lennie. `3.0`</td>
  </tr>
</table> 

A kérelemfejlécek a következőket tartalmazzák:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>Hitelesítési fejléc(ek)</td>
    <td><em>Kötelező kérelemfejléc</em>.<br/>Lásd: <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">A hitelesítéshez rendelkezésre álló lehetőségek.</a></td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Kötelező kérelemfejléc*.<br/>Megadja a hasznos adat tartalom típusát. Lehetséges értékek: `application/json`. .</td>
  </tr>
  <tr>
    <td>Tartalom hossza</td>
    <td>*Kötelező kérelemfejléc*.<br/>A kérelem törzsének hossza.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Nem kötelező*.<br/>Az ügyfél által létrehozott GUID a kérelem egyedi azonosítására. Ne feledje, hogy a fejlécet kihagyhatja, ha a nyomkövetési `ClientTraceId`azonosítót a lekérdezési karakterláncba a .</td>
  </tr>
</table> 

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömbelem egy JSON-objektum, `Text`amelynek karakterlánc-tulajdonsága van. A nyelvfelismerés a `Text` tulajdonság értékére vonatkozik. A mintakérelem törzse így néz ki:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

Az alábbi korlátozások érvényesek:

* A tömb legbőlegelhető 100 elemet tartalmazhat.
* A tömbelem szöveges értéke szóközökkel együtt nem haladhatja meg a 10 000 karaktert.
* A kérelemben szereplő teljes szöveg nem haladhatja meg az 50 000 karaktert szóközökkel együtt.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely a bemeneti tömb minden egyes karakterláncához egy-egy eredményt ad. Az eredményobjektum a következő tulajdonságokat tartalmazza:

  * `language`: Az észlelt nyelv kódja.

  * `score`: Az eredmény megbízhatóságát jelző lebegőérték. A pontszám nulla és egy között van, és az alacsony pontszám alacsony magabiztosságot jelez.

  * `isTranslationSupported`: Olyan logikai érték, amely akkor igaz, ha az észlelt nyelv a szövegfordításhoz támogatott nyelvek egyike.

  * `isTransliterationSupported`: Az átíráshoz támogatott nyelvek egyike, ha az észlelt nyelv akkor igaz, ha az észlelt nyelv igaz.
  
  * `alternatives`: Egy sor más lehetséges nyelvek. A tömb minden eleme egy másik objektum, amelynek `score` `isTranslationSupported` a `isTransliterationSupported`fent felsorolt tulajdonságai megegyeznek: `language`, , és .

Egy példa JSON válasz:

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>Válaszfejlécek

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>X-RequestId azonosító</td>
    <td>A szolgáltatás által a kérelem azonosítására létrehozott érték. Hibaelhárítási célokra használják.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Válasz állapotkódjai

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők. 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Leírás</th>
  <tr>
    <td>200</td>
    <td>Siker.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezési paraméterek egyike hiányzik vagy érvénytelen. Az újrapróbálkozás előtt javítsa ki a kérelem paramétereit.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>A kérés t nem lehetett hitelesíteni. Ellenőrizze, hogy a hitelesítő adatok meg vannak-e adva és érvényesek-e.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A kérés nem engedélyezett. Ellenőrizze a részleteket hibaüzenetet. Ez gyakran azt jelzi, hogy a próba-előfizetéssel rendelkező összes ingyenes fordítást felhasználták.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A kiszolgáló elutasította a kérést, mert az ügyfél túllépte a kérelemkorlátot.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse azt a következőkkel: `X-RequestId`a hiba dátuma `X-ClientTraceId`és időpontja, a válaszfejlécből származó kérelemazonosító és az ügyfélazonosító a kérelemfejlécből.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Próbálkozzon újra a kéréssel. Ha a hiba továbbra is fennáll, jelentse azt a következőkkel: `X-RequestId`a hiba dátuma `X-ClientTraceId`és időpontja, a válaszfejlécből származó kérelemazonosító és az ügyfélazonosító a kérelemfejlécből.</td>
  </tr>
</table> 

Ha hiba történik, a kérelem json-hibaválaszt is ad vissza. A hibakód egy 6 jegyű szám, amely a 3 jegyű HTTP-állapotkódot, majd egy 3 jegyű számot követ a hiba további kategorizálása érdekében. A gyakori hibakódok a [v3 Translator Text API referenciaoldalán](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)találhatók. 

## <a name="examples"></a>Példák

A következő példa bemutatja, hogyan lehet beolvasni a szövegfordításhoz támogatott nyelveket.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```
