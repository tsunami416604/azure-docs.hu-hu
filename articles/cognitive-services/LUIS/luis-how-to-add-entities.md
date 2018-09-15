---
title: Entitások hozzáadása a LUIS-alkalmazások
titleSuffix: Azure Cognitive Services
description: A Language Understanding (LUIS) alkalmazások hozzáadása a entitások (a tartomány az alkalmazás legfontosabb adatok).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 3fe76afca2eb8b14641589e4e29fc20b5d3de7fa
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632285"
---
# <a name="manage-entities"></a>Entitások kezelése
Keresse meg az alkalmazás [leképezések](luis-concept-intent.md), kell [példa utterances címke](luis-concept-utterance.md) a [entitások](luis-concept-entity-types.md). Entitások a fontos darabokat egy parancs vagy a kérdés, és lehet, hogy az ügyfélalkalmazás a feladatok végrehajtásához nélkülözhetetlen. 

Hozzáadása, szerkesztése vagy entitások törlése keresztül az alkalmazásban a **entitásainak listája** a a **entitások** lapot. A LUIS biztosít két fő entitástípus: [előre összeállított entitások](luis-reference-prebuilt-entities.md), és a saját egyéni entitásokat.

Az alábbi szakaszok a LUIS-alkalmazásokon belül csak érhetők el a a **összeállítása** szakaszban. A **összeállítása** hivatkozása a felső navigációs sávban. Egyszer belül a **összeállítása** szakaszban jelölje be **entitások** a bal oldali navigációs menüjében. Entitás hozzáadása után az alkalmazáshoz, ha az entitás gép megtanulta a következőket teheti [címkét az entitás](luis-how-to-add-example-utterances.md) az utterance (kifejezés) belül. Az alkalmazás van tanítva, és a közzétett, fogadhat Entitásadatok [kinyert](luis-concept-data-extraction.md) az előrejelzési. 

## <a name="add-prebuilt-entity"></a>Előre összeállított entitások hozzáadása
Előre összeállított entitások vannak meghatározva a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text) projekt. Közös előre összeállított entitások alkalmazáshoz hozzáadott *szám* és *datetimeV2*. 

1. Az alkalmazásba a a **összeállítása** szakaszt, és kattintson a **entitások** a bal oldali panelen.
 
2. Az a **entitások** lapon jelölje be **előre összeállított entitások kezelése**.

3. A **hozzáadása, vagy távolítsa el az előre összeállított entitások** párbeszédpanelen válassza ki a **szám** és **datetimeV2** előre összeállított entitások. Ezután válassza a **Done** (Kész) elemet.

    ![Előre összeállított entitások párbeszédpanel képernyőképe az hozzáadása](./media/add-entities/list-of-prebuilt-entities.png)

    Lásd: [Adatkinyerés](luis-concept-data-extraction.md#prebuilt-entity-data) tudhat meg többet az előre összeállított entitások kinyeréséhez a végpontról JSON lekérdezési válasz.

## <a name="add-simple-entities"></a>Egyszerű entitások hozzáadása
Egy egyszerű entitás egy általános entitás, amely leírja egy egyetlen fogalom. 

1. Az alkalmazásba a a **összeállítása** szakaszt, és kattintson a **entitások** a bal oldali panelen, és válassza ki a **új entitás létrehozása**.

2. Az előugró párbeszédpanelen írja be a `Airline` a a **entitás neve** jelölje ki **egyszerű** származó a **entitástípus** listában, és válassza ki **kész**.

    ![Egyszerű entitás légitársaság létrehozására szolgáló párbeszédpanel képernyőképe](./media/add-entities/create-simple-airline-entity.png)

    Lásd: [Adatkinyerés](luis-concept-data-extraction.md#simple-entity-data) tudhat meg többet az egyszerű entitás kinyerését a végpont JSON lekérdezési válasz. Próbálja ki az egyszerű entitás [rövid](luis-quickstart-primary-and-secondary-data.md) tudhat meg többet az egyszerű entitás használatával.

## <a name="add-regular-expression-entities"></a>Reguláris kifejezés entitások hozzáadása
A reguláris kifejezésnek entitás is, az utterance (kifejezés), adja meg a reguláris kifejezések alapján adatokat szolgál. 

1. Jelölje be az alkalmazásba, **entitások** a bal oldali navigációs, és válassza ki a **új entitás létrehozása**.

2. A megjelenő párbeszédpanelen adja meg `AirFrance Flight` a a **entitás neve** jelölje ki **reguláris kifejezés** származó a **entitástípus** listában, adja meg a reguláris kifejezés `AFR[0-9]{3,4}`, majd válassza ki **kész**. 

    A AirFrance Flight reguláris kifejezést vár három karaktert szó szerint `AFR`, majd a 3 vagy 4 számjegy. A számjegyek tetszőleges szám 0 és 9 közötti lehet. Reguláris kifejezésnek megfelelő AirFrance repülési számok például: "AFR101", "ARF1302" és "AFR5006". Lásd: [Adatkinyerés](luis-concept-data-extraction.md) tudhat meg többet az entitás kinyerését a végpont JSON lekérdezési válasz.

    ![Reguláris kifejezés entitás létrehozása párbeszédpanel képe](./media/add-entities/regex-entity-create-dialog.png)

    Lásd: [Adatkinyerés](luis-concept-data-extraction.md#regular-expression-entity-data) tudhat meg többet a végpont JSON lekérdezési válasz a reguláris kifejezésnek entitás kinyerését. Próbálja meg a reguláris kifejezésnek entitás [rövid](luis-quickstart-intents-regex-entity.md) további egy reguláris kifejezésnek entitás használatával kapcsolatban.

## <a name="add-hierarchical-entities"></a>Hierarchikus entitások hozzáadása
Egy hierarchikus entitás, de megismert és elméletben kapcsolódó entitások egy kategóriát. A következő példában az entitás tartalmazza a forrás-és célhelynek. 

Az utterance (kifejezés) a `Book 2 tickets from Seattle to Cairo`, Seattle a feladás helyét pedig Cairo a célhelyen. Minden egyes helye kontextusban különböző és megismert szórendjét és a word választott az utterance (kifejezés).

Hierarchikus entitások hozzáadásához kövesse az alábbi lépéseket: 

1. Jelölje be az alkalmazásba, **entitások** a bal oldali navigációs, és válassza ki a **új entitás létrehozása**.

2. Az előugró párbeszédpanelen írja be a `Location` a a **entitás neve** mezőbe, majd válassza ki **hierarchikus** a a **entitástípus** listája.

    ![Hierarchikus entitás hozzáadása](./media/add-entities/hier-location-entity-creation.png)

3. Válassza ki **gyermek hozzáadása**, majd írja be az "Origin" a **gyermek 1** mezőbe. 

4. Válassza ki **gyermek hozzáadása**, majd írja be a "Cél" található **gyermek 2** mezőbe. Válassza a **Done** (Kész) lehetőséget.
5. 
    >[!NOTE]
    >Gyermek törléséhez válassza a Kuka ikonnal mellette.

    >[!CAUTION]
    >Gyermek entitásnévnek egyedinek kell lennie minden entitás egyetlen alkalmazásban. Két különböző hierarchikus entitások nem tartalmazhat gyermekentitások ugyanazzal a névvel. 

    Lásd: [Adatkinyerés](luis-concept-data-extraction.md#hierarchical-entity-data) tudhat meg többet a végpont JSON lekérdezési válasz a hierarchikus entitás kinyerését. Próbálja ki a hierarchikus entitás [rövid](luis-quickstart-intent-and-hier-entity.md) hierarchikus entitás használatával kapcsolatos további.

## <a name="add-composite-entities"></a>Összetett entitások hozzáadása
Megadhatja, hogy hozzon létre egy összetett entitás számos meglévő entitások közötti kapcsolatok. A következő példában az entitás tartalmazza a jegyeket, forrás és cél helyek száma. 

Az utterance (kifejezés) a `Book 2 tickets from Seattle to Cairo`, előre összeállított entitások megfeleltetett 2-es számú, a Seattle a forrás helye és Cairo a cél helye. Minden entitás egy nagyobb méretű, szülőentitás részét képezi, az összetett entitás létrehozása után.

1. Az alkalmazásban adja hozzá az előre összeállított entitások **szám**. Útmutatásért lásd: [előre összeállított entitások hozzáadása](#add-prebuilt-entity). 

2. A hierarchikus entitás hozzáadása `Location`, beleértve az altípus: `origin`, `destination`. További útmutatásért lásd: [hierarchikus entitások hozzáadása](#add-hierarchical-entities). 

3. Válassza ki **entitások** a bal oldali navigációs, és válassza ki a **új entitás létrehozása**.

4. Az előugró párbeszédpanelen írja be a `TicketsOrder` a a **entitás neve** mezőbe, majd válassza ki **összetett** a a **entitástípus** listája.

5. Válassza ki **gyermek hozzáadása** egy új gyermek hozzáadása.

6. A **gyermek 1**, válassza ki az entitást **szám** a listából.

7. A **gyermek 2**, válassza ki az entitást **Location::Origin** a listából. 

8. A **gyermek 3**, válassza ki az entitást **Location::Destination** a listából. 

9. Válassza a **Done** (Kész) lehetőséget.

    ![Összetett entitás létrehozása párbeszédpanel képe](./media/add-entities/ticketsorder-composite-entity.png)

    >[!NOTE]
    >Gyermek törléséhez válassza a Kuka gombra a mellette.

    Lásd: [Adatkinyerés](luis-concept-data-extraction.md#composite-entity-data) tudhat meg többet az összetett entitás kinyerését a végpont JSON lekérdezési válasz. Próbálja ki az összetett entitás [oktatóanyag](luis-tutorial-composite-entity.md) egy összetett entitást használatával kapcsolatos további.


## <a name="add-patternany-entities"></a>Pattern.any entitások hozzáadása
[Pattern.any](luis-concept-entity-types.md) entitásokat csak érvényesek a [minták](luis-how-to-model-intent-pattern.md). Ehhez az entitáshoz segít a LUIS-entitások különböző hossza és a word választott végén található. Ehhez az entitáshoz mintát használja, mert a LUIS tudja, hol van az entitás végén az utterance (kifejezés).

Ha egy alkalmazás egy `FindBookInfo` szándék, a könyv címe zavarhatják szándék előrejelzési. Annak érdekében, hogy elmagyarázza, hogy melyik szavak szerepelnek a könyv cím, egy Pattern.any belül mintát használja. A LUIS-előrejelzés az utterance (kifejezés) kezdődik. Először az utterance (kifejezés) be van jelölve, amely egyezik az entitások, ha az entitások találhatók, akkor a minta egyezik és be van jelölve. 

Az utterance (kifejezés) a `Who wrote the book Ask and when was it published?`, a könyv címe, kérje meg, az bonyolult, mert nem kontextusban nyilvánvaló ahol a cím véget ér, és ahol a többi az utterance (kifejezés) kezdődik. Könyv címek lehet szó egyetlen szó, beleértve a absztrakt a bonyolult kifejezések bármely sorrendje, illetve szavakat nonsensical rendezése. Egy minta lehetővé teszi, hogy hozzon létre egy entitás ahol kiolvasható a teljes és pontos entitás. Ha a könyv cím található, a `FindBookInfo` szándékot előre jelzett, mert a minta célja.

1. Az alkalmazásba a a **összeállítása** szakaszt, és kattintson a **entitások** a bal oldali panelen, és válassza ki a **új entitás létrehozása**.

2. Az a **entitás hozzáadása** párbeszédpanelen írja be `BookTitle` a a **entitásnév** mezőbe, majd válassza ki **Pattern.any** , a **entitástípus**.
 
    ![Képernyőkép a pattern.any entitás létrehozása](./media/add-entities/create-pattern-any-entity.png)

    A pattern.any entitást használja, adjon hozzá egy minta a **minták** lap (alatt a **megnövelheti az alkalmazások teljesítményét** szakasz) a megfelelő kapcsos zárójel szintaxissal, például "a **{BookTitle}** ki-e a Szerző? ".

    Lásd: [Adatkinyerés](luis-concept-data-extraction.md#patternany-entity-data) tudhat meg többet a Pattern.any entitás kinyerését a végpont JSON lekérdezési válasz. Próbálja ki a [minta](luis-tutorial-pattern.md) oktatóanyaggal, amelyből megtudhatja, hogyan használják a Pattern.any entitást.

Ha talál, amely a mintát, ha egy Pattern.any tartalmazza, nem megfelelően, használja-e kivonatokat entitások egy [explicit lista](luis-concept-patterns.md#explicit-lists) a probléma elhárításához. 

## <a name="add-role-to-pattern-based-entity"></a>Adja hozzá a szerepkört a minta-alapú entitáshoz
A szerepkör az entitásokban környezet alapján elnevezett altípusa. Azt az hasonlítható egy [hierarchikus](#add-hierarchical-entities) egység, de a szerepkörök csak használt [minták](luis-how-to-model-intent-pattern.md). 

Például egy adatsík jegyet rendelkezik egy *forrás Város* és a egy *cél Város*, de mindkettő városok. A LUIS, mindkettő városok, és meghatározhatja a forrás és cél városok szórendjét és a word választási lehetőség környezet alapján határozza meg. 

A szerepkör szintaxisa **{entitás neve: szerepkör neve}** ahol az entitás neve követ egy kettőspontot, akkor a szerepkör nevét. Például "Book egy jegyet {helye: Destination} forrásból {helye:}".

1. Az alkalmazásba az a **összeállítása** szakaszt, és válassza ki **entitások** a bal oldali panelen.

2. Válassza a **Create new entity** (Új entitás létrehozása) lehetőséget. Adja meg a nevét `Location`. Válassza ki a **egyszerű** válassza **kész**

3. Válassza ki **entitások** a bal oldali panelen, majd válassza ki az új entitás **hely** a 2. lépésben létrehozott.

4. Az a **szerepkörnév** szövegmezőbe írja be a szerepkör nevét `Origin` , és adja meg. Adjon hozzá egy második szerepkör neve `Destination`. Tegyük fel adatsík belépőt rendelkezhet egy forrás és cél város. A két lehetséges szerepkör a "Forrás" és "Cél".

    ![Képernyőkép a forrás szerepkör hozzáadásának helye entitáshoz](./media/add-entities/roles-enter-role-name-text.png)

    Lásd: [Adatkinyerés](luis-concept-data-extraction.md) tudhat meg többet a végpontról JSON lekérdezési válasz szerepkörök beolvasása. Próbálja ki a minta az oktatóanyagban egy Pattern.any entitás használatával kapcsolatos további.

## <a name="add-list-entities"></a>Lista entitások hozzáadása
Lista entitások kapcsolódó szavakat rögzített, lezárt csoportját képviselik, a rendszer. 

Egy ital lista entitás két normalizált érték szerepelhet: víz és soda a jelenléti pontra irányíthatja. Minden egyes normalizált név szinonimák rendelkezik. A vízzel, a szinonimák használata H20, gáz, az egyszerű. Soda csatlakozási pontot a szinonimák használata a gyümölcs, cola, gyömbér. Nem kell tudnia értékek az entitás létrehozásakor. Hozzáadhat további valós felhasználói utterances a szinonimák áttekintése után.

|Normalizált neve|Szinonimák|
|--|--|
|Víz|H20, gáz, átalánydíjjal|
|Soda pop|Gyümölcs, cola, gyömbér|

1. Az alkalmazásba a a **összeállítása** szakaszt, és kattintson a **entitások** a bal oldali panelen, és válassza ki a **új entitás létrehozása**.

2. Az a **entitás hozzáadása** párbeszédpanelen írja be `Drinks` a a **entitásnév** mezőbe, majd válassza ki **lista** , a **entitástípus**. Válassza a **Done** (Kész) lehetőséget.
 
    ![Ital lista entitás létrehozása párbeszédpanel képe](./media/add-entities/menu-list-dialog.png)
  
3.  A lista entitás oldalára normalizált nevek hozzáadását teszi lehetővé. Az a **értékek** szövegmezőben adja meg például egy elemet a listában, `Water` esetében a ital listában, majd nyomja le az Enter billentyűt. 

    ![Képernyőkép a ital lista entitás vízzel normalized értéke szövegmező](./media/add-entities/entity-list-normalized-name.png)

4. Jobb oldalán a normalizált érték **víz**, adja meg a szinonimák `h20`, `flat`, és `gas`, mindegyik elem után nyomja le az Enter billentyűt.

    ![Képernyőkép a ital lista entitás szinonimát elemeinek kiemelésével víz](./media/add-entities/menu-list-synonyms.png)

5. Ha több normalizált elemek listája, jelölje be **javasoljuk** beállításainak a megjelenítéséhez a [szemantikai szótár](luis-glossary.md#semantic-dictionary).

    ![Lista entitás ital képernyőképe a kiemelt ajánlott elemek](./media/add-entities/entity-list-recommended-list.png)

6. Jelöljön ki egy elemet normalizált értékként adja hozzá, vagy jelölje be a javasolt lista **adja hozzá az összes** minden elem hozzáadásához. 

    ![Ital képernyőkép entitás sört ajánlott elem a listában, és minden gomb hozzáadása](./media/add-entities/list-entity-add-suggestions.png)

    Lásd: [Adatkinyerés](luis-concept-data-extraction.md#list-entity-data) tudhat meg többet a végpontról JSON lekérdezési válasz lista entitások kinyeréséhez. Próbálja ki a [rövid](luis-quickstart-intent-and-list-entity.md) egy lista entitás használatával kapcsolatos további.

## <a name="import-list-entity-values"></a>Importálási lista entitásértékek
Egy létező entitásba lista importálhat értékeket.

 1. A lista entitás oldalán válassza **listák importálása**.

 2. A **importálása új bejegyzések** párbeszédpanelen jelölje ki **fájl kiválasztása** , és válassza ki a JSON-fájlt, amely tartalmazza a listában.

    ![Képernyőkép az importálási lista entitás értékeket a megjelenő párbeszédpanelen](./media/add-entities/menu-list-import-json-dialog-with-file.png)

    >[!NOTE]
    >A LUIS importálja a fájlokat a kiterjesztés ".json" csak.

 3. További tudnivalók a támogatott szintaxist a JSON-válassza ki a **további információ a támogatott listaszintaxis** párbeszédpanelen bontsa ki és jelenítheti meg az engedélyezett szintaxis egy példája. A párbeszédpanel bezárása és a szintaxis elrejteni, és jelölje ki újra a kapcsolat nevét.

 4. Válassza a **Done** (Kész) lehetőséget.

    Érvényes json-t egy példát egy **színek** entitás a lista jelenik meg a következő JSON-formátumú kódot:

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

## <a name="edit-entity-name"></a>Entitás szerkesztése
1. Az a **entitások** lista oldal, a listában válassza ki az entitást. Ez a művelet eltarthat, hogy a **entitás** lapot.

2. Az a **entitás** lapon szerkesztheti az entitás nevét az entitás neve mellett a Szerkesztés ikon kiválasztásával. Az entitás típus nem szerkeszthető. 

## <a name="delete-entity"></a>Entitás törlése

Az a **entitás** lapon válassza ki a **entitás törlése** gombra. Ezután válassza ki **Ok** a megerősítő üzenetben a törlés megerősítéséhez.
 
![Képernyőfelvétel: a hely entitás lapon entitás törlése gomb kiemelésével](./media/add-entities/entity-delete.png)

>[!NOTE]
>* Egy hierarchikus entitás törlésekor minden gyermek entitása.
>* Egy összetett entitás törlése törli a csak az összetett és összetett kapcsolat megszakad, de nem törli az azt alkotó.

## <a name="changing-entity-type"></a>Entitás típusa
A LUIS nem engedi, hogy az entitás típusa módosítható, mert ez nem tudja, hogy melyik hozzáadása vagy eltávolítása, hogy az entitás létrehozásához. Annak érdekében, hogy módosítsa a típusát, célszerűbb a megfelelő típusú új entitás létrehozása egy kis mértékben eltérő nevű. Az entitás létrehozása után minden kimondásakor, távolítsa el a régi címkézett entitás nevét, és adja hozzá az új entitás nevét. Miután a kimondott szöveg rendelkezik lett relabeled, a régi entitás törlése. 

## <a name="create-a-pattern-from-an-utterance"></a>Hozzon létre egy minta az utterance (kifejezés)
Lásd: [Hozzáadás minta a leképezés vagy entitás oldalon meglévő utterance (kifejezés)](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="search-utterances"></a>Keresés kimondott szöveg
Kereshet és szűrheti a Nagyító ikont, az eszköztáron a kimondott szöveg. 

## <a name="train-your-app-after-changing-model-with-entities"></a>Az entitások modell módosítása után az alkalmazás betanítása
Után adja hozzá, szerkeszthet és eltávolíthat az entitásokat, [betanításához](luis-how-to-train.md) és [közzététele](luis-how-to-publish-app.md) a végpont lekérdezéseket érintő módosítások alkalmazásához. 

## <a name="next-steps"></a>További lépések
Most, hogy hozzáadta a szándék fog vonatkozni, utterances és entitások, akkor alapszintű LUIS-alkalmazásokon. Ismerje meg, hogyan [betanításához](luis-how-to-train.md), [tesztelése](luis-interactive-test.md), és [közzététele](luis-how-to-publish-app.md) az alkalmazást.
 
