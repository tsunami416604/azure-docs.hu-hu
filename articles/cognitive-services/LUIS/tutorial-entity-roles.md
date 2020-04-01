---
title: 'Oktatóanyag: Környezetfüggő adatok szerepkörökkel – LUIS'
description: Kapcsolódó adatok keresése a környezet alapján. Kapcsolódó lehet például az indulási hely és a cél egy épületből és irodából egy másik épületbe és irodába történő fizikai mozgás során.
ms.topic: tutorial
ms.date: 03/30/2020
ms.openlocfilehash: fdb463896e531619ea7ebe7c384729763dc84138
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475823"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Oktatóanyag: Környezetfüggőadatok kinyerése egy utterance (kifejezés)

Ebben az oktatóanyagban kapcsolódó adatrészleteket keresünk szövegkörnyezet alapján. Például az egyik városból a másikba történő átvitel kiindulási és célhelyei. Mindkét adatra szükség lehet, és ezek kapcsolódnak egymáshoz.

A szerepkör bármely előre összeállított vagy egyéni entitástípussal használható, és példakimondott szövegben és mintázatban is használható.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Új alkalmazás létrehozása
> * Szándék hozzáadása
> * A kezdő- és céladatok beszereznie szerepkörökkel
> * Betanítás
> * Közzététel
> * Leképezések és entitásszerepkörök beszereznie a végpontból

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Kapcsolódó adatok

Ez az alkalmazás határozza meg, hogy egy alkalmazottat hol kell áthelyezni a származási városból a célvárosba. Egy GeographyV2 előre összeállított entitást használ a városnevek azonosítására, és szerepköröket használ a helytípusok (eredet és cél) meghatározására az utterance (o. utterance (o.

Szerepkört kell használni, amikor az entitás adatok kibontása:

* Egymáshoz kapcsolódik az utterance (kifejezés) összefüggésében.
* Az egyes szerepkörek jelzésére adott szóválasztási lehetőséget használ. Ilyen szavak például: innen/oda, elhagyja/felé tart, távolodik/közeledik.
* Mindkét szerepkör gyakran ugyanabban az utterance (kifejezés), amely lehetővé teszi a LUIS tanulni ebből a gyakori környezetfüggő használat.
* Csoportosítását és feldolgozását az ügyfélalkalmazásoknak egy információegységként kell végezniük.

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

1. Jelentkezzen be a [LUIS **előzetes portáljára.** ](https://preview.luis.ai)

1. Válassza ki a + Új `HumanResources` alkalmazás **beszélgetés**, adja meg a nevét, és tartsa meg az alapértelmezett kultúra, **angol**. Hagyja üresen a leírás és az előrejelzési erőforrást. Válassza a **Done** (Kész) lehetőséget.

## <a name="create-an-intent-to-move-employees-between-cities"></a>Az alkalmazottak városok közötti áthelyezésére irányuló szándék létrehozása

A szándék a felhasználó kimondott szövegének a felhasználó szándéka alapján történő osztályozására szolgál, amelyet a természetes nyelvi szöveg határoz meg.

Annak érdekében, hogy egy utterance (kifejezés) besorolásához a szándék példákat a felhasználói utterances, amely et kell osztályozni ezzel a szándékkal.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Válassza **a + Create**lehetőséget.

1. Az előugró párbeszédpanelen írja be a `MoveEmployeeToCity` karakterláncot, majd válassza a **Kész** elemet.

    > [!div class="mx-imgBorder"]
    > ![Új szándék létrehozása párbeszédpanel képernyőképe](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Adjon hozzá több példa utterances ezt a szándékot, amely várhatóan egy felhasználó megkérdezi.

    |Példák kimondott szövegekre|
    |--|
    |mozog John W. Smith elhagyja Seattle élén Orlando|
    |Jill Jones áthelyezése Seattle-ből Kairóba|
    |Hely John Jackson távol Tampa, jön Atlanta |
    |debra Doughtery-t Chicagóból Tulsába költöztetik|
    |mv Jill Jones elhagyja Kairó élén Tampa|
    |Shift Alice Anderson oaklandi Redmond|
    |Carl Chamerlin San Franciscóból Redmondba|
    |Transzfer Steve Standish a San Diego felé Bellevue |
    |lift Tanner Thompson Kansas City és a váltás Chicago|

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a LUIS-ról, új kimondott szövegek a MoveEmployee szándékban](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Előre összeállított entitás földrajzi v2 hozzáadása

Az előre összeállított entitás, **geographyV2**, kinyeri a helyadatokat, beleértve a városneveket is. Mivel a kimondott szöveg két városnév, egymáshoz kapcsolódó környezetben, szerepkörök használatával kibontja a környezetben.

1. Válassza az **Entitások lehetőséget** a bal oldali navigációs sávon.

1. Válassza **a + Add prebuilt entitáslehetőséget**, majd írja be `geo` a keresősávba az előre összeállított entitások szűréséhez.

    > [!div class="mx-imgBorder"]
    > ![GeographyV2 előre összeállított entitás hozzáadása az alkalmazáshoz](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Jelölje be a jelölőnégyzetet, és válassza **a Kész**lehetőséget.

## <a name="add-roles-to-prebuilt-entity"></a>Szerepkörök hozzáadása előre összeállított entitáshoz

1. Az **Entitások** listában válassza ki a **geographyV2** elemet az új entitás megnyitásához.
1. Szerepkör hozzáadásához jelölje **+** ki és vegye `Origin`fel `Destination`a következő két szerepkört: . és .

    > [!div class="mx-imgBorder"]
    > ![Szerepkörök hozzáadása előre összeállított entitáshoz](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

## <a name="label-entity-roles-in-example-utterances"></a>Entitásszerepkörök címkézése példakimondott szövegben

1. Válassza **a Leképezések** lehetőséget a bal oldali navigációs sávon, majd válassza a **MoveEmployeeToCity** leképezést. Figyelje meg, hogy a városnevek az előre elkészített entitás **földrajzv2**címkével vannak ellátva.
1. A context eszköztáron válassza az **Entitás palettát** a _ceruza ikonnal_.

    > [!div class="mx-imgBorder"]
    > ![Entitáspaletta kiválasztása a tartalomeszköztárból](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. Válassza ki az előre összeállított entitást, **a geographyV2**elemet, majd válassza ki az **Entitásfelügyelőt.**
1. Az **Entitásfelügyelőben**válasszon ki egy szerepkört, **a Cél**lehetőséget. Ez megváltoztatja az egérmutatót. A kurzor segítségével címkézze a szöveget az összes utterances, amely a cél helyét.

    > [!div class="mx-imgBorder"]
    > ![Szerepkör kiválasztása az entitáspalettában](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. Visszatérés az **Entitásfelügyelőhöz**, módosítsa a szerepkört **Originre.** A kurzor segítségével címkézze a szöveget az összes kimondott szöveg, amely a kiindulási hely.

## <a name="add-example-utterances-to-the-none-intent"></a>Példakimondott szöveg hozzáadása a Nincs szándékhoz

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanítása a szándék módosításai tesztelése érdekében

Az alkalmazás betanításához válassza a **Vonat**lehetőséget. A betanítás a módosításokat, például az új entitásokat és a címkézett utterances az aktív modell.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Az alkalmazás közzététele a HTTP-végpontról való eléréséhez

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]


## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Leképezés és entitás-előrejelzés beszerezni a végponttól

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]


1. Lépjen az URL-cím végére a címsorban, és cserélje le _YOUR_QUERY_HERE_ a gombra. `Please move Carl Chamerlin from Tampa to Portland`

Ez az utterance (kifejezés) nem ugyanaz, mint a címkézett utterances, így ez egy jó teszt, és vissza kell adnia a `MoveEmployee` szándékot az entitás kibontott.

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

* [Entitások fogalmai](luis-concept-entity-types.md)
* [Szerepkörök fogalmai](luis-concept-roles.md)
* [Előre összeállított entitások listája](luis-reference-prebuilt-entities.md)
* [Hogyan kell a vonat](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)
* [Szerepkörök](luis-concept-roles.md)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag létrehozott egy új szándékot, és hozzáadott példa utterances a contextually megtanult adatok a származási és a célhelyek. Amint megtörtént az alkalmazás betanítása és közzététele, az ügyfélalkalmazások felhasználhatják az adott információt a megfelelő információt tartalmazó mozgatási jegy létrehozásához.

> [!div class="nextstepaction"]
> [Ismerkedés az összetett entitások hozzáadásának módjával](luis-tutorial-composite-entity.md)
