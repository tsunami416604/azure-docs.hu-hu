---
title: Entitás létrehozása egy összetett bontsa ki az összetett adatokat – Azure |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre összetett entitásokat a különböző típusú entitások adatainak kinyeréséhez a LUIS-alkalmazás.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: 375b52f9206f55e620d5e664844b8fa1d7249a07
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888745"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>Összetett entitást használja az összetett adatokat nyerhet ki
Az egyszerű alkalmazás van két [leképezések](luis-concept-intent.md) és több entitásokat. Feladata, például az "1 jegy Seattle péntekjén Cairo" repülőjáratok foglalható le, és visszaad egy egyetlen adat, a Foglalás összes tulajdonságairól. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
* Előre összeállított entitások datetimeV2 és szám hozzáadása
* Összetett entitás létrehozása
* A LUIS lekérdezését, és összetett entitást adatfogadás

## <a name="before-you-begin"></a>Előkészületek
* A LUIS-alkalmazás, a  **[hierarchikus rövid](luis-tutorial-composite-entity.md)**. 

> [!Tip]
> Ha Ön még nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="composite-entity-is-a-logical-grouping"></a>Összetett entitás, logikai csoportosítása 
Az entitás célja, hogy megtalálja és kategorizálja a kimondott szöveg egyes részeit. A [összetett](luis-concept-entity-types.md) entitás más entitástípusok származó környezet áll. Az utazási alkalmazás, amely a flight foglalásokat például a dátumok, a helyek és a munkaállomások száma több adatra van. 

Az információk létezik külön entitásokként összetett létrehozása előtt. Hozzon létre egy összetett entitást, ha a különálló entitások logikailag csoportosíthatók, és ez a logikai csoportosítás akkor hasznos, a csevegőrobot, vagy más LUIS-igényes alkalmazások. 

Egyszerű példák felhasználók által kimondott szövegekre:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
Az összetett entitás munkaállomásszámot, a feladás helyét, a célhely és a dátum megegyezik. 

## <a name="what-luis-does"></a>A LUIS által elvégzett feladat
A LUIS akkor van készen, ha azonosította, [kinyerte](luis-concept-data-extraction.md#list-entity-data), és a [végpontról](https://aka.ms/luis-endpoint-apis) JSON-formátumban visszaadta a kimondott szöveg szándékát és entitásait. A hívó alkalmazás vagy a csevegőrobot fogadja a JSON-választ és teljesíti a kérést a kialakításának megfelelő módon. 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>Előre összeállított entitások száma és datetimeV2 hozzáadása
1. Válassza ki a `MyTravelApp` alkalmazást a listából az alkalmazások a [LUIS](luis-reference-regions.md#luis-website) webhelyén.

2. Amikor megnyitja az alkalmazást, válassza ki a **entitások** bal oldali navigációs hivatkozás.

    ![Válassza ki az entitásokat gomb](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. Válassza a **Manage prebuilt entities** (Előre összeállított entitások kezelése) lehetőséget.

    ![Válassza ki az entitásokat gomb](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. A legördülő mezőben válassza ki a **szám** és **datetimeV2**.

    ![Válassza ki az entitásokat gomb](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. Válassza ki ahhoz, hogy az új entitások kinyerni, **Train** a felső navigációs sávban.

    ![Train (Betanítás) gomb kiválasztása](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>Meglévő leképezés használatával összetett entitás létrehozása
1. Válassza ki **leképezések** a bal oldali navigációs sávon. 

    ![Válassza ki a leképezések lap](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. Válassza ki `BookFlight` származó a **leképezések** listája.  

    ![Válassza ki a listából BookFlight leképezés](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    A szám és datetimeV2 előre összeállított entitások a megcímkézzen vannak ellátva.

3. Az utterance (kifejezés) a `book 2 flights from seattle to cairo next monday`, válassza a kék `number` entitásra, majd válassza ki **Zabalit do összetett entitást** a listából. Egy zöld vonal alatt a szavakat a kurzor követi, a jobb oldalon, egy összetett entitást jelző mozgó. Helyezze át a jogot arra, hogy válassza ki az utolsó előre összeállított entitások `datetimeV2`, majd adja meg `FlightReservation` az előugró ablakban a szövegmezőbe, majd válassza ki **hozzon létre új összetett**. 

    ![A leképezések lapon összetett entitás létrehozása](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. Egy felugró párbeszédpanel jelenik meg, ahol ellenőrizheti a összetett entitást gyermekek. Válassza a **Done** (Kész) lehetőséget.

    ![A leképezések lapon összetett entitás létrehozása](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>Az entitások zabalit do az összetett entitás
Az összetett entitás létrehozása után a fennmaradó megcímkézzen az összetett entitásban címkét. Jelölje ki a bal szélső szót, majd válassza ki kell burkolása összetett egységként kifejezés, **Zabalit do összetett entitást** a megjelenő listában, majd jelölje ki a jobb szélső szót, majd válassza a névvel rendelkező összetett entitás `FlightReservation`. Ez a gyors és zökkenőmentes lépésében beállításokat is, a következő lépések szerinti bontásban:

1. Az utterance (kifejezés) a `schedule 4 seats from paris to london for april 1`, válassza a 4 számú előre elkészített entitásként.

    ![Válassza ki a bal szélső word](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. Válassza ki **Zabalit do összetett entitást** a listából, amely akkor jelenik meg.

    ![A listáról válassza sortörés](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. Válassza ki a jobb szélső szó. Zöld vonal a kifejezést jelzi egy összetett entitást alatt jelenik meg.

    ![Válassza ki a jobb szélső word](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. Válassza ki összetett név `FlightReservation` a listából, amely akkor jelenik meg.

    ![Névvel rendelkező összetett entitás kiválasztása](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    Az utolsó utterance (kifejezés), wrap `London` és `tomorrow` az összetett entitásban ugyanezen utasításokat követve. 

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása
Amíg nincs betanítva, a LUIS nem ismeri fel a szándékok és entitások (a modell) módosításait. 

1. A LUIS-webhely jobb felső részén kattintson a **Train** (Betanítás) gombra.

    ![Az alkalmazás betanítása](./media/luis-tutorial-composite-entity/train-button.png)

2. A betanítás akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

    ![A betanítás sikeres volt](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez
Ahhoz, hogy LUIS-előrejelzéseket kaphasson egy csevegőrobotban vagy más alkalmazásban, közzé kell tennie az alkalmazást. 

1. A LUIS-webhely jobb felső részén válassza a **Publish** (Közzététel) lehetőséget. 

2. Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra.

    ![alkalmazás közzététele](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. A közzététel akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

## <a name="query-the-endpoint-with-a-different-utterance"></a>A végpont lekérdezése egy másik kimondott szöveggel
1. A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. 

    ![Válassza ki a végpont URL-címe](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. Lépjen az URL-cím végéhez, és írja be a következőt: `reserve 3 seats from London to Cairo on Sunday`. Az utolsó lekérdezési karakterlánc paraméter `q`, az utterance (kifejezés) lekérdezést. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő szándékot kell visszaadnia a kinyert hierarchikus entitással: `BookFlight`.

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

Az utterance (kifejezés) adja vissza egy összetett entitások tömb többek között a **flightreservation** objektumot ki kell olvasni az adatokat.  

## <a name="what-has-this-luis-app-accomplished"></a>Milyen műveleteket végzett el a LUIS-alkalmazás?
Az alkalmazás, amely csak két leképezések és a egy összetett entitást azonosítani a természetes nyelvű lekérdezés szándékát, és a kinyert adatokat adott vissza. 

A csevegőrobot most már rendelkezik elegendő információt határozza meg az elsődleges művelet `BookFlight`, és a fenntartás információit az utterance (kifejezés). 

## <a name="where-is-this-luis-data-used"></a>Hol vannak használatban ezek a LUIS-adatok? 
A LUIS végzett ezzel a kéréssel. A hívó alkalmazás, például egy csevegőrobot, felhasználhatja a topScoringIntent eredményt és az entitás adatait a következő lépés végrehajtásához. A LUIS nem végzi el ezt a programozható munkát a csevegőrobotnak vagy a hívó alkalmazásnak. A LUIS csak azt határozza meg, hogy mi a felhasználó szándéka. 

## <a name="next-steps"></a>További lépések

[További információ az entitások](luis-concept-entity-types.md). 
