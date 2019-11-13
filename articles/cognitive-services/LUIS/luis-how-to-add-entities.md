---
title: Entitások hozzáadása – LUIS
titleSuffix: Azure Cognitive Services
description: Entitásokat hozhat létre a Language Understanding (LUIS) alkalmazások felhasználói hosszúságú kimondott szöveg származó kulcsfontosságú adatok kinyeréséhez. Az ügyfélalkalmazás a kinyert fullfil használja.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: ed100c27d482065e244bb3dc2cca3b66dfc11986
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013469"
---
# <a name="add-entities-to-extract-data"></a>Entitások hozzáadása az adatok kinyeréséhez 

Entitásokat hozhat létre a Language Understanding (LUIS) alkalmazások felhasználói hosszúságú kimondott szöveg származó kulcsfontosságú adatok kinyeréséhez. Az ügyfélalkalmazás a kinyert entitások adatait használja az fullfilához.

Az entitás egy szót vagy kifejezést az utterance (kifejezés), amelyeket szeretne kinyert belül jelöli. Entitások ismertetik a leképezés kapcsolódó információk, és néha nélkülözhetetlenek az alkalmazás a feladat végrehajtásához. Entitásokat akkor hozhat létre, ha egy példa szerinti kiválasztást ad hozzá egy szándékhoz, vagy kívülről (előtte vagy utána), ha egy Kimondás kiválasztását adja hozzá egy szándékhoz.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="creating-an-entity-is-different-from-labeling-an-entity"></a>Az entitások létrehozása különbözik az entitások címkézésével

Először létre kell hoznia egy entitást, mielőtt címkézi az entitást a példában szereplő kifejezéssel. 

Az alábbi táblázat segítségével megtudhatja, hogy mely entitások hozzanak létre vagy adjon hozzá minden entitást az alkalmazáshoz. 

|Entitás típusa|Hol hozható létre entitás a LUIS-portálon|
|--|--|
|Gépi megtanult entitás|Entitások vagy leképezés részletei|
|Listaentitás|Entitások vagy leképezés részletei|
|Reguláriskifejezés-entitás|Entitások|
|Pattern.any entitás|Entitások|
|Előre összeállított entitások|Entitások|
|Előre elkészített tartományi entitás|Entitások|

**Az entitások lapon** hozhatja létre az összes entitást, vagy létrehozhat néhány entitást az entitás címkévé való címkézésének részeként a **leképezés részletei** lapon. Csak egy entitást lehet _címkével ellátni_ a **leképezés részletei** lapról. 

## <a name="create-a-machine-learned-entity"></a>Gépi megtanult entitás létrehozása

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Szöveggel egyező entitás létrehozása

A Text-Matching entitások használata számos módszert kínál az adatok kinyerésére:

|Szöveggel egyező entitások|Cél|
|--|--|
|[Entitás listázása](#add-list-entities-for-exact-matches)|kanonikus nevek listája a szinonimákkal együtt alternatív űrlapokként|
|Reguláriskifejezés-entitás|szöveg egyeztetése reguláris kifejezéssel rendelkező entitás használatával|
|[Előre elkészített entitás](tutorial-machine-learned-entity.md#add-prebuilt-number-to-app-to-help-extract-data)|közös adattípusok (például szám, e-mail, dátum) egyeztetése|
|Előre elkészített tartományi entitás|egyezés a kijelölt tárgyi tartományok használatával|
|[Pattern.any](#add-a-patternany-entity)| a környező szöveggel könnyen összetéveszthető entitások egyeztetése|  

Az előre elkészített entitások egyéni betanítási adatmennyiség nélkül működnek. A többi entitásnak szüksége van az ügyfél-betanítási adatok (például a lista entitás elemei) vagy egy kifejezés (például egy reguláris kifejezés vagy minta) megadására.

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Új egyéni entitás létrehozása

1. A LUIS-portálon válassza a **kezelés** szakaszt, majd az **entitások** lapot. 
1. Válassza a **+ Létrehozás**lehetőséget, majd válassza ki az entitás típusát. 
1. Folytassa az entitás konfigurálását, majd válassza a **Létrehozás** lehetőséget, ha elkészült. 

### <a name="add-list-entities-for-exact-matches"></a>Lista entitások hozzáadása a pontos egyezésekhez

Lista entitások kapcsolódó szavakat rögzített, lezárt csoportját képviselik. Amíg a szerző megváltoztathatja a listát, a LUIS nem fogja növelni vagy csökkenteni a listát. Egy meglévő List entitásba is importálhat egy [List Entity. JSON formátumú (hivatkozás-entitás-lista. MD # példa – JSON-to-import-into-List-Entity). 

A következő lista a kanonikus nevet és a szinonimákat mutatja be. 

|Szín listaelem neve|Szín – szinonimák|
|--|--|
|Piros|Crimson, Blood, Apple, Fire-Engine|
|Kék|égbolt, Azure, kobalt|
|Zöld|Kelly, lime|

A List entitás létrehozásához kövesse az eljárást. A lista entitás létrehozása után nem kell megadnia például a hosszúságú kimondott szöveg. A listaelemek és a szinonimák pontos szöveggel egyeznek meg. 

1. A **build (létrehozás** ) szakaszban válassza az **entitások** lehetőséget a bal oldali panelen, majd válassza a **+ Létrehozás**elemet.

1. Az **entitás típusának létrehozása** párbeszédpanelen adja meg az entitás nevét, például `Colors`, majd a **lista**lehetőséget.
1. Az **entitások listájának létrehozása** párbeszédpanel **új allista hozzáadása.** .. elemében adja meg a listaelem nevét, például `Green`, majd vegyen fel szinonimákat.

    > [!div class="mx-imgBorder"]
    > ![hozzon létre egy listát a színek listájaként az entitás részletei lapon.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Ha elkészült listaelemek és szinonimák hozzáadásával, válassza a **Létrehozás**elemet.

    Ha végzett az alkalmazás változásainak egy csoportján, ne felejtse el **betanítani** az alkalmazást. Egyetlen módosítás után ne tanítsa az alkalmazást. 

    > [!NOTE]
    > Ez az eljárás azt mutatja be, hogyan hozhat létre és címkéz fel egy lista entitást egy példa nélküli kifejezésből a **leképezés részletei** lapon. Ugyanezt az entitást is létrehozhatja az **entitások** lapon.

## <a name="add-a-role-for-an-entity"></a>Szerepkör hozzáadása egy entitáshoz

A szerepkör egy entitás névvel ellátott altípusa, a kontextus alapján. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Szerepkör hozzáadása a különböző kontextusok megkülönböztetéséhez

A következő Kimondás során két helyen van, és mindegyiket szemantikailag, a körülötte található szavak határozzák meg, például `to` és `from`: 

`Pick up the package from Seattle and deliver to New York City.`

Ebben az eljárásban hozzáadhat `origin` és `destination` szerepköröket egy előre elkészített geographyV2-entitáshoz.

1. Az a **összeállítása** szakaszban jelölje be **entitások** a bal oldali panelen.

1. Válassza az **+ előre összeépített entitás hozzáadása**elemet. Válassza a **geographyV2** lehetőséget, majd válassza a **kész**lehetőséget. Ezzel egy előre elkészített entitást adhat hozzá az alkalmazáshoz.
    
    Ha azt tapasztalja, hogy a Pattern.any entitást tartalmazó minta nem megfelelően vonja ki az entitásokat, egy [explicit lista](reference-pattern-syntax.md#explicit-lists) megoldhatja a problémát. 

1. Válassza ki az újonnan hozzáadott előre elkészített geographyV2 entitást **az entitások lap** entitások listájában. 
1. Új szerepkör hozzáadásához válassza a **+** melletti **szerepkörök**hozzáadása lehetőséget.
1. A **szerepkör..** . szövegmezőbe írja be a szerepkör nevét `Origin` majd írja be. Adja hozzá a második szerepkör nevét `Destination` majd írja be a nevet. 

    > [!div class="mx-imgBorder"]
    > ![a következő képernyőképet arról, hogyan adhat hozzá forrás szerepkört a Location entitáshoz](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    A szerepkör hozzá lett adva az előre összeépített entitáshoz, de az adott entitást használó egyik hosszúságú kimondott szöveg sincs hozzájuk adva. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Egy példa Kimondás szerepkörrel rendelkező szöveg felirata

1. Ugrás a szándék részletei lapra, amely a szerepkört használó hosszúságú kimondott szöveg tartalmaz. 
1. Ha címkével szeretne felcímkézni a szerepkört, válassza ki az entitás címkéjét (szöveg alatti folytonos vonal), majd a legördülő listából válassza ki a **Megtekintés az entitások palettán** lehetőséget. 

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a nézet kiválasztásáról az Entity paletta-ban](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    Az entitás paletta a jobb oldalon jelenik meg. 

1. Válassza ki az entitást, majd lépjen a paletta aljára, és válassza ki a szerepkört. 

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a nézet kiválasztásáról az Entity paletta-ban](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Minta hozzáadása bármely entitáshoz

[Minta. az](luis-concept-entity-types.md) entitások csak [mintákban](luis-how-to-model-intent-pattern.md)érvényesek, nem pedig a "példa hosszúságú kimondott szöveg". Az ilyen típusú entitás segít a LUIS-entitások különböző hossza és a word választott végén található. Ehhez az entitáshoz mintát használja, mert a LUIS tudja, ahol az entitás végén szerepel-e az utterance (kifejezés) sablont.

### <a name="steps-to-create-a-patternany-entity"></a>A minta létrehozásának lépései. bármely entitás

1. A **build (létrehozás** ) szakaszban válassza az **entitások** lehetőséget a bal oldali panelen, majd válassza a **+ Létrehozás**elemet.

1. Az **entitás típusának kiválasztása** párbeszédpanelen adja meg az entitás nevét a **név** mezőben, majd válassza a minta lehetőséget **. bármely** **típusnál** válassza a **Létrehozás**lehetőséget.

    Miután [létrehozta a mintázatot](luis-how-to-model-intent-pattern.md) az entitás használatával, az entitást egy kombinált géppel megismert és szöveggel egyező algoritmussal kell kibontani. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Minta sablonhoz való Kimondás létrehozása mintaként. bármely entitás

A pattern.any entitást használja, adjon hozzá egy minta a **minták** lap a **megnövelheti az alkalmazások teljesítményét** részben, a megfelelő kapcsos zárójel szintaxissal, mint például `Where is **{HumanResourcesFormTitle}** on the server?`.

Ha azt tapasztalja, hogy a Pattern.any entitást tartalmazó minta nem megfelelően vonja ki az entitásokat, egy [explicit lista](reference-pattern-syntax.md#explicit-lists) megoldhatja a problémát. 

## <a name="do-not-change-entity-type"></a>Entitás típusának módosítása

A LUIS nem engedi, hogy az entitás típusa módosítható, mert ez nem tudja, hogy melyik hozzáadása vagy eltávolítása, hogy az entitás létrehozásához. Annak érdekében, hogy módosítsa a típusát, célszerűbb a megfelelő típusú új entitás létrehozása egy kis mértékben eltérő nevű. Az entitás létrehozása után minden kimondásakor, távolítsa el a régi címkézett entitás nevét, és adja hozzá az új entitás nevét. Miután a kimondott szöveg rendelkezik lett relabeled, a régi entitás törlése. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Következő lépések

További információ a mintákról:

* [Minták – fogalmak](luis-concept-patterns.md)
* [Mintázatok szintaxisa](reference-pattern-syntax.md)

Előre összeállított entitások kapcsolatos további információkért lásd: a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text) projekt. 

További információ az entitás hogyan jelenik meg a JSON-végpont lekérdezésekre adott válaszok: [Adatkinyerés](luis-concept-data-extraction.md)

Most, hogy hozzáadta a szándék fog vonatkozni, utterances és entitások, akkor alapszintű LUIS-alkalmazásokon. Ismerje meg, hogyan [betanításához](luis-how-to-train.md), [tesztelése](luis-interactive-test.md), és [közzététele](luis-how-to-publish-app.md) az alkalmazást.
 
