---
title: Minták hozzáadása pontosság – LUIS
titleSuffix: Azure Cognitive Services
description: Vegyen fel mintákat a Language Understanding (LUIS) alkalmazások előrejelzési pontosságának javításához.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 7b6c8ba1517de44d01ffbceec812749403465e63
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638091"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Mintázatok hozzáadása az előrejelzési pontosság növeléséhez
Miután a LUIS-alkalmazás végponti hosszúságú kimondott szöveg kap, [](luis-concept-patterns.md) egy mintázattal növelheti az előrejelzés pontosságát olyan hosszúságú kimondott szöveg esetében, amelyek felfedik a mintázatot a Word-sorrend és a szó választása alapján. A minták az [](luis-concept-patterns.md#pattern-syntax) adott szintaxist használják a következő helyének jelzésére: [entitások](luis-concept-entity-types.md), entitások [szerepkörei](luis-concept-roles.md)és opcionális szöveg.

## <a name="add-template-utterance-to-create-pattern"></a>Adja hozzá a minta létrehozása sablon utterance (kifejezés)
1. Nyissa meg az alkalmazás nevére kattintva a **saját alkalmazások** oldalra, és kattintson **minták** a bal oldali panelen, a **megnövelheti az alkalmazások teljesítményét**.

    ![Minták listájának képernyőképe](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Válassza ki a megfelelő leképezést a minta. 

    ![Leképezés kiválasztása](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. A sablon szövegmezőbe írja be a sablon utterance (kifejezés), és nyomja le az Enter. Szeretné az entitás nevét adja meg, ha a helyes mintát entitás szintaxist használja. Az entitás-szintaxis és megkezdéséhez `{`. Entitások jeleníti meg listája. Válassza ki a megfelelő entitást, és válassza le az ENTER billentyűt. 

    ![Képernyőkép a minta entitás](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Ha az entitás tartalmaz egy [szerepkört](luis-concept-roles.md), jelölje ki a szerepkört egyetlen kettősponttal `:`, az entitás `{Location:Origin}`neve után, például:. A szerepkörök az entitások listájának listáját jeleníti meg. Válassza ki a szerepkört, és válassza le az ENTER billentyűt. 

    ![Képernyőkép a szerepkörrel rendelkező entitás](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Miután kiválasztotta a megfelelő entitás, befejezéséhez írja be a mintát, és válassza le az ENTER billentyűt. Amikor elkészült a belépés minták [betanításához](luis-how-to-train.md) az alkalmazást.

    ![Képernyőkép a megadott mintának, és mindkét típusú entitást](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Minták modell módosítása után az alkalmazás betanítása
Miután hozzáadása, szerkesztése, törlése vagy újbóli hozzárendelése egy olyan mintát, [betanításához](luis-how-to-train.md) és [közzététele](luis-how-to-publish-app.md) a végpont lekérdezéseket érintő módosítások alkalmazásához. 

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

A minták lista feletti környezetfüggő eszköztár a következőket teszi lehetővé:

* Minták keresése
* Egy szabály módosítása
* Egyes minta különböző beszédszándék újbóli hozzárendelése
* Több, különböző beszédszándék minták újbóli hozzárendelése
* Egyetlen minta törlése
* Több minták törlése
* Entitás minta listájának szűrése
* Szűrő-minta-List-by-szándéknyilatkozat
* Entitás vagy szándék szűrő eltávolítása
* Adja hozzá a minta a leképezés vagy entitás oldalon meglévő utterance (kifejezés)

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [hozhat létre mintázattal](luis-tutorial-pattern.md) ellátott mintát. a és a szerepkörök egy oktatóanyaggal rendelkeznek.
* Ismerje meg, hogyan [betanításához](luis-how-to-train.md) az alkalmazást.
