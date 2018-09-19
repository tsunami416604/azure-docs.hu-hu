---
title: Translator Text API BreakSentence metódus
titlesuffix: Azure Cognitive Services
description: A Translator Text API BreakSentence módszert használja.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 1202d49688bfd6aee50d1fa21c10423c071c6d92
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124983"
---
# <a name="translator-text-api-30-breaksentence"></a>Translator Text API 3.0: BreakSentence

Azonosítja a jelentésvásznon való elhelyezését egy adott szöveg határokon mondat helyett szerepel.

## <a name="request-url"></a>Lekérdezés URL-címe

Küldjön egy `POST` kérelmet:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>A kérés paraméterei

A kérelem lekérdezési karakterláncot az átadott paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>API-verzió</td>
    <td>*Kötelező lekérdezési paraméter*.<br/>Az ügyfél által kért API-verzió. Az érték lehet `3.0`.</td>
  </tr>
  <tr>
    <td>Nyelv</td>
    <td>*Nem kötelező lekérdezési paraméter*.<br/>A bemeneti szöveg nyelvének azonosítása nyelvcímke. Ha nincs megadva egy kódot, az automatikus nyelvfelismerést lépnek érvénybe.</td>
  </tr>
  <tr>
    <td>parancsfájl</td>
    <td>*Nem kötelező lekérdezési paraméter*.<br/>Parancsfájlkód, a parancsfájl a bemeneti szöveg által használt azonosító. Ha a parancsfájl nincs megadva, a nyelv alapértelmezett parancsfájl lesz.</td>
  </tr>
</table> 

Kérelemfejlécek a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>_Egy engedélyezési_<br/>_Fejléc_</td>
    <td>*Szükséges kérelem fejléce*.<br/>Lásd: [elérhető lehetőségek a hitelesítéshez](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Szükséges kérelem fejléce*.<br/>Megadja a hasznos tartalom típusát. Lehetséges értékek a következők: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Szükséges kérelem fejléce*.<br/>A kérelem törzsében mennyi.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Választható*.<br/>Egy ügyfél által létrehozott GUID egyedi azonosítására szolgál a kérelmet. Vegye figyelembe, hogy ez a fejléc kihagyhatja, ha a lekérdezési karakterláncban nevű lekérdezési paraméter használatával adja meg a nyomkövetési azonosító `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Kérelem törzse

A kérelem törzsében egy JSON-tömböt. Egyes tömbelemeken nevű karakterlánc tulajdonsággal rendelkező JSON-objektum `Text`. Mondat határok az értékét számítja ki a `Text` tulajdonság. A minta egy adott szöveg kérelem törzséhez hasonlóan néz ki:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 100 elemeket is rendelkezhet.
* A szöveges érték egy tömbelem nem lehet 10 000 karakternél, beleértve a szóközöket.
* A kérelem tartalmazza a teljes szöveg nem lehet 50 000 karakternél, beleértve a szóközöket.
* Ha a `language` lekérdezési paraméter meg van adva, akkor minden tömbelemek rekordsémáját ugyanazt a nyelvet kell lennie. Ellenkező esetben nyelvi automatikus észlelés függetlenül van alkalmazva minden egyes eleméhez.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömböt az egyes sztringek a bemeneti számtömbből egy eredmény. Egy objektumra a következő tulajdonságokat tartalmazza:

  * `sentLen`: Az elem a mondatok hosszának jelölő egész számok tömbje. A hossz a tömb mondatok számát, és az értékek a következők minden mondat hosszát. 

  * `detectedLanguage`: Egy objektum leíró nyelvben keresztül az alábbi tulajdonságokat:

     * `language`: A felismert nyelv kódja.

     * `score`: Egy jelző a bizalom, az eredmény a lebegőpontos értéket. A pontszám, nulla és a egy között, és a egy alacsony pontszámmal azt jelzi, hogy egy alacsony megbízhatósági.
     
    Vegye figyelembe, hogy a `detectedLanguage` tulajdonság csak akkor szerepel az eredményobjektum, nyelvi automatikus észlelés igénylésekor.

A következő egy példa JSON-választ:

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
    <td>X-RequestId:</td>
    <td>A kérelem azonosíthatja a szolgáltatás által létrehozott értéket. Hibaelhárítási célokra szolgál.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Állapotkódok

Az alábbi táblázat a lehetséges HTTP-állapotkódok, amely egy kérés adja vissza. 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Leírás</th>
  <tr>
    <td>200</td>
    <td>Siker.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezési paraméterek egyike hiányzik vagy érvénytelen. Javítsa ki a kérelem paramétereinek újrapróbálkozás előtt.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nem sikerült hitelesíteni a kérelmet. Ellenőrizze a használt hitelesítő adatok-e a megadott, és érvényes.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A kérelem nem engedélyezett. Tekintse meg a részletes hibaüzenetet. Ez gyakran azt jelenti, hogy a próba-előfizetéshez biztosított összes ingyenes fordítások használtak.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A hívó túl sok kérelmet küld.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse be a: dátum és idő a hiba, a válaszfejlécet a kérelem azonosító `X-RequestId`, és az ügyfél-azonosítója a fejléc `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Ismételje meg a kérelmet. Ha a hiba továbbra is fennáll, jelentse be a: dátum és idő a hiba, a válaszfejlécet a kérelem azonosító `X-RequestId`, és az ügyfél-azonosítója a fejléc `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Példák

Az alábbi példa bemutatja, hogyan szerezze be a mondat határokat egy egyetlen mondat helyett szerepel. A szolgáltatás automatikusan észlel a mondat nyelvét.

# <a name="curltabcurl"></a>[A curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

