---
title: 'Oktatóanyag: entitások listázása – LUIS'
titleSuffix: Azure Cognitive Services
description: Elemek előre meghatározott listájával egyező adatok lekérése. A lista minden elemének lehetnek pontosan megegyező szinonimái
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 056c64657f42d56879928f518598206d45493f60
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447789"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Oktatóanyag: pontos szöveggel egyező adatok beolvasása a listázási entitással

Ebből az oktatóanyagból megtudhatja, hogyan kérhet le olyan adatokat, amelyek pontosan megfelelnek az előre meghatározott elemek listájának.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Alkalmazás importálása és meglévő szándék használata
> * Listaentitás hozzáadása
> * Betanítás, közzététel és lekérdezés alkalmazás a kinyert adatgyűjtéshez

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Mi a lista entitás?

A lista entitások pontos szövegnek felelnek meg a kifejezésben szereplő szavaknak. A lista minden eleme tartalmazhatja szinonimák egy listáját. Ha pontos egyezést szeretne használni, használja a lista entitást.

Ehhez az importált pizza-alkalmazáshoz hozzon létre egy lista entitást a pizza-kéreg különböző típusaihoz.

A listaentitás megfelelő választás az ilyen típusú adatok esetén, amikor:

* Az adatok értékei egy ismert készletbe tartoznak.
* A készlet nem haladja meg a LUIS maximális [határait](luis-boundaries.md) ezen entitástípus esetében.
* A kimondott szöveg egy része pontosan megegyezik egy szinonimával vagy a kanonikus névvel. LUIS nem használja a listát a pontos szöveges egyezéseken felül. A kivezetés, a többes szám és más változat nem oldható fel egyetlen listával rendelkező entitással. A változatok kezeléséhez érdemes [mintát](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) használni a választható szöveges szintaxissal.

> [!CAUTION]
> Ha nem biztos abban, hogy egy lista entitást vagy egy géppel megtanult entitást leíróként egy kifejezési listával rendelkezik, a legjobb és legrugalmasabb eljárás egy olyan géppel megtanult entitás használata, amelyben leíróként szerepel egy kifejezési lista. Ez a módszer lehetővé teszi a LUIS számára a kinyerni kívánt adatok értékének megismerését és kiterjesztését.

## <a name="import-example-json-and-add-utterances"></a>Importálás példa. JSON és hosszúságú kimondott szöveg hozzáadása

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. Az importált alkalmazás `OrderPizza` szándékkal rendelkezik. Válassza ki ezt a szándékot, és vegyen fel néhány hosszúságú kimondott szöveg új kéreg-típusokkal:

    |Új hosszúságú kimondott szöveg|
    |--|--|
    |rendeljen egy kis pepperoni pizza|
    |3 vékony kéreg Hawaii pizzák|
    |2 töltött kéreg-pizzát szállít a kenyérrel|
    |egy vastag kéregű pizza a pickuphoz|
    |egy Deep Dish pepperoni pizza|

## <a name="crust-list-entity"></a>Kéreg listázása entitás

Most, hogy a **OrderPizza** célja például a hosszúságú kimondott szöveg, a Luis-nek meg kell ismernie, hogy mely szavak képviselik a kéreg típusait.

Az elsődleges név és a szinonimák példái a következők:

|Kanonikus név|Szinonimák|
|--|--|
|Deep Dish|mély<br>mély tál kéreg<br>vastag<br>sűrű kéreg|
|Pásztázás|rendszeres<br>eredeti<br>normál<br>normál kéreg<br>eredeti kéreg<br>normál kéreg|
|Töltött|töltött kéreg|
|Vékony|vékony kéreg<br>sovány<br>sovány kéreg|

1. Válassza az **Entities** (Entitások) elemet a bal oldali ablaktáblán.

1. Válassza a **+ Létrehozás**elemet.

1. A felugró párbeszédpanelen adja meg az `CrustList` értéket az entitás neveként, és a **List** (Lista) értéket az entitás típusaként. Kattintson a **Tovább** gombra.

    > [!div class="mx-imgBorder"]
    > ![képernyőkép az új entitás előugró párbeszédpanel létrehozásáról](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. Az **entitások listájának létrehozása** lapon adja meg az egyes kanonikus nevek kanonikus nevét és szinonimáit, majd válassza a **Létrehozás**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![elemek hozzáadására szolgáló képernyőkép az entitások listázásához](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    Amikor egy list entitást ad hozzá egy LUIS-alkalmazáshoz, nem kell [felcímkézni](label-entity-example-utterance.md) a szöveget a List entitással. A rendszer az összes cél összes hosszúságú kimondott szöveg alkalmazza.

## <a name="train-the-app-before-testing-or-publishing"></a>Az alkalmazás betanítása tesztelés vagy közzététel előtt

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Az alkalmazás közzététele a végpontról történő lekérdezéshez

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Cél-és entitás-előrejelzés beolvasása a végpontról

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Nyissa meg a címet az URL-cím végére, és adja meg a következő értéket:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    Az utolsó lekérdezésisztring-paraméter `query`, a kimondott szöveg pedig a **query**.


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    A kéreg típusainak pontos szövege megegyezik, és a rendszer a JSON-válaszban visszaadja. Az ügyfélalkalmazás ezt az információt használja a megrendelés feldolgozásához.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* Entitás fogalmi információinak [listázása](luis-concept-entity-types.md#list-entity)
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)
* [Koncepció – entitások](luis-concept-entity-types.md)
* [A reguláris kifejezés entitás JSON-hivatkozása](reference-entity-regular-expression.md?tabs=V3)
* [Entitások hozzáadása adatok kinyeréséhez](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag példaként adott hosszúságú kimondott szöveg, majd létrehozta a List entitást a pontos szöveges egyezések kinyeréséhez a hosszúságú kimondott szöveg. Az alkalmazás betanítása és közzététele után egy végpontlekérdezés azonosította a szándékot, és visszaadta a kinyert adatokat.

> [!div class="nextstepaction"]
> [Előre összeépített entitás hozzáadása szerepkörrel](tutorial-entity-roles.md)

