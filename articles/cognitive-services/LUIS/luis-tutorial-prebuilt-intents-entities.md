---
title: Előre összeállított szándékok és entitások felismerésére
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy alkalmazást, amellyel gyorsan nyerhetnek szándék előrejelzési és adatok kivonása előre összeállított szándékok és entitások hozzá. A kimondott szövegeket nem szükséges megcímkézni előre összeállított entitásokkal. Az entitást a rendszer automatikusan észleli.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: cf16f27a8d39871491b7cf46a509b9714a669667
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873814"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Oktatóanyag: Közös szándékok és entitások azonosítása

Ebben az oktatóanyagban adhat gyorsan nyerhetnek szándék előrejelzési és adatok kivonása az emberi erőforrások oktatóanyag a előre összeállított szándékokat és entitásokat. Nem kell megjelölni az előre összeállított entitások megszólalásokat, mert az entitás automatikusan észlelt.

Előre összeállított modellek (tartományokban, szándékok és entitások) segítségével gyorsan hozhat létre a modellben.

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


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>A gyakori felhasználói céljaira segítségével előre összeállított leképezések hozzáadása

A LUIS számos előre összeállított szándékot biztosít, amelyek segítenek a gyakori felhasználói szándékokban.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Válassza az **Add prebuilt domain intent** (Előre összeállított tartományi szándék hozzáadása) lehetőséget. 

1. Keressen a `Utilities` kifejezésre. 

    [![A keresőmezőbe segédprogramok előre összeállított leképezések párbeszédpanel képernyőképe](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

1. Válassza ki a következő szándékokat, majd a **Done** (Kész) lehetőséget: 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop

    Ezek a szándék hasznosak meghatározhatja a beszélgetést, ha a felhasználó van, és mi azok arra kérjük, hajtsa végre. 


## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Gyakori típus adatkinyerés kiküszöbölni előre összeállított entitások hozzáadása

A LUIS számos előre összeállított entitást biztosít a gyakori adatok kinyeréséhez. 

1. Válassza az **Entities** (Entitások) elemet a bal oldali navigációs menüben.

1. Válassza az **Add prebuilt entity** (Előre összeállított entitás hozzáadása) gombot.

1. Válassza ki a következő entitásokat előre összeállított entitások listájából, majd válassza ki **kész**:

    * **[PersonName](luis-reference-prebuilt-person.md)** 
    * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

    ![Képernyőkép: számválasztó az előre összeállított entitások párbeszédpanelen](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

    Ezek az entitások neve és helye felismerés ad hozzá az ügyfélalkalmazás nyújt segítséget.

## <a name="add-example-utterances-to-the-none-intent"></a>A none szándék példa beszédmódok hozzáadása 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanításához, így a módosítások a leképezés tesztelhető legyen 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, így a betanított modell lekérdezhető a végpontról

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Leképezés és egyéb entitások előrejelzés beolvasása végpont

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Lépjen az URL-cím végéhez a böngésző címsorában, és írja be a következőt: `I want to cancel my trip to Seattle to see Bob Smith`. Az utolsó lekérdezésisztring-paraméter a `q`, a kimondott szöveg pedig **query**. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.807676256
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.807676256
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0487322025
        },
        {
          "intent": "Utilities.Help",
          "score": 0.0208660364
        },
        {
          "intent": "None",
          "score": 0.008789532
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.006929268
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00136293867
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    Az eredmény a Utilities.Cancel célt előrejelzett 80 %-os, és ki kell olvasni a város és személy neve adatokat. 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

További információ az előre összeállított modellek:

* [Előre összeállított tartományok](luis-reference-prebuilt-domains.md): ezek a gyakori tartományok, amelyek csökkentik a teljes LUIS alkalmazás készítése
* Előre összeállított leképezések: ezek a gyakori tartományainak az egyes leképezések. Külön-külön helyett a teljes tartományban szándékok adhat hozzá.
* [Előre összeállított entitások](luis-prebuilt-entities.md): javasoljuk, hogy a legtöbb LUIS alkalmazás általános adatait adattípusok.

További információ a LUIS-alkalmazás használata:

* [Hogyan betanítása](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [A LUIS-portál tesztelése](luis-interactive-test.md)

## <a name="next-steps"></a>További lépések

Az előre összeállított szándékok és entitások hozzáadásával az ügyfélalkalmazás képes észlelni a gyakori felhasználói szándékokat és kinyerni a gyakori adattípusokat.  

> [!div class="nextstepaction"]
> [Reguláriskifejezés-entitás hozzáadása az alkalmazáshoz](luis-quickstart-intents-regex-entity.md)

