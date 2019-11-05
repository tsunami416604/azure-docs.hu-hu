---
title: 'Oktatóanyag: előre összeépített leképezések és entitások – LUIS'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban előre elkészített leképezéseket és entitásokat ad hozzá egy alkalmazáshoz, amellyel gyorsan elsajátíthatja a szándék előrejelzését és az kinyerést. A kimondott szövegeket nem szükséges megcímkézni előre összeállított entitásokkal. Az entitást a rendszer automatikusan észleli.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: diberry
ms.openlocfilehash: 0a59d9783eac122f96b1671f2dba5d0d708e1d83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499392"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Oktatóanyag: közös szándékok és entitások azonosítása

Ebben az oktatóanyagban előre elkészített leképezéseket és entitásokat ad hozzá egy emberi erőforrásokkal foglalkozó oktatóanyag-alkalmazáshoz, amellyel gyorsan elsajátíthatja a szándék előrejelzését és az kinyerést. Nem kell megjelölnie egy hosszúságú kimondott szöveg az előre elkészített entitásokkal, mert a rendszer automatikusan észleli az entitást.

Az előre elkészített modellek (tartományok, leképezések és entitások) segítenek a modell gyors létrehozásában.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Új alkalmazás létrehozása
> * Előre összeállított szándékok hozzáadása 
> * Előre összeállított entitások hozzáadása 
> * Betanítás 
> * Közzététel 
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Előre összeépített leképezések hozzáadása a gyakori felhasználói szándékok segítésére

A LUIS számos előre összeállított szándékot biztosít, amelyek segítenek a gyakori felhasználói szándékokban.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Válassza az **Add prebuilt domain intent** (Előre összeállított tartományi szándék hozzáadása) lehetőséget. 

1. Keressen a `Utilities` kifejezésre. 

1. Válassza ki az összes leképezést, majd válassza a **kész**lehetőséget. Ezek a szándékok segítenek megállapítani, hogy hol, a beszélgetésben, a felhasználó, és mit kérnek. 

## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Előre összeépített entitások hozzáadása a közös adattípusok kinyerésének megkönnyítéséhez

A LUIS számos előre összeállított entitást biztosít a gyakori adatok kinyeréséhez. 

1. Válassza az **Entities** (Entitások) elemet a bal oldali navigációs menüben.

1. Válassza az **Add prebuilt entity** (Előre összeállított entitás hozzáadása) gombot.

1. Válassza ki a következő entitásokat az előre elkészített entitások listájából, majd válassza a **kész**lehetőséget:

   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     Ez az entitás segítséget nyújt a hely felismerésének hozzáadásához az ügyfélalkalmazás számára.

## <a name="add-example-utterances-to-the-none-intent"></a>Példa hosszúságú kimondott szöveg hozzáadása a none szándékhoz 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanítása, hogy tesztelni lehessen a szándék változásait 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, hogy a betanított modell lekérdezhető legyen a végpontról.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Cél-és entitás-előrejelzés beolvasása a végpontról

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Lépjen az URL-cím végéhez a böngésző címsorában, és írja be a következőt: `I want to cancel my trip to Seattle`. Az utolsó lekérdezésisztring-paraméter a `q`, a kimondott szöveg pedig **query**. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.1055009
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.1055009
        },
        {
          "intent": "Utilities.SelectItem",
          "score": 0.02659072
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0253379084
        },
        {
          "intent": "Utilities.ReadAloud",
          "score": 0.02528683
        },
        {
          "intent": "Utilities.SelectNone",
          "score": 0.02434013
        },
        {
          "intent": "Utilities.Escalate",
          "score": 0.009161292
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006861785
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00633448
        },
        {
          "intent": "Utilities.ShowNext",
          "score": 0.0053827134
        },
        {
          "intent": "None",
          "score": 0.002602003
        },
        {
          "intent": "Utilities.ShowPrevious",
          "score": 0.001797354
        },
        {
          "intent": "Utilities.SelectAny",
          "score": 0.000831930141
        },
        {
          "intent": "Utilities.Repeat",
          "score": 0.0006924066
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.000606057351
        },
        {
          "intent": "Utilities.GoBack",
          "score": 0.000276725681
        },
        {
          "intent": "Utilities.FinishTask",
          "score": 0.000267822179
        },
        {
          "intent": "Utilities.Reject",
          "score": 3.21784828E-05
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        }
      ]
    }
    ```

    Az eredmény előre jelezte a segédprogramokat. az 80%-os megbízhatósággal megszakítja a szándékot, és kibontotta a városhoz tartozó 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

További információ az előre elkészített modellekről:

* [Előre elkészített tartományok](luis-reference-prebuilt-domains.md): ezek olyan általános tartományok, amelyek csökkentik a teljes Luis-alkalmazások készítését
* Előre elkészített leképezések: ezek a közös tartományok egyedi céljait jelentik. A teljes tartomány hozzáadása helyett a leképezéseket külön is hozzáadhatja.
* [Előre elkészített entitások](luis-prebuilt-entities.md): ezek a leggyakoribb adattípusok a legtöbb Luis-alkalmazás esetében hasznosak.

További információ a LUIS-alkalmazás használatáról:

* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)

## <a name="next-steps"></a>További lépések

Az előre összeállított szándékok és entitások hozzáadásával az ügyfélalkalmazás képes észlelni a gyakori felhasználói szándékokat és kinyerni a gyakori adattípusokat.  

> [!div class="nextstepaction"]
> [Reguláriskifejezés-entitás hozzáadása az alkalmazáshoz](luis-quickstart-intents-regex-entity.md)

