---
title: Adja hozzá például utterances LUIS alkalmazások |} Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan utterances hozzáadása a nyelvi ismertetése (LUIS) alkalmazások.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 74a4b77bd9823e5462eecd438cf4c1d863e79892
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300638"
---
# <a name="add-example-utterances-and-label-with-entities"></a>Példa utterances és entitások címke hozzáadása

Példa utterances Példák szöveg felhasználói kérdés vagy parancsok. Mutatja meg a nyelvi ismertetése (LUIS), meg kell adni [példa utterances](luis-concept-utterance.md) való egy [leképezés](luis-concept-intent.md).

Általában egy példa utterance hozzáadása megjelölésű először, és entitásokat és a felirat utterances hozzon létre a leképezési oldalon. Ha entitások először ahelyett, hogy eredményezne, lásd: [új entitásokat](luis-how-to-add-entities.md).

## <a name="add-an-utterance"></a>Egy utterance hozzáadása
Egy leképezési lapon adja meg a megfelelő példa utterance adatraktáraktól a felhasználók számára, például a `book 2 adult business tickets to Paris tomorrow on Air France` a szövegmezőben alatti leképezési nevét, és nyomja le az ENTER billentyűt. 
 
>[!NOTE]
>LUIS összes utterances csupa kisbetűssé alakítja.

![Képernyőfelvétel a leképezések részleteit megjelenítő oldalon, a kiemelt utterance](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

Az aktuális szándékot utterances telefonokhoz utterances kerülnek. 

## <a name="ignoring-words-and-punctuation"></a>Szavak és írásjelek figyelmen kívül hagyása
Figyelmen kívül hagyja a szavakat vagy a példa utterance írásjelek, használja a [mintát](luis-concept-patterns.md#pattern-syntax) rendelkező a _figyelmen kívül hagyása_ szintaxist. 

## <a name="add-simple-entity-label"></a>Egyszerű entitás címke hozzáadása
A következő eljárással hozzon létre, és egyéni entitások belül a leképezési lapon a következő utterance címkézését:

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. Válassza ki az egyszerű egységként megjelölésére utterance "Sz".

    > [!NOTE]
    > Címkézze, entitásokat szavak kiválasztása:
    > * Egyetlen szó egyszerűen válassza ki azt. 
    > * Állítja be a két vagy több szó jelölje ki a elején, majd a készlet végén.

2. Az entitás legördülő mezőben, amely akkor jelenik meg válasszon egy meglévő entitást, vagy adja hozzá egy új entitást. Új entitás hozzáadása, a szövegmezőbe írja be a nevét, majd válassza ki **új entitás létrehozása**. 
 
    ![Képernyőfelvétel a leképezések részleteit megjelenítő oldalon, a beállítás a kijelölt címkézés egyszerű entitás](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. Az a **milyen típusú entitás kíván létrehozni?** előugró párbeszédpanelen ellenőrizze az entitás nevét, és egyszerű entitástípus, majd válassza ki és **végzett**.

    ![Megerősítő párbeszédpanel képe](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    Lásd: [adatok kinyerése](luis-concept-data-extraction.md#simple-entity-data) tudhat meg többet az egyszerű entitás kibontása a végpont JSON választ. Az egyszerű entitás próbálja [gyors üzembe helyezés](luis-quickstart-primary-and-secondary-data.md) további egy egyszerű entitás használatáról.


## <a name="add-list-entity-and-label"></a>Lista entitás és címke hozzáadása
Lista entitások rögzített, lezárt csoportját képviselik (pontos egyezés megegyezik) kapcsolódó szavak a rendszerben. 

Egy italok lista entitás két normalizált értékeket veheti fel: vízjelek és soda pop. Minden egyes normalizált nevet már rendelkezik. A vízjel, a szinonimák használata H20, a gáz, egyszerű. A soda pop szinonimák gyümölcsöt, cola, gyömbér használata. Nem rendelkezik, ha hoz létre, hogy az entitás összes értékét. Hozzáadhat további már rendelkező felhasználó utterances áttekintése után.

|Normalizált nevet|Szinonimák|
|--|--|
|Víz|H20, a gáz, simán|
|Soda pop|Gyümölcsöt, cola, gyömbér|

A leképezési oldalról új lista entitás létrehozásakor során két dolog, amelyek nem maguktól értetődőek. Először hoz létre egy új listáját adja hozzá az első listaelemhez. Másodszor az első listaelemhez nevű szót vagy kifejezést a utterance választotta. Ezeket az entitás oldalról később módosíthatja, amíg lehet gyorsabb egy utterance, amelynek a neve, a listaelem kívánt kiválasztásához.

Például akkor, ha listájának létrehozásához ital és típusú kiválasztva a word `h2o` létrehozása az entitás utterance, az a lista egy elem, amelynek a neve lett h20 lenne. Ha több nevet, válasszon egy utterance, amely több általános nevet használja. 

1. A utterance, válassza ki a word, az első elem a listában, majd a szövegmezőben adja meg a lista nevét, majd válassza ki **új entitás létrehozása**.   

    ![Képernyőfelvétel a leképezések részleteit megjelenítő oldalra, hozzon létre új entitáshoz kiemelve](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. Az a **milyen típusú entitás kíván létrehozni?** párbeszédpanel mezőben adja meg a listaelem szinonimák. A vízjel listaelem ital, vegye fel az `h20`, `perrier`, és `waters`, és válassza ki **végzett**. Figyelje meg, hogy a "vizek" jelenik-e, mivel a lista szinonimák egyeztetve lesznek a token szinten. Az angol kulturális környezet, az, hogy szintje a word szinten úgy "vizek" volna nem egyeztetni kell "vízjel" kivéve, ha a listában volt. 

    ![Képernyőkép a milyen típusú entitást szeretne létrehozása párbeszédpanel](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    Ebben a listában italok csak egy ital típussal, vízjel rendelkezik. Hozzáadhat további ital típusok más utterances címkézését, vagy az entitás szerkesztésével a **entitások** a bal oldali navigációs. [Szerkesztés](luis-how-to-add-entities.md#add-list-entities) az entitásokat tartalmazó megfelelő szinonimák további elemek megadásáról lehetőséget biztosít vagy [importálása](luis-how-to-add-entities.md#import-list-entity-values) listáját. 

    Lásd: [adatok kinyerése](luis-concept-data-extraction.md#list-entity-data) tudhat meg többet a végpont JSON lekérdezésválaszt lista entitások kibontása. Próbálja meg a [gyors üzembe helyezés](luis-quickstart-intent-and-list-entity.md) további információt a lista entitás használata.

## <a name="add-synonyms-to-the-list-entity"></a>A lista entitás szinonimák hozzáadása 
A lista entitáshoz szinonimát hozzáadni a utterance kiválasztja a szó vagy kifejezés. Ha rendelkezik olyan entitás listában, és szeretne adni, amelyet `agua` , vízjel szinonimát, kövesse a lépéseket:

A utterance, válassza ki a azonos szót, például a `aqua` a vízjel, majd válassza ki a lista entitás nevét a legördülő listában, mint **ital**, majd válassza **szinonimát állítja**, majd válassza ki a listában elem legyen azonos a, például **vízjel**.

![Képernyőfelvétel a leképezések részleteit megjelenítő oldalon, a hozzon létre egy új szinonimát kiemelve](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>Lista entitás új elem létrehozása
Meglévő lista entitás új elem létrehozása a utterance kiválasztja a szót vagy kifejezést. Ha rendelkezik olyan listában, és szeretne adni, amelyet `tea` új elemként, kövesse a lépéseket:

A utterance, válassza ki a word, az új listaelem, például a `tea`, majd válassza ki a lista entitás neve, mint a legördülő listából válassza ki, **ital**, majd jelölje be **hozzon létre egy új szinonimát**. 

![Képernyőfelvétel az új listaelem hozzáadása](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

A word kék most már ki van jelölve. Ha az egérmutatót a word, egy címke megjeleníti az elem neve, például tea.

![Új lista elem címke képernyőképe](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>Entitások burkolása összetett címke
Összetett entitások készített **entitások**. Nem hozható létre egy összetett entitást a leképezési lapról. Az összetett entitás létrehozása után akkor entitások található a leképezési oldalon egy utterance futtathatja. 

Feltéve, hogy a utterance `book 2 tickets from Seattle to Cairo`, egy összetett utterance adhat vissza entitás információkat a jegyek (2), a forrás (Budapest) és a cél száma (Kairó) helyek egy egyetlen szülőhöz entitásban. 

Kövesse az alábbi [lépéseket](luis-how-to-add-entities.md#add-prebuilt-entity) hozzáadása a **szám** előre elkészített entitás. Az entitás létrehozása után a `2` a utterance a kék, címkézett entitás jelző. Előre elkészített entitások által LUIS tartalma. Nem adja hozzá, vagy távolítsa el az előre elkészített entitás címke egyetlen utterance. Csak adja hozzá, vagy távolítsa el az előre elkészített címkék hozzáadásával vagy eltávolításával az előre elkészített entitás az alkalmazásból.

Kövesse az alábbi [lépéseket](#add-hierarchical-entity-and-label) létrehozásához egy **hely** a hierarchikus. A forrás és cél helye a a példa utterance címkézését. 

Mielőtt egy összetett entitást a burkolása az entitásokat, győződjön meg arról az összes alárendelt entitások vannak kiemelve kék, ami azt jelenti, az a utterance címkével.

1. Az egyes entitásokat burkolása az összetett, jelölje ki az összetett entitás utterance az első címkézett entitás. A példa utterance `book 2 tickets from Seattle to Cairo`, az első entitás az a szám 2. A legördülő lista Ekkor megjelenik a választási lehetőségek a kijelölés.

    ![Képernyőkép a kijelölt szám és a kijelölt legördülő beállítások](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. Válassza ki **összetett entitást burkolása** a legördülő listából. 

    ![Képernyőkép a összetett entitást alkalmazásburkoló sortörés következik a kiemelt összetett entitást a legördülő lista beállításai](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. Válassza ki az utolsó szó, az összetett entitás. Ez a példa utterance válassza ki a "Location::Destination" (képviselő Kairó). A zöld sor most az összes szó közé tartoznak a következők nem entitás szavakat, az a utterance, amelyek az összetett.

    ![BookFlight leképezés képernyőkép lapon, a kijelölt szám](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. A legördülő listából válassza ki az összetett entitás nevét. Az ebben a példában ez **TicketOrder**.

    ![A legördülő listából válassza ki a kijelölt összetett entitást nevű összetett entitást tartalmazó szavak alkalmazásburkoló képernyőképe](./media/luis-how-to-add-example-utterances/wrap-4.png)

    Ha megfelelően burkolása az entitásokat, egy zöld sor a teljes kifejezés alatt áll.

    ![A kijelölt összetett entitással utterance képernyőképe](./media/luis-how-to-add-example-utterances/wrap-5.png)

    Lásd: [adatok kinyerése](luis-concept-data-extraction.md#composite-entity-data) tudhat meg többet az összetett entitást kibontása a végpont JSON választ. Az összetett entitást próbálja [oktatóanyag](luis-tutorial-composite-entity.md) egy összetett entitást használatáról további.

## <a name="add-hierarchical-entity-and-label"></a>A hierarchikus és címke hozzáadása
A hierarchikus egy összefüggéseikben való megismert és elméletben kapcsolódó entitás-kategóriát. A következő példában az entitás eredeti és a célhely tartalmazza. 

Az a utterance `Book 2 tickets from Seattle to Cairo`, Seattle a származási helye pedig Kairó célhelyét. Minden hely összefüggéseikben való különböző, ismerni a word és az a utterance word kiválasztott.

1. Az a leképezési oldalon, a utterance, válassza ki a "Seattle", majd adja meg az entitás neve "helyre, és válassza **új entitás létrehozása**.

    ![Képernyőfelvétel a létrehozása hierarchikus entitás címkézés párbeszédpanel](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. Az előugró párbeszédpanelen válassza ki a hierarchikus **entitástípus**, majd adja hozzá `Origin` és `Destination` gyermekek, és válassza ki, **végzett**.

    ![Képernyőfelvétel a leképezések részleteit megjelenítő oldalon kiemelve ToLocation entitással](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. A utterance a word és a hierarchikus szülőentitás lett címkézve. A word hozzárendelése egy gyermekentitáson kell. Térjen vissza a utterance a leképezési oldalon. Válassza ki a word, majd a legördülő listából válassza ki a létrehozott entitás nevét, és hajtsa végre a menüből válassza ki a megfelelő gyermekentitáson jobb.

    ![Képernyőfelvétel a leképezések részleteit megjelenítő oldalon kiemelve ToLocation entitással](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >Gyermek entitás nevének egyedinek kell lennie a egyetlen meghatározott alkalmazás összes entitásának között. Két különböző hierarchikus entitások nem tartalmazhat gyermek entitások ugyanazzal a névvel. 

    Lásd: [adatok kinyerése](luis-concept-data-extraction.md#hierarchical-entity-data) tudhat meg többet a hierarchikus entitás kibontása a végpont JSON választ. A hierarchikus entitás próbálja [gyors üzembe helyezés](luis-quickstart-intent-and-hier-entity.md) további hierarchikus entitás használatáról.


## <a name="remove-entity-labels-from-utterances"></a>Utterances entitás címkék eltávolítása
A leképezési oldalon egy utterance gép megtanulta entitás címkék eltávolíthat. Ha az entitás nincs gép megtanulta, akkor nem lehet eltávolítani egy utterance. Ha nem gép megtanulta entitás eltávolítása a utterance van szüksége, a teljes alkalmazás törölni egy entitást szeretné. 

Egy entitás gép megtanulta címke eltávolítása egy utterance, jelölje ki az entitást a utterance. Válassza ki **címke eltávolítása** a entitás legördülő mezőben, amely akkor jelenik meg.

![Képernyőfelvétel a leképezések részleteit megjelenítő oldalon kiemelve eltávolítása címkével](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>Előre elkészített entitás címke hozzáadása
Ha az előre elkészített entitásokat ad hozzá az LUIS alkalmazást, nem kell ezeket az entitásokat a címke utterances. Előre elkészített entitásokat és hogyan adja hozzá kapcsolatos további információkért lásd: [új entitásokat](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="add-regular-expression-entity-label"></a>Reguláris kifejezés entitás címke hozzáadása
A reguláris kifejezés entitások való felvételekor a LUIS alkalmazást, nem kell az ezeket az entitásokat címke utterances. Reguláris kifejezés entitásokat és hogyan adja hozzá kapcsolatos további információkért lásd: [új entitásokat](luis-how-to-add-entities.md#add-regular-expression-entities).

## <a name="create-a-pattern-from-an-utterance"></a>A minta egy utterance létrehozása
Lásd: [Hozzáadás mintát a leképezés vagy entitás oldalon meglévő utterance](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="add-patternany-entity-label"></a>Pattern.any entitás címke hozzáadása
Pattern.any entitások való felvételekor a LUIS alkalmazást, akkor nem címke utterances ezeket az entitásokat a. Csak azok a minták érvényes. Pattern.any entitásokat és hogyan adja hozzá kapcsolatos további információkért lásd: [új entitásokat](luis-how-to-add-entities.md#add-patternany-entities).

<!--
Fix this - moved to luis-how-to-add-intents.md - how ?

## Search in utterances
## Prediction discrepancy errors
## Filter by intent prediction discrepancy errors
## Filter by entity type
## Switch to token view
## Delete utterances
## Edit an utterance
## Reassign utterances

-->
## <a name="train-your-app-after-changing-model-with-utterances"></a>Az alkalmazás betanítása utterances modell módosítása után
Adja hozzá, szerkeszthet és eltávolíthat utterances, miután [betanítása](luis-how-to-train.md) és [közzététele](PublishApp.md) az alkalmazás a módosításokat az végpont lekérdezések. 

## <a name="next-steps"></a>További lépések

A leképezések az utterances címkézés után mostantól létrehozhat egy [összetett entitást](luis-how-to-add-entities.md).
