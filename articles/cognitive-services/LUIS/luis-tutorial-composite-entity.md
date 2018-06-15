---
title: Bontsa ki az összetett adatokat - Azure összetett entitás létrehozása |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy összetett entitást az Entitásadatok különböző típusú kibontásához LUIS alkalmazás.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: 5e4d5a03db6210c159227530b94fbde6873bc211
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "35349950"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>Összetett entitást használja az összetett adatok kinyerése érdekében
Az egyszerű alkalmazás két tartozik [leképezések](luis-concept-intent.md) és több entitásokat. Feladata, például az "1 jegy a budapesti péntekén Kairó" járatok foglalható le, és adja vissza egy egyetlen adat, a foglalási adatok megírását. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
* Adja hozzá az előre elkészített entitások datetimeV2 és száma
* Hozzon létre egy összetett entitást
* Lekérdezési LUIS és összetett entitást adatok fogadására

## <a name="before-you-begin"></a>Előkészületek
* Az LUIS alkalmazását a  **[hierarchikus gyors üzembe helyezés](luis-tutorial-composite-entity.md)**. 

> [!Tip]
> Ha még nem rendelkezik előfizetéssel, akkor a regisztrálhatja a [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="composite-entity-is-a-logical-grouping"></a>Összetett logikai jellegű csoportosítását 
Az entitás célja található, és kategorizálása a szöveget a utterance részeit. A [összetett](luis-concept-entity-types.md) entitás más megtanulta, a kontextusból származó név entitástípusok áll. Utazás alkalmazás, amely repülési foglalásokat a rendszer több adatra dátumok, a helyek és a helyek számát. 

Az információk létezik-e külön entitásokként összetett létrehozása előtt. Hozzon létre egy összetett entitást, ha a különálló entitások logikailag csoportosíthatók, és ez a logikai csoportosítás akkor hasznos, a chatbot vagy más LUIS-igényes alkalmazáshoz. 

A felhasználók egyszerű példa utterances a következők:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
Az összetett entitást ülések száma, a származási helye, a célhely és a dátum megegyezik. 

## <a name="what-luis-does"></a>LUIS funkciója
Ha a szándéka és entitásai számára a utterance azonosít, [kibontott](luis-concept-data-extraction.md#list-entity-data), és visszahelyezi a JSON a [végpont](https://aka.ms/luis-endpoint-apis), LUIS történik. A hívó alkalmazás vagy chatbot időt vesz igénybe, hogy a JSON-választ, és a kérelem megfelel, – bármilyen módon az alkalmazás vagy chatbot célja, hogy tegye. 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>Előre elkészített entitások száma és datetimeV2 hozzáadása
1. Válassza ki a `MyTravelApp` alkalmazást, az alkalmazások listáját a [LUIS] [ LUIS] webhelyet.

2. Amikor megnyitja az alkalmazást, válassza ki a **entitások** bal oldali navigációs hivatkozás.

    ![Válassza ki az entitások gomb](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. Válassza ki **előre elkészített entitások kezelése**.

    ![Válassza ki az entitások gomb](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. Az előugró ablakban jelölje ki **szám** és **datetimeV2**.

    ![Válassza ki az entitások gomb](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. Válassza ki ahhoz, hogy az új entitások kinyerni, **vonat** a felső navigációs sávban.

    ![Válassza ki a vonat gomb](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>Összetett entitást létrehozásához használja a meglévő leképezést
1. Válassza ki **leképezések** a bal oldali navigációs sávon. 

    ![Válassza ki a leképezések lap](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. Válassza ki `BookFlight` a a **leképezések** listája.  

    ![Válassza ki a listából BookFlight leképezés](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    A szám és datetimeV2 előre elkészített entitások a utterances a tartalma.

3. A utterance a `book 2 flights from seattle to cairo next monday`, válassza ki a kék `number` entitás, majd válassza ki **összetett entitásban burkolása** a listából. Egy zöld sor alatt a szavakat a kurzor követi, jobbra, egy összetett entitást jelző átvitel során. Helyezze át a jogot, hogy válassza ki a legutóbbi előre elkészített entitást `datetimeV2`, majd adja meg `FlightReservation` a szövegmezőben az előugró ablak, majd válassza ki **hozzon létre új összetett**. 

    ![A leképezések lapon összetett entitás létrehozása](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. Egy felugró párbeszédpanel jelenik meg, hogy lehetővé teszi az összetett entitást gyermekek ellenőrzése. Válassza ki **végzett**.

    ![A leképezések lapon összetett entitás létrehozása](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>Az entitások burkolása az összetett entitás
Az összetett entitás létrehozása után címkével a többi utterances az összetett entitásban. Jelölje ki a bal szélső szót, majd válassza ki kell ahhoz, hogy burkolása összetett egységként kifejezés, **összetett entitásban burkolása** a listából, amely akkor jelenik meg, majd jelölje ki a jobb szélső szót, majd jelölje ki a névvel rendelkező összetett entitás `FlightReservation`. Ez az beállításokat, az alábbi lépéseket bontásban gyors, fokozatos lépését:

1. Az a utterance `schedule 4 seats from paris to london for april 1`, válassza a 4 számú előre elkészített egységként.

    ![Válassza ki a bal szélső word](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. Válassza ki **összetett entitásban burkolása** a listából, amely akkor jelenik meg.

    ![Jelölje be sortörés következik a listáról](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. Jelölje ki a jobb szélső szót. Ekkor megjelenik egy zöld sor alapján a kifejezést jelzi egy összetett entitást.

    ![Válassza ki a jobb szélső word](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. Jelölje be összetett név `FlightReservation` a listából, amely akkor jelenik meg.

    ![Válassza ki a névvel rendelkező összetett entitás](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    Az utolsó utterance burkolása `London` és `tomorrow` ugyanezen utasításokat követve az összetett entitást. 

## <a name="train-the-luis-app"></a>A LUIS app képzése
LUIS a módosítások nem ismeri a leképezések és entitások (a modell), amíg be van tanítva. 

1. A jobb felső részén a LUIS webhelye, jelölje ki a **vonat** gombra.

    ![Az alkalmazás képzése](./media/luis-tutorial-composite-entity/train-button.png)

2. Képzési befejeződött, amikor megjelenik a zöld állapotsor sikeres erősítse meg a webhely elején.

    ![Képzési sikeres volt.](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Tegye közzé az alkalmazást, az a végpont URL-cím beszerzése
Ahhoz, hogy egy chatbot vagy más alkalmazásban LUIS előrejelzéshez beszerzéséhez kell tegye közzé az alkalmazást. 

1. A jobb felső részén a LUIS webhelye, jelölje ki a **közzététel** gombra. 

2. Válassza ki az éles tárhely és a **közzététel** gombra.

    ![Alkalmazás közzététele](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. A közzététel befejeződött, amikor megjelenik a zöld állapotsor sikeres erősítse meg a webhely elején.

## <a name="query-the-endpoint-with-a-different-utterance"></a>A végpont egy másik utterance lekérdezése
1. Az a **közzététel** lapon jelölje be a **végpont** hivatkozás az oldal alján. Ez a művelet egy másik böngészőablakban megnyitja a címsorba a végpont URL-címet. 

    ![Válassza ki a végpont URL-címe](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. Nyissa meg az URL-címet a cím végén, és adja meg `reserve 3 seats from London to Cairo on Sunday`. Az utolsó lekérdezési karakterlánc paraméter `q`, a utterance lekérdezés. A utterance nem ugyanaz, mint a címkézett utterances bármelyikét, hogy azok egy jó teszt, és térjen vissza a `BookFlight` leképezési kibontott hierarchikus entitással.

```
{
  "query": "reserve 3 seats from London to Cairo on Sunday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.999999046
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.999999046
    },
    {
      "intent": "None",
      "score": 0.227036044
    }
  ],
  "entities": [
    {
      "entity": "sunday",
      "type": "builtin.datetimeV2.date",
      "startIndex": 40,
      "endIndex": 45,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-03-25"
          },
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-04-01"
          }
        ]
      }
    },
    {
      "entity": "3 seats from london to cairo on sunday",
      "type": "flightreservation",
      "startIndex": 8,
      "endIndex": 45,
      "score": 0.6892485
    },
    {
      "entity": "cairo",
      "type": "Location::Destination",
      "startIndex": 31,
      "endIndex": 35,
      "score": 0.557570755
    },
    {
      "entity": "london",
      "type": "Location::Origin",
      "startIndex": 21,
      "endIndex": 26,
      "score": 0.8933808
    },
    {
      "entity": "3",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "3"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "flightreservation",
      "value": "3 seats from london to cairo on sunday",
      "children": [
        {
          "type": "builtin.datetimeV2.date",
          "value": "sunday"
        },
        {
          "type": "Location::Destination",
          "value": "cairo"
        },
        {
          "type": "builtin.number",
          "value": "3"
        },
        {
          "type": "Location::Origin",
          "value": "london"
        }
      ]
    }
  ]
}
```

A utterance adja vissza egy összetett entitások tömb többek között a **flightreservation** objektum kivonatolt adatokon.  

## <a name="what-has-this-luis-app-accomplished"></a>Mi van az LUIS alkalmazás érhető el?
Az alkalmazáshoz, csak két leképezések és egy összetett entitást egy természetes nyelvű lekérdezés szándékát azonosítható, és a kibontott adatokat adott vissza. 

A chatbot most már elegendő információt az elsődleges műveletek meghatározására `BookFlight`, és a utterance található foglalási információkat. 

## <a name="where-is-this-luis-data-used"></a>Hol használják a LUIS ezeket az adatokat? 
Az ehhez a kérelemhez LUIS végezhető el. A hívó alkalmazás, például egy chatbot is igénybe vehet, topScoringIntent eredménye és az adatok az entitás a következő lépésre. LUIS nem végez a botot vagy a hívó alkalmazás programozott munka. LUIS csak határozza meg, mi az a felhasználó szándékát. 

## <a name="next-steps"></a>További lépések

[További tudnivalók az entitások](luis-concept-entity-types.md). 

<!--References-->
[LUIS]:luis-reference-regions.md#luis-website
[LUIS-regions]:luis-reference-regions.md#publishing-regions
