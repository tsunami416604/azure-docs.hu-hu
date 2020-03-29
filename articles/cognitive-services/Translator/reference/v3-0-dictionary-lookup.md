---
title: Fordítószöveg API szótári keresgő módszere
titleSuffix: Azure Cognitive Services
description: A Szótárkeresés módszer alternatív fordításokat biztosít egy szóhoz és néhány idiomatikus kifejezéshez.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: bd27827441082698bb4e0b43e7dd22d5b7e66539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548951"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Fordító szöveg API 3.0: Szótár lookup

Alternatív fordításokat biztosít egy szóhoz és néhány idiomatikus kifejezéshez. Minden fordításnak van egy szóbeszéde és egy listája a visszafordításokról. A visszafordítások lehetővé teszik a felhasználó számára, hogy megértse a fordítást a környezetben. A [Szótár példa](./v3-0-dictionary-examples.md) művelet lehetővé teszi a további részletezést az egyes fordításpárok példafelhasználásának megtekintéséhez.

## <a name="request-url"></a>Kérés URL-címe

Kérés `POST` küldése a következő knek:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Kérelem paraméterei

A lekérdezési karakterláncon átadott kérelemparaméterek a következők:

| Lekérdezési paraméter  | Leírás |
| ------ | ----------- |
| api-verzió <img width=200/>   | **Kötelező paraméter**.<br/>Az ügyfél által kért API verziója. Az értéket`3.0` |
| honnan | **Kötelező paraméter**.<br/>Megadja a bemeneti szöveg nyelvét. A forrásnyelvnek a [supported languages](./v3-0-languages.md) `dictionary` hatókörben szereplő támogatott nyelvek egyikének kell lennie. |
| erre:   | **Kötelező paraméter**.<br/>A kimeneti szöveg nyelvét adja meg. A célnyelvnek a [supported languages](v3-0-languages.md) `dictionary` hatókörben szereplő támogatott nyelvek egyikének kell lennie. |


A kérelemfejlécek a következőket tartalmazzák:

| Fejlécek  | Leírás |
| ------ | ----------- |
| Hitelesítési fejléc(ek) <img width=200/>  | **Kötelező kérelemfejléc**.<br/>Lásd: <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">A hitelesítéshez rendelkezésre álló lehetőségek.</a> |
| Content-Type | **Kötelező kérelemfejléc**.<br/>Megadja a hasznos adat tartalom típusát. Lehetséges értékek: `application/json`. . |
| Tartalom hossza   | **Kötelező kérelemfejléc**.<br/>A kérelem törzsének hossza. |
| X-ClientTraceId   | **Nem kötelező**.<br/>Az ügyfél által létrehozott GUID a kérelem egyedi azonosítására. Ezt a fejlécet kihagyhatja, ha a nyomkövetési azonosítót `ClientTraceId`a lekérdezési karakterláncba a . |

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömbelem egy JSON-objektum, `Text`amelynek neve karakterlánc-tulajdonság, amely a keresendő kifejezést jelöli.

```json
[
    {"Text":"fly"}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legbőlegelhető 10 elemet tartalmazhat.
* A tömbelem szöveges értéke szóközökkel együtt nem haladhatja meg a 100 karaktert.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely a bemeneti tömb minden egyes karakterláncához egy-egy eredményt ad. Az eredményobjektum a következő tulajdonságokat tartalmazza:

  * `normalizedSource`: A forráskifejezés normalizált formáját megadó karakterlánc. Ha például a kérelem "JÁNOS", a normalizált űrlap "jános" lesz. Ennek a mezőnek a tartalma lesz a [példák bevitele a feltaláláshoz.](./v3-0-dictionary-examples.md)
    
  * `displaySource`: Olyan karakterlánc, amely a forráskifejezést a végfelhasználói megjelenítéshez leginkább megfelelő formában adja meg. Ha például a bemenet "JOHN", a megjelenítési űrlap a név szokásos helyesírását fogja tükrözni: "János". 

  * `translations`: A forráskifejezés fordításainak listája. A lista minden eleme a következő tulajdonságokkal rendelkező objektum:

    * `normalizedTarget`: A kifejezés normalizált formáját a célnyelven megadó karakterlánc. Ezt az értéket kell használni a [példák felírásához.](./v3-0-dictionary-examples.md)

    * `displayTarget`: A kifejezést a célnyelven és a végfelhasználói megjelenítéshez legmegfelelőbb formában megadó karakterlánc. Ez általában csak a `normalizedTarget` nagybetűk szempontjából különbözik. Például, a megfelelő főnév, mint `normalizedTarget = "juan"` `displayTarget = "Juan"`a "Juan" lesz, és .

    * `posTag`: A kifejezés nek a beszédrész-címkével társító karakterlánca.

        | Címke neve | Leírás  |
        |----------|--------------|
        | ADJ      | Melléknevek   |
        | Adv      | Határozószók      |
        | CONJ között     | Kötőszavak |
        | Det      | Determinok  |
        | Modális    | Parancsok        |
        | Főnév     | Főnevek        |
        | Prep     | Elöljárószók |
        | Elterült     | Névmások     |
        | Ige     | Parancsok        |
        | Más    | Egyéb        |

        Végrehajtási megjegyzésként ezeket a címkéket az angol oldal megjelölése, majd az egyes forrás-/célpárok leggyakoribb címkéjének elkészítése határozta meg. Tehát, ha az emberek gyakran lefordítani egy spanyol szót egy másik rész-of-speech tag angolul, címkék a végén, hogy rossz (tekintettel a spanyol szó).

    * `confidence`: 0.0 és 1.0 közötti érték, amely a fordítási pár "megbízhatóságát" (vagy talán pontosabban a "valószínűséget" jelenti a betanítási adatokban). Egy forrásszó megbízhatósági pontszámainak összege 1.0 lehet, vagy nem. 

    * `prefixWord`: Olyan karakterlánc, amely a fordítás előtagjaként megjelenítendő szót adja meg. Jelenleg ez a nemi determináns a nemi determinánsok, a nyelvek, amelyek nemi determinánsok. Például a spanyol "mosca" szó előtagja "la", mivel a "mosca" egy spanyol női főnév. Ez csak a fordítástól függ, a forrástól nem. Ha nincs előtag, az üres karakterlánc lesz.
    
    * `backTranslations`: A cél "visszafordításainak" listája. Például olyan forrásszavak, amelyekre a cél lefordítható. A lista garantáltan tartalmazza a kért forrásszót (pl. ha a felkeresett forrásszó "fly", akkor garantált, hogy a `backTranslations` "fly" a listában lesz). Azonban ez nem garantált, hogy az első helyen, és gyakran nem lesz. A `backTranslations` lista minden eleme egy objektum, amelyet a következő tulajdonságok jellemeznek:

        * `normalizedText`: A forráskifejezés normalizált formáját megadó karakterlánc, amely a cél visszafordítása. Ezt az értéket kell használni a [példák felírásához.](./v3-0-dictionary-examples.md)        

        * `displayText`: Olyan karakterlánc, amely a cél visszafordítását adja meg a forráskifejezésnek a végfelhasználói megjelenítésre leginkább alkalmas formában.

        * `numExamples`: Egész szám, amely az ehhez a fordításpárhoz rendelkezésre álló példák számát jelöli. A tényleges példákat külön hívással kell beolvasni a [kereséshez](./v3-0-dictionary-examples.md). A szám többnyire a felhasználói környezetben való megjelenítés megkönnyítésére szolgál. Ha példák száma nagyobb, mint a nulla, a felhasználói felület például hiperhivatkozást adhat a háttérfordításhoz, és a visszafordítást egyszerű szövegként jelenítheti meg, ha nincsenek példák. Vegye figyelembe, hogy a hívás által adott [példák](./v3-0-dictionary-examples.md) tényleges `numExamples`száma kisebb lehet, mint a , mert a "rossz" példák eltávolításához menet közben további szűrés is alkalmazható.
        
        * `frequencyCount`: Egész szám, amely a fordítási pár gyakoriságát jelöli az adatokban. Ennek a mezőnek a fő célja, hogy felhasználói felületet biztosítson a visszafordítások rendezéséhez, hogy a leggyakoribb kifejezések az elsők.

    > [!NOTE]
    > Ha a keresett kifejezés nem létezik a szótárban, a válasz 200 `translations` (OK), de a lista üres lista.

## <a name="examples"></a>Példák

Ez a példa bemutatja, hogyan kereshet alternatív `fly` fordításokat spanyol nyelvű angol kifejezésre .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

A válaszanyag (rövidítve az egyértelműség érdekében) a következő:

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

Ez a példa azt mutatja be, hogy mi történik, ha a keresett kifejezés nem létezik az érvényes szótárpárhoz.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Mivel a kifejezés nem található meg a szótárban, `translations` a választörzs egy üres listát tartalmaz.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
