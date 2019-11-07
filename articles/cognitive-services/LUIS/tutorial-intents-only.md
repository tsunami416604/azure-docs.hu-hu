---
title: 'Oktatóanyag: a szándékok előrejelzése – LUIS'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban hozzon létre egy egyéni alkalmazást, amely előrejelzést készít a felhasználó szándékáról. Ez az alkalmazás a legegyszerűbb típusú LUIS-alkalmazás, mert a kimondott szövegből nem nyer ki különféle adatelemeket, például e-mail-címeket vagy dátumokat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 0241af88d30d47233d972bcb3e7c58ef8de12d6e
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648250"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Oktatóanyag: a felhasználói szándékok meghatározására szolgáló LUIS-alkalmazás létrehozása

Ebben az oktatóanyagban egy egyéni alkalmazást hoz létre, amely megjósolja a felhasználó szándékát a Kimondás (szöveg) alapján. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Új alkalmazás létrehozása 
> * Szándékok létrehozása
> * Példa kimondott szövegek hozzáadása
> * Alkalmazás betanítása
> * Alkalmazás közzététele
> * Leképezési előrejelzés beolvasása a végpontról


[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]

## <a name="user-intentions-as-intents"></a>Felhasználói szándékok

Az alkalmazás célja a társalgási, természetes nyelvű szöveg szándékának meghatározása: 

`I'd like to order a veggie pizza with a salad on the side.`

Ezek különféle **szándékokként** vannak csoportosítva. 

|Szándék|Cél|
|--|--|
|`ModifyOrder`|Határozza meg a felhasználó pizzájának sorrendjét.|
|`Greeting`|Kezdje el a bot-beszélgetést.|
|`ConfirmOrder`|A pizza-sorrend megerősítése.|
|`None`|Annak megállapítása, hogy a felhasználó kér-e valamit, ha az alkalmazás nem válaszol. Ez a szándék az alkalmazások létrehozásának részeként van megadva, és nem törölhető. |

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Új szándék létrehozása 

1. A [Luis-portálon](https://preview.luis.ai), az alkalmazás **Build** szakaszán belül válassza a **+ Létrehozás**elemet. Adja meg az új cél nevet az alábbi listában, majd válassza a **kész**lehetőséget.

    A `OrderPizza` célja a következő: Ha a felhasználó egy pizzát szeretne rendelni. 

1. Vegyen fel több példát a hosszúságú kimondott szöveg erre a célra, ha a felhasználó a következőt kéri:

    |`OrderPizza` példa hosszúságú kimondott szöveg|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|

    ![Példa kimondott szövegek hozzáadása](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Ha _például hosszúságú kimondott szöveg_-t használ, akkor a Luis betanítása arról nyújt segítséget, hogy milyen hosszúságú kimondott szöveg kell előre jelezni. 

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]    

## <a name="create-remaining-intents"></a>Hátralévő leképezések létrehozása

1. Hozza létre a `Greeting` szándékot, és adja hozzá a következő példa hosszúságú kimondott szöveg. Ez a cél annak megállapítása, hogy egy felhasználó elkezd-e új pizza Order beszélgetést.

    |`Greeting` példa hosszúságú kimondott szöveg|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Hozza létre a `Confirm` szándékot, és adja hozzá a következő példa hosszúságú kimondott szöveg. Ez a szándék annak megállapítására, hogy a felhasználó megrendelése megtörtént-e, és elfogadja-e a rendelés részleteit. 

    |`Confirm` példa hosszúságú kimondott szöveg|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Nincs leképezési példa hosszúságú kimondott szöveg

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Az alkalmazás betanítása 

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Az alkalmazás közzététele 

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)] 

## <a name="get-intent-prediction"></a>Leképezési előrejelzés beolvasása

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Lépjen az URL-cím végére a címsorban, és írja be a következőt:

    `get a medium vegetarian pizza for delivery` 

    Ez nem pontosan ugyanaz, mint a Kimondás, ezért jó tesztet talál, hogy a LUIS megtudja, mit kell megjósolni ezzel a szándékkal.

    Az utolsó lekérdezésisztring-paraméter a `q`, a kimondott szöveg pedig **query**. A kimondott szöveg nem egyezik meg egyik kimondott példaszöveggel sem, ezért tesztnek megfelelő, és a `OrderPizza` szándékot kell visszaadnia a legmagasabb pontszámot elérő szándékként. 

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

    Az entitások tömbje üres, mert az alkalmazás jelenleg nem rendelkezik entitásokkal. 

    A JSON-eredmény a **`prediction.topIntent`** tulajdonságként azonosítja a legmagasabb pontszámot elérő szándékot. Az összes pontszám 1 és 0 közé esik, a jobb pontszám pedig az 1 értékhez közeledik. 

1. Módosítsa az URL- **lekérdezési** paramétert az **üdvözlési** szándék megcélzásához:

    `Howdy`

    Ez nem pontosan ugyanaz, mint a Kimondás, ezért jó tesztet talál, hogy a LUIS megtudja, mit kell megjósolni ezzel a szándékkal. 

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
 
    Ennek az előrejelzésnek a 44%-os megbízhatósági pontszáma van. A megbízhatósági pontszám növeléséhez adjon hozzá 15 és 30 közötti példa hosszúságú kimondott szöveg.  

## <a name="client-application-next-steps"></a>Ügyfél – alkalmazás következő lépései

Miután a LUIS visszaadja a JSON-választ, a LUIS nem foglalkozik tovább a kéréssel. A LUIS nem ad választ a felhasználók kimondott szövegeire, csak azonosítja a természetes nyelven kért információ típusát. A beszélgetés utáni nyomon követést az ügyfélalkalmazás, például egy Azure bot nyújtja. 


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Entitások típusai](luis-concept-entity-types.md)
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)
* [Azure-robot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag létrehozta a LUIS-alkalmazást, létrehozta a leképezéseket, felvette például az egyes szándékok hosszúságú kimondott szöveg, felvettük egy példát a hosszúságú kimondott szöveg, a kitanított, közzétett és tesztelt végponton. Ezek a LUIS-modellek létrehozásának alapvető lépései. 

> [!div class="nextstepaction"]
> [Előre összeállított szándékok és entitások hozzáadása ehhez az alkalmazáshoz](luis-tutorial-prebuilt-intents-entities.md)
