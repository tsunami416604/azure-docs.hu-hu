---
title: Példák a Translator Text API szótárra
titleSuffix: Azure Cognitive Services
description: Használja a Translator Text API szótár példákat.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: 419e11862b2c584686922cfc8d1db72ee4751a03
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934035"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3.0: Szótár – példák

Olyan példákat tartalmaz, amelyek bemutatják, hogyan használják a szótárban található kifejezéseket a kontextusban. Ez a művelet a [szótárbeli kereséssel](./v3-0-dictionary-lookup.md)párhuzamosan használatos.

## <a name="request-url"></a>Kérés URL-címe

`POST` Kérelem küldése a következőnek:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>A kérés paraméterei

A lekérdezési karakterláncon átadott kérési paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>api-version</td>
    <td>*Kötelező paraméter*.<br/>Az ügyfél által kért API-verzió. Az értéknek `3.0`a számnak kell lennie.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Kötelező paraméter*.<br/>Megadja a bemeneti szöveg nyelvét. A forrás nyelvének a `dictionary` hatókörben szereplő [támogatott nyelvek](./v3-0-languages.md) egyikének kell lennie.</td>
  </tr>
  <tr>
    <td>to</td>
    <td>*Kötelező paraméter*.<br/>Megadja a kimeneti szöveg nyelvét. A célként megadott nyelvnek a `dictionary` hatókörben szereplő [támogatott nyelvek](./v3-0-languages.md) egyikének kell lennie.</td>
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
    <td>*Választható*.<br/>Ügyfél által generált GUID a kérelem egyedi azonosításához. Kihagyhatja ezt a fejlécet, ha a lekérdezési karakterláncban szerepel a nyomkövetési azonosító a `ClientTraceId`nevű lekérdezési paraméter használatával.</td>
  </tr>
</table> 

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömb elem egy JSON-objektum, amely a következő tulajdonságokkal rendelkezik:

  * `Text`: A keresett kifejezést megadó karakterlánc. Ennek egy `normalizedText` mezőnek kell lennie egy korábbi [szótárbeli keresési](./v3-0-dictionary-lookup.md) kérelem visszafordításakor. A `normalizedSource` mező értéke is lehet.

  * `Translation`: A [szótár keresési](./v3-0-dictionary-lookup.md) művelete által korábban visszaadott lefordított szöveget megadó karakterlánc. Ennek a `normalizedTarget` [szótár keresési](./v3-0-dictionary-lookup.md) válasza mezőjében `translations` szereplő értéknek kell lennie. A szolgáltatás példákat ad vissza az adott forrás-cél Word-pair számára.

Példa:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

A következő korlátozások érvényesek:

* A tömb legfeljebb 10 elemet tartalmazhat.
* Egy tömb elemének szöveges értéke nem lehet hosszabb 100 karakternél, beleértve a szóközöket is.

## <a name="response-body"></a>Válasz törzse

A sikeres válasz egy JSON-tömb, amely egyetlen eredménnyel rendelkezik a bemeneti tömb minden karakterláncához. Az eredmény objektum a következő tulajdonságokat tartalmazza:

  * `normalizedSource`: Egy karakterlánc, amely a forrás kifejezés normalizált formáját adja. Ez általában azonos a kérelem törzsében lévő egyező lista index `Text` mezőjében szereplő értékkel.
    
  * `normalizedTarget`: Egy karakterlánc, amely a célként megadott kifejezés normalizált formáját adja. Ez általában azonos a kérelem törzsében lévő egyező lista index `Translation` mezőjében szereplő értékkel.
  
  * `examples`: A (forrás kifejezés, a célként megadott kifejezés) párokra vonatkozó példák listája. A lista minden eleme egy olyan objektum, amely a következő tulajdonságokkal rendelkezik:

    * `sourcePrefix`: A teljes példa megalkotása _előtt_ `sourceTerm` összefűzni kívánt karakterlánc. Ne adjon hozzá szóközt, mert már ott van. Ez az érték lehet üres karakterlánc.

    * `sourceTerm`: A tényleges kifejezéssel megegyező sztring. A rendszer hozzáadja `sourcePrefix` a karakterláncot `sourceSuffix` a és a teljes példa formájában. Az értéke el van különítve, így egy felhasználói felületen is megadható, például félkövérrel.

    * `sourceSuffix`: Az a karakterlánc, amelyet `sourceTerm` a rendszer az érték után ÖSSZEFŰZ a teljes példa formájában. Ne adjon hozzá szóközt, mert már ott van. Ez az érték lehet üres karakterlánc.

    * `targetPrefix`: A célhoz hasonló `sourcePrefix` karakterlánc.

    * `targetTerm`: A célhoz hasonló `sourceTerm` karakterlánc.

    * `targetSuffix`: A célhoz hasonló `sourceSuffix` karakterlánc.

    > [!NOTE]
    > Ha a szótárban nincsenek példák, a válasz 200 (ok), de a `examples` lista egy üres lista.

## <a name="examples"></a>Példák

Ez a példa bemutatja, hogyan lehet keresni példákat az angol kifejezésből `fly` és a spanyol fordításból `volar`álló párokra.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

A válasz törzse (az egyértelműség rövidítése) a következő:

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
