---
title: A minták pontosságot adnak hozzá - LUIS
titleSuffix: Azure Cognitive Services
description: Mintasablonok hozzáadása a nyelvi megértési (LUIS) alkalmazások előrejelzési pontosságának javítása érdekében.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76311716"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Minták hozzáadása az előrejelzés pontosságának javítása érdekében
Miután egy LUIS-alkalmazás végpontkimondott szövegeket kap, használjon [mintát](luis-concept-patterns.md) az előrejelzés pontosságának javítására a szósorrendben és a szóválasztásban megjelenő minta felfedéséhez. A minták meghatározott [szintaxist](luis-concept-patterns.md#pattern-syntax) használnak az [entitások,](luis-concept-entity-types.md) [entitásszerepkörök](luis-concept-roles.md)és választható szöveg helyének jelzésére.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> A minták csak a gép által megtanult entitás szülőket tartalmazzák, az alösszetevőket nem.

## <a name="adding-example-utterances-as-pattern"></a>Példakimondottszövegek hozzáadása mintaként

Ha egy entitáshoz szeretne mintát hozzáadni, a _legegyszerűbb_ módja a minta létrehozása a Szándék részletei lapon. Ez biztosítja, hogy a szintaxis megegyezik a példa utterance (kifejezés).

1. Az [előzetes LUIS portálon](https://preview.luis.ai)válassza ki az alkalmazást a **Saját alkalmazások** lapon.
1. A **szándékok** listalapon válassza ki a példa utterance (kifejezés) a példa utterance (kifejezés) a példa utterance (kifejezés) leképezési nevét.
1. A szándék részletei lapon jelölje ki a sablon utterance (kifejezés) ként használni kívánt példa utterance (kifejezés) sorát, majd válassza a **+ Hozzáadás mintaként** lehetőséget a környezet eszköztárán.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép: a példakimondott szöveg sablonmintaként való kiválasztása a Szándék részletei lapon.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. Az előugró ablakban válassza a Minták **megerősítése** lapon a **Kész** lehetőséget. Nem kell definiálnia az entitások alösszetevőit, megkötéseit vagy leíróit. Csak a gép által megtanult entitást kell felsorolnia.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép: a példa utterance (kifejezés) sablonmintaként való megerősítése a Szándék részletei lapon.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Ha a `[]` (négyzetes) zárójelekkel szerkeszteni szeretné a sablont, például a szöveg et választhatóként kell kijelölnie, akkor ezt a szerkesztést a Minták oldalról kell elkészítenie. **Patterns**

1. A navigációs sávon válassza a **Betanítás** lehetőséget, ha az alkalmazást az új mintával szeretné betanítani.

## <a name="add-template-utterance-using-correct-syntax"></a>Sablon kimondott szövege hozzáadása a megfelelő szintaxissal

1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **Saját alkalmazások** lapon, majd válassza a **Minták lehetőséget** a bal oldali panelen az **Alkalmazás teljesítményének javítása**csoportban.

    > [!div class="mx-imgBorder"]
    > ![A Minták listájának képernyőképe](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Válassza ki a minta megfelelő leképezését.

1. A sablon szövegmezőjébe írja be a sablon kimondott szövegét, és válassza az Enter lehetőséget. Ha meg szeretné adni az entitás nevét, használja a megfelelő minta entitás szintaxisát. Kezdje meg az `{`entitás szintaxisát a segítségével. Megjelenik az entitások listája. Válassza ki a megfelelő entitást.

    > [!div class="mx-imgBorder"]
    > ![A minta entitásának képernyőképe](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Ha az entitás tartalmaz egy [szerepkört,](luis-concept-roles.md)jelölje `:`meg a szerepkört `{Location:Origin}`egyetlen kettősponttal, az entitás neve után, például . Az entitások szerepköreinek listája megjelenik egy listában. Jelölje ki a szerepkört, majd válassza az Enter lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a szerepkörrel rendelkező entitásról](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Miután kiválasztotta a megfelelő entitást, fejezze be a minta beírását, majd válassza az Enter lehetőséget. Ha végzett a minták bevitelével, [tanítsa be](luis-how-to-train.md) az alkalmazást.

    > [!div class="mx-imgBorder"]
    > ![A beírt minta képernyőképe mindkét entitástípussal](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Az alkalmazás betanítása a modell mintákkal való módosítása után
Miután hozzáadott, szerkesztett, eltávolított vagy újra hozzárendelt egy mintát, [betaníthatja](luis-how-to-train.md) és [közzéteheti](luis-how-to-publish-app.md) az alkalmazást, hogy a módosítások hatással legyenek a végponti lekérdezésekre.

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Környezetfüggő eszköztár használata

A minták listája feletti környezetfüggő eszköztár lehetővé teszi a következőket:

* Minták keresése
* Minta szerkesztése
* Az egyes minták újbóli hozzárendelése különböző szándékhoz
* Több minta újrahozzárendelése különböző szándékhoz
* Egyetlen minta törlése
* Több minta törlése
* Mintalista szűrése entitás szerint
* Leképezés szerinti szűrés-mintalista
* Entitás- vagy leképezésszűrő eltávolítása
* Minta hozzáadása a leképezésvagy entitáslap meglévő utterance (kifejezés) alapján

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [hozhat létre egy mintát.any](luis-tutorial-pattern.md) és szerepkörök egy oktatóanyag.
* További információ az alkalmazás [betanításáról.](luis-how-to-train.md)
