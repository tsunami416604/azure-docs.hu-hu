---
title: Leképezések hozzáadása a LUIS-alkalmazások |} A Microsoft Docs
description: Language Understanding (LUIS) használatával, így megismerheti a felhasználói kérések, és megfelelően reagálhat rájuk alkalmazásokat leképezések hozzáadása.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 0ebf15ea49467674ab3c56aa7983131593cf5c9a
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225886"
---
# <a name="manage-intents"></a>Leképezések kezelése 
Adjon hozzá [leképezések](luis-concept-intent.md) az olyan kérdések vagy ugyanazon céljaira rendelkező parancsok azonosítására a LUIS-alkalmazás. 

Adhat hozzá és kezelhet, a leképezések a **leképezések** lapon elérhető **leképezések** a LUIS a bal oldali panelen. 

Az alábbi eljárás bemutatja, hogyan hozzáadása "Bookflight" célja az TravelAgent alkalmazásban.

## <a name="add-intent"></a>Leképezés hozzáadása

1. Nyissa meg az alkalmazás (például TravelAgent) nevére kattintva **saját alkalmazások** lapon, és kattintson a **leképezések** a bal oldali panelen. 
2. Az a **leképezések** kattintson **új leképezésének létrehozása**.

    ![Leképezések listája](./media/luis-how-to-add-intents/IntentsList.png)
3. Az a **új leképezésének létrehozása** párbeszédpanelen írja be a leképezés "BookFlight" nevet, és kattintson a **kész**.

    ![Leképezés hozzáadása](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Az újonnan hozzáadott szándékkal, szándék Részletek lapján [beszédmódok hozzáadása](#add-an-utterance-on-intent-page).

## <a name="rename-intent"></a>Nevezze át a leképezés

1. A a **szándékot** lap, kattintson az Átnevezés ikon ![átnevezése szándékot](./media/luis-how-to-add-intents/Rename-Intent-btn.png) a leképezés neve mellett. 

2. Az a **szándékot** lapon jelenik meg a párbeszédpanelen az aktuális szándék nevét. Szerkessze a leképezés nevét, és nyomja le az enter. Az új név mentett és a szándék lapon jelennek meg.

    ![Leképezés szerkesztése](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>Leképezés törlése
A none szándék megjelölésű törlésekor kiválaszthatja a Nincs leképezés hozzáadása a kimondott szöveg. Ez akkor hasznos, ha át kell helyeznie azokat törlése helyett a kimondott szöveg.   

1. Az a **szándékot** lap, kattintson a **leképezés törlése** mellett a szándék nevétől jobbra látható gombra. 

    ![Leképezési gomb törlése](./media/luis-how-to-add-intents/DeleteIntent.png)

2. Kattintson az "Ok" gombra a megerősítő párbeszédpanelen.

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>Adja hozzá az utterance (kifejezés) szándék az oldalon

A leképezési lapon adja meg egy megfelelő utterance (kifejezés) adatraktáraktól a felhasználók számára, mint például `book 2 adult business tickets to Paris tomorrow on Air France` alatti leképezési nevét, és nyomja le az ENTER billentyűt a szövegmezőben. 
 
>[!NOTE]
>A LUIS összes utterances kisbetűvé alakítja.

![Képernyőkép a leképezések Részletek lapján az utterance (kifejezés) kiemelésével](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

Beszédmódok kerülnek a kimondott szöveg listája a jelenlegi leképezés. Miután hozzáadta az utterance (kifejezés), [címkézését a entitások](luis-how-to-add-example-utterances.md) megcímkézzen belül és [betanításához](luis-how-to-train.md) az alkalmazást. 

## <a name="create-a-pattern-from-an-utterance"></a>Hozzon létre egy minta az utterance (kifejezés)
Lásd: [Hozzáadás minta a leképezés vagy entitás oldalon meglévő utterance (kifejezés)](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="edit-an-utterance-on-intent-page"></a>Egy leképezési oldalon utterance (kifejezés) szerkesztése

Az utterance (kifejezés) szerkesztéséhez kattintson a három pontra (***...*** ) gombra, hogy utterance (kifejezés) vonal a jobb végén, és válassza ki **szerkesztése**. Módosítsa a szöveget, majd nyomja le az Enter billentyűt.

![Képernyőkép a leképezések Részletek lap, három pontot ábrázoló gomb kiemelésével](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>Beszédmódok szándék oldalon újbóli hozzárendelése
Egy vagy több utterances szándéka megváltoztatást őket egy másik beszédszándék módosíthatja. 

Ismételt hozzárendelése egy egyetlen utterance (kifejezés), egy másik szándékkal, a jobb végén az utterance (kifejezés) sor, jelölje ki a megfelelő szándék nevét a a **szándékot feliratú** oszlop. Az utterance (kifejezés) törlődik a jelenlegi leképezés utterance (kifejezés) listából. 

![Képernyőkép a BookFlight szándék lap alatt Labeled szándék oszlop ki van jelölve az utterance (kifejezés) célja](./media/luis-how-to-add-intents/reassign-1-utterance.png)

Több utterances szándéka módosításához jelölje be a jelölőnégyzeteket megcímkézzen balra, és válassza **újbóli hozzárendelése a leképezés**. Válassza ki a megfelelő leképezést a listából.

![Képernyőkép a BookFlight szándék lap az utterance (kifejezés) be van jelölve, és a újbóli hozzárendelésének szándék gomb kiemelésével](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>Beszédmódok szándék oldalon törlése

Az utterance (kifejezés) törléséhez válassza ki a három pontra (***...*** ) gombra, hogy utterance (kifejezés) vonal a jobb végén, és válassza ki **törlése**. Az utterance (kifejezés) listájából és a LUIS alkalmazás törlődik.

![Képernyőkép a leképezések Részletek lap törlése lehetőséggel kiemelve](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

Több kimondott szöveg törléséhez:

1. Jelölje be a jelölőnégyzeteket megcímkézzen balra, és válassza **beszédmódok (s) törlése**. 

    ![Képernyőkép a leképezések részletek oldala, amelyen utterances be van jelölve, és törölje a utterance(s) gomb](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. Válassza ki **kész** a a **utterances törlése?** felugró párbeszédpanel.

## <a name="search-in-utterances-on-intent-page"></a>Keresés a szándék oldalon kimondott szöveg
Egy szándékkal, a kimondott szöveg (szavak vagy kifejezések) szöveget tartalmazó kereshet. Például Észreveheti, hogy egy hiba, amely magában foglalja egy adott szó, és meg szeretne keresni, hogy adott szó tartalmazó összes példákat. 

1. Az eszköztáron válassza ki a Nagyító ikont.

    ![Képernyőkép-leképezések oldala, amelyen a kiemelt Nagyító keresés ikonra](./media/luis-how-to-add-intents/magnifying-glass.png)

2. Megjelenik a keresési szövegmezőbe. Írja be a keresőmezőbe, a jobb felső sarokban található a kimondott szöveg lista szó vagy kifejezés. A kimondott szöveg listája frissítéseket, csak a kimondott szöveg, amely tartalmazza a keresett szöveg megjelenítéséhez. 

    ![Képernyőkép a leképezések oldala, amelyen kiemelve a keresési szövegmezőbe](./media/luis-how-to-add-intents/search-textbox.png)

    Szakítsa meg a keresést, és állítsa vissza a kimondott szöveg teljes listáját, törölje a beírt keresési szöveget. Gombra kattintva zárja be a keresési szövegmezőbe, válassza újra az eszköztáron a Nagyító ikont.

## <a name="prediction-discrepancy-errors-on-intent-page"></a>Előrejelzési eltérést észlelt hibák szándék az oldalon
Előfordulhat, hogy egy megjelölésű az utterance (kifejezés) a kiválasztott célt és az előrejelzési pontszám közötti eltérést. A LUIS azt jelzi, hogy ezt a pontszámot a piros Keretes eltérést észlelt. 

![BookFlight szándékot képernyőkép oldala, amelyen kiemelve előrejelzés eltérés pontszám](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>Szűrés a szándék előrejelzési eltérést észlelt hibák szándék lapon
A Váltás az utterance (kifejezés) lista, csak egy leképezési előrejelzés eltérés van a kimondott szöveg szűréséhez **összes megjelenítése** való **csak a hibák** az eszköztáron. 

## <a name="filter-by-entity-type-on-intent-page"></a>Szűrés a szándék oldalon entitás típusa
Használja a **entitás szűrők** legördülő megcímkézzen entitás alapján szűrheti az eszköztáron. 

![Képernyőkép a leképezések oldala, amelyen kiemelve entitás szűrő](./media/luis-how-to-add-intents/filter-by-entities.png) 

Távolítsa el a szűrőt, válassza a kék szűrőmezőbe az adott szó vagy kifejezés alatt az eszköztáron.  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>A leképezési lapon token nézetre váltani
Váltógomb **jogkivonatok nézet** helyett az entitás típusnevek jogkivonatok megtekintéséhez. A billentyűzet is használhatja **Control + E** megjelenítése. 

![Képernyőkép a BookFlight szándékkal, a kiemelt Token nézettel](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>Modell, szándék fog vonatkozni módosítása után az alkalmazás betanítása
Után adja hozzá, szerkeszthet és eltávolíthat szándék fog vonatkozni, [betanításához](luis-how-to-train.md) és [közzététele](luis-how-to-publish-app.md) a végpont lekérdezéseket érintő módosítások alkalmazásához. 

## <a name="next-steps"></a>További lépések

Leképezések hozzáadása az alkalmazáshoz, után a következő feladata, kezdje el hozzáadni [példa utterances](luis-how-to-add-example-utterances.md) számára a hozzáadott leképezések. 
