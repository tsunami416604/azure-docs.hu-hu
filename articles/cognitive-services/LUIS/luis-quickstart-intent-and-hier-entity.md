---
title: Hierarchikus entitás
titleSuffix: Azure Cognitive Services
description: Kapcsolódó adatrészletek keresése kontextus alapján. Kapcsolódó lehet például az indulási hely és a cél egy épületből és irodából egy másik épületbe és irodába történő fizikai mozgás során.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 91a9646e88adbfaf6d3c3fc0b06b341c647e773f
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753693"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Oktatóanyag: De kapcsolódó adatok kinyerése az utterance (kifejezés)

Ebben az oktatóanyagban kapcsolódó adatrészleteket keresünk szövegkörnyezet alapján. Ha például egy forrás és cél helye egy átvitelét egy város egy másik. Az adatok két darab lehet szükség, és kapcsolatban állnak egymással.  

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Új alkalmazás létrehozása
> * Szándék hozzáadása 
> * Forrással és célgyermekkel rendelkező helyhierarchikus entitás hozzáadása
> * Betanítás
> * Közzététel
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="hierarchical-data"></a>Hierarchikus adatokkal

Ez az alkalmazás meghatározza, hogy ha egy alkalmazott át lehet helyezni a forrás város a cél városa. A hierarchikus entitás segítségével határozza meg a kimondott szövegben található helyeket. 

A hierarchikus entitás ideális ehhez az adattípushoz, mert a két darab, az adatokat, az alárendelt helyek:

* Egyszerű entitás.
* Kapcsolódik egymáshoz a kimondott szövegkörnyezetben.
* Adott szó választott használatával minden entitás jelzi. Ilyen szavak például: innen/oda, elhagyja/felé tart, távolodik/közeledik.
* Mindkét gyermekek gyakran az azonos utterance (kifejezés) találhatók. 
* Csoportosítását és feldolgozását az ügyfélalkalmazásoknak egy információegységként kell végezniük.

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Az alkalmazottak áthelyezését a városok között leképezésének létrehozása

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

1. Az előugró párbeszédpanelen írja be a `MoveEmployeeToCity` karakterláncot, majd válassza a **Kész** elemet. 

    ![Új szándék létrehozása párbeszédpanel képernyőképe](./media/luis-quickstart-intent-and-hier-entity/create-new-intent-move-employee-to-city.png)

1. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |Helyezze át a Seattle elhagyó W. Kovács János jövőképünkből, Dallas|
    |Seattle-ből áttelepítse Jill Jones Kairó|
    |Hely John Jackson Tampai, erről Atlanta – hamarosan |
    |Váltás Perényi Doughtery Tulsa Dallas|
    |mV való Tampai jövőképünkből Jill Jones Cairo elhagyása|
    |SHIFT Alice Anderson, Oakland Redmondi|
    |Carl Chamerlin San franciscóból redmondba|
    |Steve Standish vigye át a San Diego Bellevue felé |
    |Chicago, a céges város és shift Péter Thompson áthelyezése|

    [![Képernyőkép a LUIS-ról, új kimondott szövegek a MoveEmployee szándékban](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

## <a name="create-a-location-entity"></a>Hely entitás létrehozása
A LUIS-nak meg kell értenie, hogy a hely pontosan mit jelent. Ez a kimondott szövegben található indulási és érkezési hely címkézésével lehetséges. Ha a kimondott szöveget jogkivonat (nyers) nézetben szeretné megtekinteni, válassza az **Entities View** (Entitások nézet) címkével ellátott, a kimondott szövegek fölötti sávban található kapcsolót. Miután átkapcsolta a kapcsolót, a vezérlő **Tokens View** (Jogkivonatok nézet) címkével lesz ellátva.

Vegyük például a következő kimondott szöveget:

```json
move John W. Smith leaving Seattle headed to Dallas
```

A kimondott szövegben két hely van megadva: `Seattle` és `Dallas`. Mindkét csoportosítva vannak benne egy hierarchikus entitás gyermekeiként `Location`, mivel a két darab, az adatok nyerhetők ki az utterance (kifejezés) az ügyfélalkalmazásban a kérés teljesítéséhez szükséges és kapcsolatban állnak egymással. 
 
Ha egy hierarchikus entitásnak csak az egyik gyermeke (az indulás vagy az érkezés) van jelen, az alkalmazás akkor is kinyeri. Egy vagy néhány gyermek kinyeréséhez nem szükséges az összes gyermeket megtalálni. 

1. A(z) `move John W. Smith leaving Seattle headed to Dallas` kimondott szövegben válassza ki a következő szót: `Seattle`. Megjelenik egy legördülő menü egy szövegmezővel a tetején. Adja meg az entitás nevét (`Location`) a szövegmezőben, majd a legördülő menüben válassza a **Create new entity** (Új entitás létrehozása) lehetőséget. 

    [![Képernyőkép az új entitás létrehozása a szándék lapon](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png "szándék oldalon új entitás létrehozása képernyőképe")](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png#lightbox)

1. Az előugró ablakban válassza a **Hierarchikus** entitástípust, gyermekentitásokként a következőkkel: `Origin` és `Destination`. Válassza a **Done** (Kész) lehetőséget.

    ![Képernyőkép az entitás létrehozása előugró párbeszédpanelen új helyre az entitáshoz](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "entitás létrehozása előugró párbeszédpanelen új helyre az entitáshoz képernyőképe")

1. A `Seattle` címkéje `Location`, mivel a LUIS nem tudja, hogy a kifejezés a kiindulási vagy az érkezési hely-e, vagy egyik sem. Válassza ki `Seattle`, majd **hely**, majd hajtsa végre a menü a jobb oldalon, és válassza ki `Origin`.

    [![Képernyőkép a felugró párbeszédpanel címkézés entitás módosítani az entitás gyermek helyeken](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png "módosítani az entitás gyermek helyeken entitás címkézés felugró párbeszédpanel képernyőképe")](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png#lightbox)

1. A címke az összes többi megcímkézzen más helyeiről. Az összes hely vannak megjelölve, a kimondott szöveg kezdje a következő egy minta módon. 

    [![Képernyőkép a helyek entitás címkézve a kimondott szöveg](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png "képernyőképe a helyek entitás címkézve a kimondott szöveg")](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png#lightbox)

    A piros aláhúzás azt jelzi a LUIS nem az entitás benne. Ez képzési oldja fel. 

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
          "entity": "portland",
          "type": "Location::Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "tampa",
          "type": "Location::Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    A megfelelő leképezés összegyűjtése várható és az entitások tömb a megfelelő a forrás- és célcsomóponton értékekkel rendelkező **entitások** tulajdonság.
    
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [A hierarchikus](luis-concept-entity-types.md) elméleti információk
* [Hogyan betanítása](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [A LUIS-portál tesztelése](luis-interactive-test.md)
* [Szerepkörök és a hierarchikus entitások](luis-concept-roles.md#roles-versus-hierarchical-entities)
* [Minták az előrejelzések javítása](luis-concept-patterns.md)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy új szándékot, majd a forrás és cél helyek kontextusban megismert adatok példa beszédmódok hozzáadása. Amint megtörtént az alkalmazás betanítása és közzététele, az ügyfélalkalmazások felhasználhatják az adott információt a megfelelő információt tartalmazó mozgatási jegy létrehozásához.

> [!div class="nextstepaction"] 
> [Ismerkedés az összetett entitások hozzáadásának módjával](luis-tutorial-composite-entity.md) 
