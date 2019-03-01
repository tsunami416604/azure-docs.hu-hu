---
title: Minták
titleSuffix: Azure Cognitive Services
description: Minták használata a szándék- és entitás-előrejelzések pontosságának javításához kevesebb kimondottszöveg-példa megadásával. A minta egy sablonként szolgáló kimondottszöveg-példán alapul, amelynek a szintaxisával azonosíthatók az entitások és a figyelmen kívül hagyható szövegek.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 02/22/2019
ms.author: diberry
ms.openlocfilehash: 33541d2a61c52476f6e314f6981a623390de8fa9
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193738"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats"></a>Oktatóanyag: Gyakori minta sablon utterance (kifejezés) formátumok hozzáadása

Az oktatóanyagban mintákat használunk a szándék- és entitás-előrejelzések pontosságának javításához kevesebb kimondottszöveg-példa megadásával. A minta egy sablonként szolgáló kimondottszöveg-példán alapul, amelynek a szintaxisával azonosíthatók az entitások és a figyelmen kívül hagyható szövegek. A minta a kifejezések egyeztetését gépi tanulással ötvözi.  A sablonként szolgáló kimondottszöveg-példa a szándékot tartalmazó kimondott szövegekkel együtt segít megérteni a LUIS számára, hogy milyen kimondott szövegek felelnek meg a szándéknak. 

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Példa-alkalmazás importálása 
> * Szándék létrehozása
> * Betanítás
> * Közzététel
> * Szándék és entitások lekérése a végpontról
> * Minta létrehozása
> * A minta-előrejelzési pontosság javulásának ellenőrzése
> * Szövegek megjelölése figyelmen kívül hagyhatóként, valamint beágyazásuk a mintába
> * A minta sikerességének ellenőrzése a tesztpanel használatával

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Példa-alkalmazás importálása

Folytassa az előző oktatóanyagban létrehozott **EmberiErőforrások** nevű alkalmazással. 

Ehhez a következő lépések szükségesek:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json).

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `patterns` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="create-new-intents-and-their-utterances"></a>Új szándékok és a hozzájuk tartozó kimondott szövegek létrehozása

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Az **Intents** (Szándékok) lapon válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

3. Az előugró párbeszédpanelen írja be a `OrgChart-Manager` karakterláncot, majd válassza a **Kész** elemet.

    ![Új üzenet létrehozása felugró ablak](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |Who is John W. Smith the subordinate of? (Kinek a beosztottja John W. Smith?)|
    |Who does John W. Smith report to? (Ki alá tartozik John W. Smith?)|
    |Who is John W. Smith's manager? (Ki John W. Smith vezetője?)|
    |Who does Jill Jones directly report to? (Ki Jill Jones közvetlen felettese?)|
    |Who is Jill Jones supervisor? (Ki Jill Jones főnöke?)|

    [![Képernyőkép – új kimondott szövegek hozzáadása szándékhoz a LUIS-ban](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Képernyőkép – új kimondott szövegek hozzáadása szándékhoz a LUIS-ban")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Ne aggódjon, ha a szándék kimondott szövegeiben a keyPhrase entitás van felcímkézve az employee entitás helyett. Mindkettő megfelelően van előre jelezve a Teszt panelen és a végponton. 

5. A bal oldali navigációs menüben válassza az **Intents** (Szándékok) lehetőséget.

6. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

7. Az előugró párbeszédpanelen írja be a `OrgChart-Reports` karakterláncot, majd válassza a **Kész** elemet.

8. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |Who are John W. Smith's subordinates? (Kik John W. Smith beosztottai?)|
    |Who reports to John W. Smith? (Ki tartozik John W. Smith alá?)|
    |Who does John W. Smith manage? (Kiknek a vezetője John W. Smith?)|
    |Who are Jill Jones direct reports? (Kik Jill Jones közvetlen beosztottai?)|
    |Who does Jill Jones supervise? (Kinek a főnöke Jill Jones?)|

## <a name="caution-about-example-utterance-quantity"></a>A kimondottszöveg-példák mennyiségére figyelmeztető üzenet

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>Betanítás

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Közzététel

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Szándék és entitások lekérése a végpontból

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Who is the boss of Jill Jones?`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. 

    ```json
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

Sikeres volt ez a lekérdezés? Ebben a betanítási ciklusban igen. Az első két szándék pontszáma közel van egymáshoz. Mivel a LUIS betanítási folyamata nem teljesen azonos az egyes alkalmak során, lehet némi eltérés. A két pontszám a következő betanítási ciklusban fel is cserélődhet. Ennek eredményeként előfordulhat, hogy a rendszer nem a megfelelő szándékot adja vissza. 

A minták segítségével jelentősen megnövelheti a megfelelő szándék pontszámának százalékos arányát, így az távolabb kerül a következő legmagasabb pontszámtól. 

Ne zárja be ezt a második böngészőablakot. Az oktatóanyag későbbi részében még használni fogja. 

## <a name="template-utterances"></a>Kimondottszöveg-sablonok
Az Emberi erőforrások (HR) tartomány jellegéből adódóan van néhány gyakori kifejezésmód, amellyel rá lehet kérdezni az alkalmazottak szervezeten belüli kapcsolatára. Példa:

|Beszédmódok|
|--|
|Who does Jill Jones report to? (Ki alá tartozik Jill Jones?)|
|Who reports to Jill Jones? (Ki tartozik Jill Jones alá?)|

Ezek a kimondott szövegek túl közel esnek egymáshoz ahhoz, hogy számos kimondottszöveg-példa megadása nélkül meg lehessen határozni az egyediségüket a kontextus vonatkozásában. Ha egy szándékhoz felveszünk egy mintát, azzal a LUIS képes megtanulni az adott szándék esetében gyakori kimondott szövegek mintáit anélkül, hogy ehhez sok kimondottszöveg-példát kellene megadni. 

A szándék sablonként szolgáló kimondott szövegeinek példái a következők lehetnek:

|Példák kimondottszöveg-sablonokra|szintaxis jelentése|
|--|--|
|Who does {Employee} report to[?] (Ki alá tartozik {Alkalmazott}[?])|interchangeable {Employee}, ignore [?] (felcserélhető {Alkalmazott}, kihagyás [?])|
|Who reports to {Employee}[?] (Ki tartozik {Alkalmazott} alá[?])|interchangeable {Employee}, ignore [?] (felcserélhető {Alkalmazott}, kihagyás [?])|

Az `{Employee}` szintaxis jelöli az entitás helyét a kimondottszöveg-sablonban, továbbá azonosítja az entitást is. A választható `[?]` szintaxis elhagyható szavakat vagy központozást jelöl. A LUIS egyezteti a kimondott szöveget, kihagyva a szögletes zárójelek közötti elhagyható szövegeket.

Bár a szintaxis reguláris kifejezésnek tűnhet, nem az. Csak a kapcsos (`{}`) és a szögletes (`[]`) zárójelek használata támogatott a szintaxisban. Ezek legfeljebb két szinten ágyazhatók be.

Ahhoz, hogy egy mintát egyeztetni lehessen egy kimondott szöveggel, a kimondott szövegben lévő entitásoknak előbb meg kell felelniük a kimondottszöveg-sablonban lévő entitásoknak. A sablon azonban csak a szándékokat segít előre jelezni, az entitásokat nem. 

**Habár a minták lehetővé teszik, hogy kevesebb kimondottszöveg-példát adjunk meg, ha a rendszer nem észleli az entitásokat, akkor a mintát nem lehet megfeleltetni.**

## <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Adja hozzá a minták a szervezeti diagram – vezető célja

1. A felső menüben válassza a **Build** (Létrehozás) elemet.

2. A bal oldali navigációs sávon az **Improve app performance** (Alkalmazás teljesítményének javítása) területen válassza a **Patterns** (Minták) lehetőséget.

3. Válassza ki az **OrgChart-Manager** szándékot, majd adja hozzá az alábbi kimondottszöveg-sablonokat:

    |Kimondottszöveg-sablonok|
    |:--|
    |Who is {Employee} the subordinate of[?] (Kinek a beosztottja {Alkalmazott}[?])|
    |Who does {Employee} report to[?] (Ki alá tartozik {Alkalmazott}[?])|
    |Who is {Employee}['s] manager[?] (Ki {Alkalmazott} vezetője[?])|
    |Who does {Employee} directly report to[?] (Kinek a közvetlen beosztottja {Alkalmazott}[?])|
    |Who is {Employee}['s] supervisor[?] (Ki {Alkalmazott} felettese[?])|
    |Who is the boss of {Employee}[?] (Ki {Alkalmazott} főnöke[?])|

    A szerepkörökkel rendelkező entitások szintaxisa tartalmazza a szerepkör nevét; ezeket [a szerepkörökre vonatkozó külön oktatóanyagban](luis-tutorial-pattern-roles.md) tárgyaljuk. 

    A kimondottszöveg-sablonok begépelésekor a LUIS segít kitölteni az entitás nevét, miután beírta a nyitó kapcsos zárójelet (`{`).

    [![Szándék kimondottszöveg-sablonjai megadásának képernyőképe](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Miközben továbbra is a minták lapon, válassza ki a **szervezeti diagram – jelentések** szándékot, majd adja meg a következő sablon kimondott szöveg:

    |Kimondottszöveg-sablonok|
    |:--|
    |Who are {Employee}['s] subordinates[?] (Kik {Alkalmazott} beosztottjai[?])|
    |Who reports to {Employee}[?] (Ki tartozik {Alkalmazott} alá[?])|
    |Who does {Employee} manage[?] (Kiknek a vezetője {Alkalmazott}[?])|
    |Who are {Employee} direct reports[?] (Kik {Alkalmazott} közvetlen beosztottjai[?])|
    |Who does {Employee} supervise[?] (Kinek a felettese {Alkalmazott}[?])|
    |Who does {Employee} boss[?] (Kiknek a főnöke {Alkalmazott}[?])|

## <a name="query-endpoint-when-patterns-are-used"></a>Végpont lekérdezése minták használata esetén

Most, hogy az alkalmazás hozzá vannak adva a mintákat, betanítása, közzététele és lekérdezése az alkalmazás az előrejelzési futásidejű végponton.

1. Tanítsa be és tegye ismét közzé az alkalmazást.

1. Lépjen vissza a böngésző a végpont URL-címét mutató lapjára.

1. Lépjen az URL-cím végéhez, és írja be a `Who is the boss of Jill Jones?` sztringet kimondott szövegként. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. 

    ```json
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

A leképezési előrejelzési már jelentősen sokkal magabiztosabb a munkában.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Elhagyható szövegek és előre összeállított entitások használata

Az oktatóanyag előző, mintául szolgáló kimondottszöveg-sablonjaiban alkalmaztunk néhány elhagyható szövegelemet, például az angol birtokos toldalékot (`'s`) és a kérdőjelet (`?`). Tegyük fel, hogy az utterance (kifejezés) szöveget a jelenlegi és jövőbeli dátumok engedélyeznie kell.

Példák kimondott szövegekre:

|Szándék|Kimondottszöveg-példák elhagyható szövegelemekkel és előre összeállított entitásokkal|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

A példák mindegyikében szerepel egy igeidő (`was`, `is` vagy `will be`) és egy dátum (`March 3`, `now` vagy `in a month`), amelyeket a LUIS-nak megfelelően kell tudnia előrejelezni. Figyelje meg, hogy az utolsó két példában a szövegek szinte teljesen azonosak, az `in` és az `on` prepozíciótól eltekintve.

A példában a sablon utterances, amelyek lehetővé teszik a nem kötelező információkat: 

|Szándék|Kimondottszöveg-példák elhagyható szövegelemekkel és előre összeállított entitásokkal|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


A szögletes zárójeles (`[]`) szintaxis használatával az elhagyható szövegek könnyen hozzáadhatók a kimondottszöveg-sablonhoz, továbbá két szinten is beágyazhatók (`[[]]`), valamint entitásokat vagy szövegeket tartalmazhatnak.


**Kérdés: Miért érdemes-e minden a `w` betűk, minden sablon kimondásakor, kis első betűje? Nem lehet ezeket választhatóan kis- és nagybetűvel is szerepeltetni?** A lekérdezésvégpontra az ügyfélalkalmazás által küldött kimondott szöveg kisbetűssé lesz alakítva. A sablonszöveg lehet kis- és nagybetűs is, ugyanúgy, ahogy a végponti kimondott szöveg is. Az összehasonlítás minden esetben a kisbetűssé alakítást követően megy végbe.

**Kérdés: Miért nem előre összeállított számát a sablon része utterance (kifejezés) Ha a 3. március előre jelzett mindkét számot `3` és dátum `March 3`?** A kimondottszöveg-sablon környezetileg egy dátumot használ, vagy kifejezetten (`March 3`) vagy elvontan (`in a month`). A dátumok tartalmazhatnak számokat, a számok azonban nem szükségszerűen értelmezendők dátumként. Mindig használja azt az entitást, amely a leginkább megfelel az előrejelzés JSON-eredményeiben visszaküldeni kívánt típusnak.  

**Kérdés: Mi a helyzet a rosszul phrased utterances például `Who will {Employee}['s] manager be on March 3?`.** A nyelvtanilag eltérő szerkezetű igeidőket – ahogy itt a `will` és a `be` például egymástól elszakítva szerepelnek – új kimondottszöveg-sablonként kell szerepeltetni. A meglévő kimondottszöveg-sablon nem fog egyezni ezzel. Bár a kimondott szöveg szándéka nem változott, a szavak elhelyezkedése igen. Ez a változás kihat az előrejelzésre a LUIS-ban. Is [csoport és vagy](#use-the-or-operator-and-groups) igeidőt úgy, hogy ezek a kimondott szöveg. 

**Ne feledje: a rendszer először az entitásokat keresi meg, aztán egyezteti a mintát.**

## <a name="edit-the-existing-pattern-template-utterance"></a>A meglévő kimondottszöveg-sablon mintájának szerkesztése

1. A LUIS-webhelyen válassza a felső menü **Build** (Összeállítás), majd a bal oldali menü **Patterns** (Minták) elemét. 

1. Keresse meg a meglévő sablon utterance `Who is {Employee}['s] manager[?]`, és kattintson a három pontra (***...*** ) a jobb oldalon, majd válassza ki **szerkesztése** az előugró menüben. 

1. Módosítsa a sablonszöveget a következőre: `who is {Employee}['s] manager [[on]{datetimeV2}?]`

## <a name="add-new-pattern-template-utterances"></a>Új kimondottszöveg-sablonminták hozzáadása

1. A **Build** (Összeállítás) menü **Patterns** (Minták) szakaszában maradva adjon hozzá néhány új kimondottszöveg-sablonmintát. Válassza ki az **OrgChart-Manager** elemet az Intent (Szándék) legördülő menüből, és írja be az alábbi kimondottszöveg-sablonokat:

    |Szándék|Kimondottszöveg-példák elhagyható szövegelemekkel és előre összeállított entitásokkal|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Tanítsa be az alkalmazást.

3. Válassza a panel tetején a **Test** (Teszt) lehetőséget a tesztpanel megjelenítéséhez. 

4. Adjon meg több kimondott tesztszöveget, és ellenőrizze, hogy a minta egyezik-e, illetve hogy a szándék pontszáma jelentősen magasabb-e. 

    Az első kimondott szöveg bevitele után válassza az **Inspect** (Vizsgálat) lehetőséget az eredmény alatt, hogy megtekinthesse az összes előrejelzési eredményt. Minden kimondásakor kell rendelkeznie a **szervezeti diagram – vezető** szándékot és az entitások az alkalmazottak és datetimeV2 értékeit kell csomagolja ki.

    |Kimondott szöveg|
    |--|
    |Who will be Jill Jones manager (Ki lesz Jill Jones vezetője)|
    |who will be jill jones's manager (ki lesz jill jones vezetője)|
    |Who will be Jill Jones's manager? (Ki lesz Jill Jones vezetője?)|
    |who will be Jill jones manager on March 3 (ki lesz Jill jones vezetője március 3-án)|
    |Who will be Jill Jones manager next Month (Ki lesz Jill Jones vezetője a következő hónapban)|
    |Who will be Jill Jones manager in a month? (Ki lesz Jill Jones vezetője egy hónap múlva?)|

Az összes fenti kimondott szövegben megtalálhatók az entitások, így ugyanazzal a mintával egyeznek, és magas előrejelzési pontszámmal rendelkeznek.

## <a name="use-the-or-operator-and-groups"></a>Használja a vagy operátor és a csoportok

Az előző sablon utterances számos olyan nagyon közel. Használja a **csoport** `()` és **vagy** `|` szintaxis sablon megcímkézzen csökkentése érdekében. 

A csoport használata egyetlen mintát a következő 2 minták kombinálható `()` és vagy `|` szintaxist.

|Szándék|Kimondottszöveg-példák elhagyható szövegelemekkel és előre összeállított entitásokkal|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Az új sablon utterance (kifejezés) a következő lesz: 

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`. 

Használja a **csoport** a szükséges művelet igeidőt és az opcionális `in` és `on` az egy **vagy** cső közöttük. 

1. Az a **minták** lapon válassza ki a **szervezeti diagram – vezető** szűrőt. Keressen rá a lista szűkítéséhez `manager`. 

    ![Szervezeti diagram – vezető szándék minták a "manager" kifejezés keresése](./media/luis-tutorial-pattern/search-patterns.png)

1. (A következő lépésben szerkesztése) sablon utterance (kifejezés) egyik verziójának megőrzése és az egyéb változatok törlése. 

1. Módosítsa a sablon utterance (kifejezés): 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

1. Tanítsa be az alkalmazást.

1. A teszt panel segítségével tesztelheti az utterance (kifejezés) verziói:

    |Adja meg a teszt panel kimondott szöveg|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|    
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|


## <a name="use-the-utterance-beginning-and-ending-anchors"></a>Az utterance (kifejezés) nyitó és záró horgonyok használata

A minta szintaxist biztosít a nyitó és záró utterance (kifejezés) forráshorgony szintaxisát a kalap `^`. A kezdő és záró utterance (kifejezés) horgonyok a cél jellemző, és valószínűleg szövegkonstans utterance (kifejezés) együtt használható, vagy használt külön-külön cél szándék fog vonatkozni. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban két szándékot adtunk hozzá az olyan kimondott szövegekhez, amelyeket nehezen lehetett nagy pontossággal előrejelezni anélkül, hogy sok kimondottszöveg-példát adnánk meg. Azáltal, hogy mintákat vettünk fel ezekhez, a LUIS hatékonyabban, jelentősen magasabb pontszámmal képes előrejelezni a szándékot. Az entitások és az elhagyható szövegek megjelölésével a LUIS szélesebb körben képes alkalmazni a mintát a kimondott szövegekre.

> [!div class="nextstepaction"]
> [Útmutató a szerepkörök és a minták együttes használatához](luis-tutorial-pattern-roles.md)
