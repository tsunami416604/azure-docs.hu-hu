---
title: A Microsoft Translator szöveg API BreakSentence metódus |} Microsoft Docs
description: A Microsoft Translator szöveg API BreakSentence módszert.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 8ce6644d21b397ea0e7f2e71e3c3a5a96638eec5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349271"
---
# <a name="text-api-30-breaksentence"></a>Szöveg API 3.0: BreakSentence

Azonosítja a mondatok határokhoz szöveg elhelyezkedését.

## <a name="request-url"></a>Lekérdezés URL-címe

Küldjön egy `POST` elküldeni a kérelmet:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>A kérés paraméterei

A lekérdezési karakterlánc átadja a kérelemben szereplő paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>API-verzió</td>
    <td>*Lekérdezési paraméter szükséges*.<br/>Az API-t, az ügyfél által kért verzióját. Az értéknek kell lennie `3.0`.</td>
  </tr>
  <tr>
    <td>nyelv</td>
    <td>*Nem kötelező lekérdezési paraméter*.<br/>A bemeneti szöveg nyelvi azonosító nyelvi címke. Ha kódja nincs megadva, a rendszer alkalmazza nyelv automatikus észlelése.</td>
  </tr>
  <tr>
    <td>parancsfájl</td>
    <td>*Nem kötelező lekérdezési paraméter*.<br/>Parancsfájlkód, a parancsfájl a bemeneti szöveg által használt azonosító. Ha a parancsfájl nincs megadva, a rendszer feltételezi a alapértelmezett parancsfájl nyelv.</td>
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

A kérelem törzse egy JSON-tömb. Minden tömbelem egy JSON-objektum nevű karakterlánc tulajdonsággal `Text`. Mondat határok vannak kiszámítva értékének a `Text` tulajdonság. A szöveg egy részét egy minta kérelemtörzset hasonlóan néz ki:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

A következő korlátozások vonatkoznak:

* A tömb legfeljebb 100 elemek szerepelhetnek.
* Egy tömbelem szöveges érték nem lehet 10 000 karakternél, szóközöket is beleértve.
* A kérelem tartalmazza a teljes szöveg nem lehet hosszabb 50 000 karaktereket, szóközöket is beleértve.
* Ha a `language` lekérdezési paraméter meg van adva, az összes tömbelemek nyelvével azonos nyelven kell lennie. Ellenkező esetben nyelvi automatikus észlelés alkalmazzák minden tömbelem egymástól függetlenül.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb minden egyes karakterláncot kell megadnia a bemeneti tömb egy eredménnyel:. Egy eredményobjektum tartalmazza a következő tulajdonságokkal:

  * `sentLen`: Az elem a mondatok a hosszának jelző egész számok egy tömbjét. A tömb hossza mondatok számát, és az értékek a következők minden mondat hosszát. 

  * `detectedLanguage`: Az objektum leírására, hogy az észlelt a következő tulajdonságok nyelv szerint:

     * `language`: Az észlelt nyelvi kódot.

     * `score`: Egy a lebegőpontos érték, amely az eredményben az vetett bizalmat. A pontszám nulla és között és alacsony pontszámmal egy kis abban, hogy jelzi.
     
    Vegye figyelembe, hogy a `detectedLanguage` tulajdonság csak akkor szerepel a eredményobjektum, ha van szükség a nyelvi beállítások automatikus észlelése.

Egy JSON-válasz példája:

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

A következő példa bemutatja, hogyan mondat határokat egyetlen mondatban az beszerzése. A szolgáltatás automatikusan felismeri a mondatok nyelvét.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

