---
title: Előre összeállított szándékok és entitások hozzáadása a gyakori adatok kinyeréséhez a Language Understandingben – Azure | Microsoft Docs
description: Megismerheti, hogyan használhatja az előre összeállított szándékokat és entitásokat a különböző típusú entitásadatok kinyeréséhez.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: diberry
ms.openlocfilehash: 0ec6f002b35b1224118b62accda1f69e7be22fb8
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358522"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>Oktatóanyag: 2. Előre összeállított szándékok és entitások hozzáadása
Előre összeállított szándékokat és entitásokat adhat az oktatóanyagbeli Human Resources (Emberi erőforrások) alkalmazáshoz, így gyorsan hozzáférhet a szándék-előrejelzéshez és az adatok kinyeréséhez. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
* Előre összeállított szándékok hozzáadása 
* Előre összeállított entitások, datetimeV2 és szám hozzáadása
* Betanítás és közzététel
* A LUIS lekérdezése és az előrejelzési válasz fogadása

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Előkészületek
Ha nincs meg a [Human Resources](luis-quickstart-intents-only.md) (Emberi erőforrások) alkalmazása az előző oktatóanyagból, [importálja](luis-how-to-start-new-app.md#import-new-app) a JSON-t új alkalmazásba a [LUIS](luis-reference-regions.md#luis-website) webhelyén a [LUIS-minták](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) GitHub-adattárából.

Ha meg szeretné tartani az eredeti Emberi erőforrások alkalmazást, klónozza a [Settings](luis-how-to-manage-versions.md#clone-a-version) (Beállítások) lapon a verziót, és adja neki a következő nevet: `prebuilts`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. 

## <a name="add-prebuilt-intents"></a>Előre összeállított szándékok hozzáadása
A LUIS számos előre összeállított szándékot biztosít, amelyek segítenek a gyakori felhasználói szándékokban.  

1. Győződjön meg arról, hogy az alkalmazás a LUIS **Build** (Létrehozás) szakaszában van. Ha erre a szakaszra szeretne lépni, válassza a jobb felső menüsávon a **Build** (Létrehozás) elemet. 

2. Válassza az **Add prebuilt domain intent** (Előre összeállított tartományi szándék hozzáadása) lehetőséget. 

    [ ![A szándékok lapjának képernyőképe az előre összeállított tartományi szándék hozzáadására szolgáló gomb kiemelésével](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. Keressen a `Utilities` kifejezésre. 

    [ ![Képernyőkép az előre összeállított szándékok párbeszédpanelről a keresőmezőben a Utilities kifejezéssel](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Válassza ki a következő szándékokat, majd a **Done** (Kész) lehetőséget: 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>Előre összeállított entitások hozzáadása
A LUIS számos előre összeállított entitást biztosít a gyakori adatok kinyeréséhez. 

1. Válassza az **Entities** (Entitások) elemet a bal oldali navigációs menüben.

    [ ![A szándékok listájának képernyőképe, amelyen az Entities (Entitások) lehetőség ki van emelve a bal oldali navigációs panelen](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Válassza a **Manage prebuilt entities** (Előre összeállított entitások kezelése) gombot.

    [ ![Entitások lista képernyőképe, Előre összeállított entitások gomb kiemelve](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. Az előre összeállított entitások listájából válassza a **number** (szám) és a **datetimeV2** (dátum és idő) entitást, majd kattintson a **Done** (Kész) gombra.

    ![Képernyőkép: számválasztó az előre összeállított entitások párbeszédpanelen](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Az alkalmazás betanítása és közzététele

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>Alkalmazás közzététele a végponton

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-endpoint-with-an-utterance"></a>A kimondott szöveget tartalmazó végpont lekérdezése

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `I want to cancel on March 3`. Az utolsó lekérdezésisztring-paraméter a `q`, a kimondott szöveg pedig **query**. 

    Az eredmény előrejelezte a Utilities.Cancel szándékot, és kinyerte a március 3. dátumot és a 3-as számot. 

    ```
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    Két érték is tartozik a március 3-hoz, mert a kimondott szöveg nem határozta meg, hogy március 3. a múltra vagy a jövőre vonatkozik-e. A LUIS-t meghívó alkalmazástól függ, hogy feltételezi a helyes megoldást vagy tisztázást kér, ha szükséges. 

    Az előre összeállított szándékok és entitások könnyű és gyors hozzáadásával az ügyfélalkalmazás beszélgetés-felügyeletet adhat hozzá, és kinyerheti a gyakori adattípusokat. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Reguláriskifejezés-entitás hozzáadása az alkalmazáshoz](luis-quickstart-intents-regex-entity.md)

