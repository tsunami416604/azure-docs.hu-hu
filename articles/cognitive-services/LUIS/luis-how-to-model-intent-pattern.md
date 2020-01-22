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
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311716"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Mintázatok hozzáadása az előrejelzési pontosság növeléséhez
Miután a LUIS-alkalmazás végponti hosszúságú kimondott szöveg kap, egy [mintázattal](luis-concept-patterns.md) növelheti az előrejelzés pontosságát olyan hosszúságú kimondott szöveg esetében, amelyek felfedik a mintázatot a Word-sorrend és a szó választása alapján. A minták az adott [szintaxist](luis-concept-patterns.md#pattern-syntax) használják a következő helyének jelzésére: [entitások](luis-concept-entity-types.md), entitások [szerepkörei](luis-concept-roles.md)és opcionális szöveg.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> A minták csak a gépi megtanult entitások szüleit tartalmazzák, nem alösszetevőkből.

## <a name="adding-example-utterances-as-pattern"></a>Példa hosszúságú kimondott szöveg mintaként való hozzáadására

Ha egy entitáshoz mintázatot szeretne hozzáadni, a _legegyszerűbb_ módszer a minta létrehozása a szándék részletei lapról. Ez biztosítja, hogy a szintaxis megfelel a példának.

1. A [Luis-portálon](https://preview.luis.ai)válassza ki az alkalmazást a **saját alkalmazások** lapról.
1. A **cél** lista lapon válassza ki annak a példának a leképezési nevét, amelynek a sablonból való kiválasztását szeretné létrehozni.
1. A leképezés részletei lapon válassza ki a sablon-kifejezésként használni kívánt példa részletének sorát, majd válassza a **+ Hozzáadás mint minta** lehetőséget a helyi eszköztáron.

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a példa szerinti kiválasztásról sablon mintaként a szándék részletei lapon.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. Az előugró ablakban válassza a **kész** lehetőséget a **minták megerősítése** lapon. Nem kell meghatároznia az entitások alösszetevőit, korlátozásait vagy leíróit. Csak a géppel megtanult entitást kell listáznia.

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a megerősítő példa részleteit tartalmazó sablon mintaként a leképezés részletei lapon.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Ha szerkesztenie kell a sablont, például a szöveg választhatóként való kiválasztását, a `[]` (szögletes) zárójelekkel, ezt a szerkesztést a **minták** lapról kell végrehajtania.

1. A navigációs sávon válassza a **betanítás** lehetőséget az alkalmazás új mintázattal való betanításához.

## <a name="add-template-utterance-using-correct-syntax"></a>Sablon teljes megadása a helyes szintaxissal

1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** lapon, majd a bal oldali panelen válassza a **mintázatok** lehetőséget az **alkalmazás teljesítményének javítása**lehetőségnél.

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a minták listájáról](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Válassza ki a minta megfelelő szándékát.

1. A sablon szövegmezőbe írja be a sablon szövegét, és válassza az ENTER billentyűt. Ha meg szeretné adni az entitás nevét, használja a megfelelő minta entitás szintaxisát. Az entitás szintaxisának megkezdése `{`vel. Megjelenik az entitások listája. Válassza ki a megfelelő entitást.

    > [!div class="mx-imgBorder"]
    > az entitás ![képernyőképe a minta](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Ha az entitás tartalmaz egy [szerepkört](luis-concept-roles.md), jelölje ki a szerepkört egyetlen kettősponttal, `:`az entitás neve után, például `{Location:Origin}`. Az entitások szerepköreinek listája megjelenik a listában. Válassza ki a szerepkört, majd kattintson az ENTER gombra.

    > [!div class="mx-imgBorder"]
    > ![a szerepkörrel rendelkező entitás képernyőképe](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Miután kiválasztotta a megfelelő entitást, fejezze be a minta beírását, majd válassza az ENTER billentyűt. Ha elkészült a minták beírásával, [betaníthatja](luis-how-to-train.md) az alkalmazást.

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a beírt mintázatról mindkét típusú entitással](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Az alkalmazás betanítása a modell mintázattal való módosítása után
A minták hozzáadása, szerkesztése, eltávolítása vagy újbóli társítása után a rendszer [betanítja](luis-how-to-train.md) és [közzéteszi](luis-how-to-publish-app.md) az alkalmazást, hogy a módosítások hatással legyenek a végponti lekérdezésekre.

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
* Minta szerkesztése
* Egyéni minta ismételt társítása más szándékhoz
* Több minta ismételt társítása más szándékhoz
* Egyetlen minta törlése
* Több minta törlése
* Minták listájának szűrése entitás szerint
* Szűrő-minta-List-by-szándéknyilatkozat
* Entitás vagy leképezési szűrő eltávolítása
* Minta hozzáadása a meglévő kifejezésből a szándék vagy az entitás oldalon

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [hozhat létre mintázattal](luis-tutorial-pattern.md) ellátott mintát. a és a szerepkörök egy oktatóanyaggal rendelkeznek.
* Útmutató az alkalmazás [betanításához](luis-how-to-train.md) .
