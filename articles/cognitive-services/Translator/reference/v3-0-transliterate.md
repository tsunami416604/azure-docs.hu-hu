---
title: Translator Text API Átbetűzés metódus
titleSuffix: Azure Cognitive Services
description: Az egyik parancsfájlból egy másik parancsfájlba alakítja át a szöveget az Translator Text API Átbetűzés metódussal.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: e6bb1541b2b668796b352bebc68d59b4ade143e3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73837279"
---
# <a name="translator-text-api-30-transliterate"></a>Translator Text API 3,0: Átbetűzés

A szöveget egy adott nyelven alakítja át egy másik parancsfájlba.

## <a name="request-url"></a>Kérés URL-címe

`POST` Kérelem küldése a következőnek:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
  <tr>
    <td>language</td>
    <td>*Kötelező paraméter*.<br/>Megadja az egyik parancsfájlból a másikba konvertálandó szöveg nyelvét. A lehetséges nyelvek a szolgáltatásnak `transliteration` a [támogatott nyelvekre](./v3-0-languages.md)való lekérdezésével a kapott hatókörben vannak felsorolva.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Kötelező paraméter*.<br/>Megadja a bemeneti szöveg által használt parancsfájlt. A `transliteration` hatókör használatával kereshet [támogatott nyelveket](./v3-0-languages.md) a kiválasztott nyelvhez elérhető bemeneti parancsfájlok kereséséhez.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Kötelező paraméter*.<br/>Megadja a kimeneti parancsfájlt. A hatókör használatával megkeresheti `transliteration` a [támogatott nyelveket](./v3-0-languages.md) , hogy megtalálja a bemeneti nyelv és a bemeneti parancsfájl kiválasztott kombinációjával elérhető kimeneti parancsfájlokat.</td>
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

A kérelem törzse egy JSON-tömb. Minden tömb elem egy nevű `Text`JSON-objektum, amely az átalakítandó karakterláncot jelöli.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 10 elemet tartalmazhat.
* Egy tömb elemének szöveges értéke nem lehet hosszabb 1 000 karakternél, beleértve a szóközöket is.
* A kérelemben szereplő teljes szöveg nem lehet hosszabb 5 000 karakternél, beleértve a szóközöket is.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely egyetlen eredménnyel rendelkezik a bemeneti tömb minden eleméhez. Az eredmény objektum a következő tulajdonságokat tartalmazza:

  * `text`: Egy karakterlánc, amely a bemeneti karakterlánc kimeneti parancsfájlba való átalakításának eredményét eredményezi.
  
  * `script`: A kimenetben használt parancsfájlt megadó karakterlánc.

Példa JSON-válaszra:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

Az alábbi példa bemutatja, hogyan alakíthat át két japán karakterláncot a római Japánra.

A kérelem JSON-adattartalma a következő példában:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Ha a fürtöket olyan parancssori ablakban használja, amely nem támogatja a Unicode-karaktereket, hajtsa végre a következő JSON-adattartalmat, és mentse egy `request.txt`nevű fájlba. Ügyeljen arra, hogy a fájlt `UTF-8` kódolással mentse.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```
