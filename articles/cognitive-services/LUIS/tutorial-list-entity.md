---
title: 'Oktatóanyag: Lista entitás - LUIS'
description: Elemek előre meghatározott listájával egyező adatok lekérése. A lista minden elemének lehetnek pontosan megegyező szinonimái
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 1cfeccbd54e8ef8ec315d53fc7a766760c92a0d1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79297407"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Oktatóanyag: Pontos szövegegyezésű adatok beszereznie egy listaentitással rendelkező utterance (kifejezés)

Ebben az oktatóanyagban ismerje meg, hogyan szerezhet be olyan adatokat, amelyek pontosan megegyeznek egy előre meghatározott elemlistával.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Alkalmazás importálása és meglévő szándék használata
> * Listaentitás hozzáadása
> * Kinyert adatok betanítása, közzététele és lekérdezése

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Mi az a listaentitás?

A listaentitás pontos szövegegyezés a szövegkimondott szövegben szereplő szavakkal. A lista minden eleme tartalmazhatja szinonimák egy listáját. Ha pontos egyezést szeretne használni, használjon listaentitást.

Ehhez az importált pizza alkalmazáshoz hozzon létre egy listaentitást a különböző típusú pizzakéreghez.

A listaentitás megfelelő választás az ilyen típusú adatok esetén, amikor:

* Az adatok értékei egy ismert készletbe tartoznak.
* A készlet nem haladja meg a LUIS maximális [határait](luis-boundaries.md) ezen entitástípus esetében.
* Az utterance (kifejezés) szöveg egy kis- és nagybetűket nem megkülönböztető egyezés egy szinonimával vagy a gyűjtőnévvel. A LUIS nem használja a listát az egyezésen kívül. A származtatott, többes szám és egyéb változatok nem oldódnak meg csak egy listaentitással. A változatok kezeléséhez érdemes lehet egy [mintát](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) használni a választható szövegszintaxissal.

> [!CAUTION]
> Ha nem biztos abban, hogy szeretne egy lista entitás vagy egy gép által megtanult entitás egy kifejezéslistát, mint egy leíró, a legjobb és legrugalmasabb gyakorlat az, hogy egy gép által megtanult entitás egy kifejezéslistát, mint a leíró. Ez a módszer lehetővé teszi a LUIS számára, hogy megtanulja és kiterjessze az adatok értékeit a kibontáshoz.

## <a name="import-example-json-and-add-utterances"></a>Példa .json importálása és kimondott szöveg hozzáadása

1.  Töltse le és mentse az [alkalmazás JSON fájlt](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. Az importált alkalmazásnak van egy `OrderPizza` szándéka. Válassza ki ezt a szándékot, és adjon hozzá néhány kimondott szöveget új kéregtípusokkal:

    |Új kimondott szöveg|
    |--|--|
    |kérjük, hogy egy serpenyőben kéreg kis pepperoni pizza|
    |3 vékony kéreg hawaii pizzák|
    |szállít 2 töltött kéreg pizzák kenyér pálcikával|
    |egy vastag kéreg pizza pickup|
    |egy mély tál pepperoni pizza|

## <a name="crust-list-entity"></a>Crust lista entitás

Most, hogy az **OrderPizza** szándék példa utterances a kéreg típusok, LUIS meg kell értenie, hogy mely szavak képviselik a kéreg típusok.

Példák az elsődleges névre és a szinonimákra:

|Kanonikus név|Szinonimák|
|--|--|
|Mély étel|Mély<br>mély edény kéreg<br>Vastag<br>vastag kéreg|
|Pásztázás|Rendszeres<br>Eredeti<br>Normál<br>rendszeres kéreg<br>eredeti kéreg<br>normál kéreg|
|Töltött|töltött kéreg|
|Vékony|vékony kéreg<br>Sovány<br>sovány kéreg|

1. Válassza az **Entities** (Entitások) elemet a bal oldali ablaktáblán.

1. Válassza **a + Create**lehetőséget.

1. A felugró párbeszédpanelen adja meg az `CrustList` értéket az entitás neveként, és a **List** (Lista) értéket az entitás típusaként. Válassza a **Tovább lehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![Új entitás létrehozása előugró párbeszédpanel képernyőképe](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. A **Listaentitás létrehozása** lapon adja meg az egyes gyűjtőnevek gyűjtőnevét és szinonimáit, majd válassza a **Létrehozás lehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép: elemek hozzáadása a listaentitáshoz](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    Amikor egy listaentitást ad hozzá egy LUIS-alkalmazáshoz, nem kell [a](label-entity-example-utterance.md) szöveget a listaentitással címkézni. Minden szándékban minden kimondott szövegre vonatkozik.

## <a name="train-the-app-before-testing-or-publishing"></a>Az alkalmazás betanítása tesztelés vagy közzététel előtt

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Az alkalmazás közzététele lekérdezéshez a végpontról

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Leképezés és entitás-előrejelzés beszerezni a végponttól

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Lépjen az URL-cím végére a címben, és adja meg a következő utterance (kifejezés) értéket:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    Az utolsó querystring `query`paraméter a ( utterance ( kifejezés ) **lekérdezés.**


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

    A fajta kéreg találtak pontos szöveget egyezik, és visszatért a JSON választ. Ezt az információt az ügyfélalkalmazás használja a rendelés feldolgozásához.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* Entitás fogalmi adatainak [listázása](luis-concept-entity-types.md#list-entity)
* [Hogyan kell a vonat](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)
* [Koncepció - entitások](luis-concept-entity-types.md)
* [A reguláris kifejezés entitás JSON-hivatkozása](reference-entity-regular-expression.md?tabs=V3)
* [Entitások hozzáadása adatok kinyeréséhez](luis-how-to-add-entities.md)

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag hozzáadott példa utterances, majd létrehozott egy lista entitás kinyerése pontos szöveges egyezések a kimondott szövegből. Az alkalmazás betanítása és közzététele után egy végpontlekérdezés azonosította a szándékot, és visszaadta a kinyert adatokat.

> [!div class="nextstepaction"]
> [Előre összeállított entitás hozzáadása szerepkörrel](tutorial-entity-roles.md)

