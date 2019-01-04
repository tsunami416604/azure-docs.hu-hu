---
title: Pontos egyezés egyeztetése
titleSuffix: Azure Cognitive Services
description: Elemek előre meghatározott listájával egyező adatok lekérése. A lista minden elemének lehetnek pontosan megegyező szinonimái
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: bf4fd5d2a3a9bb06882dcd1b4674ccdf8ad894ee
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971409"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Oktatóanyag: Pontos szövege egyezik-adatokat kérhet le az utterance (kifejezés)

Ebből az oktatóanyagból megtudhatja, hogyan Entitásadatok, amely megegyezik az előre meghatározott elemek listájának beolvasása. 

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App létrehozása
> * Szándék hozzáadása
> * Listaentitás hozzáadása 
> * Betanítás 
> * Közzététel
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Mi az a lista entitást?

Egy lista entitás egy pontos szövege egyezik az utterance (kifejezés) szavakat. 

A lista minden eleme tartalmazhatja szinonimák egy listáját. Az emberi erőforrások alkalmazás vállalati részleg több kulcsfontosságú adatokat, például egy hivatalos nevét, a köznapi mozaikszavak és a számlázási részleg kódok segítségével azonosítható. 

Az emberi erőforrások alkalmazáson kell meghatározni a részleg egy alkalmazott átvitele. 

A listaentitás megfelelő választás az ilyen típusú adatok esetén, amikor:

* Az adatok értékei egy ismert készletbe tartoznak.
* A készlet nem haladja meg a LUIS maximális [határait](luis-boundaries.md) ezen entitástípus esetében.
* A kimondott szöveg egy része pontosan megegyezik egy szinonimával vagy a kanonikus névvel. A LUIS túli szöveg pontos egyezés a lista nem használ. Amely szerint elnevezéseket és egyéb változatok csak adott entitással listában nincs feloldva. Kezelheti a változatok, fontolja meg egy [minta](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) választható szöveg szintaxissal. 

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Az alkalmazottak át egy másik részleghez leképezésének létrehozása

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

3. Az előugró párbeszédpanelen írja be a `TransferEmployeeToDepartment` karakterláncot, majd válassza a **Kész** elemet. 

    ![Új szándék létrehozása párbeszédpanel képernyőképe](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |Helyezze át a könyvelési részleg W. Kovács János|
    |R & D Jill Jones, a átvitele|
    |Részleg 1234 rendelkezik számlázási Bradstreettől nevű új tag|
    |John Jackson helyezze a mérnöki csapathoz |
    |Helyezze át Perényi Doughtery belüli értékesítések|
    |mV Jill Jones, informatikai|
    |SHIFT Alice Anderson devops|
    |A pénzügyi Carl Chamerlin|
    |Steve Standish az 1234|
    |A 3456 Tanner Thompson|

    [![Képernyőkép a leképezést és példa utterances](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "leképezést és példa utterances képernyőképe")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Részleg lista entitás

Most, hogy a **TransferEmployeeToDepartment** szándékot példa utterances, a LUIS meg kell ismernie, mi az a szervezeti egység. 

Az elsődleges _kanonikus_nevű minden elem van a részleg nevét. A szinonimák minden egyes canonical név példák: 

|Kanonikus név|Szinonimák|
|--|--|
|Könyvelés|Acct<br>accting<br>3456|
|Fejlesztési műveletek|Fejlesztés és üzemeltetés<br>4949|
|Mérnöki tudományok|angol<br>naplófeladat<br>4567|
|Pénzügy|Pénzügy<br>2020|
|Információtechnológiai|IT<br>2323|
|Belső értékesítés|isale<br>insale<br>1414|
|Kutatás-fejlesztés|R &AMP; D<br>1234|

1. Válassza az **Entities** (Entitások) elemet a bal oldali ablaktáblán.

1. Válassza a **Create new entity** (Új entitás létrehozása) lehetőséget.

1. A felugró párbeszédpanelen adja meg az `Department` értéket az entitás neveként, és a **List** (Lista) értéket az entitás típusaként. Válassza a **Done** (Kész) lehetőséget.  

    [![Új entitás felugró párbeszédpanel létrehozásának képernyőképe](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "létrehozása új entitás felugró párbeszédpanel képernyőképe")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. A részleg entitás oldalán `Accounting` új értéket.

    [![Képernyőkép a érték megadása](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "érték megadása képernyőképe")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

1. A szinonimák a szinonimák felvétele a korábbi táblából.

    [![Képernyőkép a szinonimák megadásáról](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Képernyőkép a szinonimák megadása")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

1. Továbbra is a canonical nevek és a szinonimák hozzáadásával. 

## <a name="add-example-utterances-to-the-none-intent"></a>A none szándék példa beszédmódok hozzáadása 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanításához, így a módosítások a leképezés tesztelhető legyen 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, így a betanított modell lekérdezhető a végpontról

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Leképezés és egyéb entitások előrejelzés beolvasása végpont

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Lépjen az URL-cím végéhez, és írja be a következőt: `shift Joe Smith to IT`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **q**uery. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a `Department` szándékot kell visszaadnia, kinyerve a következőt: `TransferEmployeeToDepartment`.

  ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
  ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Entitás listában](luis-concept-entity-types.md#list-entity) elméleti információk
* [Hogyan betanítása](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [A LUIS-portál tesztelése](luis-interactive-test.md)


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag létrehozott egy új szándékot, kimondottszöveg-példákat adott hozzá, majd létrehozott egy listaentitást a pontos szövegegyezések kimondott szövegből történő kinyerése céljából. Az alkalmazás betanítása és közzététele után egy végpontlekérdezés azonosította a szándékot, és visszaadta a kinyert adatokat.

Ez az alkalmazás folytatásához [hozzáadása egy összetett entitást](luis-tutorial-composite-entity.md).

> [!div class="nextstepaction"]
> [Hierarchikus entitás hozzáadása az alkalmazáshoz](luis-quickstart-intent-and-hier-entity.md)

