---
title: 'Oktatóanyag: minták – LUIS'
titleSuffix: Azure Cognitive Services
description: Használjon mintázatokat a szándékok és az entitások előrejelzésének növeléséhez, miközben kevesebb példát hosszúságú kimondott szöveg ebben az oktatóanyagban. A minta sablon-kiírási példaként van megadva, amely az entitások és a figyelmen kívül hagyható szöveg azonosítására szolgáló szintaxist tartalmaz.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 69894dfc6bcbe9eb56451524c78e82da2745aa52
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979770"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Oktatóanyag: általános minta sablon-megadási formátumok hozzáadása az előrejelzések tökéletesítéséhez

Ebben az oktatóanyagban mintákat használ a szándékok és az entitások előrejelzésének növeléséhez, ami lehetővé teszi, hogy kevesebb példát hosszúságú kimondott szöveg. A minta egy leképezéshez rendelt sablon-Kimondás, amely az entitások és a figyelmen kívül hagyható szöveg azonosítására szolgáló szintaxist tartalmaz.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

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

A rendszer a kifejezések egyezésének és a gépi tanulásnak a kombinációját alkalmazza.  A sablon-Kimondás és a példa hosszúságú kimondott szöveg együtt a LUIS jobban megértette, hogy a hosszúságú kimondott szöveg hogyan illeszkedik a szándékhoz.

## <a name="import-example-app-and-clone-to-new-version"></a>Példa importálása az alkalmazásra és a klónozás új verzióra

Ehhez a következő lépések szükségesek:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json?raw=true).

1. Importálja a JSON-t egy új alkalmazásba az [előnézeti Luis-portálon](https://preview.luis.ai).

1. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `patterns` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="create-new-intents-and-their-utterances"></a>Új szándékok és a hozzájuk tartozó kimondott szövegek létrehozása

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

    Ne aggódjon, ha a szándék kimondott szövegeiben a keyPhrase entitás van felcímkézve az employee entitás helyett. Mindkettő megfelelően van előre jelezve a Teszt panelen és a végponton.

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

1. Lépjen az URL-cím végéhez, és írja be a következőt: `Who is the boss of Jill Jones?`. Az utolsó querystring paraméter a teljes `query`.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.313054234
                },
                "OrgChart-Reports": {
                    "score": 0.2462688
                },
                "EmployeeFeedback": {
                    "score": 0.0488328524
                },
                "GetJobInformation": {
                    "score": 0.0156933
                },
                "MoveEmployee": {
                    "score": 0.011265873
                },
                "Utilities.StartOver": {
                    "score": 0.003065792
                },
                "Utilities.Stop": {
                    "score": 0.00300148362
                },
                "Utilities.Cancel": {
                    "score": 0.00271081156
                },
                "None": {
                    "score": 0.00212835032
                },
                "ApplyForJob": {
                    "score": 0.0020669254
                },
                "Utilities.Confirm": {
                    "score": 0.00200891262
                },
                "FindForm": {
                    "score": 0.00194145238
                },
                "Utilities.Help": {
                    "score": 0.00182301877
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
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
                    "Employee": [
                        {
                            "type": "Employee",
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

Sikeres volt ez a lekérdezés? Ebben a betanítási ciklusban igen. A két legfontosabb cél pontszáma a közelben van, de a legmagasabb szándék nem számottevően magas (több mint 60%) és nem elég messze a következő cél pontszáma fölé.

Mivel a LUIS betanítási folyamata nem teljesen azonos az egyes alkalmak során, lehet némi eltérés. A két pontszám a következő betanítási ciklusban fel is cserélődhet. Ennek eredményeként előfordulhat, hogy a rendszer nem a megfelelő szándékot adja vissza.

A minták segítségével jelentősen megnövelheti a megfelelő szándék pontszámának százalékos arányát, így az távolabb kerül a következő legmagasabb pontszámtól.

Ne zárja be ezt a második böngészőablakot. Az oktatóanyag későbbi részében még használni fogja.

## <a name="template-utterances"></a>Kimondottszöveg-sablon
Az Emberi erőforrások (HR) tartomány jellegéből adódóan van néhány gyakori kifejezésmód, amellyel rá lehet kérdezni az alkalmazottak szervezeten belüli kapcsolatára. Példa:

|Beszédmódok|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Ezek a kimondott szövegek túl közel esnek egymáshoz ahhoz, hogy számos kimondottszöveg-példa megadása nélkül meg lehessen határozni az egyediségüket a kontextus vonatkozásában. Ha egy szándékhoz felveszünk egy mintát, azzal a LUIS képes megtanulni az adott szándék esetében gyakori kimondott szövegek mintáit anélkül, hogy ehhez sok kimondottszöveg-példát kellene megadni.

A szándék sablonként szolgáló kimondott szövegeinek példái a következők lehetnek:

|Példák kimondottszöveg-sablonokra|szintaxis jelentése|
|--|--|
|`Who does {Employee} report to[?]`|felcserélhető `{Employee}`<br>`[?]` figyelmen kívül hagyása|
|`Who reports to {Employee}[?]`|felcserélhető `{Employee}`<br>`[?]` figyelmen kívül hagyása|

Az `{Employee}` szintaxis jelöli az entitás helyét a kimondottszöveg-sablonban, továbbá azonosítja az entitást is. A nem kötelező szintaxis, `[?]`, jelölő szavak vagy írásjelek. A LUIS egyezteti a kimondott szöveget, kihagyva a szögletes zárójelek közötti elhagyható szövegeket.

A szintaxis úgy tűnik, hogy a reguláris kifejezés nem reguláris kifejezés. Csak a kapcsos (`{}`) és a szögletes (`[]`) zárójelek használata támogatott a szintaxisban. Ezek legfeljebb két szinten ágyazhatók be.

Ahhoz, hogy egy mintát egyeztetni lehessen egy kimondott szöveggel, a kimondott szövegben lévő entitásoknak előbb meg kell felelniük a kimondottszöveg-sablonban lévő entitásoknak. Ez azt jelenti, hogy az entitásoknak megfelelő példákkal kell rendelkezniük, mint például a hosszúságú kimondott szöveg, hogy az entitásokkal rendelkező mintázatok sikeresek legyenek. A sablon azonban csak a szándékokat segít előre jelezni, az entitásokat nem.

**Habár a minták lehetővé teszik, hogy kevesebb kimondottszöveg-példát adjunk meg, ha a rendszer nem észleli az entitásokat, akkor a mintát nem lehet megfeleltetni.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>A szervezeti diagram – Manager leképezés mintáinak hozzáadása

1. A felső menüben válassza a **Build** (Létrehozás) elemet.

1. A bal oldali navigációs sávon az **Improve app performance** (Alkalmazás teljesítményének javítása) területen válassza a **Patterns** (Minták) lehetőséget.

1. Válassza ki az **OrgChart-Manager** szándékot, majd adja hozzá az alábbi kimondottszöveg-sablonokat:

    |Kimondottszöveg-sablon|
    |:--|
    |`Who is {Employee} the subordinate of[?]`|
    |`Who does {Employee} report to[?]`|
    |`Who is {Employee}['s] manager[?]`|
    |`Who does {Employee} directly report to[?]`|
    |`Who is {Employee}['s] supervisor[?]`|
    |`Who is the boss of {Employee}[?]`|

1. Miközben továbbra is a minták lapon válassza a **szervezeti diagram – jelentések** szándék lehetőséget, majd adja meg a következő sablon hosszúságú kimondott szöveg:

    |Kimondottszöveg-sablon|
    |:--|
    |`Who are {Employee}['s] subordinates[?]`|
    |`Who reports to {Employee}[?]`|
    |`Who does {Employee} manage[?]`|
    |`Who are {Employee} direct reports[?]`|
    |`Who does {Employee} supervise[?]`|
    |`Who does {Employee} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Végpont lekérdezése minták használata esetén

Most, hogy a mintákat hozzáadja az alkalmazáshoz, a betanításhoz, a közzétételhez és az alkalmazás lekérdezéséhez az előrejelzési futtatókörnyezet végpontján.

1. Válassza a **betanítás**lehetőséget. A betanítás befejezése után válassza a **Közzététel** lehetőséget, majd válassza ki az **üzemi** tárolóhelyet, majd válassza a **kész**lehetőséget.

1. A közzététel befejezése után váltson vissza a böngésző lapjaira a végpont URL-címe lapra.

1. Lépjen az URL-cím végéhez, és írja be a `Who is the boss of Jill Jones?` sztringet kimondott szövegként. Az utolsó querystring paraméter a `query`.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999997854
                },
                "OrgChart-Reports": {
                    "score": 6.13748343E-05
                },
                "EmployeeFeedback": {
                    "score": 8.052567E-06
                },
                "GetJobInformation": {
                    "score": 1.18197136E-06
                },
                "MoveEmployee": {
                    "score": 7.65549657E-07
                },
                "None": {
                    "score": 3.975E-09
                },
                "Utilities.StartOver": {
                    "score": 1.53E-09
                },
                "Utilities.Confirm": {
                    "score": 1.38181822E-09
                },
                "Utilities.Help": {
                    "score": 1.38181822E-09
                },
                "Utilities.Stop": {
                    "score": 1.38181822E-09
                },
                "Utilities.Cancel": {
                    "score": 1.25833333E-09
                },
                "FindForm": {
                    "score": 1.15384613E-09
                },
                "ApplyForJob": {
                    "score": 5.26923061E-10
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
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
                    "Employee": [
                        {
                            "type": "Employee",
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

A szándék előrejelzése mostantól lényegesen nagyobb mértékben bízik, és a következő legmagasabb cél a pontszám jelentősen alacsonyabb. Ez a két cél nem lesz flip-flop a betanítás során.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Elhagyható szövegek és előre összeállított entitások használata

Az oktatóanyag előző, mintául szolgáló kimondottszöveg-sablonjaiban alkalmaztunk néhány elhagyható szövegelemet, például az angol birtokos toldalékot (`'s`) és a kérdőjelet (`?`). Tegyük fel, hogy engedélyezni kell a jelenlegi és a jövőbeli dátumokat a teljes szövegben.

Példák kimondott szövegekre:

|Szándék|Kimondottszöveg-példák elhagyható szövegelemekkel és előre összeállított entitásokkal|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

A példák mindegyikében szerepel egy igeidő (`was`, `is` vagy `will be`) és egy dátum (`March 3`, `now` vagy `in a month`), amelyeket a LUIS-nak megfelelően kell tudnia előrejelezni. Figyelje meg, hogy a tábla utolsó két példája majdnem ugyanazt a szöveget használja `in` és `on`kivételével.

Példa a sablon hosszúságú kimondott szöveg, amely a következő opcionális információkat engedélyezi:

|Szándék|Kimondottszöveg-példák elhagyható szövegelemekkel és előre összeállított entitásokkal|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


A szögletes zárójeles (`[]`) szintaxis használatával az elhagyható szövegek könnyen hozzáadhatók a kimondottszöveg-sablonhoz, továbbá két szinten is beágyazhatók (`[[]]`), valamint entitásokat vagy szövegeket tartalmazhatnak.


**Kérdés: Miért van az összes `w` betű, a sablon első betűje, kisbetűs? Nem lenne opcionálisan felső vagy kisbetűs?** A lekérdezésvégpontra az ügyfélalkalmazás által küldött kimondott szöveg kisbetűssé lesz alakítva. A sablonszöveg lehet kis- és nagybetűs is, ugyanúgy, ahogy a végponti kimondott szöveg is. Az összehasonlítás minden esetben a kisbetűssé alakítást követően megy végbe.

**Kérdés: Miért nem képezi az előre összeállított szám a kimondottszöveg-sablon részét, ha a March 3 (március 3.) elemet a rendszer számként (`3`) és dátumként (`March 3`) is előrejelzi?** A kimondottszöveg-sablon környezetileg egy dátumot használ, vagy kifejezetten (`March 3`) vagy elvontan (`in a month`). A dátumok tartalmazhatnak számokat, a számok azonban nem szükségszerűen értelmezendők dátumként. Mindig használja azt az entitást, amely a leginkább megfelel az előrejelzés JSON-eredményeiben visszaküldeni kívánt típusnak.

**Kérdés: Mi a helyzet a helytelenül megfogalmazott szövegekkel, amilyen például a `Who will {Employee}['s] manager be on March 3?`?** A nyelvtanilag eltérő szerkezetű igeidőket – ahogy itt a `will` és a `be` például egymástól elszakítva szerepelnek – új kimondottszöveg-sablonként kell szerepeltetni. A meglévő kimondottszöveg-sablon nem fog egyezni ezzel. Bár a kimondott szöveg szándéka nem változott, a szavak elhelyezkedése igen. Ez a változás kihat az előrejelzésre a LUIS-ban. Ezeket a hosszúságú kimondott szöveg egyesítheti [és](#use-the-or-operator-and-groups) elvégezheti a műveletekben.

**Ne feledje: a rendszer először az entitásokat keresi meg, aztán egyezteti a mintát.**

### <a name="edit-the-existing-pattern-template-utterance"></a>A meglévő kimondottszöveg-sablon mintájának szerkesztése

1. A LUIS-portálon válassza a **Létrehozás** lehetőséget a felső menüben, majd válassza a **mintázatok** lehetőséget a bal oldali menüben.

1. Keresse meg a sablon meglévő kiválasztását, `Who is {Employee}['s] manager[?]`, majd a jobb oldalon válassza a három pontot (***..***.), majd válassza a **Szerkesztés** lehetőséget az előugró menüben.

1. Módosítsa a sablonszöveget a következőre: `who is {Employee}['s] manager [[on]{datetimeV2}?]`

### <a name="add-new-pattern-template-utterances"></a>Új kimondottszöveg-sablonminták hozzáadása

1. A **Build** (Összeállítás) menü **Patterns** (Minták) szakaszában maradva adjon hozzá néhány új kimondottszöveg-sablonmintát. Válassza ki az **OrgChart-Manager** elemet az Intent (Szándék) legördülő menüből, és írja be az alábbi kimondottszöveg-sablonokat:

    |Szándék|Kimondottszöveg-példák elhagyható szövegelemekkel és előre összeállított entitásokkal|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Válassza ki a navigációs sávon a **vonat** lehetőséget az alkalmazás betanításához.

3. A betanítás befejezése után válassza a **teszt** elemet a panel tetején a tesztelés panel megnyitásához.

4. Adjon meg több kimondott tesztszöveget, és ellenőrizze, hogy a minta egyezik-e, illetve hogy a szándék pontszáma jelentősen magasabb-e.

    Az első kimondott szöveg bevitele után válassza az **Inspect** (Vizsgálat) lehetőséget az eredmény alatt, hogy megtekinthesse az összes előrejelzési eredményt. Minden részletnek a **szervezeti diagram-kezelő** szándékkal kell rendelkeznie, és ki kell bontania az alkalmazott és a datetimeV2 entitások értékeit.

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
* magasabb előrejelzési pontszámok
* ugyanazzal a példával hosszúságú kimondott szöveg a szándék
* csak néhány jól felépített sablon hosszúságú kimondott szöveg a mintában

### <a name="use-the-or-operator-and-groups"></a>A vagy a operátor és a csoportok használata

Az előző sablon hosszúságú kimondott szöveg közül több nagyon közel van. A sablon hosszúságú kimondott szöveg csökkentéséhez használja **a `()` és** **a `|`** szintaxist.

A következő két minta egyetlen mintában egyesíthető a `()` és a `|` szintaxis használatával.

|Szándék|Kimondottszöveg-példák elhagyható szövegelemekkel és előre összeállított entitásokkal|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Az új sablon kimondása a következőket eredményezi:

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]` kérdésre adott válaszban foglalt lépéseket.

Ez egy **csoportot** használ a szükséges műveleti idő és a választható `in` és a `on` között egy **vagy** több között.

1. A **minták** lapon válassza a **szervezeti diagram – kezelő** szűrőt. Szűkítse a listát a `manager`keresésével.

1. Tartsa meg a sablon teljes verziójának egy verzióját (a következő lépésben való szerkesztéshez), és törölje a többi változatot.

1. A sablon teljes értékének módosítása a következőre:

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`

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

A további mintázatok szintaxisának használatával csökkentheti az alkalmazásban fenntartott hosszúságú kimondott szöveg számát, miközben továbbra is nagy előrejelzési pontszám szükséges.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>A kezdő és záró horgonyok használata

A minta szintaxisa egy kalap elejének és végének kiírását, `^`. A kezdő és a záró kilépési horgonyok együtt használhatók a nagyon konkrét és valószínűleg literál kifejezésekre, vagy a cél szándékok külön történő használatára.

## <a name="using-patternany-entity"></a>Minta. bármely entitás használata

A pattern.any entitás lehetővé teszi szabad formátumú adatok keresését olyankor, amikor az entitás szövegezése nehézzé teszi az entitás végének a kimondott szöveg fennmaradó részétől való elkülönítését.

Ez az Emberierőforrások-alkalmazás segítséget nyújt az alkalmazottaknak a vállalati űrlapok keresésében.

|Kimondott szöveg|
|--|
|Hol található a **HRF-123456**?|
|Ki a szerzője ennek: **HRF-123234**?|
|A **HRF-456098** űrlap francia nyelven lett közzétéve?|

Ugyanakkor minden űrlapnak két neve van: egyrészt az előző táblázatban használt formázott neve, másrészt egy könnyebb elnevezése, mint például `Request relocation from employee new to the company 2018 version 5`.

A könnyebb űrlapnévvel rendelkező kimondott szöveg a következőképpen néz ki:

|Kimondott szöveg|
|--|
|Hol található a **2018-ban a vállalathoz került alkalmazott áthelyezési kérelme, 5. verzió**?|
|Ki a szerzője ennek: **2018-ban a vállalathoz került alkalmazott áthelyezési kérelme, 5. verzió**?|
|A **2018-ban a vállalathoz került alkalmazott áthelyezési kérelme, 5. verzió** űrlap francia nyelven lett közzétéve?|

A változó hosszúságú szöveg olyan szavakat tartalmaz, amelyek megnehezítik a LUIS szolgáltatás számára az entitás végének meghatározását. A Pattern.any entitás mintában való használata lehetővé teszi az űrlapnév elejének és végének megadását, így a LUIS helyesen nyeri ki az űrlapnevet.

|Példa kimondott sablonszövegre|
|--|
|Hol található a {FormName}[?]|
|Ki a szerzője ennek: {FormName}[?]|
|A {FormName} űrlap francia nyelven lett közzétéve[?]|

### <a name="add-example-utterances-with-patternany"></a>Példaként adja hozzá a hosszúságú kimondott szöveg mintát. any

1. Válassza a **Build** (Összeállítás) lehetőséget a felső navigációs sávon, majd az **Intents** (Leképezések) elemet a bal oldali navigációs sávon.

1. A leképezések listájáról válassza a **FindForm** (Űrlap keresése) lehetőséget.

1. Adjon hozzá néhány példa kimondott szöveget:

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

1. Válassza a **+ Létrehozás**lehetőséget, írja be a `FormName`nevet, és válassza a **minta. any** típust. Kattintson a **Létrehozás** gombra.

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

## <a name="next-steps"></a>Következő lépések


> [!div class="nextstepaction"]
> [Ismerje meg a szerepkörök mintával való használatát](luis-tutorial-pattern.md)
