---
title: A Microsoft Translator szöveg API Transliterate metódus |} Microsoft Docs
description: A Microsoft Translator szöveg API Transliterate módszer használatát.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: fdd6fa9236f0c02685198b6de3228c444993dad6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349263"
---
# <a name="text-api-30-transliterate"></a>Szöveg API 3.0: Transliterate

Egy nyelven írt szöveg konvertál egy parancsfájl egy másik parancsprogramra.

## <a name="request-url"></a>Lekérdezés URL-címe

Küldjön egy `POST` elküldeni a kérelmet:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>A kérés paraméterei

A lekérdezési karakterlánc átadja a kérelemben szereplő paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>API-verzió</td>
    <td>*A paraméter kötelező*.<br/>Az API-t, az ügyfél által kért verzióját. Az értéknek kell lennie `3.0`.</td>
  </tr>
  <tr>
    <td>nyelv</td>
    <td>*A paraméter kötelező*.<br/>Meghatározza a szöveg átalakítása egy parancsfájl egy másik nyelvét. Lehetséges nyelven jelennek meg a `transliteration` hatókör a szolgáltatás lekérdezésével kapott a [támogatott nyelv](.\v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*A paraméter kötelező*.<br/>A bemeneti szöveg által használt parancsfájl megadása Keresési [támogatott nyelv](.\v3-0-languages.md) használatával a `transliteration` hatókörben található bemeneti parancsfájlok érhető el a kiválasztott nyelven.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*A paraméter kötelező*.<br/>Adja meg a kimeneti parancsfájlt. Keresési [támogatott nyelv](.\v3-0-languages.md) használatával a `transliteration` hatókörben található kimeneti parancsfájlok érhető el a kijelölt kombinációjának beviteli nyelv és a bemeneti parancsfájl.</td>
  </tr>
</table> 

Kérelemfejléc a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>_Egy engedélyezési_<br/>_Fejléc_</td>
    <td>*Szükséges fejléc*.<br/>Lásd: [elérhető lehetőségek a hitelesítéshez](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Szükséges fejléc*.<br/>Adja meg a tartalom a tartalom. Lehetséges értékek a következők: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Szükséges fejléc*.<br/>A kérelem törzsében hosszát.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Választható*.<br/>Ügyfél által létrehozott GUID-azonosító egyedi módon azonosítja a kérelmet. Vegye figyelembe, hogy ezt a fejlécet kihagyhatja, ha a lekérdezési karakterláncban nevű lekérdezési paraméter használatával adja meg a nyomkövetési azonosító `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Kérelem törzse

A kérelem törzse egy JSON-tömb. Minden tömbelem egy JSON-objektum nevű karakterlánc tulajdonsággal `Text`, amely alakítandó karakterláncot jelenti.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

A következő korlátozások vonatkoznak:

* A tömb lehet legfeljebb 10 elemet.
* A tömb elem szöveges érték nagyobb, mint 1000 karaktereket, szóközöket is beleértve.
* A kérelem tartalmazza a teljes szöveg legfeljebb 5000 karaktereket, szóközöket is beleértve.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb egyes elemei a bemeneti tömb egy eredményt a rendszer. Egy eredményobjektum tartalmazza a következő tulajdonságokkal:

  * `text`: Egy karakterlánc, amely a bemeneti karakterlánc átalakításakor a kimeneti parancsfájlt eredménye.
  
  * `script`: A kimenet használt parancsfájl megadása egy karakterlánc.

Egy JSON-válasz példája:

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
    <td>X-kérelemazonosító</td>
    <td>A szolgáltatás azonosítására a kérelem által generált érték. Hibaelhárítási célokra szolgál.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Állapotkódok

Az alábbiakban a lehetséges HTTP-állapotkódok, egy kérelem ad vissza. 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Leírás</th>
  <tr>
    <td>200</td>
    <td>Siker.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezés-paraméterek egyike hiányzik vagy érvénytelen. A kérelemben szereplő paraméterek megkísérlése előtt javítsa ki.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nem sikerült hitelesíteni a kérelmet. Ellenőrizze, hogy hitelesítő adatok megadott és érvényes.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A kérelem nem engedélyezett. Tekintse meg a részleteket hibaüzenetet. Ez gyakran azt jelenti, hogy egy próba-előfizetést megadott összes szabad fordítások használtak.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A hívó túl sok kérelmet küld.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a probléma továbbra is fennáll, jelentse a: dátum és idő, a hiba, a válaszfejléc azonosítóját `X-RequestId`, és a kérelem fejlécében ügyfél-azonosítókra `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló ideiglenesen nem érhető el. Próbálkozzon újra a kéréssel. Ha a probléma továbbra is fennáll, jelentse a: dátum és idő, a hiba, a válaszfejléc azonosítóját `X-RequestId`, és a kérelem fejlécében ügyfél-azonosítókra `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Példák

A következő példa bemutatja, hogyan japán Romanized két japán karakterláncok konvertálhatók.

# <a name="curltabcurl"></a>[curl](#tab/curl)

Ebben a példában a kérelem JSON-adattartalmat:

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Ha a cUrl egy parancssori ablakot, és nem támogatja a Unicode-karaktereket használ, a következő JSON-adattartalmat szükség, és mentse fájlba `request.txt`. Ügyeljen arra, hogy a fájl mentése `UTF-8` kódolást.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
