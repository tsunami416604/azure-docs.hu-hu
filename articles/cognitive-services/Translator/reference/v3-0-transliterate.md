---
title: Fordító szöveg API transzkitrát módszer
titleSuffix: Azure Cognitive Services
description: A Translator Text API Transliterate metódussal az egyik nyelven lévő szöveget konvertálhatja egy parancsfájlból egy másikba.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: e6bb1541b2b668796b352bebc68d59b4ade143e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837279"
---
# <a name="translator-text-api-30-transliterate"></a>Fordító szöveg API 3.0: Átírás

Az egyik nyelven lévő szöveget egyik parancsfájlból a másikba konvertálja.

## <a name="request-url"></a>Kérés URL-címe

Kérés `POST` küldése a következő knek:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
  <tr>
    <td>language</td>
    <td>*Kötelező paraméter*.<br/>Megadja a konvertálandó szöveg nyelvét az egyik parancsfájlból a másikba. A lehetséges nyelvek a `transliteration` szolgáltatás [támogatott nyelveinek](./v3-0-languages.md)lekérdezésével kapott hatókörben jelennek meg.</td>
  </tr>
  <tr>
    <td>inScript</td>
    <td>*Kötelező paraméter*.<br/>Megadja a bemeneti szöveg által használt parancsfájlt. Keresse meg a `transliteration` hatókört használó támogatott [nyelveket,](./v3-0-languages.md) és keresse meg a kiválasztott nyelvhez elérhető bemeneti parancsfájlokat.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Kötelező paraméter*.<br/>Megadja a kimeneti parancsfájlt. Keresse meg a `transliteration` hatókört használó [támogatott nyelveket,](./v3-0-languages.md) hogy megtalálja a bemeneti nyelv és a bemeneti parancsfájl kiválasztott kombinációjához elérhető kimeneti parancsfájlokat.</td>
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

A kérelem törzse egy JSON-tömb. Minden tömbelem egy JSON-objektum, `Text`amelynek neve karakterlánc-tulajdonság, amely a konvertálandó karakterláncot jelöli.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legbőlegelhető 10 elemet tartalmazhat.
* A tömbelem szöveges értéke szóközökkel együtt nem haladhatja meg az 1000 karaktert.
* A kérelemben szereplő teljes szöveg nem haladhatja meg az 5000 karaktert szóközökkel együtt.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely a bemeneti tömb minden eleméhez egy eredményt tartalmaz. Az eredményobjektum a következő tulajdonságokat tartalmazza:

  * `text`: Olyan karakterlánc, amely a bemeneti karakterlánc kimeneti parancsfájllá történő konvertálásának eredménye.
  
  * `script`: A kimenetben használt parancsfájlt meghatározó karakterlánc.

Egy példa JSON válasz:

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

A következő példa bemutatja, hogyan konvertálható két japán karakterlánc romanizált japán.

A json hasznos teher a kérelem ebben a példában:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Ha olyan parancssori ablakban használja a cURL-t, amely nem támogatja a Unicode karaktereket, `request.txt`vegye ki a következő JSON-hasznos adatra, és mentse egy fájlba, amelynek neve . Ügyeljen arra, hogy `UTF-8` a fájlt kódolással mentse.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```
