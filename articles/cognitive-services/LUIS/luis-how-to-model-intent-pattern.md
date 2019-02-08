---
title: Minták hozzáadása pontossága
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ismerje meg, a minta-sablonok hozzáadása a Language Understanding (LUIS) alkalmazások növelve előrejelzés pontosságát.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 9d3352017723f5beac318d461a537820b6593bef
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881672"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Az előrejelzési pontosság növeléséhez minták hozzáadása
LUIS-alkalmazásokon végpont utterances kap, miután a [fogalom](luis-concept-patterns.md) minták növelve előrejelzés pontosságát, hogy egy mintát szórendjét és a word választási lehetőség felfedéséhez megcímkézzen számára. Használati mintáit [entitások](luis-concept-entity-types.md) és azok szerepköreivel adott minta szintaxis segítségével adatokat szeretne kinyerni. 

## <a name="add-template-utterance-to-create-pattern"></a>Adja hozzá a minta létrehozása sablon utterance (kifejezés)
1. Nyissa meg az alkalmazás nevére kattintva a **saját alkalmazások** oldalra, és kattintson **minták** a bal oldali panelen, a **megnövelheti az alkalmazások teljesítményét**.

    ![Minták listájának képernyőképe](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Válassza ki a megfelelő leképezést a minta. 

    ![Leképezés kiválasztása](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. A sablon szövegmezőbe írja be a sablon utterance (kifejezés), és nyomja le az Enter. Szeretné az entitás nevét adja meg, ha a helyes mintát entitás szintaxist használja. Az entitás-szintaxis és megkezdéséhez `{`. Entitások jeleníti meg listája. Válassza ki a megfelelő entitást, és válassza le az ENTER billentyűt. 

    ![Képernyőkép a minta entitás](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Ha az entitás tartalmaz egy szerepkörhöz, jelezze a szerepkör egyetlen kettősponttal, `:`, miután az entitás nevével, például `{Location:Origin}`. A szerepkörök az entitások listájának listáját jeleníti meg. Válassza ki a szerepkört, és válassza le az ENTER billentyűt. 

    ![Képernyőkép a szerepkörrel rendelkező entitás](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Miután kiválasztotta a megfelelő entitás, befejezéséhez írja be a mintát, és válassza le az ENTER billentyűt. Amikor elkészült a belépés minták [betanításához](luis-how-to-train.md) az alkalmazást.

    ![Képernyőkép a megadott mintának, és mindkét típusú entitást](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>Minták keresése
A keresés lehetővé teszi a megadott szöveget tartalmazó mintákat találni.  

1. Válassza a Nagyító ikont.

    ![Képernyőkép a minták lap a search eszköz ikonja kiemelve](./media/luis-how-to-model-intent-pattern/search-icon.png)

    Írja be a keresett szöveget a keresőmezőbe, a jobb felső sarokban található mintákat listáját, és nyomja le az Enter. A minták listájának megjelenítéséhez csak azokat a mintákat, többek között a keresési szöveget frissül.

    ![Képernyőkép a minták lapon a keresési szöveget a keresőmezőbe kiemelésével](./media/luis-how-to-model-intent-pattern/search-text.png)

    A Keresés megszakítása és a visszaállítás a mintákat teljes listáját, törölje a beírt keresési szöveget.

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>Egy szabály módosítása
1. A minta szerkesztéséhez kattintson a három pontra (***...*** ) minta vonal a jobb végén gombra, majd válassza **szerkesztése**. 

    ![Minta sorban menüpont szerkesztése képernyőképe](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. A szövegmezőben adja meg a módosításokat. Amikor elkészült, válassza ki adja meg. Amikor elkészült a szerkesztési minták [betanításához](luis-how-to-train.md) az alkalmazást.

    ![Képernyőkép a minta szerkesztéséhez](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>Egyes minta különböző beszédszándék újbóli hozzárendelése

Szeretné hozzárendelni egy másik beszédszándék egyetlen mintát, a mintának a szöveg jobb a szándék legördülő listából válassza ki, és válassza ki egy másik leképezést.

![Képernyőkép a különböző beszédszándék egyes minta újbóli hozzárendelése](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>Több, különböző beszédszándék minták újbóli hozzárendelése

Egy másik beszédszándék több mintákat szeretné hozzárendelni, jelölje be a jelölőnégyzetet minden minta balra, vagy jelölje be a felső. A **újbóli hozzárendelése a leképezés** lehetőség jelenít meg, az eszköztáron. Válassza ki a megfelelő leképezést a mintákat. 

![Képernyőkép a különböző beszédszándék több minták újbóli hozzárendelése](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>Egyetlen mintát törlése

1. A minta törléséhez válassza ki a három pontra (***...*** ) minta vonal a jobb végén gombra, majd válassza **törlése**. 

    ![Törlés képernyőfelvétel utterance (kifejezés)](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. Válassza ki **Ok** a törlés megerősítéséhez.

    ![Törlés képernyőfelvétel megerősítése](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>Több minták törlése

1. Több minták törléséhez jelölje be a jelölőnégyzetet minden minta balra, vagy jelölje be a felső. A **törlése (s). mintázatok** lehetőség jelenít meg, az eszköztáron. Válassza ki **törlése (s). mintázatok**.  

    ![Több minták törlésének képernyőképe](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. A **minták törlése** megerősítő párbeszédpanel jelenik meg. Válassza ki **Ok** a Törlés gombra.

    ![Több minták mezőről törlésének képernyőképe](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>Entitás minta listájának szűrése

Egy adott entitás minták a lista szűréséhez válassza **entitás szűrők** fent a minták az eszköztáron. 

![Képernyőkép a minták entitás szerinti szűrés](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

Miután a szűrő alkalmazása, az entitás nevét az eszköztár alatt jelenik meg. 

## <a name="filter-pattern-list-by-intent"></a>A minta szűrőlista szándékosan

Minták listája szűrhet egy adott szándékot, válassza ki a **szándékot szűri** az eszköztár fölött a minták. 

![Képernyőkép a minták leképezés szerinti szűrés](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

A szűrő alkalmazásakor a leképezés neve jelenik meg az eszköztáron. 

## <a name="remove-entity-or-intent-filter"></a>Entitás vagy szándék szűrő eltávolítása
Ha a minta a szűrt lista, az eszköztáron az entitás vagy a leképezés neve jelenik meg. A szűrő eltávolításához válassza ki a nevét.

![Képernyőkép az entitás szűrő eltávolítása](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

A szűrő törlődik, és minden minták megjelenítése. 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>Adja hozzá a minta a leképezés vagy entitás oldalon meglévő utterance (kifejezés)
Egy meglévő utterance (kifejezés) sem hozhat létre egy minta a **szándékot** vagy **entitás** lap. Minden utterances bármely leképezés vagy entitás lapján jelennek meg listáját a jobb oldali oszlopban, mint például a utterance (kifejezés) szintű beállításokat való hozzáférés biztosítása a **szerkesztése**, **törlése**, és **mintakénthozzáadása**.

1. A kijelölt sora az utterance (kifejezés), válassza a három pontra (***...*** ) az utterance (kifejezés) jobb gombra, és válassza **mintaként Hozzáadás**.

    [![Képernyőkép a beállítások menü kiemelt Hozzáadás mintával utterances tábla](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "Képernyőkép a beállítások menü kiemelt Hozzáadás minta utterances-tábla")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. A minta szerint módosíthatja a [szintaxis szabályainak](luis-concept-patterns.md#pattern-syntax). Ha bejelölte az utterance (kifejezés) feliratú entitásokkal, ezeket az entitásokat is még a mintában a helyes szintaxis.

    ![Képernyőkép a szűrt minták entitás](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Minták modell módosítása után az alkalmazás betanítása
Miután hozzáadása, szerkesztése, törlése vagy újbóli hozzárendelése egy olyan mintát, [betanításához](luis-how-to-train.md) és [közzététele](luis-how-to-publish-app.md) a végpont lekérdezéseket érintő módosítások alkalmazásához. 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [hozhat létre egy minta](luis-tutorial-pattern.md) egy pattern.any és szerepköröket.
* Ismerje meg, hogyan [betanításához](luis-how-to-train.md) az alkalmazást.
