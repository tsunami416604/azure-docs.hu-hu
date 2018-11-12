---
title: '2. oktatóanyag: Előre összeállított szándékok és entitások – előre összeállított gyakori kimondott szövegek használata – gyakori adatok kinyerése a LUIS-ban'
titleSuffix: Azure Cognitive Services
description: Előre összeállított szándékokat és entitásokat adhat az oktatóanyagbeli Human Resources (Emberi erőforrások) alkalmazáshoz, így gyorsan hozzáférhet a szándék-előrejelzéshez és az adatok kinyeréséhez. A kimondott szövegeket nem szükséges megcímkézni előre összeállított entitásokkal. Az entitást a rendszer automatikusan észleli.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d73284ecf40b2dfcd4954c1ddee82bf92a8931b9
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281795"
---
# <a name="tutorial-2-identify-common-intents-and-entities"></a>2. oktatóanyag: Gyakori szándékok és entitások azonosítása
Ebben az oktatóanyagban módosítja az Emberi erőforrások alkalmazást. Előre összeállított szándékokat és entitásokat adhat az oktatóanyagbeli Human Resources (Emberi erőforrások) alkalmazáshoz, így gyorsan hozzáférhet a szándék-előrejelzéshez és az adatok kinyeréséhez. A kimondott szövegeket nem szükséges megcímkézni előre összeállított entitásokkal, mert az entitást a rendszer automatikusan észleli.

A gyakori témák és adattípusok előre összeállított modelljei lehetővé teszik saját modellje gyors létrehozását, és jó példák arra, hogyan épül fel egy modell. 

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Meglévő oktatóalkalmazás használata
> * Előre összeállított szándékok hozzáadása 
> * Előre összeállított entitások hozzáadása 
> * Betanítás 
> * Közzététel 
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata
Folytassa az előző oktatóanyagban létrehozott **EmberiErőforrások** nevű alkalmazással. 

Amennyiben nem rendelkezik az előző oktatóanyagból származó EmberiErőforrások alkalmazással, kövesse a következő lépéseket:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-intent-only-HumanResources.json).

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `prebuilts` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes. 

## <a name="add-prebuilt-intents"></a>Előre összeállított szándékok hozzáadása
A LUIS számos előre összeállított szándékot biztosít, amelyek segítenek a gyakori felhasználói szándékokban.  

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Válassza az **Add prebuilt intent** (Előre összeállított szándék hozzáadása) lehetőséget. 

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

2. Válassza a **Manage prebuilt entity** (Előre összeállított entitás kezelése) gombot.

3. Az előre összeállított entitások listájából válassza a **number** (szám) és a **datetimeV2** (dátum és idő) entitást, majd kattintson a **Done** (Kész) gombra.

    ![Képernyőkép: számválasztó az előre összeállított entitások párbeszédpanelen](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train"></a>Betanítás

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Közzététel

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Szándék és entitások lekérése a végpontból

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez a böngésző címsorában, és írja be a következőt: `I want to cancel on March 3`. Az utolsó lekérdezésisztring-paraméter a `q`, a kimondott szöveg pedig **query**. 

    ```JSON
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

    Az eredmény előrejelezte a Utilities.Cancel szándékot, és kinyerte a március 3. dátumot és a 3-as számot. 

    Két érték is tartozik a március 3-hoz, mert a kimondott szöveg nem határozta meg, hogy március 3. a múltra vagy a jövőre vonatkozik-e. Az ügyfélalkalmazástól függ, hogy feltételezi a helyes megoldást vagy tisztázást kér, ha szükséges. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Az előre összeállított szándékok és entitások hozzáadásával az ügyfélalkalmazás képes észlelni a gyakori felhasználói szándékokat és kinyerni a gyakori adattípusokat. 

> [!div class="nextstepaction"]
> [Reguláriskifejezés-entitás hozzáadása az alkalmazáshoz](luis-quickstart-intents-regex-entity.md)

