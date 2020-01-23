---
title: Translator Text API szótár keresési metódusa
titleSuffix: Azure Cognitive Services
description: A szótári keresési módszer alternatív fordításokat biztosít egy szó és néhány köznyelvi kifejezés esetében.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: bd27827441082698bb4e0b43e7dd22d5b7e66539
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548951"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Translator Text API 3,0: szótár keresése

Alternatív fordításokat biztosít egy szóhoz és néhány köznyelvi-kifejezéshez. A fordítások egy része a beszédnek, valamint a háttér-fordítások listáját tartalmazza. A háttér-fordítások lehetővé teszik a felhasználók számára, hogy megértsék a fordítást a kontextusban. A [példában szereplő szótári](./v3-0-dictionary-examples.md) művelet további részletezést tesz lehetővé az egyes fordítási párok példáinak megjelenítéséhez.

## <a name="request-url"></a>Kérés URL-címe

`POST` kérelem küldése a következőnek:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Kérelmek paramétereinek megadása

A lekérdezési karakterláncon átadott kérési paraméterek a következők:

| Lekérdezési paraméter  | Leírás |
| ------ | ----------- |
| api-verzió <img width=200/>   | **Kötelező paraméter**.<br/>Az ügyfél által kért API-verzió. Az értéknek `3.0`nak kell lennie |
| forrás | **Kötelező paraméter**.<br/>Megadja a bemeneti szöveg nyelvét. A forrás nyelvének a `dictionary` hatókörében szereplő [támogatott nyelvek](./v3-0-languages.md) egyikének kell lennie. |
| erre:   | **Kötelező paraméter**.<br/>Megadja a kimeneti szöveg nyelvét. A célként megadott nyelvnek a `dictionary` hatókörében szereplő [támogatott nyelvek](v3-0-languages.md) egyikének kell lennie. |


A kérelem fejlécei a következők:

| Fejlécek  | Leírás |
| ------ | ----------- |
| Hitelesítési fejléc (ek) <img width=200/>  | **Kötelező kérelem fejléce**<br/>Tekintse <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">meg a hitelesítés elérhető beállításait</a>. |
| Tartalomtípus | **Kötelező kérelem fejléce**<br/>Megadja az adattartalom tartalomtípusát. A lehetséges értékek a következők: `application/json`. |
| Content-Length   | **Kötelező kérelem fejléce**<br/>A kérelem törzsének hossza |
| X – ClientTraceId   | **Választható**.<br/>Ügyfél által generált GUID a kérelem egyedi azonosításához. Ezt a fejlécet kihagyhatja, ha a lekérdezési karakterláncban szerepel a nyomkövetési azonosító egy `ClientTraceId`nevű lekérdezési paraméter használatával. |

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömb elem egy `Text`nevű karakterlánc-tulajdonságú JSON-objektum, amely a keresett kifejezést jelöli.

```json
[
    {"Text":"fly"}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 10 elemet tartalmazhat.
* Egy tömb elemének szöveges értéke nem lehet hosszabb 100 karakternél, beleértve a szóközöket is.

## <a name="response-body"></a>Válasz törzse

A sikeres válasz egy JSON-tömb, amely egyetlen eredménnyel rendelkezik a bemeneti tömb minden karakterláncához. Az eredmény objektum a következő tulajdonságokat tartalmazza:

  * `normalizedSource`: egy karakterlánc, amely a forrás kifejezés normalizált formáját adja. Ha például a kérelem "JOHN", a normalizált űrlap "John" lesz. A mező tartalma a [keresési példákba](./v3-0-dictionary-examples.md)kerül.
    
  * `displaySource`: egy karakterlánc, amely a forrás kifejezést adja meg a végfelhasználók számára legmegfelelőbb formában. Ha például a bemenet "János", a megjelenítési űrlap a következő név szokásos helyesírását tükrözi: "John". 

  * `translations`: a forrás kifejezés fordításait tartalmazó lista. A lista minden eleme egy olyan objektum, amely a következő tulajdonságokkal rendelkezik:

    * `normalizedTarget`: a kifejezés normalizált formáját megadó karakterlánc a célként megadott nyelven. Ez az érték a [keresési példákban](./v3-0-dictionary-examples.md)bemenetként használható.

    * `displayTarget`: egy karakterláncot, amely a célként megadott nyelven és a végfelhasználók számára legmegfelelőbb formában jeleníti meg a kifejezést. Ez általában csak a `normalizedTarget` különbözik a tőkésítés szempontjából. Például a "Juan" kifejezésnek megfelelő főnév `normalizedTarget = "juan"` és `displayTarget = "Juan"`.

    * `posTag`: az ezt a kifejezést társító karakterlánc egy beszédfelismerési címkével.

        | címke neve | Leírás  |
        |----------|--------------|
        | ADJ      | Melléknevek   |
        | ADV      | Határozószók      |
        | CONJ     | Kötőszavak |
        | DET      | Határozószók  |
        | MODÁLIS    | Parancsok        |
        | Főnév     | Főnevek        |
        | PREP     | Elöljárószavakat |
        | PRON     | Névmások     |
        | MŰVELET     | Parancsok        |
        | EGYÉB    | Egyéb        |

        Megvalósítási megjegyzésként ezeket a címkéket az angol oldal beszédfelismerési címkézése, majd az egyes forrás/cél párok számára a leggyakoribb címke alapján állapították meg. Tehát ha a felhasználók gyakran fordítanak egy spanyol szót egy másik, angol nyelvű szövegrészre, akkor a címkék helytelenek lehetnek (a spanyol szó esetében).

    * `confidence`: 0,0 és 1,0 közötti érték, amely a fordítási pár "megbízhatóságát" (vagy talán pontosabban, "a betanítási adatmennyiség" valószínűségét) jelöli. Egy adott forrású szó megbízhatósági pontszámának összege nem lehet 1,0. 

    * `prefixWord`: egy karakterlánc, amely a szót a fordítás előtagjaként jeleníti meg. Jelenleg ez a nemek között megjelenő, a nem megfelelő nyelveket meghatározó névmások. Például a "Mosca" spanyol szó előtagja "La", mivel a "Mosca" egy spanyol nőnemű főnév. Ez csak a fordítástól függ, és nem a forráson. Ha nincs előtag, az üres karakterlánc lesz.
    
    * `backTranslations`: a cél "Back translations" listája. Például a cél által lefordítható forrás szavak. A lista garantálja, hogy tartalmazza a kért forrás szót (például ha a keresett forrás szó "fly", akkor a "fly" a `backTranslations` listában jelenik meg). Azonban nem garantált, hogy az első helyen legyen, és gyakran nem. A `backTranslations` lista minden eleme a következő tulajdonságok által leírt objektum:

        * `normalizedText`: egy karakterlánc, amely a cél visszafordítására szolgáló forrás kifejezés normalizált formáját adja. Ez az érték a [keresési példákban](./v3-0-dictionary-examples.md)bemenetként használható.        

        * `displayText`: a forrás kifejezését megadó karakterlánc, amely a cél egy, a végfelhasználók számára legmegfelelőbb formában való fordítását mutatja.

        * `numExamples`: egy egész szám, amely a fordítási pár számára elérhető példák számát jelöli. A tényleges példákat a [keresési példákhoz](./v3-0-dictionary-examples.md)külön hívással kell beolvasni. A szám általában egy UX-ben való megjelenítés megkönnyítésére szolgál. Egy felhasználói felület például hozzáadhat egy hiperhivatkozást a háttér-fordításhoz, ha a példák száma nagyobb nullánál, és a háttér-fordítást egyszerű szövegként jeleníti meg, ha nincsenek példák. Vegye figyelembe, hogy a [keresési példák](./v3-0-dictionary-examples.md) által visszaadott példák száma kisebb lehet, mint `numExamples`, mert a további szűrést menet közben is alkalmazhatja a "rossz" példák eltávolításához.
        
        * `frequencyCount`: egy egész szám, amely az adatfordítási pár gyakoriságát jelöli. Ennek a mezőnek a fő célja, hogy olyan felhasználói felületet biztosítson, amely lehetővé teszi a háttér-fordítások rendezését, így a leggyakoribb kifejezések az elsők.

    > [!NOTE]
    > Ha a felkeresett kifejezés nem létezik a szótárban, a válasz 200 (OK), de a `translations` lista egy üres lista.

## <a name="examples"></a>Példák

Ebből a példából megtudhatja, hogyan keresheti meg az alternatív fordításokat spanyol nyelven az angol kifejezés `fly`.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

A válasz törzse (az egyértelműség rövidítése) a következő:

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

Ez a példa azt mutatja be, hogy mi történik, ha a keresett kifejezés nem létezik az érvényes szótári pár esetében.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Mivel a kifejezés nem található a szótárban, a válasz törzse üres `translations` listát tartalmaz.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
