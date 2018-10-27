---
title: Entitások hozzáadása a LUIS-alkalmazások
titleSuffix: Azure Cognitive Services
description: A Language Understanding (LUIS) alkalmazások hozzáadása a entitások (a tartomány az alkalmazás legfontosabb adatok).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/24/2018
ms.author: diberry
ms.openlocfilehash: 178f4314f9ede86444ee60fd2a64f85dc283080b
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138531"
---
# <a name="create-entities-without-utterances"></a>Beszédmódok nélkül entitások létrehozása

Az entitás egy szót vagy kifejezést az utterance (kifejezés), amelyeket szeretne kinyert belül jelöli. Az utterance (kifejezés) is tartalmazhat számos entitás vagy nincs minden. Egy entitás egy osztályt, beleértve a hasonló objektumok (helyek, dolog, személyek, események vagy fogalmak) gyűjteményét jelképezi. Entitások ismertetik a leképezés kapcsolódó információk, és néha nélkülözhetetlenek az alkalmazás a feladat végrehajtásához. 

Előtti vagy szereplőkkel leképezések létrehozásához entitásokat hozhat létre.

Hozzáadása, szerkesztése vagy a LUIS-alkalmazásokon keresztül az entitások törlése a **entitásainak listája** a a **entitások** lapot. A LUIS biztosít két fő entitástípus: [előre összeállított entitások](luis-reference-prebuilt-entities.md), és a saját [egyéni entitások](luis-concept-entity-types.md#types-of-entities).

Entitás létrehozása után azt is lehet címkézett-leképezés példa utterance (kifejezés), az a **szándékot** részleteit megjelenítő oldalon. 

## <a name="add-prebuilt-entity"></a>Előre összeállított entitások hozzáadása

Közös előre összeállított entitások alkalmazáshoz hozzáadott *szám* és *datetimeV2*. 

1. Az alkalmazásba a a **összeállítása** szakaszban jelölje be **entitások** a bal oldali panelen.
 
1. Az a **entitások** lapon jelölje be **előre összeállított entitások hozzáadása**.

1. A **előre összeállított entitások hozzáadása** párbeszédpanelen válassza ki a **szám** és **datetimeV2** előre összeállított entitások. Ezután válassza a **Done** (Kész) elemet.

    ![Előre összeállított entitások párbeszédpanel képernyőképe az hozzáadása](./media/add-entities/list-of-prebuilt-entities.png)

## <a name="add-simple-entities"></a>Egyszerű entitások hozzáadása

Egy egyszerű entitás egyetlen fogalom írja le. Az alábbi eljárással hozhat létre olyan entitás, amely kinyeri a vállalat részleg neve például *emberi erőforrások* vagy *műveletek*.   

1. Az alkalmazásban, válassza ki a **hozhat létre** területen, majd válassza ki **entitások** a bal oldali panelen, majd válassza ki **új entitás létrehozása**.

1. Az előugró párbeszédpanelen írja be a `Location` a a **entitás neve** jelölje ki **egyszerű** származó a **entitástípus** listában, és válassza ki **kész**.

    Az entitás létrehozása után nyissa meg az entitást tartalmazó példa utterances rendelkezik szándék fog vonatkozni. Válassza ki a szöveget a példa utterance (kifejezés), és a szöveg nyelve az entitás. 

    A [kifejezéslista](luis-concept-feature.md) gyakran használatos a jel, egy egyszerű entitás növelése érdekében.

## <a name="add-regular-expression-entities"></a>Reguláris kifejezés entitások hozzáadása

A reguláris kifejezésnek entitás is, az utterance (kifejezés), adja meg a reguláris kifejezések alapján adatokat szolgál. 

1. Jelölje be az alkalmazásba, **entitások** a bal oldali navigációs, és válassza ki a **új entitás létrehozása**.

1. A megjelenő párbeszédpanelen adja meg `Human resources form name` a a **entitás neve** jelölje ki **reguláris kifejezés** származó a **entitástípus** listában, adja meg a reguláris kifejezés `hrf-[0-9]{6}`, majd válassza ki **kész**. 

    A reguláris kifejezés literális karakter `hrf-`, majd 6 számjegyű, amelyek egy űrlapot az emberi erőforrások űrlap száma.

## <a name="add-hierarchical-entities"></a>Hierarchikus entitások hozzáadása

Egy hierarchikus entitás, de megismert és elméletben kapcsolódó entitások egy kategóriát. A következő példában az entitás tartalmazza a forrás-és célhelynek. 

Az utterance (kifejezés) a `Move John Smith from Seattle to Cairo`, Seattle a feladás helyét pedig Cairo a célhelyen. Minden egyes helye kontextusban különböző és megismert szórendjét és a word választott az utterance (kifejezés).

Hierarchikus entitások hozzáadásához kövesse az alábbi lépéseket: 

1. Jelölje be az alkalmazásba, **entitások** a bal oldali navigációs, és válassza ki a **új entitás létrehozása**.

1. Az előugró párbeszédpanelen írja be a `Location` a a **entitás neve** mezőbe, majd válassza ki **hierarchikus** a a **entitástípus** listája.

    ![Hierarchikus entitás hozzáadása](./media/add-entities/hier-location-entity-creation.png)

1. Válassza ki **gyermek hozzáadása**, majd adja meg `Origin` a **gyermek 1** mezőbe. 

1. Válassza ki **gyermek hozzáadása**, majd adja meg `Destination` a **gyermek 2** mezőbe. Válassza a **Done** (Kész) lehetőséget.

    >[!CAUTION]
    >Gyermek entitásnévnek egyedinek kell lennie minden entitás egyetlen alkalmazásban. Két különböző hierarchikus entitások nem tartalmazhat gyermekentitások ugyanazzal a névvel. 

    Az entitás létrehozása után nyissa meg az entitást tartalmazó példa utterances rendelkezik szándék fog vonatkozni. Válassza ki a szöveget a példa utterance (kifejezés), és a szöveg nyelve az entitás. 

## <a name="add-composite-entities"></a>Összetett entitások hozzáadása

Definiálhat egy összetett entitást létrehozásával különböző típusú entitások közötti kapcsolatok. A következő példában az entitás egy adott reguláris kifejezésnek, és a egy előre létrehozott entitás neve tartalmazza.  

Az utterance (kifejezés) a `Send hrf-123456 to John Smith`, a szöveg `hrf-123456` egy emberi erőforrások megfeleltetett [reguláris kifejezés](#add-regular-expression-entities) és `John Smith` és az előre összeállított entitások personName ki kell olvasni. Minden entitás egy nagyobb méretű, szülőentitás részét képezi. 

1. Jelölje be az alkalmazásba, **entitások** a bal oldali navigációs sávján a **hozhat létre** szakaszt, és válassza ki **előre összeállított entitás hozzáadása**.

1. Adja hozzá az előre összeállított entitások **PersonName**. Útmutatásért lásd: [előre összeállított entitások hozzáadása](#add-prebuilt-entity). 

1. Válassza ki **entitások** a bal oldali navigációs, és válassza ki a **új entitás létrehozása**.

1. A megjelenő párbeszédpanelen adja meg `SendHrForm` a a **entitás neve** mezőbe, majd válassza a **összetett** a a **entitástípus** listája.

1. Válassza ki **gyermek hozzáadása** egy új gyermek hozzáadása.

1. A **gyermek 1**, válassza ki az entitást **szám** a listából.

1. A **gyermek 2**, válassza ki az entitást **emberi erőforrások űrlapnév** a listából. 

1. Válassza a **Done** (Kész) lehetőséget.

## <a name="add-patternany-entities"></a>Pattern.any entitások hozzáadása

[Pattern.any](luis-concept-entity-types.md) entitásokat csak érvényesek a [minták](luis-how-to-model-intent-pattern.md), nem a szándék fog vonatkozni. Az ilyen típusú entitás segít a LUIS-entitások különböző hossza és a word választott végén található. Ehhez az entitáshoz mintát használja, mert a LUIS tudja, ahol az entitás végén szerepel-e az utterance (kifejezés) sablont.

Ha egy alkalmazás egy `FindHumanResourcesForm` szándék, a kivont űrlap címe zavarhatják szándék előrejelzési. Annak érdekében, hogy elmagyarázza, hogy melyik szavak szerepelnek az űrlap címe, egy Pattern.any belül mintát használja. A LUIS-előrejelzés az utterance (kifejezés) kezdődik. Először az utterance (kifejezés) be van jelölve, amely egyezik az entitások, ha az entitások találhatók, akkor a minta egyezik és be van jelölve. 

Az utterance (kifejezés) a `Where is Request relocation from employee new to the company on the server?`, a képernyő címe az bonyolult, mert nem kontextusban nyilvánvaló ahol a cím véget ér, és ahol a többi az utterance (kifejezés) kezdődik. Címek lehet szó egyetlen szó, beleértve a absztrakt a bonyolult kifejezések bármely sorrendje, illetve szavakat nonsensical rendezése. Egy minta lehetővé teszi, hogy hozzon létre egy entitás ahol kiolvasható a teljes és pontos entitás. Ha a cím található, a `FindHumanResourcesForm` szándékot előre jelzett, mert a minta célja.

1. A a **összeállítása** szakaszban jelölje be **entitások** a bal oldali panelen, és válassza ki a **új entitás létrehozása**.

1. Az a **entitás hozzáadása** párbeszédpanelen adja meg az entitás neve mezőbe, majd válassza **Pattern.any** , a **entitástípus**.

    A pattern.any entitást használja, adjon hozzá egy minta a **minták** lap a **megnövelheti az alkalmazások teljesítményét** részben, a megfelelő kapcsos zárójel szintaxissal, mint például `Where is **{HumanResourcesFormTitle}** on the server?`.

    Ha azt tapasztalja, hogy a Pattern.any entitást tartalmazó minta nem megfelelően vonja ki az entitásokat, egy [explicit lista](luis-concept-patterns.md#explicit-lists) megoldhatja a problémát. 

## <a name="add-a-role-to-pattern-based-entity"></a>A szerepkör hozzáadása az entitáshoz minta alapján

A szerepkör az entitásokban környezet alapján elnevezett altípusa. Azt az hasonlítható egy [hierarchikus](#add-hierarchical-entities) egység, de a szerepkörök csak használt [minták](luis-how-to-model-intent-pattern.md). 

Ugyanebben a példában a forrás és cél városok, a különbség a hierarchikus entitásként használata, hogy a szerepkör neve helyett egy hierarchikus gyermek forrása. 

A szerepkör szintaxisa **{Entityname:Rolename}** ahol az entitás neve követ egy kettőspontot, akkor a szerepkör nevét. Például: `Move {personName} from {LocationUsingRoles:Origin} to {LocationUsingRoles:Destination}`.

1. Az a **összeállítása** szakaszban jelölje be **entitások** a bal oldali panelen.

1. Válassza a **Create new entity** (Új entitás létrehozása) lehetőséget. Adja meg a nevét `LocationUsingRoles`. Válassza ki a **egyszerű** válassza **kész**. 

1. Válassza ki **entitások** a bal oldali panelen, majd válassza ki az új entitás **LocationUsingRoles** az előző lépésben létrehozott.

1. Az a **szerepkörnév** szövegmezőbe írja be a szerepkör nevét `Origin` , és adja meg. Adjon hozzá egy második szerepkör neve `Destination`. 

    ![Képernyőkép a forrás szerepkör hozzáadásának helye entitáshoz](./media/add-entities/roles-enter-role-name-text.png)

## <a name="add-list-entities"></a>Lista entitások hozzáadása

Lista entitások kapcsolódó szavakat rögzített, lezárt csoportját képviselik. 

Emberi erőforrások alkalmazások esetén lehet a részlegek számára bármely szinonimák együtt minden részleg listáját. Nem kell tudnia értékek az entitás létrehozásakor. Hozzáadhat további valós felhasználói utterances a szinonimák áttekintése után.

1. A a **összeállítása** szakaszban jelölje be **entitások** a bal oldali panelen, és válassza ki a **új entitás létrehozása**.

1. Az a **entitás hozzáadása** párbeszédpanelen írja be `Department` a a **entitásnév** mezőbe, majd válassza ki **lista** , a **entitástípus**. Válassza a **Done** (Kész) lehetőséget.
  
1. A lista entitás oldalára normalizált nevek hozzáadását teszi lehetővé. Az a **értékek** szövegmezőben adja meg például a részleg nevét a listát `HumanResources` nyomja le az Enter billentyűt. 

1. Jobb oldalán a normalizált értéket írja be a szinonimákat, mindegyik elem után nyomja le az Enter billentyűt.

1. Ha több normalizált elemek listája, jelölje be **javasoljuk** beállításainak a megjelenítéséhez a [szemantikai szótár](luis-glossary.md#semantic-dictionary).

    ![Képernyőkép a forrás szerepkör hozzáadásának helye entitáshoz](./media/add-entities/hr-list-2.png)


1. Jelöljön ki egy elemet normalizált értékként adja hozzá, vagy jelölje be a javasolt lista **adja hozzá az összes** minden elem hozzáadásához. 
    A következő JSON-formátumban meglévő lista entitás értékek importálhatja:

    ```
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


## <a name="change-entity-type"></a>Entitás típusának módosítása

A LUIS nem engedi, hogy az entitás típusa módosítható, mert ez nem tudja, hogy melyik hozzáadása vagy eltávolítása, hogy az entitás létrehozásához. Annak érdekében, hogy módosítsa a típusát, célszerűbb a megfelelő típusú új entitás létrehozása egy kis mértékben eltérő nevű. Az entitás létrehozása után minden kimondásakor, távolítsa el a régi címkézett entitás nevét, és adja hozzá az új entitás nevét. Miután a kimondott szöveg rendelkezik lett relabeled, a régi entitás törlése. 

## <a name="create-a-pattern-from-an-utterance"></a>Hozzon létre egy minta az utterance (kifejezés)

Lásd: [Hozzáadás minta a leképezés vagy entitás oldalon meglévő utterance (kifejezés)](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Az entitások modell módosítása után az alkalmazás betanítása

Után adja hozzá, szerkeszthet és eltávolíthat az entitásokat, [betanításához](luis-how-to-train.md) és [közzététele](luis-how-to-publish-app.md) a végpont lekérdezéseket érintő módosítások alkalmazásához. 

## <a name="next-steps"></a>További lépések

Előre összeállított entitások kapcsolatos további információkért lásd: a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text) projekt. 

További információ az entitás hogyan jelenik meg a JSON-végpont lekérdezésekre adott válaszok: [Adatkinyerés](luis-concept-data-extraction.md)

Most, hogy hozzáadta a szándék fog vonatkozni, utterances és entitások, akkor alapszintű LUIS-alkalmazásokon. Ismerje meg, hogyan [betanításához](luis-how-to-train.md), [tesztelése](luis-interactive-test.md), és [közzététele](luis-how-to-publish-app.md) az alkalmazást.
 
