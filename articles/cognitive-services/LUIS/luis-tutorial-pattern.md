---
title: Az oktatóanyag minták segítségével javíthatja a LUIS-előrejelzés – Azure |} A Microsoft Docs
titleSuffix: Cognitive Services
description: Ebben az oktatóanyagban a leképezések mintájának használatával javíthatja a LUIS szándékot és egyéb entitások előrejelzéseket.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: 9ad1d9e1543c3d9a74025fb23bd1767478b53b4b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238454"
---
# <a name="tutorial-improve-app-with-patterns"></a>Oktatóanyag: Alkalmazás minták és javítása

Ebben az oktatóanyagban minták használatával növelheti a leképezés és egyéb entitások előrejelzési.  

> [!div class="checklist"]
* Hogyan azonosíthatja, hogy egy minta segíthet az alkalmazás
* A minta létrehozása 
* Azt, hogyan ellenőrizheti, mintát előrejelzési fejlesztései

Ehhez a cikkhez egy ingyenes [LUIS](luis-reference-regions.md)-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="before-you-begin"></a>Előkészületek
Ha nem rendelkezik az emberi erőforrások alkalmazásból a [batch teszt](luis-tutorial-batch-testing.md) az oktatóanyagban [importálása](luis-how-to-start-new-app.md#import-new-app) a JSON-kódot egy új alkalmazást a [LUIS](luis-reference-regions.md#luis-website) webhely. Az alkalmazás importálása megtalálható a [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json) GitHub-adattárban.

Ha meg szeretné tartani az eredeti Emberi erőforrások alkalmazást, klónozza a [Settings](luis-how-to-manage-versions.md#clone-a-version) (Beállítások) lapon a verziót, és adja neki a következő nevet: `patterns`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Minták tanít LUIS kevesebb példákkal közös kimondott szöveg
Az emberi erőforrás-tartomány jellege miatt a szervezetben alkalmazott kapcsolatok kapcsolatos néhány gyakori módon is. Példa:

```
Who does Jill Jones report to?
Who reports to Jill Jones? 
```

Ezek a kimondott szöveg szorosan határozzák meg az egyes nélkül számos utterance (kifejezés) példákkal környezetfüggő egyediségét. Ad hozzá egy minta megjelölésű, LUIS megtanulja megjelölésű közös utterance (kifejezés) minta számos utterance (kifejezés) Példák megadása nélkül. 

Példa a sablon utterances ezt szándék a következők:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

A minta példaképpen egy sablon utterance (kifejezés), beleértve a szintaxist, entitások és figyelmen kívül hagyható, szöveg van megadva. A reguláris kifejezések egyeztetésének és a machine learning egyik.  Az utterance (kifejezés) példasablonban szándék megcímkézzen, valamint egy jobb megértéséhez, hogy milyen kimondott szöveg igazítása a célt adjon meg a LUIS.

Ahhoz, hogy egy mintát egyeztetni kell az utterance (kifejezés) az entitások az utterance (kifejezés) kell először felel meg a sablon utterance (kifejezés) entitást. Azonban a sablon nem segít előre jelezni az entitásokat, csak a szándék fog vonatkozni. 

**Minták lehetővé teszik, hogy kevesebb példa kimondott szöveg, ha az entitások nem észlelt, miközben a mintázat nem felel meg.**

Ne feledje, hogy az alkalmazottak létrejöttek a [lista entitás oktatóanyag](luis-quickstart-intent-and-list-entity.md).

## <a name="create-new-intents-and-their-utterances"></a>Hozzon létre új leképezések és a kimondott szöveg
Két új leképezések hozzáadása: `OrgChart-Manager` és `OrgChart-Reports`. Miután a LUIS-előrejelzés az ügyfélalkalmazásnak a leképezés neve lehet használni a függvény nevét az ügyfélalkalmazásban, és, hogy a függvény paramétereként használható-e az alkalmazott entitás adja vissza.

```
OrgChart-Manager(employee){
    ///
}
```

1. Győződjön meg arról, hogy az Emberi erőforrások alkalmazás a LUIS **Build** (Létrehozás) szakaszában van. Ha erre a szakaszra szeretne lépni, válassza a jobb felső menüsávon a **Build** (Létrehozás) elemet. 

2. Az **Intents** (Szándékok) lapon válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

3. Az előugró párbeszédpanelen írja be a `OrgChart-Manager` karakterláncot, majd válassza a **Kész** elemet.

    ![Hozzon létre új üzenet előugró ablak](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |Az alárendelt, akik az W. Kovács János?|
    |Ki nem W. Kovács János jelentést?|
    |Akik az W. János manager?|
    |Akik Jill Jones közvetlenül jelentést készít a?|
    |Akik az Jill Jones felügyelő?|

    [![](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Képernyőkép a LUIS beszédszándék új beszédmódok hozzáadása")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Ne aggódjon, ha az alkalmazott entitás helyett a leképezés megcímkézzen a címkével ellátott keyPhrase entitás. Mindkét megfelelően előrejelzett, a teszt panelre, és a végponton. 

5. A bal oldali navigációs menüben válassza az **Intents** (Szándékok) lehetőséget.

6. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

7. Az előugró párbeszédpanelen írja be a `OrgChart-Reports` karakterláncot, majd válassza a **Kész** elemet.

8. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |Kik W. Kovács János a beosztottak?|
    |Akik jelentéseket W. Kovács János?|
    |Akik W. Kovács János kezelni?|
    |Kik Jill Jones közvetlen beosztottak?|
    |Akik Jill Jones felügyeletére?|

## <a name="caution-about-example-utterance-quantity"></a>Példa utterance (kifejezés) mennyiségére vonatkozó figyelmeztetés
Ezeket a leképezéseket a példa utterances mennyiségét már nem elég a LUIS megfelelően betanításához. Egy valós alkalmazásban minden egyes szándékot legalább 15 utterances számos választási lehetőség és az utterance (kifejezés) hossza a word kell rendelkeznie. Ezek néhány utterances ki van jelölve, kifejezetten a jelölje ki a mintákat. 

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása
Az új szándékot és a kimondott szöveg szükséges képzés. 

1. A LUIS-webhely jobb felső részén kattintson a **Train** (Betanítás) gombra.

    ![A Training (Betanítás) gomb képe](./media/luis-tutorial-pattern/hr-train-button.png)

2. A betanítás akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

    ![A sikerességet jelző értesítési sáv képe](./media/luis-tutorial-pattern/hr-trained-inline.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez
Ahhoz, hogy LUIS-előrejelzéseket kaphasson egy csevegőrobotban vagy más alkalmazásban, közzé kell tennie az alkalmazást. 

1. A LUIS-webhely jobb felső részén válassza a **Publish** (Közzététel) lehetőséget. 

2. Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra.

    [ ![Képernyőfelvétel a Közzététel lapot közzététel az éles tárhely gomb](./media/luis-tutorial-pattern/hr-publish-to-production.png)](./media/luis-tutorial-pattern/hr-publish-to-production.png#lightbox)

3. A közzététel akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

## <a name="query-the-endpoint-with-a-different-utterance"></a>A végpont lekérdezése egy másik kimondott szöveggel
1. A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. 

    [ ![Képernyőfelvétel a közzététel lap a végpont URL-Címének kiemelésével](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png)](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png#lightbox)


2. Lépjen az URL-cím végéhez, és írja be a következőt: `Who is the boss of Jill Jones?`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Ez a lekérdezés sikertelen volt? E képzési ciklusig az újbóli volt. A két leggyakoribb leképezések pontszámok bezárása. Mivel a LUIS képzési lehetőségek a lehetséges nem pontosan ugyanaz, minden alkalommal, van egy kis variation, két pontszámok sikerült megfordítása követő képzési ciklusban. Az eredménye, hogy a nem megfelelő leképezés sikerült visszaadni. 

Minták a megfelelő leképezés pontszám jelentősen nagyobb százalékban és segítségével a következő legnagyobb pontszámot távolabb kerül. 

## <a name="add-the-template-utterances"></a>A sablon beszédmódok hozzáadása

1. Válassza ki **összeállítása** a felső menüben.

2. A bal oldali navigációs területen **megnövelheti az alkalmazások teljesítményét**, jelölje be **minták** a bal oldali navigációs sávon.

3. Válassza ki a **szervezeti diagram – vezető** szándékot, majd adja meg a következő sablon megcímkézzen, egyenként meg kiválasztása után minden sablon utterance (kifejezés):

    |Sablon kimondott szöveg|
    |:--|
    |{Alkalmazott} [?] beosztottja ki|
    |Ki {alkalmazott} tesz jelentést [?]|
    |Akik az [?] [a] {alkalmazott} manager|
    |Ki {alkalmazott} tesz jelentést közvetlenül [?]|
    |Akik az [?] [a] {alkalmazott} felügyelő|
    |Akik az, hogy a főnök {alkalmazott} [?]|

    A `{Employee}` szintaxis jelöli meg a sablon utterance (kifejezés), valamint entitáshoz van az entitás helyére. 
    
    Szerepkörök rendelkező entitások szintaxist használja, amely tartalmazza a szerepkör nevét,, és a egy [szerepkörök külön oktatóanyag](luis-tutorial-pattern-roles.md). 

    A választható szintaxist `[]`, feldolgozottként jelöli meg a szavakat vagy absztrakt, amelyek nem kötelező. LUIS megegyezik az utterance (kifejezés), a rendszer figyelmen kívül hagyja a nem kötelező szöveg a zárójelek között lévő.

    Ha a sablon utterance (kifejezés), a LUIS segítségével, töltse ki az entitás a bal oldali kapcsos zárójel megadásakor `{`.

    [ ![Képernyőkép a leképezés a sablon utterances megadása](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)



4. Válassza ki a **szervezeti diagram – jelentések** szándékot, majd adja meg a következő sablon megcímkézzen, egyenként meg kiválasztása után minden sablon utterance (kifejezés):

    |Sablon kimondott szöveg|
    |:--|
    |[?] [A] {alkalmazott}, akik fölött.|
    |Akik jelentéseket {alkalmazott} [?]|
    |Ki tesz {alkalmazott} kezelése [?]|
    |Kik {alkalmazott} közvetlen beosztottak [?]|
    |Ki tesz {alkalmazott} supervise [?]|
    |Ki tesz {alkalmazott} főnökétől [?]|

## <a name="query-endpoint-when-patterns-are-used"></a>Végpont lekérdezéséhez használt minták

1. Betanítása, és tegye közzé újra az alkalmazást.

2. A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. 

3. Nyissa meg a végfelhasználók az URL-címét, és adja meg `Who is the boss of Jill Jones?` , az utterance (kifejezés). Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

A leképezési előrejelzési már jóval magasabb. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Ehhez válassza a három pontot (***...*** ) az alkalmazások listájában, az alkalmazás nevétől jobbra, válassza ki a **törlése**. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan használja a szerepkörök mintával](luis-tutorial-pattern-roles.md)