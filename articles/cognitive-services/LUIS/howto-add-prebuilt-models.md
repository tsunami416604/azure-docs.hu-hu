---
title: Előre elkészített modellek a Language Understandinghoz
titleSuffix: Azure Cognitive Services
description: A LUIS előre elkészített modelleket tartalmaz, amelyekkel gyorsan hozzáadhat gyakori, társalgási felhasználói forgatókönyveket.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 2e90a5f3f7cd2cfde2a2ead26674d2348a24ae6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507780"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Előre összeépített modellek hozzáadása általános használati forgatókönyvekhez 

A LUIS az előre elkészített tartományokból álló előre összeállított leképezéseket tartalmaz, amelyekkel gyorsan hozzáadhat közös szándékokat és hosszúságú kimondott szöveg. Ezzel a módszerrel gyorsan és egyszerűen hozzáadhat képességeket a társalgási ügyfélalkalmazás számára anélkül, hogy a modelleket meg kellene terveznie az adott képességekhez. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-a-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása

1. A **saját alkalmazások** lapon válassza ki az alkalmazást. Ekkor megnyílik az alkalmazás az alkalmazás **Build** szakasza. 

1. A **leképezések** lapon válassza az **előre elkészített tartományok hozzáadása** lehetőséget az alsó, bal oldali eszköztáron. 

1. Válassza ki a **Naptár** szándékát, majd kattintson a **tartomány hozzáadása** gombra.

    ![Előre összeépített naptári tartomány hozzáadása](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. A naptári leképezések megtekintéséhez válassza a bal oldali navigációs sarokban található **leképezések** lehetőséget. A tartomány minden szándéka `Calendar.`-előtaggal van ellátva. A hosszúságú kimondott szöveg együtt a tartomány két entitása is hozzá lesz adva az alkalmazáshoz: `Calendar.Location` és `Calendar.Subject`. 

### <a name="train-and-publish"></a>Betanítás és közzététel

1. A tartomány hozzáadása után betaníthatja az alkalmazást a felső, a jobb oldali eszköztáron a **betanítás lehetőség kiválasztásával** . 

1. A felső eszköztáron válassza a **Közzététel**lehetőséget. Közzététel **éles**környezetben. 

1. Amikor megjelenik a zöld sikerre vonatkozó értesítés, válassza a végpontok **listájának** hivatkozását a végpontok megtekintéséhez.

1. Válasszon ki egy végpontot. Megnyílik egy új böngésző lap a végponthoz. Tartsa megnyitva a böngésző fület, és folytassa a **tesztelési** szakasszal.

### <a name="test"></a>Tesztelés

Tesztelje az új leképezést a végponton úgy, hogy hozzáadott egy értéket a **q** paraméterhez: `Schedule a meeting with John Smith in Seattle next week`.

LUIS a megfelelő szándékot és értekezlet tárgyát adja vissza:

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="add-a-prebuilt-intent"></a>Előre összeépített leképezés hozzáadása

1. A **saját alkalmazások** lapon válassza ki az alkalmazást. Ekkor megnyílik az alkalmazás az alkalmazás **Build** szakasza. 

1. A **szándékok** lapon válassza az **előre összeépített leképezés hozzáadása** lehetőséget az eszköztáron a cél lista fölött. 

1. Válassza ki a **segédprogramokat.** a felugró párbeszédpanelen törölje a szándékot. 

    ![Előre összeépített leképezés hozzáadása](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Kattintson a **kész** gombra.

### <a name="train-and-test"></a>Betanítás és tesztelés

1. A szándék hozzáadása után betaníthatja az alkalmazást úgy, hogy **kiválasztja a betanítás lehetőséget** a felső, jobb oldali eszköztáron. 

1. Tesztelje az új szándékot a jobb oldali eszköztáron a **teszt** lehetőség kiválasztásával. 

1. A szövegmezőbe írja be a hosszúságú kimondott szöveg a megszakításhoz:

    |Teszt kimondása|Előrejelzési pontszám|
    |--|:--|
    |Szeretném megszakítani a repülést.|0,67|
    |A vásárlás megszakítása.|0,52|
    |Az értekezlet megszakítása.|0,56|

    ![Előre összeépített leképezés tesztelése](./media/luis-prebuilt-intents/test.png)

## <a name="add-a-prebuilt-entity"></a>Előre összeépített entitás hozzáadása

1. Nyissa meg az alkalmazást a saját **alkalmazások** lapján a nevére kattintva, majd kattintson a bal oldalon található **entitások** elemre. 

1. Az **entitások** lapon kattintson az **előre elkészített entitás hozzáadása**lehetőségre.

1. Az **előre elkészített entitások hozzáadása** párbeszédpanelen válassza ki az datetimeV2 előre elkészített entitást. 

    ![Előre összeépített entitás hozzáadása párbeszédpanel](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Válassza a **Done** (Kész) lehetőséget. Az entitás hozzáadása után nem kell betanítania az alkalmazást. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Közzététel az előre elkészített modell előrejelzési végpontból való megtekintéséhez

Egy előre elkészített modell értékének a legegyszerűbb módja a közzétett végpontról történő lekérdezés. 

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Előre elkészített entitás-tokent tartalmazó entitások megjelölése
 Ha olyan szöveggel rendelkezik, mint például a `HH-1234`, amelyet egyéni entitásként szeretne megjelölni, _és_ a modellhez hozzáadott előre összeállított [számmal](luis-reference-prebuilt-number.md) rendelkezik, akkor nem fogja tudni megjelölni az egyéni entitást a Luis-portálon. Ezt megteheti az API-val. 

 Az ilyen típusú tokenek megjelöléséhez, ahol egy része már előre elkészített entitásként van megjelölve, távolítsa el az előre elkészített entitást a LUIS alkalmazásból. Nem kell betanítania az alkalmazást. Ezután jelölje meg a tokent a saját egyéni entitásával. Ezután adja hozzá az előre elkészített entitást a LUIS-alkalmazáshoz.

 Egy másik példaként tekintse meg az osztály-beállítások listáját: `I want first year spanish, second year calculus, and fourth year english lit.`, ha a LUIS-alkalmazáshoz hozzáadva az előépítés sorszáma, `first`, `second`, és a `fourth` már sorszámmal van megjelölve. Ha rögzíteni szeretné a sorszámot és az osztályt, létrehozhat egy összetett entitást, és becsomagolhatja az előre elkészített sorszám és az egyéni entitás köré az osztálynév alapján.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Modell létrehozása a. csv-ből REST API-kkal](./luis-tutorial-node-import-utterances-csv.md)
