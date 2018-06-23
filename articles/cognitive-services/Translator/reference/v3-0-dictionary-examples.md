---
title: A Microsoft Translator szöveg API szótár példák metódus |} Microsoft Docs
description: A Microsoft Translator szöveg API szótár példák módszert.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 9960f3be42090edaec1df935d70e4c1a0d25b691
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349270"
---
# <a name="text-api-30-dictionary-examples"></a>Szöveg API 3.0: Szótár példák

Példákat talál, amelyek megjelenítik a módját a szótárban kifejezések a környezetben. Ez a művelet területtel együtt használatos [szótár keresési](.\v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Lekérdezés URL-címe

Küldjön egy `POST` elküldeni a kérelmet:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
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
    <td>forrás:</td>
    <td>*A paraméter kötelező*.<br/>Meghatározza a bemeneti szöveg nyelvét. Az adatforrás nyelvi közül kell a [támogatott nyelv](.\v3-0-languages.md) szerepel a `dictionary` hatókör.</td>
  </tr>
  <tr>
    <td>erre:</td>
    <td>*A paraméter kötelező*.<br/>Meghatározza a kimeneti szöveg. A megadott nyelv közül kell a [támogatott nyelv](.\v3-0-languages.md) szerepel a `dictionary` hatókör.</td>
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
    <td>*Választható*.<br/>Ügyfél által létrehozott GUID-azonosító egyedi módon azonosítja a kérelmet. Kihagyhatja ezt a fejlécet, ha a lekérdezési karakterláncban nevű lekérdezési paraméter használatával adja meg a nyomkövetési azonosító `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Kérelem törzse

A kérelem törzse egy JSON-tömb. Minden tömbelem egy JSON-objektum a következő tulajdonságokkal:

  * `Text`: A karakterlánc a keresési kifejezés megadásával. Ez legyen a értékének egy `normalizedText` mezőre abban a korábbi biztonsági-fordítások [szótár keresési](.\v3-0-dictionary-lookup.md) kérelem. Értékének is lehet a `normalizedSource` mező.

  * `Translation`: A lefordított által korábban visszaadott szöveg egy olyan karakterláncot a [szótár keresési](.\v3-0-dictionary-lookup.md) műveletet. Ennek a számnak kell lennie a `normalizedTarget` mező mellett a `translations` listája a [szótár keresési](.\v3-0-dictionary-lookup.md) választ. A szolgáltatás visszatér a megadott forrás-cél word-pár példák.

Példa::

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

A következő korlátozások vonatkoznak:

* A tömb lehet legfeljebb 10 elemet.
* A szöveges érték, egy tömbelem legfeljebb 100 karakter lehet, beleértve a szóközöket.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb minden egyes karakterláncot kell megadnia a bemeneti tömb egy eredménnyel:. Egy eredményobjektum tartalmazza a következő tulajdonságokkal:

  * `normalizedSource`: Jogosultságot ad a forrás kifejezés normalizált formája egy karakterlánc. Általában ez értékének egyeznie kell a `Text` mezőt a kérelem törzsében a megfelelő listában indexnél.
    
  * `normalizedTarget`: A karakterlánc megadva a cél kifejezés normalizált formája. Általában ez értékének egyeznie kell a `Translation` mezőt a kérelem törzsében a megfelelő listában indexnél.
  
  * `examples`: A listáját (forrás kifejezés, cél kifejezés) Példák pár. A lista minden eleme egy objektum a következő tulajdonságokkal:

    * `sourcePrefix`: A karakterlánc összefűzésére _előtt_ értékének `sourceTerm` teljes példa kialakításához. Nem adható hozzá, szóközzel, mert már telepítve van, amikor meg kell. Ez az érték lehet üres karakterlánc.

    * `sourceTerm`: A karakterlánc értéke a tényleges kifejezést keresi. A karakterlánc hozzá rendelkező `sourcePrefix` és `sourceSuffix` teljes példa kialakításához. Az értékét, akkor is jelzést felhasználói felületén, például félkövér stílus különül el azt.

    * `sourceSuffix`: A karakterlánc összefűzésére _után_ értékének `sourceTerm` teljes példa kialakításához. Nem adható hozzá, szóközzel, mert már telepítve van, amikor meg kell. Ez az érték lehet üres karakterlánc.

    * `targetPrefix`: A karakterlánc hasonló `sourcePrefix` , de a cél.

    * `targetTerm`: A karakterlánc hasonló `sourceTerm` , de a cél.

    * `targetSuffix`: A karakterlánc hasonló `sourceSuffix` , de a cél.

    > [!NOTE]
    > Ha a szótár nem példákat is tartalmaz, a rendszer a választ 200 (OK), de a `examples` lista üres lista lesz.

## <a name="examples"></a>Példák

Ez a példa bemutatja, hogyan példák a áll angol kifejezés pár használva `fly` és a spanyol fordítási `volar`.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

Az adott válasz törzsének (jobb érthetőség kedvéért bizonyos rövidítése) a következő:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
