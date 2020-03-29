---
title: Fordítószöveg API szótár példák módszer
titleSuffix: Azure Cognitive Services
description: A Translator Text API Dictionary Examples módszer példákat tartalmaz, amelyek bemutatják, hogyan kifejezések a szótárban használják környezetben.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: b3551a8df19e47178c7bacd9218cfa60b66d81f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548067"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Fordító szöveg API 3.0: Szótár példák

Példák, amelyek bemutatják, hogyan használják a kifejezéseket a szótárban a környezetben. Ez a művelet a [Szótár-keresmel](./v3-0-dictionary-lookup.md)párhuzamosan használatos.

## <a name="request-url"></a>Kérés URL-címe

Kérés `POST` küldése a következő knek:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Kérelem paraméterei

A lekérdezési karakterláncon átadott kérelemparaméterek a következők:

| Lekérdezési paraméter | Leírás |
| --------- | ----------- |
| api-verzió <img width=200/> | **Kötelező paraméter**.<br/>Az ügyfél által kért API verziója. Az értéknek kell lennie. `3.0` |
| honnan | **Kötelező paraméter**.<br/>Megadja a bemeneti szöveg nyelvét. A forrásnyelvnek a [supported languages](./v3-0-languages.md) `dictionary` hatókörben szereplő támogatott nyelvek egyikének kell lennie. |
| erre: | **Kötelező paraméter**.<br/>A kimeneti szöveg nyelvét adja meg. A célnyelvnek a [supported languages](./v3-0-languages.md) `dictionary` hatókörben szereplő támogatott nyelvek egyikének kell lennie.  | 

A kérelemfejlécek a következőket tartalmazzák:

| Fejlécek  | Leírás |
| ------ | ----------- |
| Hitelesítési fejléc(ek) <img width=200/>  | **Kötelező kérelemfejléc**.<br/>Lásd: <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">A hitelesítéshez rendelkezésre álló lehetőségek.</a> |
| Content-Type | **Kötelező kérelemfejléc**.<br/>Megadja a hasznos adat tartalom típusát. Lehetséges értékek: `application/json`. . |
| Tartalom hossza   | **Kötelező kérelemfejléc**.<br/>A kérelem törzsének hossza. |
| X-ClientTraceId   | **Nem kötelező**.<br/>Az ügyfél által létrehozott GUID a kérelem egyedi azonosítására. Ezt a fejlécet kihagyhatja, ha a nyomkövetési azonosítót `ClientTraceId`a lekérdezési karakterláncba a . |

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömbelem egy JSON-objektum, amelynek tulajdonságai a következők:

  * `Text`: A keresni kívánt kifejezést meghatározó karakterlánc. Ennek egy korábbi `normalizedText` [szótár-keresőkérelem](./v3-0-dictionary-lookup.md) háttérfordításaiból származó mező értékének kell lennie. Ez lehet a `normalizedSource` mező értéke is.

  * `Translation`: A [Szótár-követési](./v3-0-dictionary-lookup.md) művelet által korábban visszaadott lefordított szöveget meghatározó karakterlánc. Ennek a `normalizedTarget` [Szótár-alapú válasz](./v3-0-dictionary-lookup.md) `translations` listájában szereplő mező ben szereplő értéknek kell lennie. A szolgáltatás példákat ad vissza az adott forrás-cél szópárhoz.

Egy példa a következő:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legbőlegelhető 10 elemet tartalmazhat.
* A tömbelem szöveges értéke szóközökkel együtt nem haladhatja meg a 100 karaktert.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely a bemeneti tömb minden egyes karakterláncához egy-egy eredményt ad. Az eredményobjektum a következő tulajdonságokat tartalmazza:

  * `normalizedSource`: A forráskifejezés normalizált formáját megadó karakterlánc. Ennek általában meg kell egyeznie `Text` a kérelem törzsében lévő egyezési lista indexében lévő mező értékével.
    
  * `normalizedTarget`: A célkifejezés normalizált formáját megadó karakterlánc. Ennek általában meg kell egyeznie `Translation` a kérelem törzsében lévő egyezési lista indexében lévő mező értékével.
  
  * `examples`: A (forráskifejezés, célkifejezés) párra vonatkozó példák listája. A lista minden eleme a következő tulajdonságokkal rendelkező objektum:

    * `sourcePrefix`: A teljes példa formájú `sourceTerm` érték _előtt_ összefűző karakterlánc. Ne adjon hozzá szóközt, mivel már ott van, amikor lennie kell. Ez az érték lehet üres karakterlánc.

    * `sourceTerm`: A tényleges kifejezéssel megegyező karakterlánc. A karakterlánc hozzáadódik, `sourcePrefix` és `sourceSuffix` alkotja a teljes példát. Az értéke el van választva, így megjelölhető egy felhasználói felületen, pl. félkövérré vonással.

    * `sourceSuffix`: A teljes példa után `sourceTerm` _összefűző_ karakterlánc. Ne adjon hozzá szóközt, mivel már ott van, amikor lennie kell. Ez az érték lehet üres karakterlánc.

    * `targetPrefix`: A célhoz `sourcePrefix` hasonló karakterlánc.

    * `targetTerm`: A célhoz `sourceTerm` hasonló karakterlánc.

    * `targetSuffix`: A célhoz `sourceSuffix` hasonló karakterlánc.

    > [!NOTE]
    > Ha nincsenek példák a szótárban, a válasz 200 `examples` (OK), de a lista üres lista.

## <a name="examples"></a>Példák

Ez a példa bemutatja, hogyan kereshet példákat `fly` az angol `volar`kifejezésből és a spanyol fordításból álló párra.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

A válaszanyag (rövidítve az egyértelműség érdekében) a következő:

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
