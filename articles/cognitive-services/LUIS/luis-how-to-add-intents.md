---
title: Adja hozzá a leképezések LUIS alkalmazásokban |} Microsoft Docs
description: Nyelvi ismertetése (LUIS) használatával adhatja hozzá a leképezések érdekében az alkalmazások felhasználói kérelmek megértéséhez, valamint megfelelően reagálni rájuk.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.service: cognitive-services
ms.openlocfilehash: 6e013e994a3bcb60c3104aa10cd7bad1535706f1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349794"
---
# <a name="manage-intents"></a>Leképezések kezelése 
Adja hozzá [leképezések](luis-concept-intent.md) azonosítására kérdések vagy az azonos céljaira rendelkező parancsok a LUIS alkalmazásba. 

Vegye fel és kezelheti a leképezések az a **leképezések** lapján érhetők el az **leképezések** LUIS a bal oldali panelen. 

Az alábbi eljárás bemutatja, hogyan hozzáadása a "Bookflight" célt a TravelAgent alkalmazásban.

## <a name="add-intent"></a>Leképezés hozzáadása

1. Nyissa meg az alkalmazás (például TravelAgent) a neve kattintva **saját alkalmazások** lapon, majd **leképezések** a bal oldali panelen. 
2. Az a **leképezések** kattintson **új leképezés létrehozásához**.

    ![Leképezések listája](./media/luis-how-to-add-intents/IntentsList.png)
3. Az a **új leképezés létrehozásához** párbeszédpanelen írja be a cél "BookFlight" nevet, és kattintson a **végzett**.

    ![Leképezés hozzáadása](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Az újonnan hozzáadott leképezés leképezési részleteit megjelenítő oldalon [utterances hozzáadása](#add-an-utterance-on-intent-page).

## <a name="rename-intent"></a>Nevezze át a cél

1. Az a **leképezés** lapján kattintson az Átnevezés ikon ![átnevezése leképezés](./media/luis-how-to-add-intents/Rename-Intent-btn.png) leképezési neve melletti. 

2. Az a **leképezés** lapon is látható egy párbeszédpanel az aktuális leképezési nevét. Szerkessze a leképezési nevét, és nyomja le az enter. Az új neve mentett és a leképezési lapon jelennek meg.

    ![Leképezés szerkesztése](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>Leképezés törlése
A none leképezési megjelölésű törlésekor kiválaszthatja a utterances hozzáadása a nincs szándék. Ez akkor hasznos, ha át kell helyeznie a utterances helyett törölni őket.   

1. Az a **leképezés** lapján kattintson a **szándéka Törlés** mellett a leképezési nevétől jobbra látható gombra. 

    ![Leképezési gomb törlése](./media/luis-how-to-add-intents/DeleteIntent.png)

2. Az "Ok" gombra a megerősítő párbeszédpanelen.

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>Adja hozzá egy utterance leképezési az oldalon

A leképezési lapon adja meg a megfelelő utterance adatraktáraktól a felhasználók számára, például a `book 2 adult business tickets to Paris tomorrow on Air France` a szövegmezőben alatti leképezési nevét, és nyomja le az ENTER billentyűt. 
 
>[!NOTE]
>LUIS összes utterances csupa kisbetűssé alakítja.

![Képernyőfelvétel a leképezések részleteit megjelenítő oldalon, a kiemelt utterance](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

Az aktuális szándékot utterances telefonokhoz utterances kerülnek. Egy utterance hozzáadása után [címke a entitások](luis-how-to-add-example-utterances.md) belül a utterances és [betanítása](luis-how-to-train.md) az alkalmazást. 

## <a name="create-a-pattern-from-an-utterance"></a>A minta egy utterance létrehozása
Lásd: [Hozzáadás mintát a leképezés vagy entitás oldalon meglévő utterance](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="edit-an-utterance-on-intent-page"></a>Egy leképezési oldalon utterance szerkesztése

Egy utterance szerkesztéséhez jelölje ki a három pont (…) ikonra az adott utterance a sor jobb oldali végén, majd **szerkesztése**. Módosítsa a szöveget, majd nyomja le az Enter billentyűt.

![Képernyőfelvétel a leképezések részleteit megjelenítő oldalon a három pont ikonja kiemelve](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>A leképezési lapon utterances ismételt hozzárendelése
Egy vagy több utterances szándékával módosításához újrakiosztása őket a másik célt. 

Egy egyetlen utterance más célra, a utterance sorának jobb szélén való hozzárendelésének jelölje ki a megfelelő leképezési nevét a a **leképezés feliratú** oszlop. A utterance törlődik az aktuális szándékot utterance listája. 

![Képernyőfelvétel a BookFlight leképezési lap egy utterance biztonsági mentés Labeled kiválasztott leképezési oszlop alapján](./media/luis-how-to-add-intents/reassign-1-utterance.png)

Több utterances szándékával módosításához jelölje be a jelölőnégyzeteket a utterances balra, és válassza **ismételt hozzárendelése leképezés**. Válassza ki a megfelelő leképezést a listából.

![Képernyőfelvétel a BookFlight leképezési lapon be van jelölve egy utterance és az újbóli hozzárendelésének leképezési gomb](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>A leképezési lapon utterances törlése

Egy utterance törléséhez jelölje ki az adott utterance a sor jobb oldali végén a három pont (…) ikonra, majd **törlése**. A utterance távolítja el a lista és a LUIS alkalmazást.

![Képernyőfelvétel a leképezések részleteit megjelenítő oldalra, törlési lehetőséggel kiemelve](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

Több utterances törlése:

1. Jelölje be a jelölőnégyzeteket a utterances balra, és válassza ki **utterances (s) törlése**. 

    ![Képernyőfelvétel a leképezések részleteit megjelenítő oldalon a utterances be van jelölve, és törölje a utterance(s) gomb](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. Válassza ki **végzett** a a **utterances törlése?** előugró párbeszédpanelen.

## <a name="search-in-utterances-on-intent-page"></a>A leképezési oldalon utterances keresése
A megjelölésű utterances (szavakat vagy kifejezéseket) szöveget tartalmazó kereshet. Például előfordulhat, hogy figyelje meg, amely magában foglalja egy adott word hiba, és minden példák, amely tartalmazza az adott adott word keresi. 

1. Válassza ki a Nagyítót ábrázoló ikonra az eszköztárban.

    ![Képernyőfelvétel a leképezések lap, a keresés ikonra a kijelölt Nagyító](./media/luis-how-to-add-intents/magnifying-glass.png)

2. A keresési szövegmezőben megjelenik. Írja be a szót vagy kifejezést a keresőmezőbe a utterances listájának jobb felső sarokban lévő. A utterances frissítések, csak a keresett szöveget tartalmazó a utterances megjelenítendő listában. 

    ![Képernyőfelvétel a leképezések lapon, a kijelölt keresési szövegmező](./media/luis-how-to-add-intents/search-textbox.png)

    Szakítsa meg a keresési, és visszaállítja a utterances teljes listáját, törölje a megadott keresési szöveg. Zárja be a keresési mezőbe, jelölje be a Nagyítót ábrázoló ikonra az eszköztárban ismét.

## <a name="prediction-discrepancy-errors-on-intent-page"></a>Előrejelzés ellentmondás hibák leképezési az oldalon
Egy utterance a megjelölésű előfordulhat, hogy a kijelölt leképezés és az előrejelzés pontszám közötti eltérés. LUIS azt jelzi, hogy ez a pontszám vörös téglalappal eltérést észlelt. 

![BookFlight leképezés képernyőkép lapot, előrejelzés ellentmondás pontszám kiemelve](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>Leképezési előrejelzés ellentmondás hibák leképezési oldalon szűrés
A Váltás csak egy szándék előrejelzés ellentmondás a utterances utterance lista szűréséhez **összes megjelenítése** való **csak a hibák** az eszköztáron. 

## <a name="filter-by-entity-type-on-intent-page"></a>Az entitástípus leképezési oldalon szűrés
Használja a **entitás szűrők** legördülő entitás a utterances szűrése az eszköztáron. 

![Képernyőfelvétel a leképezések lapon, a kijelölt entitás szűrő](./media/luis-how-to-add-intents/filter-by-entities.png) 

A szűrő eltávolításához jelölje ki az adott szó vagy kifejezés az eszköztár a kék szűrő mezőbe.  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>A leképezési lapon token nézetre váltani
Váltás **jogkivonatok nézet** megtekintéséhez a jogkivonatok az entitás típusnevek helyett. A billentyűzet használhatja **vezérlő + E** megjelenítése. 

![Képernyőkép a BookFlight leképezést, és a kijelölt Token nézet](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>Az alkalmazás betanítása leképezések modell módosítása után
Adja hozzá, szerkeszthet és eltávolíthat leképezések, miután [betanítása](luis-how-to-train.md) és [közzététele](PublishApp.md) az alkalmazás a módosításokat az végpont lekérdezések. 

## <a name="next-steps"></a>További lépések

Leképezések hozzáadása az alkalmazáshoz, után a következő feladathoz felvételéhez [példa utterances](luis-how-to-add-example-utterances.md) az adott hozzá, a leképezések. 
