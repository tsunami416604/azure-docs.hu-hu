---
title: Előre összeépített leképezések és entitások – LUIS
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban előre elkészített leképezéseket és entitásokat ad hozzá egy alkalmazáshoz, amellyel gyorsan elsajátíthatja a szándék előrejelzését és az kinyerést. A kimondott szövegeket nem szükséges megcímkézni előre összeállított entitásokkal. Az entitást a rendszer automatikusan észleli.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 4697bad15a374bed0de08b7cabc5aceaad7f1259
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876709"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Oktatóanyag: Közös szándékok és entitások azonosítása

Ebben az oktatóanyagban előre elkészített leképezéseket és entitásokat ad hozzá egy emberi erőforrásokkal foglalkozó oktatóanyag-alkalmazáshoz, amellyel gyorsan elsajátíthatja a szándék előrejelzését és az kinyerést. Nem kell megjelölnie egy hosszúságú kimondott szöveg az előre elkészített entitásokkal, mert a rendszer automatikusan észleli az entitást.

Az előre elkészített modellek (tartományok, leképezések és entitások) segítenek a modell gyors létrehozásában.

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

   * **[PersonName](luis-reference-prebuilt-person.md)** 
   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     ![Képernyőkép az előre elkészített entitások párbeszédpanelen kiválasztott számról](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

     Ezek az entitások segítséget nyújtanak a név és a hely felismerésének hozzáadásához az ügyfélalkalmazás számára.

## <a name="add-example-utterances-to-the-none-intent"></a>Példa hosszúságú kimondott szöveg hozzáadása a none szándékhoz 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanítása, hogy tesztelni lehessen a szándék változásait 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, hogy a betanított modell lekérdezhető legyen a végpontról.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Cél-és entitás-előrejelzés beolvasása a végpontról

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Lépjen az URL-cím végéhez a böngésző címsorában, és írja be a következőt: `I want to cancel my trip to Seattle to see Bob Smith`. Az utolsó lekérdezésisztring-paraméter a `q`, a kimondott szöveg pedig **query**. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith.",
      "topScoringIntent": {
        "intent": "Utilities.ReadAloud",
        "score": 0.100361854
      },
      "intents": [
        {
          "intent": "Utilities.ReadAloud",
          "score": 0.100361854
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.08102781
        },
        {
          "intent": "Utilities.SelectNone",
          "score": 0.0398852825
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0277276486
        },
        {
          "intent": "Utilities.SelectItem",
          "score": 0.0220712926
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0145813478
        },
        {
          "intent": "None",
          "score": 0.012434179
        },
        {
          "intent": "Utilities.Escalate",
          "score": 0.0122632384
        },
        {
          "intent": "Utilities.ShowNext",
          "score": 0.008534077
        },
        {
          "intent": "Utilities.ShowPrevious",
          "score": 0.00547111453
        },
        {
          "intent": "Utilities.SelectAny",
          "score": 0.00152912608
        },
        {
          "intent": "Utilities.Repeat",
          "score": 0.0005556819
        },
        {
          "intent": "Utilities.FinishTask",
          "score": 0.000169488427
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.000149565312
        },
        {
          "intent": "Utilities.GoBack",
          "score": 0.000141017343
        },
        {
          "intent": "Utilities.Reject",
          "score": 6.27324E-06
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

    Az eredmény előre jelezte a segédprogramokat. az 80%-os megbízhatósággal megszakítja a szándékot, és kibontotta a város és a személy nevét. 


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

