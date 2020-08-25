---
title: 'Oktatóanyag: minták – LUIS'
description: Használjon mintázatokat a szándékok és az entitások előrejelzésének növeléséhez, miközben kevesebb példát hosszúságú kimondott szöveg ebben az oktatóanyagban. A minta sablon-kiírási példaként van megadva, amely az entitások és a figyelmen kívül hagyható szöveg azonosítására szolgáló szintaxist tartalmaz.
ms.topic: tutorial
ms.date: 07/06/2020
ms.openlocfilehash: 3ca8bb15d19b0fa0dd6b33d35a380c0b1b07abe0
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "86039500"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Oktatóanyag: általános minta sablon-megadási formátumok hozzáadása az előrejelzések tökéletesítéséhez

Ebben az oktatóanyagban mintákat használ a szándékok és az entitások előrejelzésének növeléséhez, ami lehetővé teszi, hogy kevesebb példát hosszúságú kimondott szöveg. A minta egy leképezéshez rendelt sablon-Kimondás, amely az entitások és a figyelmen kívül hagyható szöveg azonosítására szolgáló szintaxist tartalmaz.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Minta létrehozása
> * A minta-előrejelzési pontosság javulásának ellenőrzése
> * Szövegek megjelölése figyelmen kívül hagyhatóként, valamint beágyazásuk a mintába
> * A minta sikerességének ellenőrzése a tesztpanel használatával

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Hosszúságú kimondott szöveg a szándékban és a mintában

A LUIS-alkalmazásban két típusú hosszúságú kimondott szöveg tárol:

* Példa hosszúságú kimondott szöveg a szándékban
* Sablon hosszúságú kimondott szöveg a mintában

A sablon hosszúságú kimondott szöveg hozzáadásával a minta lehetővé teszi, hogy az általános hosszúságú kimondott szöveg kevesebb példát szolgáltasson.

A minták alkalmazása a szövegegyeztetés és a gépi tanulás kombinációjaként történik.  A mintában található sablonbeszédelem a szándék példabeszédelemeivel együtt segít megérteni a LUIS számára, hogy milyen beszédelemek felelnek meg a szándéknak.

## <a name="import-example-app-and-clone-to-new-version"></a>Példa importálása az alkalmazásra és a klónozás új verzióra

Ehhez a következő lépések szükségesek:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true).

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Importálja a JSON-t egy új alkalmazásba a [Luis portálra](https://www.luis.ai). A **saját alkalmazások** lapon válassza az **+ új alkalmazás a beszélgetéshez**lehetőséget, majd válassza az **Importálás JSON-ként**lehetőséget. Válassza ki az előző lépésben letöltött fájlt, nevezze el az alkalmazást `Patterns tutorial` .

## <a name="create-new-intents-and-their-utterances"></a>Új szándékok és a hozzájuk tartozó kimondott szövegek létrehozása

A két cél a kezelőt vagy a felettes közvetlen jelentéseit keresi a teljes szöveg alapján. A probléma az, hogy a két cél különböző dolgokat _jelent_ , de a szavak többsége ugyanaz. Csak a szó sorrendje különbözik. Ahhoz, hogy a szándékot helyesen lehessen előre jelezni, számos példát kellene megadnia.

1. Válassza a **Létrehozás** lehetőséget a navigációs sávon.

1. A **szándékok** lapon válassza a **+ Létrehozás** lehetőséget új szándék létrehozásához.

1. Az előugró párbeszédpanelen írja be a `OrgChart-Manager` karakterláncot, majd válassza a **Kész** elemet.

    ![Új üzenet létrehozása felugró ablak](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Adjon hozzá kimondott példaszövegeket a szándékhoz. Ezek a _hosszúságú kimondott szöveg nem_ azonosak, de kinyerhető mintázattal rendelkeznek.

    |Példák kimondott szövegekre|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

1. A bal oldali navigációs menüben válassza az **Intents** (Szándékok) lehetőséget.

1. Válassza a **+ Létrehozás** lehetőséget egy új szándék létrehozásához. Az előugró párbeszédpanelen írja be a `OrgChart-Reports` karakterláncot, majd válassza a **Kész** elemet.

1. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>A kimondottszöveg-példák mennyiségére figyelmeztető üzenet

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>Az alkalmazás betanítása tesztelés vagy közzététel előtt

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>Az alkalmazás közzététele a végpontról történő lekérdezéshez

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Szándék és entitások lekérése a végpontból

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Lépjen az URL-cím végére a címsorban, és cserélje le a _YOUR_QUERY_HERE_ a következőre: `Who is the boss of Jill Jones?` .

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.326605469
                },
                "OrgChart-Reports": {
                    "score": 0.127583548
                },
                "EmployeeFeedback": {
                    "score": 0.0299124215
                },
                "MoveEmployee": {
                    "score": 0.01159851
                },
                "GetJobInformation": {
                    "score": 0.0104600191
                },
                "ApplyForJob": {
                    "score": 0.007508645
                },
                "Utilities.StartOver": {
                    "score": 0.00359402061
                },
                "Utilities.Stop": {
                    "score": 0.00336530479
                },
                "FindForm": {
                    "score": 0.002653719
                },
                "Utilities.Cancel": {
                    "score": 0.00263288687
                },
                "None": {
                    "score": 0.00238638581
                },
                "Utilities.Help": {
                    "score": 0.00226386427
                },
                "Utilities.Confirm": {
                    "score": 0.00211663754
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

A helyes elsődleges szándék előre jelezve lett, `OrgChart-Manager` de a pontszám nem 70% fölött van, és nem elég nagy a következő legmagasabb szándéknál. A minták segítségével jelentősen megnövelheti a megfelelő szándék pontszámának százalékos arányát, így az távolabb kerül a következő legmagasabb pontszámtól.

Ne zárja be ezt a második böngészőablakot. Később ismét használni fogja az oktatóanyagban.

## <a name="template-utterances"></a>Kimondottszöveg-sablon
A humán erőforrás tárgyi tartományának jellegéből adódóan néhány gyakori módszer a szervezeten belüli alkalmazottak kapcsolatainak megkérdezésére. Például:

|Beszédelemek|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Ezek a hosszúságú kimondott szöveg túl vannak zárva a kontextus egyediségének meghatározásához, anélkül, hogy _sok_ teljes példát kellene megadniuk. A minta hozzáadásával a LUIS megtanítja a közös kiírási mintákat egy szándékhoz anélkül, hogy számos további példát kellene megadnia.

A szándék sablonként szolgáló kimondott szövegeinek példái a következők lehetnek:

|Példák kimondottszöveg-sablonokra|szintaxis jelentése|
|--|--|
|`Who does {EmployeeListEntity} report to[?]`|felcserélhetők `{EmployeeListEntity}`<br>figyelmen kívül `[?]`|
|`Who reports to {EmployeeListEntity}[?]`|felcserélhetők `{EmployeeListEntity}`<br>figyelmen kívül `[?]`|

Az `{EmployeeListEntity}` szintaxis jelöli az entitás helyét a kimondottszöveg-sablonban, továbbá azonosítja az entitást is. A választható szintaxis, a `[?]` jelölők szavai vagy a nem kötelező [írásjelek](luis-reference-application-settings.md#punctuation-normalization) . A LUIS egyezteti a kimondott szöveget, kihagyva a szögletes zárójelek közötti elhagyható szövegeket.

A szintaxis úgy tűnik, hogy a reguláris kifejezés nem reguláris kifejezés. Csak a kapcsos (`{}`) és a szögletes (`[]`) zárójelek használata támogatott a szintaxisban. Ezek legfeljebb két szinten ágyazhatók be.

Ahhoz, hogy egy minta kimondható legyen, _először_ a Kimondás alá tartozó entitásoknak egyezniük kell a sablon teljes számával. Ez azt jelenti, hogy az entitásoknak megfelelő példákkal kell rendelkezniük, mint például a hosszúságú kimondott szöveg, hogy az entitásokkal rendelkező mintázatok sikeresek legyenek. A sablon azonban csak a szándékokat segít előre jelezni, az entitásokat nem.

**Habár a minták lehetővé teszik, hogy kevesebb kimondottszöveg-példát adjunk meg, ha a rendszer nem észleli az entitásokat, akkor a mintát nem lehet megfeleltetni.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>A szervezeti diagram – Manager leképezés mintáinak hozzáadása

1. A felső menüben válassza a **Build** (Létrehozás) elemet.

1. A bal oldali navigációs sávon az **Improve app performance** (Alkalmazás teljesítményének javítása) területen válassza a **Patterns** (Minták) lehetőséget.

1. Válassza ki az **OrgChart-Manager** szándékot, majd adja hozzá az alábbi kimondottszöveg-sablonokat:

    |Kimondottszöveg-sablon|
    |:--|
    |`Who is {EmployeeListEntity} the subordinate of[?]`|
    |`Who does {EmployeeListEntity} report to[?]`|
    |`Who is {EmployeeListEntity}['s] manager[?]`|
    |`Who does {EmployeeListEntity} directly report to[?]`|
    |`Who is {EmployeeListEntity}['s] supervisor[?]`|
    |`Who is the boss of {EmployeeListEntity}[?]`|

    A sablon hosszúságú kimondott szöveg tartalmazza a **EmployeeListEntity** entitást a kapcsos zárójel jelölésével.

1. Miközben továbbra is a minták lapon válassza a **szervezeti diagram – jelentések** szándék lehetőséget, majd adja meg a következő sablon hosszúságú kimondott szöveg:

    |Kimondottszöveg-sablon|
    |:--|
    |`Who are {EmployeeListEntity}['s] subordinates[?]`|
    |`Who reports to {EmployeeListEntity}[?]`|
    |`Who does {EmployeeListEntity} manage[?]`|
    |`Who are {EmployeeListEntity} direct reports[?]`|
    |`Who does {EmployeeListEntity} supervise[?]`|
    |`Who does {EmployeeListEntity} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Végpont lekérdezése minták használata esetén

Most, hogy a mintákat hozzáadja az alkalmazáshoz, a betanításhoz, a közzétételhez és az alkalmazás lekérdezéséhez az előrejelzési futtatókörnyezet végpontján.

1. Válassza a **Betanítás** lehetőséget. A betanítás befejezése után válassza a **Közzététel** lehetőséget, majd válassza ki az **üzemi** tárolóhelyet, majd válassza a **kész**lehetőséget.

1. A közzététel befejezése után váltson vissza a böngésző lapjaira a végpont URL-címe lapra.

1. Nyissa meg az URL-cím végét a címsorban, és ellenőrizze, hogy a lekérdezés továbbra is `Who is the boss of Jill Jones?` megjelenik-e, majd küldje el az új előrejelzési URL-címet.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999999046
                },
                "OrgChart-Reports": {
                    "score": 3.237443E-05
                },
                "EmployeeFeedback": {
                    "score": 4.364242E-06
                },
                "GetJobInformation": {
                    "score": 1.616159E-06
                },
                "MoveEmployee": {
                    "score": 7.575752E-07
                },
                "ApplyForJob": {
                    "score": 5.234157E-07
                },
                "None": {
                    "score": 3.3E-09
                },
                "Utilities.StartOver": {
                    "score": 1.26E-09
                },
                "FindForm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Cancel": {
                    "score": 1.13636367E-09
                },
                "Utilities.Confirm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Help": {
                    "score": 1.13636367E-09
                },
                "Utilities.Stop": {
                    "score": 1.13636367E-09
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

A szándék előrejelzése mostantól sokkal nagyobb mértékben bízik, és a következő legmagasabb cél a pontszám nagyon alacsony. Ez a két cél nem lesz flip-flop a betanítás során.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Elhagyható szövegek és előre összeállított entitások használata

Az oktatóanyag előző, mintául szolgáló kimondottszöveg-sablonjaiban alkalmaztunk néhány elhagyható szövegelemet, például az angol birtokos toldalékot (`'s`) és a kérdőjelet (`?`). Tegyük fel, hogy engedélyezni kell a jelenlegi és a jövőbeli dátumokat a teljes szövegben.

Példák kimondott szövegekre:

|Szándék|Kimondottszöveg-példák elhagyható szövegelemekkel és előre összeállított entitásokkal|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

A példák mindegyikében szerepel egy igeidő (`was`, `is` vagy `will be`) és egy dátum (`March 3`, `now` vagy `in a month`), amelyeket a LUIS-nak megfelelően kell tudnia előrejelezni. Figyelje meg, hogy a tábla utolsó két példája majdnem ugyanazt a szöveget használja, a és a kivételével `in` `on` .

Példa a sablon hosszúságú kimondott szöveg, amely a következő opcionális információkat engedélyezi:

|Szándék|Kimondottszöveg-példák elhagyható szövegelemekkel és előre összeállított entitásokkal|
|:--|:--|
|OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|


A szögletes zárójeles (`[]`) szintaxis használatával az elhagyható szövegek könnyen hozzáadhatók a kimondottszöveg-sablonhoz, továbbá két szinten is beágyazhatók (`[[]]`), valamint entitásokat vagy szövegeket tartalmazhatnak.


**Kérdés: Miért minden `w` betű, az első betű az egyes sablonokban, kisbetűs? Nem lenne opcionálisan felső vagy kisbetűs?** A lekérdezésvégpontra az ügyfélalkalmazás által küldött kimondott szöveg kisbetűssé lesz alakítva. A sablonszöveg lehet kis- és nagybetűs is, ugyanúgy, ahogy a végponti kimondott szöveg is. Az összehasonlítás minden esetben a kisbetűssé alakítást követően megy végbe.

**Kérdés: Miért nem képezi az előre összeállított szám a kimondottszöveg-sablon részét, ha a March 3 (március 3.) elemet a rendszer számként (`3`) és dátumként (`March 3`) is előrejelzi?** A kimondottszöveg-sablon környezetileg egy dátumot használ, vagy kifejezetten (`March 3`) vagy elvontan (`in a month`). A dátumok tartalmazhatnak számokat, a számok azonban nem szükségszerűen értelmezendők dátumként. Mindig használja azt az entitást, amely a leginkább megfelel az előrejelzés JSON-eredményeiben visszaküldeni kívánt típusnak.

**Kérdés: Mi a helyzet a helytelenül megfogalmazott szövegekkel, amilyen például a `Who will {EmployeeListEntity}['s] manager be on March 3?`?** A nyelvtanilag eltérő szerkezetű igeidőket – ahogy itt a `will` és a `be` például egymástól elszakítva szerepelnek – új kimondottszöveg-sablonként kell szerepeltetni. A meglévő kimondottszöveg-sablon nem fog egyezni ezzel. Bár a kimondott szöveg szándéka nem változott, a szavak elhelyezkedése igen. Ez a változás kihat az előrejelzésre a LUIS-ban. Ezeket a hosszúságú kimondott szöveg egyesítheti [és](#use-the-or-operator-and-groups) elvégezheti a műveletekben.

> [!CAUTION]
> **Ne feledje: a rendszer először az entitásokat keresi meg, aztán egyezteti a mintát.**

### <a name="add-new-pattern-template-utterances"></a>Új kimondottszöveg-sablonminták hozzáadása

1. A **Build** (Összeállítás) menü **Patterns** (Minták) szakaszában maradva adjon hozzá néhány új kimondottszöveg-sablonmintát. Válassza ki az **OrgChart-Manager** elemet az Intent (Szándék) legördülő menüből, és írja be az alábbi kimondottszöveg-sablonokat:

    |Szándék|Kimondottszöveg-példák elhagyható szövegelemekkel és előre összeállított entitásokkal|
    |--|--|
    |OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

2. Válassza ki a navigációs sávon a **vonat** lehetőséget az alkalmazás betanításához.

3. A betanítás befejezése után válassza a **teszt** elemet a panel tetején a tesztelés panel megnyitásához.

4. Adjon meg több kimondott tesztszöveget, és ellenőrizze, hogy a minta egyezik-e, illetve hogy a szándék pontszáma jelentősen magasabb-e.

    Az első kimondott szöveg bevitele után válassza az **Inspect** (Vizsgálat) lehetőséget az eredmény alatt, hogy megtekinthesse az összes előrejelzési eredményt. Minden részletnek a **szervezeti diagram-kezelő** szándékkal kell rendelkeznie, és ki kell bontania a `EmployeeListEntity` és az `datetimeV2` entitások értékeit.

    |Kimondott szöveg|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Az összes fenti kimondott szövegben megtalálhatók az entitások, így ugyanazzal a mintával egyeznek, és magas előrejelzési pontszámmal rendelkeznek. Néhány olyan mintázatot adott hozzá, amely megfelel a hosszúságú kimondott szöveg számos változatának. Nem kell felvennie egy példa hosszúságú kimondott szöveg a szándékban, hogy a sablon teljes működése a mintában megtörténjen.

A megadott minták használata:
* Magasabb előrejelzési pontszámok
* Ugyanazzal a példával hosszúságú kimondott szöveg a szándék
* Mindössze néhány jól felépített sablon hosszúságú kimondott szöveg a mintában

### <a name="use-the-or-operator-and-groups"></a>A vagy a operátor és a csoportok használata

Az előző sablon hosszúságú kimondott szöveg közül több nagyon közel van. A **csoport** `()` és a **OR** `|` szintaxis használatával csökkentse a sablon hosszúságú kimondott szöveg.

A következő két minta egyetlen mintával kombinálható a csoport és a `()` szintaxis használatával `|` .

|Szándék|Kimondottszöveg-példák elhagyható szövegelemekkel és előre összeállított entitásokkal|
|--|--|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

Az új sablon kimondása a következőket eredményezi:

`who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`.

Ez egy **csoportot** használ a szükséges műveleti idő körül, és a választható `in` , `on` **illetve egy vagy** több közötti adatcsatornával.

> [!NOTE]
> A _vagy_ a szimbólum `|` (pipe) használatakor ügyeljen arra, hogy a cső szimbólumát a példa sablon előtt és után szóközzel válassza el.

1. A **minták** lapon válassza a **szervezeti diagram – kezelő** szűrőt. Szűkítse a listát a keresésével `manager` .

1. Tartsa meg a sablon teljes verziójának egy verzióját (a következő lépésben való szerkesztéshez), és törölje a többi változatot.

1. Módosítsa a sablonszöveget a következőre: 

    `who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`

2. Válassza ki a navigációs sávon a **vonat** lehetőséget az alkalmazás betanításához.

3. A betanítás befejezése után válassza a **teszt** elemet a panel tetején a tesztelés panel megnyitásához.

    A teszt panel használatával tesztelheti a teljes verzió verzióját:

    |Hosszúságú kimondott szöveg a teszt ablaktáblán|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

A további mintázat szintaxisának használatával csökkentheti az alkalmazásban fenntartott sablon hosszúságú kimondott szöveg számát, miközben továbbra is nagy előrejelzési pontszám szükséges.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>A kezdő és záró horgonyok használata

A minta szintaxisa egy kalap elejének és végének kiírási horgonyának szintaxisát tartalmazza `^` . A kezdő és a záró kilépési horgonyok együtt használhatók a nagyon konkrét és valószínűleg literál kifejezésekre, vagy a cél szándékok külön történő használatára.

## <a name="using-patternany-entity"></a>Minta. bármely entitás használata

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

### <a name="add-example-utterances-with-patternany"></a>Példaként adja hozzá a hosszúságú kimondott szöveg mintát. any

1. Válassza a **Build** (Összeállítás) lehetőséget a felső navigációs sávon, majd az **Intents** (Leképezések) elemet a bal oldali navigációs sávon.

1. A leképezések listájáról válassza a **FindForm** (Űrlap keresése) lehetőséget.

1. Adjon hozzá néhány példát a hosszúságú kimondott szöveg. Az a szöveg, amelyet mintaként kell megjósolni. bármelyik **félkövér szövegben**van. Az űrlap nevét nehéz megállapítani a szövegben lévő többi szó alapján. A minta. bármelyik segítséget nyújt az entitás határainak megjelölésével.

    |Példa kimondott szöveg|Űrlap neve|
    |--|--|
    |Hol található a **Mi a teendő, ha tűz keletkezik a laborban** űrlap, és kinek kell aláírnia, miután elolvastam?|Mi a teendő, ha egy tűz kikerül a laborba
    |Hol található a **2018-ban a vállalathoz került alkalmazott áthelyezési kérelme** űrlap a kiszolgálón?|Áttelepítési kérelem áthelyezése az alkalmazottaktól a vállalatnál|
    |Ki a szerzője ennek: "**Egészségügyi és wellnesskérelmek a fő campuson**", és melyik a jelenlegi verziója?|Egészségügyi és wellness-kérelmek a fő Egyetemen|
    |Az alábbi űrlapot keresem: "**Irodaáthelyezési kérelem, beleértve a fizikai eszközöket**". |Office-áthelyezési kérelem, beleértve a fizikai eszközöket|

    Mivel az űrlapnevek nagyon változatosak, a Pattern.any entitás hiánya megnehezíti a LUIS szolgáltatás számára az űrlapcím végének meghatározását.

### <a name="create-a-patternany-entity"></a>Pattern.any entitás létrehozása
A Pattern.any entitás változó hosszúságú entitások kinyerését végzi. Ez csak a mintában működik, mert a minta az entitás kezdetét és végét jelöli meg szintaxissal.

1. Válassza az **Entities** (Entitások) elemet a bal oldali navigációs sávon.

1. Válassza a **+ Létrehozás**lehetőséget, írja be a nevet `FormName` , és válassza a **minta. any** típust. Kattintson a **Létrehozás** gombra.

### <a name="add-a-pattern-that-uses-the-patternany"></a>A Pattern.any entitást használó minta hozzáadása

1. Válassza a **Patterns** (Minták) elemet a bal oldali navigációs sávban.

1. Válassza ki a **FindForm** (Űrlap keresése) leképezést.

1. Adja meg a következő, az új entitást használó kimondottszöveg-sablont:

    |Kimondottszöveg-sablon|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. Tanítsa be az alkalmazást.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Tesztelje az új mintát a szabad formátumú adatok kinyerésére
1. A tesztelési panel megnyitásához válassza a felső sávon a **Test** lehetőséget.

1. Adja meg a következő kimondott szöveget:

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Az entitáshoz és a leképezéshez tartozó teszteredmények megtekintéséhez válassza az eredmény alatti **Inspect** (Vizsgálat) lehetőséget.

    A leképezést az először megtalált `FormName` entitás, majd a megtalált minta határozza meg. Ha a tesztelés során a rendszer nem észlelte az entitásokat, és ezért nem találta a mintát, további példa kimondott szövegeket kell megadnia a leképezésben (nem a mintában).

1. A felső navigációs sáv **Test** gombjával zárja be a tesztelési panelt.

### <a name="using-an-explicit-list"></a>Explicit lista használata

Ha azt tapasztalja, hogy a Pattern.any entitást tartalmazó minta nem megfelelően vonja ki az entitásokat, egy [explicit lista](reference-pattern-syntax.md#explicit-lists) megoldhatja a problémát.

## <a name="what-did-this-tutorial-accomplish"></a>Mit ért el ez az oktatóanyag?

Ez az oktatóanyag olyan mintákat vett fel, amelyek segítségével a LUIS előre jelezheti a szándékot, és nem kell további példát hosszúságú kimondott szöveg hozzá. Az entitások és az elhagyható szövegek megjelölésével a LUIS szélesebb körben képes alkalmazni a mintát a kimondott szövegekre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések


> [!div class="nextstepaction"]
> [Ismerje meg a szerepkörök mintával való használatát](luis-tutorial-pattern.md)
