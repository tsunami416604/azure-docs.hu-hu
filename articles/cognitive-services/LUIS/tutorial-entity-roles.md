---
title: 'Oktatóanyag: környezetfüggő adatkezelési szerepkörök – LUIS'
titleSuffix: Azure Cognitive Services
description: Kapcsolódó adat keresése a kontextus alapján. Kapcsolódó lehet például az indulási hely és a cél egy épületből és irodából egy másik épületbe és irodába történő fizikai mozgás során.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: cd646ef061a0be06a9b1a56b72a4f35d9796aa63
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447860"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Oktatóanyag: a kontextussal kapcsolatos adatok kinyerése a teljes tartalomból

Ebben az oktatóanyagban kapcsolódó adatrészleteket keresünk szövegkörnyezet alapján. Például egy forrás-és célhely az egyik városból a másikba történő átvitelhez. Mindkét adathoz szükség lehet, és egymáshoz kapcsolódnak.

A szerepkörök bármely előre elkészített vagy egyéni entitás típussal használhatók, és a példaként használt hosszúságú kimondott szöveg és mintákban is használhatók.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Új alkalmazás létrehozása
> * Szándék hozzáadása
> * Forrás-és célhely információinak beolvasása szerepkörök használatával
> * Tanítás
> * Közzététel
> * Leképezések és entitások szerepköreinek beolvasása a végpontból

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Kapcsolódó adat

Ez az alkalmazás határozza meg, hogy a rendszer hol helyezi át az alkalmazottat a forrás városból a célhelyre. Egy GeographyV2 előre összeépített entitást használ a városok nevének azonosítására, és szerepkörök használatával határozza meg a hely típusát (forrás és cél) a teljes értéken belül.

A kinyerni kívánt entitások esetében egy szerepkört kell használni:

* Kapcsolódik egymáshoz a Kimondás kontextusában.
* Az egyes szerepköröket az adott szó alapján határozza meg. Ilyen szavak például: innen/oda, elhagyja/felé tart, távolodik/közeledik.
* Mindkét szerepkör gyakran ugyanabban a feltételben van, így a LUIS ezt a gyakori használatot is megismerheti.
* Csoportosítását és feldolgozását az ügyfélalkalmazásoknak egy információegységként kell végezniük.

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

1. Jelentkezzen be az előnézeti LUIS portálra a [https://preview.luis.ai](https://preview.luis.ai)URL-címével.

1. Válassza az **új alkalmazás létrehozása**lehetőséget, írja be a `HumanResources` nevet, és tartsa meg az alapértelmezett kulturális kultúrát **angol nyelven**. A leírást hagyja üresen.

1. Válassza a **Done** (Kész) lehetőséget.

## <a name="create-an-intent-to-move-employees-between-cities"></a>Hozzon létre egy szándékot az alkalmazottak városok közötti áthelyezéséhez

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget.

1. Az előugró párbeszédpanelen írja be a `MoveEmployeeToCity` karakterláncot, majd válassza a **Kész** elemet.

    > [!div class="mx-imgBorder"]
    > ![képernyőkép az új leképezési párbeszédpanel létrehozásáról](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |John W. Smith áthelyezése a Seattle-ből az Orlando felé|
    |Jill Jones átvitele Seattle-ből Kairóba|
    |Helyezze el John Jacksont a Tampatól, amely az Atlanta-re érkezik |
    |Debra Doughtery és Tulsa áthelyezése a Chicago-ból|
    |MV Jill Jones, amely elhagyta, hogy Kairó a Tampa felé|
    |Alice Anderson és Oakland közötti váltás Redmondból|
    |Carl Chamerlin San Francisco-ból Redmondba|
    |Steve Standish átvitele San Diegoból a Bellevue felé |
    |a Kansas Cityből emelt Tanner Thompson és a Chicago felé irányuló váltás|

    > [!div class="mx-imgBorder"]
    > ![a LUIS új hosszúságú kimondott szöveg a MoveEmployee szándékban](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Előre összeépített entitás geographyV2 hozzáadása

Az előre elkészített entitás, geographyV2, Kinyeri a hely adatait, beleértve a városok nevét. Mivel a hosszúságú kimondott szöveg két városi névvel rendelkeznek, a kontextusban egymással kapcsolatban, a szerepkörök használatával kinyerheti ezt a környezetet.

1. Válassza ki az **entitásokat** a bal oldali navigációs sávon.

1. Válassza az **előre elkészített entitás hozzáadása**lehetőséget, majd a keresési sávban a `geo` gombra kattintva szűrheti az előre elkészített entitásokat.

    > [!div class="mx-imgBorder"]
    > ![geographyV2 előre elkészített entitás hozzáadása az alkalmazáshoz](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Jelölje be a jelölőnégyzetet, majd válassza a **kész**lehetőséget.
1. Az **entitások** listában válassza ki a **geographyV2** az új entitás megnyitásához.
1. Vegyen fel két szerepkört, `Origin`és `Destination`.

    > [!div class="mx-imgBorder"]
    > ![Szerepkörök hozzáadása az előre felépített entitáshoz](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

1. A bal oldali navigációs sávon válassza a **leképezések** lehetőséget, majd válassza ki a **MoveEmployeeToCity** szándékát. Figyelje meg, hogy a város neve az előre elkészített entitás **geographyV2**van megjelölve.
1. A környezeti eszköztáron válassza az **entitás palettát**.

    > [!div class="mx-imgBorder"]
    > ![válassza ki az Entity paletta elemet a tartalom eszköztárból](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. Válassza ki az előre elkészített entitást, **geographyV2**, majd válassza ki az **entitás-felügyelőt**.
1. Az **entitás-felügyelőben**válassza ki az egyik szerepkört, a **célhelyet**. Ez megváltoztatja az egér kurzorát. A kurzor használatával címkézheti a célhelyen lévő összes hosszúságú kimondott szöveg szövegét.

    > [!div class="mx-imgBorder"]
    > ![válassza ki a szerepkört az entitások palettáján](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. Térjen vissza az **entitás-felügyelőhöz**, váltson a **forrásra**a szerepkörre. A kurzor használatával címkézze fel a szöveget az összes olyan hosszúságú kimondott szöveg, amely a forrás helyét jelöli.

## <a name="add-example-utterances-to-the-none-intent"></a>Példa hosszúságú kimondott szöveg hozzáadása a none szándékhoz

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanítása, hogy tesztelni lehessen a szándék változásait

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, hogy a betanított modell lekérdezhető legyen a végpontról.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Cél-és entitás-előrejelzés beolvasása a végpontról

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Lépjen az URL-cím végéhez a címsorban, és írja be a következőt: `Please move Carl Chamerlin from Tampa to Portland`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. Ez a Kimondás nem ugyanaz, mint a címkével ellátott hosszúságú kimondott szöveg, ezért ez jó teszt, és a kinyert entitás `MoveEmployee` szándékát kell visszaadnia.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.9706451
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.9706451
        },
        {
          "intent": "None",
          "score": 0.0307451729
        }
      ],
      "entities": [
        {
          "entity": "tampa",
          "type": "builtin.geographyV2.city",
          "startIndex": 32,
          "endIndex": 36,
          "role": "Origin"
        },
        {
          "entity": "portland",
          "type": "builtin.geographyV2.city",
          "startIndex": 41,
          "endIndex": 48,
          "role": "Destination"
        }
      ]
    }
    ```

    A megfelelő szándék előre jelezve van, és az entitások tömbje a megfelelő **entitások** tulajdonságban található forrás-és célhelyi szerepköröket is tartalmaz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Entitások – fogalmak](luis-concept-entity-types.md)
* [Szerepkörök – fogalmak](luis-concept-roles.md)
* [Előre összeépített entitások listája](luis-reference-prebuilt-entities.md)
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)
* [Szerepkörök](luis-concept-roles.md)

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag egy új szándékot hozott létre, és a forrás-és célhelyek kontextusban megismert hosszúságú kimondott szöveg kapcsolatos példát vett fel. Amint megtörtént az alkalmazás betanítása és közzététele, az ügyfélalkalmazások felhasználhatják az adott információt a megfelelő információt tartalmazó mozgatási jegy létrehozásához.

> [!div class="nextstepaction"]
> [Ismerkedés az összetett entitások hozzáadásának módjával](luis-tutorial-composite-entity.md)
