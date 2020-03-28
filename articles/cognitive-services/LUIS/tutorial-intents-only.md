---
title: 'Oktatóanyag: A szándékok előrejelzése - LUIS'
description: Ebben az oktatóanyagban hozzon létre egy egyéni alkalmazást, amely előre jelzi a felhasználó szándékát. Ez az alkalmazás a legegyszerűbb típusú LUIS-alkalmazás, mert a kimondott szövegből nem nyer ki különféle adatelemeket, például e-mail-címeket vagy dátumokat.
ms.topic: tutorial
ms.date: 03/24/2020
ms.openlocfilehash: c58c96f717de77c065d7f844928714eb4fb3e4db
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80286744"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Oktatóanyag: Hozzon létre egy LUIS-alkalmazást a felhasználói szándékok meghatározásához

Ebben az oktatóanyagban egy egyéni alkalmazást hoz létre, amely előre jelzi a felhasználó szándékát az utterance (szöveg) alapján.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Új alkalmazás létrehozása
> * Szándékok létrehozása
> * Példa kimondott szövegek hozzáadása
> * Alkalmazás betanítása
> * Alkalmazás közzététele
> * Leképezési előrejelzés beszereznie a végpontból

## <a name="user-intentions-as-intents"></a>Felhasználói szándékok leképezésként

Az alkalmazás célja a társalgási, természetes nyelvű szöveg szándékának meghatározása:

`I'd like to order a veggie pizza with a salad on the side.`

Ezek különféle **szándékokként** vannak csoportosítva.

|Szándék|Cél|
|--|--|
|`ModifyOrder`|Határozza meg a felhasználó pizza rendelését.|
|`Greeting`|Kezdődik bot beszélgetés.|
|`ConfirmOrder`|Pizza rendelés megerősítése.|
|`None`|Határozza meg, hogy a felhasználó kér-e valamit, amire az alkalmazásnak nem kellene válaszolnia. Ez a szándék, ha az alkalmazás létrehozása részeként, és nem lehet törölni. |

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Új szándék létrehozása

A szándék a felhasználó kimondott szövegének a felhasználó szándéka alapján történő osztályozására szolgál, amelyet a természetes nyelvi szöveg határoz meg.

Annak érdekében, hogy egy utterance (kifejezés) besorolásához a szándék példákat a felhasználói utterances, amely et kell osztályozni ezzel a szándékkal.

1. A **Build (Build)** csoport **Leképezések** lapján válassza a **+ Create** lehetőséget új szándék létrehozásához. Írja be az `OrderPizza`új szándék nevét, majd válassza **a Kész**lehetőséget.

    A `OrderPizza` szándék előre megvárható, ha a felhasználó pizzát szeretne rendelni.

1. Adjon hozzá több példa utterances ezt a szándékot, hogy elvárja, hogy a felhasználó megkérdezi:

    |`OrderPizza`példa kimondott szöveg|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    ![Példa kimondott szövegek hozzáadása](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    _Példautterances megadásával,_ a LUIS-t tanítja arról, hogy milyen típusú kimondott szövegeket kell előre jelezni ezt a szándékot.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Hátralévő leképezések létrehozása

1. Hozza `Greeting` létre a szándékot, és adja hozzá a következő példa utterances. Ez a szándék annak meghatározása, hogy a felhasználó új pizzarendelési beszélgetést kezd-e.

    |`Greeting`példa kimondott szöveg|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Hozza `Confirm` létre a szándékot, és adja hozzá a következő példa utterances. Ez a szándék annak meghatározása, hogy a felhasználó végzett-e a rendeléssel, és elfogadja-e a rendelés részleteit.

    |`Confirm`példa kimondott szöveg|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Nincs szándék példa utterances

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Az alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Az alkalmazás közzététele

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Leképezési előrejelzés

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Lépjen az URL végére a címsorban, és írja be a következőt:

    `get a medium vegetarian pizza for delivery`

    Ez nem pontosan ugyanaz, mint egy példa utterance (kifejezés) így egy jó teszt, hogy ha a LUIS meg tudja tanulni, mit kell előre ezzel a szándékkal.

    Az utolsó lekérdezésisztring-paraméter a `query`, a kimondott szöveg pedig **query**. A kimondott szöveg nem egyezik meg egyik kimondott példaszöveggel sem, ezért tesztnek megfelelő, és a `OrderPizza` szándékot kell visszaadnia a legmagasabb pontszámot elérő szándékként.

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    Az entitások tömb üres, mert ez az alkalmazás jelenleg nem rendelkezik entitások (egységnyi adat az utterance (kibontása az utterance (egység).

    A JSON-eredmény tulajdonságként **`prediction.topIntent`** azonosítja a legfelső pontozási szándékot. Minden pontszám 1 és 0 között van, a jobb pontszám pedig közelebb van az 1-hez.

1. Módosítsa az **URL-lekérdezés** paraméterét a **Megszólítás leképezés** ének céljához:

    `Howdy`

    Ez nem pontosan ugyanaz, mint egy példa utterance (kifejezés) így egy jó teszt, hogy ha a LUIS meg tudja tanulni, mit kell előre ezzel a szándékkal.

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }
    ```

    Ez az előrejelzés 44%-os megbízhatósági pontszámmal rendelkezik. A megbízhatósági pontszám növeléséhez adjon hozzá 15 és 30 példa utterances.

## <a name="client-application-next-steps"></a>Ügyfél-alkalmazás következő lépései

Miután a LUIS visszaadja a JSON-választ, a LUIS nem foglalkozik tovább a kéréssel. A LUIS nem ad választ a felhasználók kimondott szövegeire, csak azonosítja a természetes nyelven kért információ típusát. A társalgási nyomon követésaz ügyfélalkalmazás, például egy Azure-robot biztosítja.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Az entitások típusai](luis-concept-entity-types.md)
* [Hogyan kell a vonat](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag létrehozott egy LUIS-alkalmazást, leképezéseket hozott létre, hozzáadott példa utterances minden szándékhoz, hozzáadott példa utterances a Nincs szándék, betanított, közzétett és tesztelt a végponton. Ezek a LUIS-modellek létrehozásának alapvető lépései.

> [!div class="nextstepaction"]
> [Lebomló entitás hozzáadása ehhez az alkalmazáshoz](tutorial-machine-learned-entity.md)
