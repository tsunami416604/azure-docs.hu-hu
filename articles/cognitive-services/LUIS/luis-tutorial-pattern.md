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
ms.date: 07/30/2018
ms.author: diberry
ms.openlocfilehash: 355c1edd4fa7433e68a9c0e903f4f782203326fe
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365878"
---
# <a name="tutorial-improve-app-with-patterns"></a>Oktatóanyag: Alkalmazás minták és javítása

Ebben az oktatóanyagban minták használatával növelheti a leképezés és egyéb entitások előrejelzési.  

> [!div class="checklist"]
* Hogyan azonosíthatja, hogy egy minta segíthet az alkalmazás
* A minta létrehozása
* Azt, hogyan ellenőrizheti, mintát előrejelzési fejlesztései

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Előkészületek

Ha nem rendelkezik az emberi erőforrások alkalmazásból a [batch teszt](luis-tutorial-batch-testing.md) az oktatóanyagban [importálása](luis-how-to-start-new-app.md#import-new-app) a JSON-kódot egy új alkalmazást a [LUIS](luis-reference-regions.md#luis-website) webhely. Az alkalmazás importálása megtalálható a [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json) GitHub-adattárban.

Ha meg szeretné tartani az eredeti Emberi erőforrások alkalmazást, klónozza a [Settings](luis-how-to-manage-versions.md#clone-a-version) (Beállítások) lapon a verziót, és adja neki a következő nevet: `patterns`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Minták tanít LUIS kevesebb példákkal közös kimondott szöveg

Az emberi erőforrás-tartomány jellege miatt a szervezetben alkalmazott kapcsolatok kapcsolatos néhány gyakori módon is. Példa:

|Beszédmódok|
|--|
|Ki nem Jill Jones jelentést?|
|Akik jelentéseket Jill Jones?|

Ezek a kimondott szöveg szorosan határozzák meg az egyes nélkül számos utterance (kifejezés) példákkal környezetfüggő egyediségét. Ad hozzá egy minta megjelölésű, LUIS megtanulja megjelölésű közös utterance (kifejezés) minta számos utterance (kifejezés) Példák megadása nélkül. 

Példa a sablon utterances ezt szándék a következők:

|A példában a sablon kimondott szöveg|
|--|
|Ki {alkalmazott} tesz jelentést?|
|Akik jelentéseket {alkalmazott}?|

A minta példaképpen egy sablon utterance (kifejezés), beleértve a szintaxist, entitások és figyelmen kívül hagyható, szöveg van megadva. A reguláris kifejezések egyeztetésének és a machine learning egyik.  Az utterance (kifejezés) példasablonban szándék megcímkézzen, valamint egy jobb megértéséhez, hogy milyen kimondott szöveg igazítása a célt adjon meg a LUIS.

Ahhoz, hogy egy mintát egyeztetni kell az utterance (kifejezés) az entitások az utterance (kifejezés) kell először felel meg a sablon utterance (kifejezés) entitást. Azonban a sablon nem segít előre jelezni az entitásokat, csak a szándék fog vonatkozni. 

**Minták lehetővé teszik, hogy kevesebb példa kimondott szöveg, ha az entitások nem észlelt, miközben a mintázat nem felel meg.**

Ne feledje, hogy az alkalmazottak létrejöttek a [lista entitás oktatóanyag](luis-quickstart-intent-and-list-entity.md).

## <a name="create-new-intents-and-their-utterances"></a>Hozzon létre új leképezések és a kimondott szöveg

Két új leképezések hozzáadása: `OrgChart-Manager` és `OrgChart-Reports`. Miután a LUIS-előrejelzés az ügyfélalkalmazásnak a leképezés neve lehet használni a függvény nevét az ügyfélalkalmazásban, és, hogy a függvény paramétereként használható-e az alkalmazott entitás adja vissza.

```Javascript
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

    [![Képernyőkép a LUIS új beszédmódok hozzáadása beszédszándék](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "képernyőképe, a LUIS beszédszándék új beszédmódok hozzáadása")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

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

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>A végpont lekérdezése egy másik kimondott szöveggel

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

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

Hagyja nyitva a második böngészőablakot. Később az oktatóanyagban használja. 

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

    Szerepkörök rendelkező entitások szintaxist használja, amely tartalmazza a szerepkör nevét, és fedi le egy [szerepkörök külön oktatóanyag](luis-tutorial-pattern-roles.md). 

    A választható szintaxist `[]`, feldolgozottként jelöli meg a szavakat vagy absztrakt, amelyek nem kötelező. LUIS megegyezik az utterance (kifejezés), a rendszer figyelmen kívül hagyja a nem kötelező szöveg a zárójelek között lévő.

    Ha a sablon utterance (kifejezés), a LUIS segítségével, töltse ki az entitás a bal oldali kapcsos zárójel megadásakor `{`.

    [![Képernyőkép a leképezés a sablon utterances megadása](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

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

2. Böngészőlapokon váltson vissza a végpont URL-cím fülre.

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

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Nem kötelező szöveg, és előre összeállított entitások használata

Ez az oktatóanyag korábbi minta sablon megcímkézzen volt néhány példa a nem kötelező szöveg, például a birtokos s, betű `'s`, és a kérdőjel felhasználása `?`. Tegyük fel, hogy a végpont megcímkézzen valamint jelennek meg, hogy menedzserek és az emberi erőforrások képviselői előzményadatok keres tervezünk alkalmazott áthelyezését egy későbbi időpontban történik a vállalaton belül.

Példa utterances a következők:

|Leképezés|Példa utterances opcionális szöveget, és előre összeállított entitások|
|:--|:--|
|Szervezeti diagram – vezető|`Who was Jill Jones manager on March 3?`|
|Szervezeti diagram – vezető|`Who is Jill Jones manager now?`|
|Szervezeti diagram – vezető|`Who will be Jill Jones manager in a month?`|
|Szervezeti diagram – vezető|`Who will be Jill Jones manager on March 3?`|

Ezekben a példákban mindegyike használ egy művelet igeidőt `was`, `is`, `will be`, dátum, valamint `March 3`, `now`, és `in a month`, LUIS igénylő megfelelően előrejelzésére. Figyelje meg, hogy az utolsó két példákban csaknem megegyező szöveget az alábbiakat kivéve `in` és `on`.

A példában a sablon kimondott szöveg:
|Leképezés|Példa utterances opcionális szöveget, és előre összeállított entitások|
|:--|:--|
|Szervezeti diagram – vezető|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|Szervezeti diagram – vezető|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
|Szervezeti diagram – vezető|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|Szervezeti diagram – vezető|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Nem kötelező szintaxisa a következő szögletes zárójelben használatát `[]`, ez nem kötelező szöveg megkönnyíti a sablon utterance (kifejezés) ad hozzá, és legfeljebb egy második szint ágyazhatók `[[]]`, és entitásokat vagy szöveget tartalmaznak.

**Kérdés: Miért nem sikerült az utolsó két példa utterances egyesítése egy egyetlen sablonban utterance (kifejezés)?** A minta-sablon nem támogatja vagy szintaxist. Annak érdekében, hogy a tényleges egyaránt a `in` verziója és a `on` verziója, minden egyes kell lennie egy külön sablon utterance (kifejezés).

**Kérdés: Miért érdemes-e minden a `w` betűk, minden sablon kimondásakor, kis első betűje? Igény szerint kis- és nagybetűket nem lehetnek?** A lekérdezés végpont az ügyfélalkalmazás által benyújtott az utterance (kifejezés) kisbetűk lesz konvertálva. A sablon utterance (kifejezés) is lehet, kis- és nagybetűket, és a végpont utterance (kifejezés) is lehet. Az összehasonlítás mindig kisbetűs az átalakítás után végezhető el.

**Kérdés: Miért nem előre összeállított számát a sablon része utterance (kifejezés) Ha a 3. március előre jelzett mindkét számot `3` és dátum `March 3`?** A sablon utterance (kifejezés) kontextusban használ egy dátumot, vagy a szó szerint `March 3` vagy mint hálója `in a month`. Egy dátumot egy számot tartalmazhat, de számos lehetséges, hogy nem feltétlenül látható egy dátum. Mindig használjon az entitást, amely a legjobban jelképezi a visszaküldött JSON előrejelzési eredményeket a kívánt típusra.  

**Kérdés: Mi a helyzet a rosszul phrased utterances például `Who will {Employee}['s] manager be on March 3?`.** Nyelvtanilag különböző igeidőt, például a ezt a `will` és `be` vannak elválasztva kell lennie egy új sablont utterance (kifejezés). A meglévő sablon utterance (kifejezés), nem egyeznek meg. Bár az utterance (kifejezés) célja nem változott, a word elhelyezését az utterance (kifejezés) a megváltozott. Ez a változás az előrejelzést, a LUIS hatással van.

**Ne feledje: entitások találhatók először, majd a minta egyezik.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Szerkessze a meglévő mintát sablon utterance (kifejezés)

1. A LUIS-webhelyen válassza ki a **hozhat létre** a felső menüben válassza ki **minták** a bal oldali menüben. 

2. Keresse meg a meglévő sablon utterance `Who is {Employee}['s] manager[?]`, és kattintson a három pontra (***...*** ) a jobb oldalon. 

3. Válassza ki **szerkesztése** az előugró menüben. 

4. Módosítsa a sablon utterance (kifejezés): `who is {Employee}['s] manager [[on]{datetimeV2}?]]`

## <a name="add-new-pattern-template-utterances"></a>Új minta sablon beszédmódok hozzáadása

1. Mialatt továbbra is a **minták** szakaszában **összeállítása**, több új mintát, a sablon beszédmódok hozzáadása. Válassza ki **szervezeti diagram – vezető** a szándék legördülő menüből, és adja meg a következő sablon utterances mindegyike:

    |Leképezés|Példa utterances opcionális szöveget, és előre összeállított entitások|
    |--|--|
    |Szervezeti diagram – vezető|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |Szervezeti diagram – vezető|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
    |Szervezeti diagram – vezető|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |Szervezeti diagram – vezető|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Az alkalmazás betanításához.

3. Válassza ki **teszt** a tesztelési panel megnyitásához a panel tetején. 

4. Adja meg arról, hogy a minta egyezik, és a szándék pontszám a rendkívül nagy több teszt utterances. 

    Miután megadta az első utterance (kifejezés), válassza ki a **vizsgálat** alatt az eredmény, így láthatja az összes előrejelzési eredményeket.

    |Kimondott szöveg|
    |--|
    |Kinek lesz Jill Jones manager|
    |kinek lesz jill jones manager|
    |Kinek lesz Jill Jones manager?|
    |kinek lesz a Jill jones manager március 3-án|
    |Kinek Jill Jones manager következő hónapban|
    |Kinek Jill Jones manager egy hónapban?|

Az összes alábbi kimondott szöveg található az entitások belül, így azok egyeznek ugyanazt a mintát, és egy nagy előrejelzési pontszám rendelkezik.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan használja a szerepkörök mintával](luis-tutorial-pattern-roles.md)