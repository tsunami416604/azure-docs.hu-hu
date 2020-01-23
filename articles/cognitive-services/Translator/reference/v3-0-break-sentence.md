---
title: Translator Text API BreakSentence metódus
titleSuffix: Azure Cognitive Services
description: A Translator Text API BreakSentence metódus azonosítja a mondatok határait egy szövegben.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: 4c314148b8e1495a8b5a12c42d4989d13cdd6a08
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548118"
---
# <a name="translator-text-api-30-breaksentence"></a>Translator Text API 3,0: BreakSentence

Meghatározza a mondatok szegélyének elhelyezését egy szövegben.

## <a name="request-url"></a>Kérés URL-címe

`POST` kérelem küldése a következőnek:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Kérelmek paramétereinek megadása

A lekérdezési karakterláncon átadott kérési paraméterek a következők:

| Lekérdezési paraméter | Leírás |
| -------| ----------- |
| api-verzió <img width=200/>   | **Szükséges lekérdezési paraméter**.<br/>Az ügyfél által kért API-verzió. Az értéknek `3.0`nak kell lennie. |
| language | **Opcionális lekérdezési paraméter**.<br/>A szövegbeviteli szöveg nyelvét azonosító nyelvi címke. Ha nincs megadva kód, az automatikus nyelvfelismerés lesz alkalmazva. |
| szkriptet.    | **Opcionális lekérdezési paraméter**.<br/>A bemeneti szöveg által használt parancsfájlt azonosító szkript címkéje. Ha nincs megadva parancsfájl, a rendszer a nyelv alapértelmezett parancsfájlját fogja feltételezni.  | 

A kérelem fejlécei a következők:

| Fejlécek | Leírás |
| ------- | ----------- |
| Hitelesítési fejléc (ek) <img width=200/>  | **Kötelező kérelem fejléce**<br/>Tekintse <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">meg a hitelesítés elérhető beállításait</a>. |
| Tartalomtípus | **Kötelező kérelem fejléce**<br/>Megadja az adattartalom tartalomtípusát. A lehetséges értékek a következők: `application/json`. |
| Content-Length    | **Kötelező kérelem fejléce**<br/>A kérelem törzsének hossza  | 
| X – ClientTraceId   | **Választható**.<br/>Ügyfél által generált GUID a kérelem egyedi azonosításához. Vegye figyelembe, hogy kihagyhatja ezt a fejlécet, ha a lekérdezési karakterláncban szerepel a nyomkövetési azonosító egy `ClientTraceId`nevű lekérdezési paraméter használatával.  | 

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömb elem egy `Text`nevű karakterlánc-tulajdonságú JSON-objektum. A mondatok határainak kiszámítása a `Text` tulajdonság értékeként történik. A minta kérések törzse egy szöveggel így néz ki:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 100 elemet tartalmazhat.
* Egy tömb elemének szöveges értéke nem lehet hosszabb 10 000 karakternél, beleértve a szóközöket is.
* A kérelemben szereplő teljes szöveg nem lehet hosszabb 50 000 karakternél, beleértve a szóközöket is.
* Ha a `language` lekérdezési paraméter meg van adva, akkor az összes tömb elemnek ugyanabban a nyelven kell lennie. Ellenkező esetben a rendszer minden egyes tömb elemhez külön alkalmazza a nyelv automatikus észlelését.

## <a name="response-body"></a>Válasz törzse

A sikeres válasz egy JSON-tömb, amely egyetlen eredménnyel rendelkezik a bemeneti tömb minden karakterláncához. Az eredmény objektum a következő tulajdonságokat tartalmazza:

  * `sentLen`: egész számok tömbje, amely a mondatok hosszát jelképezi a szöveges elemben. A tömb hossza a mondatok száma, az értékek pedig az egyes mondatok hossza. 

  * `detectedLanguage`: az észlelt nyelvet leíró objektum a következő tulajdonságokkal:

     * `language`: az észlelt nyelv kódja.

     * `score`: egy lebegőpontos érték, amely az eredmény megbízhatóságát jelzi. A pontszám nulla és egy, az alacsony pontszám pedig alacsony megbízhatóságot jelez.
     
    Vegye figyelembe, hogy a `detectedLanguage` tulajdonság csak akkor jelenik meg az eredmény objektumban, ha a rendszer automatikus észlelést kér.

Példa JSON-válaszra:

```json
[
  {
    "sentLen": [ 13, 11, 22 ]
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
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse a következőt: a hiba dátuma és időpontja, a kérelem azonosítója a válasz fejlécének `X-RequestId`, valamint az ügyfél-azonosító a kérelem fejlécében `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Próbálja megismételni a kérelmet. Ha a hiba továbbra is fennáll, jelentse a következőt: a hiba dátuma és időpontja, a kérelem azonosítója a válasz fejlécének `X-RequestId`, valamint az ügyfél-azonosító a kérelem fejlécében `X-ClientTraceId`.</td>
  </tr>
</table> 

Ha hiba történik, a kérés JSON-hibaüzenetet is ad vissza. A hibakód egy 6 számjegyből álló szám, amely a 3 számjegyből álló HTTP-állapotkódot kombinálja, majd egy 3 számjegyű számot, amely további kategorizálja a hibát. Gyakori hibakódok a [v3 Translator Text API hivatkozási oldalon](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)találhatók. 

## <a name="examples"></a>Példák

Az alábbi példa bemutatja, hogyan szerezhet be mondatokat egy mondathoz. A szolgáltatás automatikusan észleli a mondat nyelvét.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

