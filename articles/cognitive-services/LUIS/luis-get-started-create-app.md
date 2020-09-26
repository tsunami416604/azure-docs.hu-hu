---
title: 'Gyors útmutató: alkalmazás létrehozása – LUIS'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy olyan LUIS-alkalmazást, amely az előre elkészített tartományt használja a `HomeAutomation` fények és a készülékek be-és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/05/2020
ms.openlocfilehash: 28bf79b61c0278a3f45820a23cd2c69f0b609700
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316485"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rövid útmutató: Előre összeállított otthonautomatizálási alkalmazás használata

Ebben a rövid útmutatóban egy LUIS-alkalmazást hoz létre, amely az előre összeállított `HomeAutomation` tartományt használja a világítás és a berendezések be- és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása
A **My Apps** (Saját alkalmazások) lapon hozhatja létre és kezelheti az alkalmazásokat.

1. A saját alkalmazások listában válassza az **+ új alkalmazás a beszélgetéshez**lehetőséget, majd a beállítások listájában válassza az **+ új alkalmazás lehetőséget a beszélgetéshez** .

1. A párbeszédpanelen nevezze el az alkalmazást `Home Automation` .
1. Válassza az **angol nyelvet** kulturális környezetként.
1. Adja meg a leírást (nem kötelező).
1. Ne válasszon előrejelzési erőforrást, ha még nem hozta létre az erőforrást. Az alkalmazás előrejelzési végpontjának (átmeneti vagy éles üzem) használatához hozzá kell rendelnie egy előrejelzési erőforrást.
1. Válassza a **Kész** lehetőséget.

    LUIS létrehozza az alkalmazást.

    ![A párbeszédpanelen nevezze el az alkalmazás "Home Automation"](./media/create-new-app-details.png)

    >[!NOTE]
    >A kulturális környezet az alkalmazás létrehozása után nem módosítható.

## <a name="add-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása

1. A bal oldali navigációs sávon válassza az **előre elkészített tartományok**elemet.
1. **HomeAutomation**keresése.
1. Válassza a **tartomány hozzáadása** elemet a HomeAutomation kártyán.

    > [!div class="mx-imgBorder"]
    > ![Válassza az "előre elkészített tartományok" lehetőséget, majd keressen rá a "HomeAutomation" kifejezésre. Válassza a "tartomány hozzáadása" lehetőséget a HomeAutomation kártyán.](media/luis-quickstart-new-app/home-automation.png)

    A tartomány sikeres hozzáadása után az előre összeállított tartomány mezőben megjelenik egy **Remove domain** (Tartomány eltávolítása) gomb.

## <a name="intents-and-entities"></a>Szándékok és entitások

1. Válassza a **leképezések** lehetőséget a HomeAutomation-tartomány szándékának áttekintéséhez. Az előre elkészített tartomány-leképezések például hosszúságú kimondott szöveg rendelkeznek.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a HomeAutomation-leképezések listájáról](media/luis-quickstart-new-app/home-automation-intents.png "Képernyőkép a HomeAutomation-leképezések listájáról")

    > [!NOTE]
    > A **None** (Nincs) az összes LUIS-alkalmazásban szereplő szándék. Olyan kimondott szövegek kezelésére szolgál, amelyek nem felelnek meg az alkalmazás által nyújtott funkcióknak.

1. Válassza a **HomeAutomation.TurnOff** szándékot. A szándék az entitásokkal jelölt hosszúságú kimondott szöveg listáját tartalmazza.

    > [!div class="mx-imgBorder"]
    > [![A HomeAutomation.TurnOff szándék képernyőképe](media/luis-quickstart-new-app/home-automation-turnoff.png "A HomeAutomation.TurnOff szándék képernyőképe")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Az alkalmazás tesztelése
Miután betanította az alkalmazását, tesztelheti is.

1. Válassza a **teszt** lehetőséget a jobb felső navigációs sávon.

1. Írjon be egy tesztet `Turn off the lights` , például az interaktív teszt ablaktáblába, és nyomja le az ENTER billentyűt.

    ```
    Turn off the lights
    ```

    Ebben a példában `Turn off the lights` helyesen van azonosítva, mint a **HomeAutomation. kanyart**legfelső pontozási szándéka.

    ![A tesztelési ablaktábla képernyőképe a kiemelt kimondott szöveggel](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Az előrejelzéssel kapcsolatos további információk megtekintéséhez válassza a **vizsgálat** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A teszt panel képernyőképe ellenőrzési információkkal](media/luis-quickstart-new-app/test.png)

1. Zárjuk be a teszt panelt.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>A V3 API előrejelzési végpontjának lekérdezése

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. A böngésző címsorába a lekérdezési karakterlánchoz ellenőrizze, hogy a következő név és érték sávok szerepelnek-e az URL-címben. Ha nem szerepelnek a lekérdezési karakterláncban, adja hozzá őket:

    |Név/érték párok|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. A böngésző címsorába lépjen az URL-cím végére, és adja meg a `turn off the living room light` _lekérdezési_ értéket, majd nyomja le az ENTER billentyűt.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
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

    További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Behívhatja a végpontot kódból:

> [!div class="nextstepaction"]
> [LUIS-végpont hívása kóddal](luis-get-started-cs-get-intent.md)
