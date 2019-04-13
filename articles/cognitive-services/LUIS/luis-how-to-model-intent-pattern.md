---
title: Minták hozzáadása pontossága
titleSuffix: Language Understanding - Azure Cognitive Services
description: A minta sablonok növelve előrejelzés pontosságát a Language Understanding (LUIS) alkalmazások hozzáadása.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 202b9632b7a7faaf955874a0300edbe5134b7fa1
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521254"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Az előrejelzési pontosság növeléséhez minták hozzáadása
Miután a LUIS-alkalmazásokon végpont utterances megkapja, egy [minta](luis-concept-patterns.md) növelve előrejelzés pontosságát, hogy egy mintát szórendjét és a word választási lehetőség felfedéséhez megcímkézzen számára. Minták használata adott [szintaxis](luis-concept-patterns.md#pattern-syntax) helyét jelzi: [entitások](luis-concept-entity-types.md), entitás [szerepkörök](luis-concept-roles.md), és nem kötelező szöveg.

## <a name="add-template-utterance-to-create-pattern"></a>Adja hozzá a minta létrehozása sablon utterance (kifejezés)
1. Nyissa meg az alkalmazás nevére kattintva a **saját alkalmazások** oldalra, és kattintson **minták** a bal oldali panelen, a **megnövelheti az alkalmazások teljesítményét**.

    ![Minták listájának képernyőképe](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Válassza ki a megfelelő leképezést a minta. 

    ![Leképezés kiválasztása](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. A sablon szövegmezőbe írja be a sablon utterance (kifejezés), és nyomja le az Enter. Szeretné az entitás nevét adja meg, ha a helyes mintát entitás szintaxist használja. Az entitás-szintaxis és megkezdéséhez `{`. Entitások jeleníti meg listája. Válassza ki a megfelelő entitást, és válassza le az ENTER billentyűt. 

    ![Képernyőkép a minta entitás](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Ha az entitás tartalmaz egy [szerepkör](luis-concept-roles.md), jelezze a szerepkör egyetlen kettősponttal `:`, miután az entitás nevével, például `{Location:Origin}`. A szerepkörök az entitások listájának listáját jeleníti meg. Válassza ki a szerepkört, és válassza le az ENTER billentyűt. 

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

## <a name="use-contextual-toolbar"></a>Környezetfüggő eszköztár

A környezetfüggő eszköztár fölött a minták listájának lehetővé teszi:

* Minták keresése
* Egy szabály módosítása
* Egyes minta különböző beszédszándék újbóli hozzárendelése
* Több, különböző beszédszándék minták újbóli hozzárendelése
* Delete-a-single-pattern
* Több minták törlése
* Entitás minta listájának szűrése
* Filter-pattern-list-by-intent
* Entitás vagy szándék szűrő eltávolítása
* Adja hozzá a minta a leképezés vagy entitás oldalon meglévő utterance (kifejezés)

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [hozhat létre egy minta](luis-tutorial-pattern.md) egy pattern.any és a egy oktatóanyag szerepköröket.
* Ismerje meg, hogyan [betanításához](luis-how-to-train.md) az alkalmazást.
