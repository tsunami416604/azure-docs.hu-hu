---
title: Környezetfüggő adatok-szerepkörök – beszédfelismerés
titleSuffix: Azure Cognitive Services
description: Keresse meg a környezettől függően a kapcsolódó adatokat. Kapcsolódó lehet például az indulási hely és a cél egy épületből és irodából egy másik épületbe és irodába történő fizikai mozgás során.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: diberry
ms.openlocfilehash: 98c2ad15d836162608affbfbc89908a15e274cee
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528703"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Oktatóanyag: De kapcsolódó adatok kinyerése az utterance (kifejezés)

Ebben az oktatóanyagban kapcsolódó adatrészleteket keresünk szövegkörnyezet alapján. Ha például egy forrás és cél helye egy átvitelét egy város egy másik. Az adatok két darab lehet szükség, és kapcsolatban állnak egymással.  

Ebben az oktatóanyagban korábban hierarchikus entitások használatával készült. Entitás szerepkörök cserélje le a hierarchikus entitástípus van szükség. A szerepkör minden olyan előre összeállított vagy egyéni entitás típushoz használt, és használni például kimondott szöveg és a minták. 

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Új alkalmazás létrehozása
> * Szándék hozzáadása 
> * Forrás és cél információk szerepkörök használata
> * Betanítás
> * Közzététel
> * A végpont szándékok és entitások szerepkörök beolvasása

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Kapcsolódó adatok

Ez az alkalmazás meghatározza, hogy ha egy alkalmazott át lehet helyezni a forrás város a cél városa. Használja a GeographyV2 azonosíthatja a városok nevei, és előre összeállított entitások szerepkörök segítségével határozhatja meg a helyet (forrás és cél) az utterance (kifejezés) belül.

Egy szerepkör lehet használni az Entitásadatok kibontásához:

* Kapcsolódik egymáshoz az utterance (kifejezés) környezetében.
* Használja adott szó választott minden szerepkörhöz. Ilyen szavak például: innen/oda, elhagyja/felé tart, távolodik/közeledik.
* Mindkét szerepkört az azonos utterance (kifejezés), így környezetfüggő gyakori felhasználási tanuljon LUIS gyakran használják.
* Csoportosítását és feldolgozását az ügyfélalkalmazásoknak egy információegységként kell végezniük.

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Az alkalmazottak áthelyezését a városok között leképezésének létrehozása

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

1. Az előugró párbeszédpanelen írja be a `MoveEmployeeToCity` karakterláncot, majd válassza a **Kész** elemet. 

    ![Új szándék létrehozása párbeszédpanel képernyőképe](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |Seattle elhagyó W. Kovács János jövőképünkből Orlandóban, áthelyezése|
    |Seattle-ből áttelepítse Jill Jones Kairó|
    |Hely John Jackson Tampai, erről Atlanta – hamarosan |
    |Tulsa chicagóból Perényi Doughtery áthelyezése|
    |mV való Tampai jövőképünkből Jill Jones Cairo elhagyása|
    |SHIFT Alice Anderson, Oakland Redmondi|
    |Carl Chamerlin San franciscóból redmondba|
    |Steve Standish vigye át a San Diego Bellevue felé |
    |Chicago, a céges város és shift Péter Thompson áthelyezése|

    [![Képernyőkép a LUIS az új utterances MoveEmployee leképezés a](./media/tutorial-entity-roles/hr-enter-utterances.png)](./media/tutorial-entity-roles/hr-enter-utterances.png#lightbox)

## <a name="add-prebuilt-entity-geographyv2"></a>Előre összeállított entitások geographyV2 hozzáadása

Az előre összeállított entitások geographyV2, kinyeri a helyadatokhoz, beleértve a város nevét. Mivel megcímkézzen két városok nevei, egymással összefüggésben vonatkozó szerepkörök használatával csomagolja ki, hogy a környezetben.

1. Válassza ki **entitások** a bal oldali navigációs sávon.

1. Válassza ki **előre összeállított entitás hozzáadása**, majd `geo` az előre összeállított entitások szűréséhez a keresősávba. 

    ![Adja hozzá a geographyV2 app előre összeállított entitások](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)
1. Jelölje be a jelölőnégyzetet, és válassza ki **kész**.
1. Az a **entitások** listáról válassza ki a **geographyV2** az új entitás megnyitásához. 
1. Adjon hozzá két szerepkör `Origin`, és `Destination`. 

    ![Előre összeállított entitások szerepkörök hozzáadása](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)
1. Válassza ki **leképezések** a bal oldali navigációs panelen, majd válassza ki a **MoveEmployeeToCity** szándékot. Figyelje meg, hogy a városok nevei vannak ellátva, az előre összeállított entitáshoz **geogrpahyV2**.
1. A lista első utterance válassza a feladás helyét. Megjelenik egy legördülő menü. Válassza ki **geographyV2** a listában, majd hajtsa végre a keresztben, válassza a menü **forrás**.

    [![Képernyőkép a jelölést az városa származási helyeként](media/tutorial-entity-roles/tag-origin-city-with-role.png "jelölést az városa származási helyeként képernyőképe")](media/tutorial-entity-roles/tag-origin-city-with-role.png#lightbox)

1. A metódus az előző lépésben segítségével jelölje meg az összes szerepkör-helyeken és a kimondott szöveg. 

    [![Képernyőkép a helyek entitás címkézve a kimondott szöveg](media/tutorial-entity-roles/all-locations-marked-with-roles.png "képernyőképe a helyek entitás címkézve a kimondott szöveg")](media/tutorial-entity-roles/all-locations-marked-with-roles.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>A none szándék példa beszédmódok hozzáadása 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanításához, így a módosítások a leképezés tesztelhető legyen 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, így a betanított modell lekérdezhető a végpontról

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Leképezés és egyéb entitások előrejelzés beolvasása végpont

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Lépjen az URL-cím végéhez a címsorban, és írja be a következőt: `Please move Carl Chamerlin from Tampa to Portland`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő szándékot kell visszaadnia a kinyert hierarchikus entitással: `MoveEmployee`.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.979823351
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.979823351
        },
        {
          "intent": "None",
          "score": 0.0156363435
        }
      ],
      "entities": [
        {
          "entity": "geographyV2",
          "role": "Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "geographyV2",
          "role": "Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    A megfelelő leképezés összegyűjtése várható és az entitások tömb a megfelelő a forrás- és célcsomóponton szerepkörrel rendelkezik **entitások** tulajdonság.
    
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Entitások fogalmak](luis-concept-entity-types.md)
* [Szerepkörök fogalmak](luis-concept-roles.md)
* [Előre összeállított entitások listája](luis-reference-prebuilt-entities.md)
* [Hogyan betanítása](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [A LUIS-portál tesztelése](luis-interactive-test.md)
* [Szerepkörök](luis-concept-roles.md)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy új szándékot, majd a forrás és cél helyek kontextusban megismert adatok példa beszédmódok hozzáadása. Amint megtörtént az alkalmazás betanítása és közzététele, az ügyfélalkalmazások felhasználhatják az adott információt a megfelelő információt tartalmazó mozgatási jegy létrehozásához.

> [!div class="nextstepaction"] 
> [Ismerkedés az összetett entitások hozzáadásának módjával](luis-tutorial-composite-entity.md) 
