---
title: Translator Text API BreakSentence metódus
titleSuffix: Azure Cognitive Services
description: Használja a Translator Text API BreakSentence metódust.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 184677589b3aa777ec556215455f8018e0d71f3f
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934042"
---
# <a name="translator-text-api-30-breaksentence"></a>Translator Text API 3.0: BreakSentence

Meghatározza a mondatok szegélyének elhelyezését egy szövegben.

## <a name="request-url"></a>Kérés URL-címe

`POST` Kérelem küldése a következőnek:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>A kérés paraméterei

A lekérdezési karakterláncon átadott kérési paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>api-version</td>
    <td>*Szükséges lekérdezési paraméter*.<br/>Az ügyfél által kért API-verzió. Az értéknek `3.0`a számnak kell lennie.</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Opcionális lekérdezési paraméter*.<br/>A szövegbeviteli szöveg nyelvét azonosító nyelvi címke. Ha nincs megadva kód, az automatikus nyelvfelismerés lesz alkalmazva.</td>
  </tr>
  <tr>
    <td>script</td>
    <td>*Opcionális lekérdezési paraméter*.<br/>A bemeneti szöveg által használt parancsfájlt azonosító szkript címkéje. Ha nincs megadva parancsfájl, a rendszer a nyelv alapértelmezett parancsfájlját fogja feltételezni.</td>
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
    <td>X-ClientTraceId</td>
    <td>*Választható*.<br/>Ügyfél által generált GUID a kérelem egyedi azonosításához. Vegye figyelembe, hogy kihagyhatja ezt a fejlécet, ha a lekérdezési karakterláncban szerepel a nyomkövetési `ClientTraceId`azonosító a nevű lekérdezési paraméter használatával.</td>
  </tr>
</table> 

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömb elem egy nevű `Text`JSON-objektum. A `Text` rendszer kiszámítja a mondatok határait a tulajdonság értékeként. A minta kérések törzse egy szöveggel így néz ki:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

A következő korlátozások érvényesek:

* A tömb legfeljebb 100 elemet tartalmazhat.
* Egy tömb elemének szöveges értéke nem lehet hosszabb 10 000 karakternél, beleértve a szóközöket is.
* A kérelemben szereplő teljes szöveg nem lehet hosszabb 50 000 karakternél, beleértve a szóközöket is.
* Ha a `language` lekérdezési paraméter meg van adva, akkor az összes tömb elemnek ugyanabban a nyelven kell lennie. Ellenkező esetben a rendszer minden egyes tömb elemhez külön alkalmazza a nyelv automatikus észlelését.

## <a name="response-body"></a>Válasz törzse

A sikeres válasz egy JSON-tömb, amely egyetlen eredménnyel rendelkezik a bemeneti tömb minden karakterláncához. Az eredmény objektum a következő tulajdonságokat tartalmazza:

  * `sentLen`: Egész számok tömbje, amely a mondatok hosszát jelképezi a szöveges elemben. A tömb hossza a mondatok száma, az értékek pedig az egyes mondatok hossza. 

  * `detectedLanguage`: Az észlelt nyelvet leíró objektum a következő tulajdonságokkal:

     * `language`: Az észlelt nyelv kódja.

     * `score`: Egy lebegőpontos érték, amely az eredmény megbízhatóságát jelzi. A pontszám nulla és egy, az alacsony pontszám pedig alacsony megbízhatóságot jelez.
     
    Vegye figyelembe, `detectedLanguage` hogy a tulajdonság csak akkor jelenik meg az eredmény objektumban, ha a rendszer automatikus észlelést kér.

Példa JSON-válaszra:

```json
[
  {
    "sentenceLengths": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
  }
]
```

## <a name="response-headers"></a>Válaszfejlécek

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>X-RequestId</td>
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
    <td>Sikeres művelet.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezési paraméterek egyike hiányzik vagy érvénytelen. Az újrapróbálkozás előtt javítsa a kérelmek paramétereit.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nem sikerült hitelesíteni a kérelmet. Győződjön meg arról, hogy a hitelesítő adatok meg vannak adva és érvényesek.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A kérelem nem engedélyezett. Olvassa el a részletek hibaüzenetét. Ez gyakran azt jelzi, hogy a próbaverziós előfizetéssel biztosított összes ingyenes fordítás fel lett használva.</td>
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

Az alábbi példa bemutatja, hogyan szerezhet be mondatokat egy mondathoz. A szolgáltatás automatikusan észleli a mondat nyelvét.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

