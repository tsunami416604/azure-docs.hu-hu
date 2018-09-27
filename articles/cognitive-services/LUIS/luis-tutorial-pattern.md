---
title: '3. oktatóanyag: Minták LUIS előrejelzéseket javítása érdekében'
titleSuffix: Azure Cognitive Services
description: Minták használatával növelheti a leképezés és egyéb entitások előrejelzési művelet során gondoskodik a kevesebb példa kimondott szöveg. A minta példaképpen egy sablon utterance (kifejezés), beleértve a szintaxist, entitások és figyelmen kívül hagyható, szöveg van megadva.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: f4b267dda3c05d490d91fe02fbcfde4e49674603
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166401"
---
# <a name="tutorial-3-add-common-utterance-formats"></a>3. oktatóanyag: Általános utterance (kifejezés) formátumok hozzáadása

Ebben az oktatóanyagban minták használatával növelheti a leképezés és egyéb entitások előrejelzési művelet során gondoskodik a kevesebb példa kimondott szöveg. A minta példaképpen egy sablon utterance (kifejezés), beleértve a szintaxist, entitások és figyelmen kívül hagyható, szöveg van megadva. A minta akkor kifejezések egyeztetésének és a gépi tanulás.  Az utterance (kifejezés) példasablonban szándék megcímkézzen, valamint egy jobb megértéséhez, hogy milyen kimondott szöveg igazítása a célt adjon meg a LUIS. 

**Ebből az oktatóanyagból megtudhatja, hogyan lehet:**

> [!div class="checklist"]
> * Használja meglévő oktatóanyag alkalmazása 
> * Leképezésének létrehozása
> * Betanítás
> * Közzététel
> * Végpont szándékok és entitások beolvasása
> * Hozzon létre egy minta
> * Ellenőrizze a minta előrejelzési fejlesztései
> * Szöveg nyelve figyelmen kívül hagyható és minta belül beágyazása
> * Teszt panel segítségével minta sikerességének ellenőrzése

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata

Folytassa az alkalmazás nevű az előző oktatóanyagban létrehozott **emberi**. 

Ha az előző oktatóanyagban az emberi alkalmazás nem rendelkezik, használja az alábbi lépéseket:

1.  Töltse le és mentse [alkalmazás JSON-fájlt](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json).

2. A JSON importálja egy új alkalmazást.

3. Az a **kezelés** részben, a a **verziók** lapon klónozza a verziót, és adja neki `patterns`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. A verzió nevét az URL-útvonal részeként használja, mert a név nem tartalmazhat, amelyek nem érvényes URL-karaktereket.

## <a name="create-new-intents-and-their-utterances"></a>Hozzon létre új leképezések és a kimondott szöveg

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

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

[!include[Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>Betanítás

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Közzététel

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Leképezés és entitások kaphat végpont

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

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

## <a name="template-utterances"></a>Sablon kimondott szöveg
Az emberi erőforrás-tartomány jellege miatt a szervezetben alkalmazott kapcsolatok kapcsolatos néhány gyakori módon is. Példa:

|Beszédmódok|
|--|
|Ki nem Jill Jones jelentést?|
|Akik jelentéseket Jill Jones?|

Ezek a kimondott szöveg szorosan határozzák meg az egyes nélkül számos utterance (kifejezés) példákkal környezetfüggő egyediségét. Ad hozzá egy minta megjelölésű, LUIS megtanulja megjelölésű közös utterance (kifejezés) minta számos utterance (kifejezés) Példák megadása nélkül. 

Sablon utterance (kifejezés) Példák a szándék a következők lehetnek:

|Sablon utterances példák|Szintaxis-jelentése|
|--|--|
|Ki {alkalmazott} tesz jelentést [?]|cserélhető {alkalmazott}, figyelmen kívül hagyása [?]}|
|Akik jelentéseket {alkalmazott} [?]|cserélhető {alkalmazott}, figyelmen kívül hagyása [?]}|

A `{Employee}` szintaxis jelöli meg a sablon utterance (kifejezés), valamint entitáshoz van az entitás helyére. A választható szintaxist `[?]`, feldolgozottként jelöli meg a szavakat vagy absztrakt, amelyek nem kötelező. LUIS megegyezik az utterance (kifejezés), a rendszer figyelmen kívül hagyja a nem kötelező szöveg a zárójelek között lévő.

Reguláris kifejezések néz ki a szintaxist, amíg nem reguláris kifejezéseket. Csak a kapcsos zárójelet `{}`, és a szögletes zárójelet, `[]`, szintaxis támogatott. Legfeljebb két szinten ágyazhatók.

Ahhoz, hogy egy mintát egyeztetni kell az utterance (kifejezés) az entitások az utterance (kifejezés) kell először felel meg a sablon utterance (kifejezés) entitást. Azonban a sablon nem segít előre jelezni az entitásokat, csak a szándék fog vonatkozni. 

**Minták lehetővé teszik, hogy kevesebb példa kimondott szöveg, ha az entitások nem észlelt, miközben a mintázat nem felel meg.**

Ebben az oktatóanyagban két új leképezések hozzáadása: `OrgChart-Manager` és `OrgChart-Reports`. 

|Szándék|Kimondott szöveg|
|--|--|
|Szervezeti diagram – vezető|Ki nem Jill Jones jelentést?|
|Szervezeti diagram – jelentések|Akik jelentéseket Jill Jones?|

Miután a LUIS-előrejelzés az ügyfélalkalmazásnak a leképezés neve lehet használni a függvény nevét az ügyfélalkalmazásban, és, hogy a függvény paramétereként használható-e az alkalmazott entitás adja vissza.

```Javascript
OrgChartManager(employee){
    ///
}
```

Ne feledje, hogy az alkalmazottak létrejöttek a [lista entitás oktatóanyag](luis-quickstart-intent-and-list-entity.md).

1. Válassza ki **összeállítása** a felső menüben.

2. A bal oldali navigációs területen **megnövelheti az alkalmazások teljesítményét**, jelölje be **minták** a bal oldali navigációs sávon.

3. Válassza ki a **szervezeti diagram – vezető** szándékot, majd adja meg a következő sablon kimondott szöveg:

    |Sablon kimondott szöveg|
    |:--|
    |{Alkalmazott} [?] beosztottja ki|
    |Ki {alkalmazott} tesz jelentést [?]|
    |Akik az [?] [a] {alkalmazott} manager|
    |Ki {alkalmazott} tesz jelentést közvetlenül [?]|
    |Akik az [?] [a] {alkalmazott} felügyelő|
    |Akik az, hogy a főnök {alkalmazott} [?]|

    Szerepkörök rendelkező entitások szintaxist használja, amely tartalmazza a szerepkör nevét, és fedi le egy [szerepkörök külön oktatóanyag](luis-tutorial-pattern-roles.md). 

    Ha a sablon utterance (kifejezés), a LUIS segítségével, töltse ki az entitás a bal oldali kapcsos zárójel megadásakor `{`.

    [![Képernyőkép a leképezés a sablon utterances megadása](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Válassza ki a **szervezeti diagram – jelentések** szándékot, majd adja meg a következő sablon kimondott szöveg:

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

|Szándék|Példa utterances opcionális szöveget, és előre összeállított entitások|
|:--|:--|
|Szervezeti diagram – vezető|`Who was Jill Jones manager on March 3?`|
|Szervezeti diagram – vezető|`Who is Jill Jones manager now?`|
|Szervezeti diagram – vezető|`Who will be Jill Jones manager in a month?`|
|Szervezeti diagram – vezető|`Who will be Jill Jones manager on March 3?`|

Ezekben a példákban mindegyike használ egy művelet igeidőt `was`, `is`, `will be`, dátum, valamint `March 3`, `now`, és `in a month`, LUIS igénylő megfelelően előrejelzésére. Figyelje meg, hogy az utolsó két példákban csaknem megegyező szöveget az alábbiakat kivéve `in` és `on`.

A példában a sablon kimondott szöveg:
|Szándék|Példa utterances opcionális szöveget, és előre összeállított entitások|
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

    |Szándék|Példa utterances opcionális szöveget, és előre összeállított entitások|
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

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban hozzáadja a két szándék, hogy sok példa utterances nélkül nagy pontosságú-előrejelzés nehéz volt megcímkézzen számára. Ezek engedélyezett LUIS jobb hozzáadása mintákat előre jelezni a leképezést, és jelentősen magasabb pontszámot. A minta alkalmazásához utterances választéka LUIS entitásokat és figyelmen kívül hagyható, szöveges jelölés engedélyezett.

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan használja a szerepkörök mintával](luis-tutorial-pattern-roles.md)