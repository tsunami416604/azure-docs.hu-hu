---
title: Entitások hozzáadása – LUIS
titleSuffix: Azure Cognitive Services
description: Entitásokat hozhat létre a Language Understanding (LUIS) alkalmazások felhasználói hosszúságú kimondott szöveg származó kulcsfontosságú adatok kinyeréséhez.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 54c9d79c62052daeee76de5dffb1099dc7d75180
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467722"
---
# <a name="create-entities-without-utterances"></a>Entitások létrehozása hosszúságú kimondott szöveg nélkül

Az entitás egy szót vagy kifejezést jelöl a kinyerni kívánt szövegen belül. Az entitások osztályt jelölnek, beleértve a hasonló objektumok (helyek, dolgok, személyek, események vagy fogalmak) gyűjteményét. Az entitások a szándékkal kapcsolatos információkat írják le, és esetenként elengedhetetlenek, hogy az alkalmazás elvégezze a feladatát. Entitásokat akkor hozhat létre, ha egy teljes leképezést ad hozzá egy szándékhoz, vagy kívülről (előtte vagy után).

A LUIS-alkalmazásban lévő entitásokat **az entitások lap** **entitások listájában** adhatja hozzá, szerkesztheti vagy törölheti. A LUIS két fő típusú entitást kínál: [előre összeépített entitásokat](luis-reference-prebuilt-entities.md)és saját [egyéni entitásokat](luis-concept-entity-types.md#types-of-entities).

Miután létrehozta a gép által megtanult entitást, meg kell jelölnie az entitást az összes, az általa megjelenő cél teljes tartalmában.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Előre elkészített entitás hozzáadása az alkalmazáshoz

Az alkalmazásokhoz hozzáadott közös előre elkészített entitások *száma* -és *datetimeV2*. 

1. Az alkalmazásban a **Létrehozás** szakaszban válassza az **entitások** lehetőséget a bal oldali panelen.
 
1. Az **entitások** lapon válassza az **előre elkészített entitások hozzáadása**elemet.

1. Az **előre felépített entitások hozzáadása** párbeszédpanelen válassza ki az előre elkészített entitások **számát** és **datetimeV2** . Ezután válassza a **Done** (Kész) elemet.

    ![Képernyőkép az előre elkészített entitás hozzáadása párbeszédpanelről](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Egyszerű entitások hozzáadása egyetlen fogalomhoz

Egy egyszerű entitás egyetlen koncepciót ismertet. A következő eljárással hozhat létre olyan entitást, amely Kinyeri a vállalati részlegek nevét, például az *emberi erőforrásokat* vagy a *műveleteket*.   

1. Az alkalmazásban válassza a létrehozás **szakaszt,** majd válassza az **entitások** lehetőséget a bal oldali panelen, majd válassza az **új entitás létrehozása**lehetőséget.

1. Az előugró párbeszédpanelen írja be a `Location` értéket az **entitás neve** mezőbe, válassza az **egyszerű** lehetőséget az **entitás típusa** listából, majd válassza a **kész**lehetőséget.

    Az entitás létrehozása után ugorjon az összes olyan leképezésre, amely az entitást tartalmazó hosszúságú kimondott szöveg tartalmaz. Jelölje ki a példában szereplő szöveget, és jelölje meg a szöveget entitásként. 

    A [kifejezések listáját](luis-concept-feature.md) általában egy egyszerű entitás jelzésének növelésére használják.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Reguláris kifejezések entitások hozzáadása a jól strukturált fogalmakhoz

A reguláris kifejezéssel rendelkező entitások az Ön által megadott reguláris kifejezésen alapuló adatok lekérésére szolgálnak. 

1. Az alkalmazásban válassza az **entitások** lehetőséget a bal oldali navigációs sávon, majd válassza az **új entitás létrehozása**lehetőséget.

1. Az előugró párbeszédpanelen írja be a `Human resources form name` értéket az **entitás neve** mezőbe, válassza ki a **reguláris kifejezés** lehetőséget az **entitás típusa** listából, adja meg a reguláris kifejezést, `hrf-[0-9]{6}`, majd kattintson a **kész**gombra. 

    Ez a reguláris kifejezés megfelel a literális karaktereknek `hrf-`, majd 6 számjegyből áll, hogy egy emberi erőforrás űrlaphoz tartozó számot képviselje.

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Összetett entitások hozzáadása egy szülő-gyermek kapcsolathoz a csoportba

Összetett entitás létrehozásával különböző típusú entitások közötti kapcsolatokat határozhat meg. A következő példában az entitás egy reguláris kifejezést és egy előre összeépített nevű entitást tartalmaz.  

A teljes `Send hrf-123456 to John Smith`a szöveg `hrf-123456` egy emberi erőforrás [reguláris kifejezésnek](#add-regular-expression-entities) felel meg, és a `John Smith` az előre elkészített entitás personName van kibontva. Minden entitás egy nagyobb, szülő entitás részét képezi. 

1. Az alkalmazásban válassza az **entitások** lehetőséget a **Build** szakasz bal oldali navigációs sávján, majd válassza az **előre felépített entitás hozzáadása**elemet.

1. Adja hozzá az előre elkészített entitás **PersonName**. Útmutatásért lásd: [előre összeépített entitások hozzáadása](#add-prebuilt-entity). 

1. Válassza az **entitások** lehetőséget a bal oldali navigációs sávon, majd válassza az **új entitás létrehozása**lehetőséget.

1. Az előugró párbeszédpanelen írja be a `SendHrForm` értéket az **entitás neve** mezőbe, majd válassza az **összetett** lehetőséget az **entitás típusa** listából.

1. Új gyermek hozzáadásához válassza a **gyermek hozzáadása** lehetőséget.

1. A **gyermek #1**területen válassza ki az entitás **számát** a listából.

1. A **gyermek #2**területen válassza ki az entitás **emberi erőforrások űrlapjának nevét** a listából. 

1. Válassza a **Done** (Kész) lehetőséget.

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Minta hozzáadása. bármely entitás a szabad formátumú entitások rögzítéséhez

[Minta. az](luis-concept-entity-types.md) entitások csak [mintákban](luis-how-to-model-intent-pattern.md)érvényesek, nem szándékok. Ez a típusú entitás segítséget nyújt a LUIS számára, hogy megkeresse a különböző hosszúságú és a Word által választott entitások végét. Mivel ez az entitás egy mintában van használatban, LUIS tudja, hogy az entitás vége a teljes sablonban van-e.

Ha egy alkalmazásnak `FindHumanResourcesForm` szándéka van, akkor a kinyert űrlap címe zavarhatja a szándék-előrejelzést. Annak tisztázásához, hogy mely szavak szerepelnek az űrlap címében, használjon mintát. a mintázaton belül. A LUIS-előrejelzés a teljes kifejezéssel kezdődik. Először is a rendszer ellenőrzi és egyezteti az entitásokat, amikor az entitások találhatók, majd a minta be van jelölve, és egyeztetve van. 

A teljes `Where is Request relocation from employee new to the company on the server?`az űrlap címe trükkös, mert nem egyértelmű, hogy hol fejeződik be a cím, és hogy hol kezdődik a Kimondás. A címek tetszőleges szavakat tartalmazhatnak, többek között egyetlen szót, a központozás összetett kifejezéseit és a szavak értelmetlen rendezését. A minta lehetővé teszi, hogy olyan entitást hozzon létre, amelyben a teljes és a pontos entitás kinyerhető. A cím megtalálása után a rendszer a `FindHumanResourcesForm` szándékot jelzi, mert ez a minta szándéka.

1. A **build (létrehozás** ) szakaszban válassza az **entitások** lehetőséget a bal oldali panelen, majd válassza az **új entitás létrehozása**lehetőséget.

1. Az **entitás hozzáadása** párbeszédpanelen írja be a `HumanResourcesFormTitle` értéket az **entitás neve** mezőbe, és válassza a **minta. any** elemet az entitás **típusaként**.

    A minta. bármely entitás használatához vegyen fel egy mintát a **minták** lapon az **alkalmazás javítása** szakaszban, a megfelelő kapcsos zárójel szintaxissal, például `Where is **{HumanResourcesFormTitle}** on the server?`.

    Ha azt tapasztalja, hogy a Pattern.any entitást tartalmazó minta nem megfelelően vonja ki az entitásokat, egy [explicit lista](luis-concept-patterns.md#explicit-lists) megoldhatja a problémát. 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Szerepkör hozzáadása a különböző kontextusok megkülönböztetéséhez

A szerepkörök névvel ellátott altípusok a kontextus alapján. Minden entitásban elérhető, beleértve az előre összeépített és a nem géppel megismert entitásokat is. 

A szerepkör szintaxisa **`{Entityname:Rolename}`** , ahol az entitás nevét egy kettőspont, majd a szerepkör neve követi. Például: `Move {personName} from {Location:Origin} to {Location:Destination}`.

1. A **build (létrehozás** ) szakaszban válassza az **entitások** lehetőséget a bal oldali panelen.

1. Válassza a **Create new entity** (Új entitás létrehozása) lehetőséget. Adja meg `Location`nevét. Válassza ki az **egyszerű** típust, és válassza a **kész**lehetőséget. 

1. Válassza ki az **entitások** lehetőséget a bal oldali panelen, majd válassza az előző lépésben létrehozott új entitás **helyet** .

1. A **szerepkör neve** szövegmezőbe írja be `Origin` szerepkör nevét, és adja meg a nevet. Adja hozzá a `Destination`második szerepkörének nevét. 

    ![Képernyőkép a forrásként szolgáló szerepkör hozzáadásáról a Location entitáshoz](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Lista entitások hozzáadása a pontos egyezésekhez

A lista entitások rögzített, lezárt kapcsolódó szavakat jelölnek. 

Az emberi erőforrások alkalmazás esetében az összes részleg listáját, valamint a részlegek szinonimáit is használhatja. Az entitás létrehozásakor nem kell tudnia az összes értéket. A valós felhasználói hosszúságú kimondott szöveg szinonimákkal való áttekintése után további hozzáadást is hozzáadhat.

1. A **build (létrehozás** ) szakaszban válassza az **entitások** lehetőséget a bal oldali panelen, majd válassza az **új entitás létrehozása**lehetőséget.

1. Az **entitás hozzáadása** párbeszédpanelen írja be a `Department` értéket az **entitás neve** mezőbe, és válassza a **lista** lehetőséget az **entitás típusaként**. Válassza a **Done** (Kész) lehetőséget.
  
1. Az entitások listázása lap lehetővé teszi a normalizált nevek hozzáadását. Az **értékek** szövegmezőbe írja be a lista részlegének nevét, például `HumanResources`, majd nyomja le az ENTER billentyűt a billentyűzeten. 

1. A normalizált értéktől jobbra adja meg a szinonimákat, majd minden elem után nyomja le az ENTER billentyűt a billentyűzeten.

1. Ha további normalizált elemeket szeretne használni a listához, válassza a **javasolt** lehetőséget a [szemantikai szótár](luis-glossary.md#semantic-dictionary)beállításainak megjelenítéséhez.

    ![Képernyőkép az ajánlott funkció kiválasztásáról a lehetőségek megjelenítéséhez](./media/add-entities/hr-list-2.png)


1. Válasszon ki egy elemet az ajánlott listán, és adja hozzá normalizált értékként, vagy válassza az **összes hozzáadása** lehetőséget az összes elem hozzáadásához. 
    Az értékeket a következő JSON formátum használatával importálhatja egy meglévő listára:

    ```JSON
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

<a name="change-entity-type"></a>

## <a name="do-not-change-entity-type"></a>Entitás típusának módosítása

LUIS nem teszi lehetővé az entitás típusának módosítását, mert nem tudja, mit kell hozzáadni vagy eltávolítani az entitás létrehozásához. A típus módosításához jobb, ha a megfelelő típusú új entitást kis mértékben eltérő névvel hozza létre. Az entitás létrehozása után minden egyes Kimondás után távolítsa el a régi címkével ellátott entitás nevét, és adja hozzá az új entitás nevét. Ha az összes hosszúságú kimondott szöveg újracímkézték, törölje a régi entitást. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Minta létrehozása példa Kimondás alapján

Tekintse [meg a minta hozzáadása meglévő kifejezésből a szándék vagy az entitás oldalon](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Az alkalmazás betanítása a modell entitásokkal való módosítása után

Az entitások hozzáadása, szerkesztése vagy eltávolítása után az alkalmazás [betaníthatja](luis-how-to-train.md) és [közzéteheti](luis-how-to-publish-app.md) a módosításokat, hogy azok hatással legyenek a végponti lekérdezésekre. 

## <a name="next-steps"></a>További lépések

További információ az előre elkészített entitásokról: [felismerők – Text](https://github.com/Microsoft/Recognizers-Text) projekt. 

További információ arról, hogy az entitás hogyan jelenik meg a JSON-végpont lekérdezési válaszában: [adatok kinyerése](luis-concept-data-extraction.md)

Most, hogy hozzáadta a szándékokat, a hosszúságú kimondott szöveg és az entitásokat, rendelkezik egy alapszintű LUIS-alkalmazással. Ismerje meg, hogyan lehet [betanítani](luis-how-to-train.md)az alkalmazást, [tesztelni](luis-interactive-test.md)és [közzétenni](luis-how-to-publish-app.md) .
 
