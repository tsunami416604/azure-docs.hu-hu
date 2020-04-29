---
title: Translator Text API észlelési módszer
titleSuffix: Azure Cognitive Services
description: Azonosítsa egy szöveg nyelvét az Azure Cognitive Services Translator Text API észlelési metódussal.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 370f3b14c12fc05f181d6497b7069bbf1cf3c9cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73837298"
---
# <a name="translator-text-api-30-detect"></a>Translator Text API 3,0: észlelés

Egy szöveg nyelvét azonosítja.

## <a name="request-url"></a>Kérés URL-címe

`POST` Kérelem küldése a következőnek:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Kérelmek paramétereinek megadása

A lekérdezési karakterláncon átadott kérési paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>api-verzió</td>
    <td>*Kötelező paraméter*.<br/>Az ügyfél által kért API-verzió. Az értéknek `3.0`a számnak kell lennie.</td>
  </tr>
</table> 

A kérelem fejlécei a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>Hitelesítési fejléc (ek)</td>
    <td><em>Kötelező kérelem fejléce</em><br/>Tekintse <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">meg a hitelesítés elérhető beállításait</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Kötelező kérelem fejléce*<br/>Megadja az adattartalom tartalomtípusát. A lehetséges értékek a `application/json`következők:.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Kötelező kérelem fejléce*<br/>A kérelem törzsének hossza</td>
  </tr>
  <tr>
    <td>X – ClientTraceId</td>
    <td>Nem *kötelező*.<br/>Ügyfél által generált GUID a kérelem egyedi azonosításához. Vegye figyelembe, hogy kihagyhatja ezt a fejlécet, ha a lekérdezési karakterláncban szerepel a nyomkövetési `ClientTraceId`azonosító a nevű lekérdezési paraméter használatával.</td>
  </tr>
</table> 

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömb elem egy nevű `Text`JSON-objektum. A `Text` nyelvfelismerés a tulajdonság értékére lesz alkalmazva. A mintául szolgáló kérelem törzse így néz ki:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 100 elemet tartalmazhat.
* Egy tömb elemének szöveges értéke nem lehet hosszabb 10 000 karakternél, beleértve a szóközöket is.
* A kérelemben szereplő teljes szöveg nem lehet hosszabb 50 000 karakternél, beleértve a szóközöket is.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely egyetlen eredménnyel rendelkezik a bemeneti tömb minden karakterláncához. Az eredmény objektum a következő tulajdonságokat tartalmazza:

  * `language`: Az észlelt nyelv kódja.

  * `score`: Egy lebegőpontos érték, amely az eredmény megbízhatóságát jelzi. A pontszám nulla és egy, az alacsony pontszám pedig alacsony megbízhatóságot jelez.

  * `isTranslationSupported`: Logikai érték, amely akkor igaz, ha az észlelt nyelv a szöveges fordításhoz támogatott nyelvek egyike.

  * `isTransliterationSupported`: Egy logikai érték, amely akkor igaz, ha az észlelt nyelv az írásos nyelvek egyike.
  
  * `alternatives`: Más lehetséges nyelvek tömbje. A tömb minden eleme egy másik objektum, amely ugyanazokat a tulajdonságokat tartalmazza: `language`, `score` `isTranslationSupported` és `isTransliterationSupported`.

Példa JSON-válaszra:

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
    <td>X – kérelemazonosító</td>
    <td>A szolgáltatás által a kérelem azonosítására generált érték. Hibaelhárítási célokra szolgál.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Válasz-állapotkódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők: 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Leírás</th>
  <tr>
    <td>200</td>
    <td>Siker.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezési paraméterek egyike hiányzik vagy érvénytelen. Az újrapróbálkozás előtt javítsa a kérelmek paramétereit.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>A kérést nem lehetett hitelesíteni. Győződjön meg arról, hogy a hitelesítő adatok meg vannak adva és érvényesek.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A kérés nincs engedélyezve. Olvassa el a részletek hibaüzenetét. Ez gyakran azt jelzi, hogy a próbaverziós előfizetéssel biztosított összes ingyenes fordítás fel lett használva.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A kiszolgáló elutasította a kérelmet, mert az ügyfél túllépte a kérelmek korlátait.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse a következőt: a hiba dátuma és időpontja, a kérelem azonosítója `X-RequestId`a válasz fejlécből és az ügyfél `X-ClientTraceId`azonosítója a kérelem fejlécében.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Próbálja megismételni a kérelmet. Ha a hiba továbbra is fennáll, jelentse a következőt: a hiba dátuma és időpontja, a kérelem azonosítója `X-RequestId`a válasz fejlécből és az ügyfél `X-ClientTraceId`azonosítója a kérelem fejlécében.</td>
  </tr>
</table> 

Ha hiba történik, a kérés JSON-hibaüzenetet is ad vissza. A hibakód egy 6 számjegyből álló szám, amely a 3 számjegyből álló HTTP-állapotkódot kombinálja, majd egy 3 számjegyű számot, amely további kategorizálja a hibát. Gyakori hibakódok a [v3 Translator Text API hivatkozási oldalon](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)találhatók. 

## <a name="examples"></a>Példák

Az alábbi példa bemutatja, hogyan kérheti le a szöveg fordításához támogatott nyelveket.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```
