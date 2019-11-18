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
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 39b56c5e73c8ce85a020402dafb622b90c536a1e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143833"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Mintázatok hozzáadása az előrejelzési pontosság növeléséhez
Miután a LUIS-alkalmazás végponti hosszúságú kimondott szöveg kap, egy [mintázattal](luis-concept-patterns.md) növelheti az előrejelzés pontosságát olyan hosszúságú kimondott szöveg esetében, amelyek felfedik a mintázatot a Word-sorrend és a szó választása alapján. A minták az adott [szintaxist](luis-concept-patterns.md#pattern-syntax) használják a következő helyének jelzésére: [entitások](luis-concept-entity-types.md), entitások [szerepkörei](luis-concept-roles.md)és opcionális szöveg.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-template-utterance-to-create-pattern"></a>Adja hozzá a minta létrehozása sablon utterance (kifejezés)

1. Nyissa meg az alkalmazás nevére kattintva a **saját alkalmazások** oldalra, és kattintson **minták** a bal oldali panelen, a **megnövelheti az alkalmazások teljesítményét**.

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a minták listájáról](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Válassza ki a megfelelő leképezést a minta. 

1. A sablon szövegmezőbe írja be a sablon utterance (kifejezés), és nyomja le az Enter. Szeretné az entitás nevét adja meg, ha a helyes mintát entitás szintaxist használja. Az entitás-szintaxis és megkezdéséhez `{`. Entitások jeleníti meg listája. Válassza ki a megfelelő entitást. 

    > [!div class="mx-imgBorder"]
    > az entitás ![képernyőképe a minta](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Ha az entitás tartalmaz egy [szerepkört](luis-concept-roles.md), jelölje ki a szerepkört egyetlen kettősponttal, `:`az entitás neve után, például `{Location:Origin}`. A szerepkörök az entitások listájának listáját jeleníti meg. Válassza ki a szerepkört, és válassza le az ENTER billentyűt. 

    > [!div class="mx-imgBorder"]
    > ![a szerepkörrel rendelkező entitás képernyőképe](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Miután kiválasztotta a megfelelő entitás, befejezéséhez írja be a mintát, és válassza le az ENTER billentyűt. Amikor elkészült a belépés minták [betanításához](luis-how-to-train.md) az alkalmazást.

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a beírt mintázatról mindkét típusú entitással](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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
