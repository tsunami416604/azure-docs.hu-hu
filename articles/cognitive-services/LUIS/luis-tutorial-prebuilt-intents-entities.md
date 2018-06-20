---
title: Adja hozzá a közös nyelvi ismertetése - Azure adatok kibontásához előre elkészített leképezések és entitások |} Microsoft Docs
description: Útmutató előre elkészített leképezések és entitások Entitásadatok különböző típusú kibontásához.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 06/11/2018
ms.author: v-geberr
ms.openlocfilehash: 20950ced66497fb0dc96365975b37f244f677ce3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266379"
---
# <a name="use-prebuilt-intents-and-entities-to-handle-common-intents-and-data"></a>Előre elkészített leképezések és entitások használata közös leképezések és adatok kezelése
Előre elkészített leképezések és entitások hozzáadása az emberi erőforrások gyors üzembe helyezés alkalmazás leképezési előrejelzés és az adatok kinyerése gyors eléréséhez. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
* Előre elkészített leképezések hozzáadása 
* Adja hozzá az előre elkészített entitások datetimeV2 és száma
* Betanítása és közzététele
* LUIS lekérdezése, és előrejelzéses válaszideje

## <a name="before-you-begin"></a>Előkészületek
Ha nem rendelkezik az emberi erőforrások alkalmazásból a [egyéni tartomány](luis-quickstart-intents-only.md) gyors üzembe helyezés, [importálása](create-new-app.md#import-new-app) a JSON-kódot az új alkalmazás a [LUIS] [ LUIS] webhely , a a [LUIS-minták](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) Github-tárházban.

Ha meg szeretné tartani az eredeti HR-alkalmazást, a verzió klónozza a a [beállítások](luis-how-to-manage-versions.md#clone-a-version) lapon, és adjon neki nevet `prebuilts`. A Klónozás használata számára, hogy a különböző LUIS szolgáltatásai nem befolyásolja az eredeti verzió kiváló módja. 

## <a name="add-prebuilt-intents"></a>Előre elkészített leképezések hozzáadása
LUIS nyújt segítséget az általános felhasználói céljaira számos előre elkészített leképezések.  

1. Győződjön meg arról, hogy az alkalmazás megtalálható a **Build** LUIS szakasza. Ez a szakasz a választásával módosíthatja **Build** felső, jobb gombbal az egérrel a menüsoron. 

    [ ![Képernyőfelvétel a LUIS app Build hightlighted felső, jobb oldali navigációs sávon a](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. Válassza ki **előre elkészített tartomány leképezés hozzáadása**. 

    [ ![Képernyőfelvétel a leképezések lap Hozzáadás előre elkészített tartomány leképezési gomb](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. Keresse meg `Utilities`. 

    [ ![A keresőmezőbe segédprogramok előre elkészített leképezések párbeszédpanel képernyőképe](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Válassza ki a következő leképezések, majd **végzett**: 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.Stop
    * Utilities.StartOver

## <a name="add-prebuilt-entities"></a>Adja hozzá az előre elkészített entitások
LUIS számos előre elkészített entitásokat biztosít a közös adatok kinyerése. 

1. Válassza ki **entitások** bal oldali navigációs menüjében.

    [ ![Képernyőfelvétel a leképezések lista kiemeli a bal oldali navigációs entitások](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Válassza ki **előre elkészített entitások kezelése** gombra.

    [ ![Képernyőkép az entitások lista előre elkészített entitások kiemelt kezelése](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. Válassza ki **szám** és **datetimeV2** az előre elkészített entitások listájából válassza ki **végzett**.

    ![Szám válassza ki az előre elkészített entitások párbeszédpanel képernyőképe](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>A vonat és az alkalmazás közzététele
1. A jobb felső részén a LUIS webhelye, jelölje ki a **vonat** gombra. 

    ![Vonat gomb](./media/luis-quickstart-intents-only/train-button.png)

    Képzési befejeződött, amikor megjelenik a zöld állapotsor sikeres erősítse meg a webhely elején.

    ![Betanított állapotsor](./media/luis-quickstart-intents-only/trained.png)

2. A felső, jobb oldalán a LUIS webhelyen, válassza ki a **közzététel** gombra kattintva jelenítse meg a közzététele. Az éles tárolóhelyre alapértelmezettként van beállítva. Válassza ki a **közzététel** által az éles tárhely választott gombra. A közzététel befejeződött, amikor megjelenik a zöld állapotsor sikeres erősítse meg a webhely elején.

    Nem kell létrehoznia egy LUIS kulcsot az Azure portálon közzététele előtt, vagy a végpont URL-cím tesztelése előtt. Minden LUIS app tartalomkészítéshez szabad alapszintű kulccsal rendelkezik. Biztosít korlátlan szerzői és egy [néhány végpont találatok](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-with-an-utterance"></a>Egy utterance lekérdezés végpont
Az a **közzététel** lapon jelölje be a **végpont** hivatkozás az oldal alján. Ez a művelet egy másik böngészőablakban megnyitja a címsorba a végpont URL-címet. Nyissa meg az URL-címet a cím végén, és adja meg `I want to cancel on March 3`. Az utolsó lekérdezési karakterlánc-paraméter `q`, a utterance **lekérdezés**. 

Az eredmény előre jelezni az Utilities.Cancel leképezés, és a 3. március dátumát és 3-as számú kibontott. 

    ```
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

Egyszerűen és gyorsan hozzá előre elkészített leképezések és entitások, az ügyfélalkalmazás beszélgetés felügyeleti hozzáadhat és közös adattípusok kibontásához. 

## <a name="next-steps"></a>További lépések

[További tudnivalók az entitások](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
