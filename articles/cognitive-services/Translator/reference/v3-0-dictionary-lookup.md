---
title: Translator Text API szótár keresési metódusa
titleSuffix: Azure Cognitive Services
description: Használja a Translator Text API szótár keresési metódusát.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: 0bbdba343888bc91521245d0c9a0e4eaa87c5538
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932000"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Translator Text API 3.0: Keresés a szótárban

Alternatív fordításokat biztosít egy szóhoz és néhány köznyelvi-kifejezéshez. A fordítások egy része a beszédnek, valamint a háttér-fordítások listáját tartalmazza. A háttér-fordítások lehetővé teszik a felhasználók számára, hogy megértsék a fordítást a kontextusban. A [példában](./v3-0-dictionary-examples.md) szereplő szótári művelet további részletezést tesz lehetővé az egyes fordítási párok példáinak megjelenítéséhez.

## <a name="request-url"></a>Kérés URL-címe

`POST` Kérelem küldése a következőnek:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
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

A kérelem törzse egy JSON-tömb. Minden tömb elem egy nevű `Text`JSON-objektum, amely a keresett kifejezést jelöli.

```json
[
    {"Text":"fly"}
]
```

A következő korlátozások érvényesek:

* A tömb legfeljebb 10 elemet tartalmazhat.
* Egy tömb elemének szöveges értéke nem lehet hosszabb 100 karakternél, beleértve a szóközöket is.

## <a name="response-body"></a>Válasz törzse

A sikeres válasz egy JSON-tömb, amely egyetlen eredménnyel rendelkezik a bemeneti tömb minden karakterláncához. Az eredmény objektum a következő tulajdonságokat tartalmazza:

  * `normalizedSource`: Egy karakterlánc, amely a forrás kifejezés normalizált formáját adja. Ha például a kérelem "JOHN", a normalizált űrlap "John" lesz. A mező tartalma a [keresési példákba](./v3-0-dictionary-examples.md)kerül.
    
  * `displaySource`: Egy karakterlánc, amely a forrás kifejezést a végfelhasználók számára legmegfelelőbb formában jeleníti meg. Ha például a bemenet "János", a megjelenítési űrlap a név szokásos helyesírását fogja tükrözni: "John". 

  * `translations`: A forrás kifejezés fordításának listája. A lista minden eleme egy olyan objektum, amely a következő tulajdonságokkal rendelkezik:

    * `normalizedTarget`: Egy karakterlánc, amely a kifejezés normalizált formáját adja a célként megadott nyelven. Ez az érték a [keresési példákban](./v3-0-dictionary-examples.md)bemenetként használható.

    * `displayTarget`: Egy karakterlánc, amely a célként megadott nyelven és a végfelhasználók számára legmegfelelőbb formában jeleníti meg a kifejezést. Általában ez a kifejezés csak a `normalizedTarget` nagybetűktől függ. Például a "Juan" kifejezésnek megfelelő főnév a következő lesz `normalizedTarget = "juan"` : `displayTarget = "Juan"`és.

    * `posTag`: A kifejezést társító karakterlánc egy rész-beszéd címkével.

        | Címke neve | Leírás  |
        |----------|--------------|
        | ADJ      | Melléknevek   |
        | ADV      | Határozószók      |
        | CONJ     | Kötőszavak |
        | DET      | Határozószók  |
        | MODÁLIS    | Igék        |
        | FŐNÉV     | Főnevek        |
        | PREP     | Elöljárószavakat |
        | PRON     | Névmások     |
        | VERB     | Igék        |
        | MÁS    | Egyéb        |

        Megvalósítási megjegyzésként ezeket a címkéket az angol oldal beszédfelismerési címkézése, majd az egyes forrás/cél párok számára a leggyakoribb címke alapján állapították meg. Tehát ha a felhasználók gyakran fordítanak egy spanyol szót egy másik, angol nyelvű szövegrészre, akkor a címkék helytelenek lehetnek (a spanyol szó esetében).

    * `confidence`: 0,0 és 1,0 közötti érték, amely a fordítási pár "megbízhatóságát" (vagy talán pontosabban, "a betanítási adatmennyiség" valószínűségét) jelöli. Egy adott forrású szó megbízhatósági pontszámának összege nem lehet 1,0. 

    * `prefixWord`: Egy karakterlánc, amely a szót a fordítás előtagjaként jeleníti meg. Jelenleg ez a nemek között megjelenő, a nem megfelelő nyelveket meghatározó névmások. Például a "Mosca" spanyol szó előtagja "La", mivel a "Mosca" egy spanyol nőnemű főnév. Ez csak a fordítástól függ, és nem a forráson. Ha nincs előtag, az üres karakterlánc lesz.
    
    * `backTranslations`: A cél "Back translations" listája. Például a cél által lefordítható forrás szavak. A lista garantálja, hogy tartalmazza a kért forrás szót (például ha a forrásként felkeresett szó "fly", akkor a "fly" a `backTranslations` listában jelenik meg). Azonban nem garantált, hogy az első helyen legyen, és gyakran nem. A `backTranslations` lista minden eleme a következő tulajdonságok által leírt objektum:

        * `normalizedText`: Egy karakterlánc, amely a cél visszafordítására szolgáló forrás kifejezés normalizált formáját adja. Ez az érték a [keresési példákban](./v3-0-dictionary-examples.md)bemenetként használható.        

        * `displayText`: Egy karakterlánc, amely azt a forrás kifejezést adja vissza, amely a cél visszafordítása a végfelhasználók számára legmegfelelőbb formában.

        * `numExamples`: Egy egész szám, amely a fordítási pár számára elérhető példák számát jelöli. A tényleges példákat a [keresési példákhoz](./v3-0-dictionary-examples.md)külön hívással kell beolvasni. A szám általában egy UX-ben való megjelenítés megkönnyítésére szolgál. Egy felhasználói felület például hozzáadhat egy hiperhivatkozást a háttér-fordításhoz, ha a példák száma nagyobb nullánál, és a háttér-fordítást egyszerű szövegként jeleníti meg, ha nincsenek példák. Vegye figyelembe, hogy a [keresési példák](./v3-0-dictionary-examples.md) által visszaadott példák száma nem lehet kisebb `numExamples`, mert további szűrést alkalmazhat menet közben a "hibás" példák eltávolításához.
        
        * `frequencyCount`: Egy egész szám, amely az adatfordítási pár gyakoriságát jelöli. Ennek a mezőnek a fő célja, hogy olyan felhasználói felületet biztosítson, amely lehetővé teszi a háttér-fordítások rendezését, így a leggyakoribb kifejezések az elsők.

    > [!NOTE]
    > Ha a felkeresett kifejezés nem létezik a szótárban, a válasz 200 (ok), de a `translations` lista egy üres lista.

## <a name="examples"></a>Példák

Ebből a példából megtudhatja, hogyan lehet megkeresni az `fly` alternatív fordításokat spanyol nyelven az angol kifejezéssel.

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
