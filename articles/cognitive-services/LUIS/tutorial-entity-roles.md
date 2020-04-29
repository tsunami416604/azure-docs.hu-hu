---
title: 'Oktatóanyag: környezetfüggő adatkezelési szerepkörök – LUIS'
description: Kapcsolódó adat keresése a kontextus alapján. Kapcsolódó lehet például az indulási hely és a cél egy épületből és irodából egy másik épületbe és irodába történő fizikai mozgás során.
ms.topic: tutorial
ms.date: 03/30/2020
ms.openlocfilehash: fdb463896e531619ea7ebe7c384729763dc84138
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80475823"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Oktatóanyag: a kontextussal kapcsolatos adatok kinyerése a teljes tartalomból

Ebben az oktatóanyagban kapcsolódó adatrészleteket keresünk szövegkörnyezet alapján. Például a forrás-és célhelyek az egyik városból a másikba történő átvitelhez. Mindkét adathoz szükség lehet, és egymáshoz kapcsolódnak.

A szerepkörök bármely előre elkészített vagy egyéni entitás típussal használhatók, és a példaként használt hosszúságú kimondott szöveg és mintákban is használhatók.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Új alkalmazás létrehozása
> * Szándék hozzáadása
> * Forrás-és célhely információinak beolvasása szerepkörök használatával
> * Betanítás
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

1. Jelentkezzen be a [Luis **betekintő** portálra](https://preview.luis.ai).

1. Válassza az **+ új alkalmazás a beszélgetéshez**lehetőséget, `HumanResources` írja be a nevet, és tartsa meg az alapértelmezett kulturális kultúrát **angol nyelven**. Hagyja üresen a leírást és az előrejelzési erőforrást. Válassza a **Done** (Kész) lehetőséget.

## <a name="create-an-intent-to-move-employees-between-cities"></a>Hozzon létre egy szándékot az alkalmazottak városok közötti áthelyezéséhez

A rendszer a felhasználói hosszúságú kimondott szöveg a természetes nyelvű szöveg alapján meghatározott szándék alapján történő besorolására használja.

A Kimondás besorolásához a szándéknak példákat kell besorolnia a felhasználói hosszúságú kimondott szöveg.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Válassza a **+ Létrehozás**elemet.

1. Az előugró párbeszédpanelen írja be a `MoveEmployeeToCity` karakterláncot, majd válassza a **Kész** elemet.

    > [!div class="mx-imgBorder"]
    > ![Új szándék létrehozása párbeszédpanel képernyőképe](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Vegyen fel több példát arra a célra, amelyet a felhasználó hosszúságú kimondott szöveg vár.

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
    > ![Képernyőkép a LUIS-ról, új kimondott szövegek a MoveEmployee szándékban](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Előre összeépített entitás geographyV2 hozzáadása

Az előre elkészített entitás, **geographyV2**, Kinyeri a hely adatait, beleértve a városok nevét. Mivel a hosszúságú kimondott szöveg két városi névvel rendelkeznek, a kontextusban egymással kapcsolatban, a szerepkörök használatával kinyerheti ezt a környezetet.

1. Válassza ki az **entitásokat** a bal oldali navigációs sávon.

1. Válassza az **+ előre összeépített entitás hozzáadása**lehetőséget, majd írja be `geo` a keresési sávban az előre elkészített entitások szűréséhez.

    > [!div class="mx-imgBorder"]
    > ![Előre elkészített geographyV2-entitás hozzáadása az alkalmazáshoz](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Jelölje be a jelölőnégyzetet, majd válassza a **kész**lehetőséget.

## <a name="add-roles-to-prebuilt-entity"></a>Szerepkörök hozzáadása előre felépített entitáshoz

1. Az **entitások** listában válassza ki a **geographyV2** az új entitás megnyitásához.
1. Szerepkör hozzáadásához válassza ki **+** és adja hozzá a következő két szerepkört `Origin`: és `Destination`.

    > [!div class="mx-imgBorder"]
    > ![Szerepkörök hozzáadása előre felépített entitáshoz](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

## <a name="label-entity-roles-in-example-utterances"></a>Az entitás szerepköreinek címkézése például hosszúságú kimondott szöveg

1. A bal oldali navigációs sávon válassza a **leképezések** lehetőséget, majd válassza ki a **MoveEmployeeToCity** szándékát. Figyelje meg, hogy a város neve az előre elkészített entitás **geographyV2**van megjelölve.
1. A helyi eszköztáron válassza az **entitás palettát** a _ceruza ikonnal_.

    > [!div class="mx-imgBorder"]
    > ![Válassza ki az Entity paletta elemet a tartalom eszköztárból](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. Válassza ki az előre elkészített entitást, **geographyV2**, majd válassza ki az **entitás-felügyelőt**.
1. Az **entitás-felügyelőben**válassza ki az egyik szerepkört, a **célhelyet**. Ez megváltoztatja az egér kurzorát. A kurzor használatával címkézheti a célhelyen lévő összes hosszúságú kimondott szöveg szövegét.

    > [!div class="mx-imgBorder"]
    > ![Szerepkör kiválasztása az entitások palettáján](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. Térjen vissza az **entitás-felügyelőhöz**, váltson a **forrásra**a szerepkörre. A kurzor használatával címkézze fel a szöveget az összes olyan hosszúságú kimondott szöveg, amely a forrás helyét jelöli.

## <a name="add-example-utterances-to-the-none-intent"></a>Példa hosszúságú kimondott szöveg hozzáadása a none szándékhoz

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanítása, hogy tesztelni lehessen a szándék változásait

Az alkalmazás betanításához válassza a **betanítás**lehetőséget. A képzés alkalmazza a módosításokat, például az új entitásokat és a címkével ellátott hosszúságú kimondott szöveg az aktív modellre.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Az alkalmazás közzététele a HTTP-végpontról való hozzáféréshez

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]


## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Cél-és entitás-előrejelzés beolvasása a végpontról

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]


1. Lépjen az URL-cím végére a címsorban, és cserélje le a `Please move Carl Chamerlin from Tampa to Portland` _YOUR_QUERY_HEREt_ a következőre:.

Ez a Kimondás nem ugyanaz, mint a címkézett hosszúságú kimondott szöveg, ezért jó teszt, és vissza kell térnie `MoveEmployee` a kinyert entitás szándékával.

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

    The correct intent is predicted and the entities array has both the origin and destination roles in the corresponding **entities** property.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Entitások – fogalmak](luis-concept-entity-types.md)
* [Szerepkörök – fogalmak](luis-concept-roles.md)
* [Előre összeépített entitások listája](luis-reference-prebuilt-entities.md)
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)
* [Szerepkörök](luis-concept-roles.md)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag egy új szándékot hozott létre, és a forrás-és célhelyek kontextusban megismert hosszúságú kimondott szöveg kapcsolatos példát vett fel. Amint megtörtént az alkalmazás betanítása és közzététele, az ügyfélalkalmazások felhasználhatják az adott információt a megfelelő információt tartalmazó mozgatási jegy létrehozásához.

> [!div class="nextstepaction"]
> [Ismerkedés az összetett entitások hozzáadásának módjával](luis-tutorial-composite-entity.md)
