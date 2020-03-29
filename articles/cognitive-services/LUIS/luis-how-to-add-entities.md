---
title: Entitások hozzáadása - LUIS
titleSuffix: Azure Cognitive Services
description: Entitások létrehozása a kulcsadatok kinyerése a felhasználói utterances a language understanding (LUIS) alkalmazásokban. A kinyert entitásadatokat az ügyfélalkalmazás használja a fullfil ügyfélkérelmekhez.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220907"
---
# <a name="add-entities-to-extract-data"></a>Entitások hozzáadása adatok kinyeréséhez 

Entitások létrehozása a kulcsadatok kinyerése a felhasználói utterances a language understanding (LUIS) alkalmazásokban. A kinyert entitásadatokat az ügyfélalkalmazás használja a fullfil ügyfélkérelmekhez.

Az entitás egy szót vagy kifejezést jelöl az utterance (kifejezés) kibontásához kívánt kibontáshoz. Az entitások a szándékszempontjából releváns információkat írnak le, és néha elengedhetetlenek az alkalmazás feladatának elvégzéséhez. Entitásokat hozhat létre, ha egy példa utterance (kifejezés egy szándék, vagy eltekintve (előtte vagy után) hozzáadása egy példa utterance (kifejezés egy szándékot.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Entitások tervezése, majd létrehozás és címkézés

Gépmegtanult entitások hozhatók létre a példa utterances vagy az entitások lapról hozható **létre.** 

Általában ajánlott az, hogy időt az entitások tervezésével, mielőtt egy gép megtanult entitás a portálon. Ezután hozza létre a gép megtanult entitást a példa utterance (kifejezés) az alösszetevők és leírók és korlátozások, mint tudod abban az időben. A [lebomló entitás oktatóanyag](tutorial-machine-learned-entity.md) bemutatja, hogyan kell használni ezt a módszert. 

Az entitások tervezésének részeként előfordulhat, hogy tudnia kell, hogy szövegegyeztetési entitásokra van szüksége (például előre összeállított entitásokra, reguláris kifejezés entitásokra vagy listaentitásokra). Ezeket az **Entitások** lapon hozhatja létre, mielőtt a példa kimondott szövegekben meg vannak címkézve. 

A címkézés során megjelölheti az egyes entitásokat, majd felépítheti a szülő gép által megtanult entitást. Vagy kezdheti a szülő gép által megtanult entitás, és gyermek entitásokba bomlaszt. 

> [!TIP] 
>Címkézze fel az összes olyan szót, amely egy entitást jelezhet, még akkor is, ha a szavakat nem használja az ügyfélalkalmazásban történő kibontáskor. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Entitás létrehozása címkézés előtt vagy azzal

Az alábbi táblázat segítségével megismerheti, hogy mely entitásokat hozhat létre vagy adhat hozzá az egyes entitásokhoz az alkalmazáshoz. 

|Entitástípus|Hol hozhat létre entitást a LUIS-portálon?|
|--|--|
|Gépi tanulással létrejött entitás|Entitások vagy szándék részletei|
|Listaentitás|Entitások vagy szándék részletei|
|Reguláriskifejezés-entitás|Entitások|
|Pattern.any entitás|Entitások|
|Előre összeállított entitás|Entitások|
|Előre összeállított tartományi entitás|Entitások|

Létrehozhatja az összes entitást az **Entitások** lapon, vagy létrehozhat néhány entitást az entitás címkézésének részeként a szándék részletei lapon a példa utterance **(kifejezés)** feliratában. Csak _egy entitást címkézhet_ egy példa utterance (kifejezés) a **szándék részletes** oldal. 

## <a name="create-a-machine-learned-entity"></a>Gépmegtanult entitás létrehozása

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Szövegegyezési entitás létrehozása

A szövegegyezési entitások használata számos lehetőséget kínál az adatok kinyerésére:

|Szövegegyezési entitások|Cél|
|--|--|
|[Listaentitás](#add-list-entities-for-exact-matches)|a kanonikus nevek listája, valamint a szinonimák alternatív formájaként|
|Reguláriskifejezés-entitás|szöveg egyeztetése reguláris kifejezés entitás használatával|
|[Előre összeállított entitás](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|olyan közös adattípusoknak, mint a szám, az e-mail, a dátum|
|Előre összeállított tartományi entitás|egyezés a kijelölt tárgytartományokkal|
|[Minta.minden](#add-a-patternany-entity)| olyan entitásokat egyeztet, amelyek könnyen összetéveszthetők a környező szöveggel|  

Az előre összeállított entitások egyéni betanítási adatok megadása nélkül működnek. A többi entitásnak meg kell adnia az ügyfélbetanítási adatokat (például a Lista entitás tételeit) vagy egy kifejezést (például reguláris kifejezést vagy pattern.any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Új egyéni entitás létrehozása

1. A LUIS portálon nyissa meg a **Kezelés** szakaszt, majd az **Entitások** lapot. 
1. Válassza a **+ Create**lehetőséget, majd válassza ki az entitástípust. 
1. Folytassa az entitás konfigurálását, majd válassza a **Létrehozás** lehetőséget, amikor elkészült. 

### <a name="add-list-entities-for-exact-matches"></a>Listaentitások hozzáadása pontos egyezéshez

A listaentitások a kapcsolódó szavak rögzített, zárt készletét képviselik. Amíg ön, mint a szerző, módosíthatja a listát, a LUIS nem növekszik, vagy zsugorítja a listát. Importálhat egy meglévő listaentitásba is egy [listaentitás .json formátum(reference-entity-list.md#example-json-import-into-list-entity) használatával. 

Az alábbi lista a gyűjtőnevet és a szinonimákat mutatja be. 

|Szín - listaelem neve|Szín - szinonimák|
|--|--|
|Vörös|bíbor, vér, alma, tűzoltóautó|
|Kék|ég, azúrkék, kobalt|
|Zöld|kelly, mész|

Az eljárás sal hozzon létre egy listaentitást. A listaentitás létrehozása után nem kell címkéznie a példák kimondott szöveget egy szándékban. A listaelemek és szinonimák pontos szöveggel egyeztetve jelennek meg. 

1. A **Build (Build)** csoportban válassza az **Entitások** lehetőséget a bal oldali panelen, majd kattintson a + Create ( **Létrehozás )** lehetőségre.

1. Az **Entitástípus létrehozása** párbeszédpanelen adja meg az entitás `Colors` nevét, például a **List (Lista)** lehetőséget.
1. A **Listaentitás létrehozása** párbeszédpanel Új **allista ....** mezőjébe írja be a `Green`listaelem nevét, például a tanusítményeket.

    > [!div class="mx-imgBorder"]
    > ![Hozzon létre egy színlistát listaentitásként az Entitás részletei lapon.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Ha befejezte a listaelemek és szinonimák hozzáadását, válassza a **Létrehozás gombot.**

    Ha végzett az alkalmazás módosítási csoportjával, ne felejtse el **betanítani** az alkalmazást. Egyetlen módosítás után ne tanítsa be az alkalmazást. 

    > [!NOTE]
    > Ez az eljárás bemutatja egy listaentitás létrehozását és címkézését egy példa utterance (kifejezés) kifejezési **módból** a Szándék részletei lapon. Ugyanazt az entitást az Entitások lapon is **létrehozhatja.**

## <a name="add-a-role-for-an-entity"></a>Szerepkör hozzáadása entitáshoz

A szerepkör egy entitás nevesített altípusa a környezet alapján. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Szerepkör hozzáadása a különböző környezetek megkülönböztetéséhez

A következő utterance (kifejezés) két helyen, és mindegyik van megadva szemantikailag a szavak körül, mint `to` például: és: `from` 

`Pick up the package from Seattle and deliver to New York City.`

Ebben az eljárásban adjon hozzá `origin` és `destination` szerepköröket egy előre összeállított geographyV2 entitáshoz.

1. A **Build (Build)** szakaszban válassza az **Entitások lehetőséget** a bal oldali panelen.

1. Válassza **a + Előre összeállított entitás hozzáadása**lehetőséget. Válassza a **geographyV2 lehetőséget,** majd a **Kész**lehetőséget. Ez egy előre összeállított entitást ad hozzá az alkalmazáshoz.
    
    Ha azt tapasztalja, hogy a Pattern.any entitást tartalmazó minta nem megfelelően vonja ki az entitásokat, egy [explicit lista](reference-pattern-syntax.md#explicit-lists) megoldhatja a problémát. 

1. Válassza ki az újonnan hozzáadott előre összeállított geographyV2 entitást az Entitások lapon **entitások** listájából. 
1. Új szerepkör hozzáadásához **+** jelölje be a **Nincs hozzáadott szerepkör**lehetőséget.
1. A **Típus szerepkör...** mezőbe írja be a `Origin` szerepkör nevét, majd írja be. Adja meg a `Destination` második szerepkör nevét, majd írja be. 

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép: Origin szerepkör hozzáadása a Hely entitáshoz](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    A szerepkör hozzáadódik az előre összeállított entitáshoz, de nem kerül hozzá az adott entitás használatával megjelenő kimondott szöveghez. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Szöveg felirata egy példa utterance (kifejezés) példaszövegben szerepkörrel

1. Ugrás a szándék részletei lap, amely a szerepkört használó példautterances. 
1. A szerepkörrel való címkézéshez jelölje ki az entitásfeliratot (tömör vonal a szöveg alatt) a példa kimondott szövegben, majd válassza a **Nézet entitáspalettikán** lehetőséget a legördülő listából. 

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép: a Nézet entitáspalettán lehetőséget választva](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    Az entitáspaletta jobbra nyílik meg. 

1. Jelölje ki az entitást, majd lépjen a paletta aljára, és válassza ki a szerepkört. 

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép: a Nézet entitáspalettán lehetőséget választva](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Minta hozzáadása.any entitás

[Pattern.any](luis-concept-entity-types.md) entitások csak [mintákban](luis-how-to-model-intent-pattern.md)érvényesek, nem szándékok példa utterances. Ez a típusú entitás segít a LUIS-nak megtalálni a különböző hosszúságú és szóválasztási entitások végét. Mivel ez az entitás egy mintában használatos, a LUIS tudja, hogy az entitás vége hol van az utterance (kifejezés) sablonban.

### <a name="steps-to-create-a-patternany-entity"></a>Minta létrehozásának lépései.any entitás

1. A **Build (Build)** csoportban válassza az **Entitások** lehetőséget a bal oldali panelen, majd kattintson a + Create ( **Létrehozás )** lehetőségre.

1. Az **Entitástípus kiválasztása** párbeszédpanelen írja be az entitás nevét a **Név** mezőbe, és válassza a **Minta lehetőséget.A** **Típus** lehetőséget válassza a **Létrehozás gombra.**

    Miután [létrehozott egy minta utterance (kifejezés)](luis-how-to-model-intent-pattern.md) ezzel az entitással, az entitás kinyeri a kombinált gép megtanult és szöveg-egyeztetési algoritmus. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Mintasablon utterances használatával pattern.any entitás

A pattern.any entitás használatához adjon hozzá egy mintát a **Minták** lapon az **Alkalmazás teljesítményének javítása** szakaszban a megfelelő kapcsos zárójel szintaxissal, például `Where is **{HumanResourcesFormTitle}** on the server?`.

Ha azt tapasztalja, hogy a Pattern.any entitást tartalmazó minta nem megfelelően vonja ki az entitásokat, egy [explicit lista](reference-pattern-syntax.md#explicit-lists) megoldhatja a problémát. 

## <a name="do-not-change-entity-type"></a>Entitástípus módosítása nem változik

A LUIS nem teszi lehetővé az entitás típusának módosítását, mert nem tudja, mit vegyen fel vagy távolítson el az entitás létrehozásához. A típus módosításához jobb, ha egy új, megfelelő típusú entitást hoz létre egy kissé eltérő névvel. Az entitás létrehozása után minden utterance (kifejezés) távolítsa el a régi címkézett entitás nevét, és adja hozzá az új entitás nevét. Miután az összes utterances újracímkézve, törölje a régi entitást. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Előre összeállított modellek használata](howto-add-prebuilt-models.md) 

További információk:
* Hogyan kell a [vonat](luis-how-to-train.md)
* Hogyan kell [tesztelni](luis-interactive-test.md)
* Hogyan lehet [közzétenni](luis-how-to-publish-app.md)
* Minták:
    * [Fogalmak](luis-concept-patterns.md)
    * [Szintaxis](reference-pattern-syntax.md)
* [Előre összeállított entitások GitHub-tárház](https://github.com/Microsoft/Recognizers-Text)
* [Adatkinyerési fogalmak](luis-concept-data-extraction.md)


 
