---
title: Translator Text API Átbetűzés metódus
titlesuffix: Azure Cognitive Services
description: A Translator Text API Átbetűzés módszert használja.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: bafe2d92a6c0176f1bcd9e56b067b5bf90a36007
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55693955"
---
# <a name="translator-text-api-30-transliterate"></a>Translator Text API 3.0: Transliterate

Egy nyelvű szöveget egy parancsfájlból származó alakítja egy másik szkriptet.

## <a name="request-url"></a>Kérés URL-címe

Küldjön egy `POST` kérelmet:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>A kérés paraméterei

A kérelem lekérdezési karakterláncot az átadott paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>API-verzió</td>
    <td>*Kötelező paraméter*.<br/>Az ügyfél által kért API-verzió. Az érték lehet `3.0`.</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Kötelező paraméter*.<br/>Meghatározza a szöveg átalakítása egy parancsfájl egy másik nyelvét. Lehetséges nyelven szerepelnek a `transliteration` hatókör számára a szolgáltatás lekérdezésével kapott a [támogatott nyelvek](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Kötelező paraméter*.<br/>Megadja a parancsprogramot, a bemeneti szöveg által használt. Kereshet [támogatott nyelvek](./v3-0-languages.md) használatával a `transliteration` hatókörét, a bemeneti érhető el parancsprogram található a kiválasztott nyelven.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Kötelező paraméter*.<br/>Megadja a kimeneti parancsfájlt. Kereshet [támogatott nyelvek](./v3-0-languages.md) használatával a `transliteration` hatókörét, a kimeneti parancsfájlok érhető el a kiválasztott kombinációjának beviteli nyelv és a bemeneti parancsprogram található.</td>
  </tr>
</table> 

Kérelemfejlécek a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>_Egy engedélyezési_<br/>_header_</td>
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

## <a name="request-body"></a>A kérés törzse

A kérelem törzsében egy JSON-tömböt. Egyes tömbelemeken nevű karakterlánc tulajdonsággal rendelkező JSON-objektum `Text`, amelyek az átalakítandó sztring jelöli.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 10 elemet is rendelkezhet.
* A szöveges érték egy tömbelem legfeljebb 1000 karakter, beleértve a szóközöket.
* A kérelem tartalmazza a teljes szöveg nem lehet hosszabb 5000 karakternél, beleértve a szóközöket.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb egyes elemei a bemeneti számtömbből egy eredmény. Egy objektumra a következő tulajdonságokat tartalmazza:

  * `text`: Egy karakterlánc, amely a bemeneti karakterlánc átalakításával a kimeneti parancsfájlt.
  
  * `script`: A parancsfájl kimenetében használt megadó karakterlánc.

A következő egy példa JSON-választ:

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
    <td>X-RequestId</td>
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

Ha hiba történik, a kérelem is hiba JSON-választ adja vissza. A hibakód egy 6 jegyű számot csoportba foglalása 3 számjegyből HTTP-állapotkód további követ és 3-jegyű szám kategorizálása a hibát. Gyakori hibakódok találhatók a [v3 a Translator Text API referencialapja](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Példák

Az alábbi példa bemutatja, hogyan alakíthatja japán Romanized két japán karakterlánc.

# <a name="curltabcurl"></a>[curl](#tab/curl)

A JSON-adattartalom-ebben a példában a kérelmet:

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

A cURL egy parancssori ablakban, amely nem támogatja a Unicode-karaktereket használ, ha a következő JSON-adattartalom igénybe, és mentse fájlba `request.txt`. Ügyeljen arra, hogy mentse a fájlt a `UTF-8` kódolást.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
