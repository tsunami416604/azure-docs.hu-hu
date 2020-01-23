---
title: Példák a Translator Text API szótárra
titleSuffix: Azure Cognitive Services
description: Az Translator Text API Dictionary példák módszere olyan példákat mutat be, amelyek bemutatják, hogyan használják a szótárban található kifejezéseket a kontextusban.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: b3551a8df19e47178c7bacd9218cfa60b66d81f9
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548067"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3,0: példák a szótárra

Olyan példákat tartalmaz, amelyek bemutatják, hogyan használják a szótárban található kifejezéseket a kontextusban. Ez a művelet a [szótárbeli kereséssel](./v3-0-dictionary-lookup.md)párhuzamosan használatos.

## <a name="request-url"></a>Kérés URL-címe

`POST` kérelem küldése a következőnek:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Kérelmek paramétereinek megadása

A lekérdezési karakterláncon átadott kérési paraméterek a következők:

| Lekérdezési paraméter | Leírás |
| --------- | ----------- |
| api-verzió <img width=200/> | **Kötelező paraméter**.<br/>Az ügyfél által kért API-verzió. Az értéknek `3.0`nak kell lennie. |
| forrás | **Kötelező paraméter**.<br/>Megadja a bemeneti szöveg nyelvét. A forrás nyelvének a `dictionary` hatókörében szereplő [támogatott nyelvek](./v3-0-languages.md) egyikének kell lennie. |
| erre: | **Kötelező paraméter**.<br/>Megadja a kimeneti szöveg nyelvét. A célként megadott nyelvnek a `dictionary` hatókörében szereplő [támogatott nyelvek](./v3-0-languages.md) egyikének kell lennie.  | 

A kérelem fejlécei a következők:

| Fejlécek  | Leírás |
| ------ | ----------- |
| Hitelesítési fejléc (ek) <img width=200/>  | **Kötelező kérelem fejléce**<br/>Tekintse <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">meg a hitelesítés elérhető beállításait</a>. |
| Tartalomtípus | **Kötelező kérelem fejléce**<br/>Megadja az adattartalom tartalomtípusát. A lehetséges értékek a következők: `application/json`. |
| Content-Length   | **Kötelező kérelem fejléce**<br/>A kérelem törzsének hossza |
| X – ClientTraceId   | **Választható**.<br/>Ügyfél által generált GUID a kérelem egyedi azonosításához. Ezt a fejlécet kihagyhatja, ha a lekérdezési karakterláncban szerepel a nyomkövetési azonosító egy `ClientTraceId`nevű lekérdezési paraméter használatával. |

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömb elem egy JSON-objektum, amely a következő tulajdonságokkal rendelkezik:

  * `Text`: a keresett kifejezést megadó karakterlánc. Ennek egy `normalizedText` mező értéke kell, hogy legyen egy korábbi [szótári keresési](./v3-0-dictionary-lookup.md) kérelem háttér-fordításai. A `normalizedSource` mező értéke is lehet.

  * `Translation`: a [szótár keresési](./v3-0-dictionary-lookup.md) művelete által korábban visszaadott lefordított szöveget megadó karakterlánc. Ennek az értéknek kell lennie a [szótár keresési](./v3-0-dictionary-lookup.md) válasz `translations` listájának `normalizedTarget` mezőjében. A szolgáltatás példákat ad vissza az adott forrás-cél Word-pair számára.

Példa:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 10 elemet tartalmazhat.
* Egy tömb elemének szöveges értéke nem lehet hosszabb 100 karakternél, beleértve a szóközöket is.

## <a name="response-body"></a>Válasz törzse

A sikeres válasz egy JSON-tömb, amely egyetlen eredménnyel rendelkezik a bemeneti tömb minden karakterláncához. Az eredmény objektum a következő tulajdonságokat tartalmazza:

  * `normalizedSource`: egy karakterlánc, amely a forrás kifejezés normalizált formáját adja. Ez általában azonos a kérelem törzsében található egyező lista index `Text` mezőjének értékével.
    
  * `normalizedTarget`: a célként megadott kifejezés normalizált formáját megadó karakterlánc. Ez általában azonos a kérelem törzsében található egyező lista index `Translation` mezőjének értékével.
  
  * `examples`: a (forrás kifejezés, a célként megadott kifejezés) párokra vonatkozó példák listája. A lista minden eleme egy olyan objektum, amely a következő tulajdonságokkal rendelkezik:

    * `sourcePrefix`: _az a karakterlánc_ , amely a `sourceTerm` értékének a teljes példaként való összefűzéséhez szükséges. Ne adjon hozzá szóközt, mert már ott van. Ez az érték lehet üres karakterlánc.

    * `sourceTerm`: a tényleges kifejezéssel megegyező sztring. A rendszer hozzáadja a karakterláncot `sourcePrefix` és `sourceSuffix` a teljes példa formájában. Az értéke el van különítve, így egy felhasználói felületen is megadható, például félkövérrel.

    * `sourceSuffix`: _az a karakterlánc_ , amely a `sourceTerm` értékének a teljes példaként való összefűzéséhez szükséges. Ne adjon hozzá szóközt, mert már ott van. Ez az érték lehet üres karakterlánc.

    * `targetPrefix`: a `sourcePrefix`hoz hasonló sztring, de a cél.

    * `targetTerm`: a `sourceTerm`hoz hasonló sztring, de a cél.

    * `targetSuffix`: a `sourceSuffix`hoz hasonló sztring, de a cél.

    > [!NOTE]
    > Ha a szótárban nincsenek példák, a válasz 200 (OK), de a `examples` lista egy üres lista.

## <a name="examples"></a>Példák

Ez a példa bemutatja, hogyan lehet megkeresni példákat az angol nyelvű `fly` és a spanyol fordítási `volar`.

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
