---
title: Adja hozzá a minta sablonok LUIS alkalmazásokban |} Microsoft Docs
titleSuffix: Azure
description: Ismerje meg a minta sablonok hozzáadása a nyelvi ismertetése (LUIS) alkalmazások, az előrejelzési pontosság növeléséhez.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: 68c0ea1fd3f2e60e0adec631f33c8bd09a3d9960
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35350062"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Az előrejelzési pontosság növeléséhez minták hozzáadása
Miután egy LUIS app végpont utterances kap, a [koncepció](luis-concept-patterns.md) mintázatok, amely tartalmaz a word és az word választott mintát utterances az előrejelzési pontosság növeléséhez. Használjon mintákra [entitások](luis-concept-entity-types.md) és szerepük az adott minta szintaxis használatával adatok kinyerése érdekében. 

## <a name="add-template-utterance-to-create-pattern"></a>Adja hozzá a sablon utterance minta létrehozása
1. Nyissa meg az alkalmazás nevét a kiválasztásával **saját alkalmazások** lapon, majd válassza ki **minták** a bal oldali panelen a **alkalmazás teljesítményének javítása**.

    ![Képernyőkép a minták listájának](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Válassza ki a megfelelő leképezést a minta. 

    ![Válassza ki a leképezést](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. A sablon szövegmezőjének írja be a sablon utterance, és válassza ki az ENTER billentyűt. Az entitás nevének megadása, használja a megfelelő mintát entitás szintaxist. Az entitás szintaxist a Begin `{`. Entitások megjeleníti listája. Válassza ki a megfelelő entitást, és válassza le az ENTER billentyűt. 

    ![Képernyőkép a minta entitás](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Az entitás a szerepkör magában foglalja, azt jelezheti a szerepkör egyetlen kettősponttal `:`, miután az entitás nevével, például a `{Location:Origin}`. Az entitások szerepkörök listája listáját jeleníti meg. Válassza ki a szerepkört, és válassza le az ENTER billentyűt. 

    ![Képernyőkép a szerepkörrel rendelkező entitás](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Miután kiválasztotta a megfelelő entitás, a minta megadott, és válassza le az ENTER billentyűt. Ha befejezte az belépés kombinációját, [betanítása](luis-how-to-train.md) az alkalmazást.

    ![Képernyőkép a megadott mintának mindkét típusú entitásokat](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>Keresési minták
Keresés lehetővé teszi megadott szöveget tartalmazó minták kereséséhez.  

1. Válassza ki a Nagyítót ábrázoló ikonra.

    ![Képernyőfelvétel a minták lapon a keresési eszköz ikonja kiemelve](./media/luis-how-to-model-intent-pattern/search-icon.png)

    A minták listájának jobb felső sarokban, a keresőmezőbe írja be a keresett szöveg, és válassza ki az ENTER billentyűt. A minták listájának megjelenítéséhez csak a például a keresési szöveget minták frissül.

    ![Képernyőfelvétel a minták lapon a keresési szöveget a keresőmezőbe a kijelölt](./media/luis-how-to-model-intent-pattern/search-text.png)

    Szakítsa meg a keresést, és állítsa vissza a teljes listát annak a, törölje a megadott keresési szöveg.

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>A szabály módosítása
1. A minta szerkesztéséhez válassza ki, hogy a minta a sor jobb oldali végén a három pont (…) ikonra, majd jelölje ki **szerkesztése**. 

    ![Minta sor menüpontja szerkesztése képernyőképe](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. A mezőben adja meg a módosításokat. Amikor elkészült, válassza ki adja meg. Ha befejezte az szerkesztési kombinációját, [betanítása](luis-how-to-train.md) az alkalmazást.

    ![Képernyőkép a minta szerkesztése](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>Egyes minta más célra való ismételt hozzárendelése

Más célra egyetlen mintát hozzárendelni a minta szöveg jobb a leképezési listában válassza ki és más célra.

![Képernyőkép a más célra egyes minta újbóli hozzárendelése](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>Több minták más célra való ismételt hozzárendelése

Több minták más célra való hozzárendelésének, jelölje be a jelölőnégyzetet minden egyes minta a bal oldali vagy felső jelölőnégyzet bejelölésével. A **ismételt hozzárendelése leképezés** lehetőséget választja, megnyílik az eszköztáron. Válassza ki a megfelelő leképezésének a minták. 

![Több minták más célra való újbóli képernyőképe](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>Egyetlen szabály törlése

1. A minta törléséhez válassza ki, hogy a minta a sor jobb oldali végén a három pont (…) ikonra, majd jelölje ki **törlése**. 

    ![Utterance törlése képernyőképe](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. Válassza ki **Ok** gombra a törlés jóváhagyásához.

    ![Jóváhagyás törlése képernyőképe](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>Több minták törlése

1. Több minták törléséhez jelölje be a jelölőnégyzetet minden egyes minta a bal oldali vagy felső jelölőnégyzet bejelölésével. A **minták (s) törlése** lehetőséget választja, megnyílik az eszköztáron. Válassza ki **minták (s) törlése**.  

    ![Képernyőkép a több minták törlése](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. A **minták törlése** megerősítése párbeszédpanel jelenik meg. Válassza ki **Ok** törlésének befejezéséhez.

    ![Képernyőkép a több minták törlése](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>Az entitás minta listájának szűrése

Egy adott entitás minták listájának szűréséhez válassza **entitás szűrők** az eszköztár fölött a minták. 

![Képernyőkép a minták entitás szerinti szűrés](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

A szűrő alkalmazása az eszköztár alatt megjelenik az entitás nevét. 

## <a name="filter-pattern-list-by-intent"></a>Minta szűrőlista szándékosan

Egy adott szándékosan minták listájának szűréséhez válassza **leképezés szűrők** az eszköztár fölött a minták. 

![Képernyőkép a minták leképezés szerinti szűrés](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

A szűrő alkalmazása a leképezési nevét az eszköztár alatt jelenik meg. 

## <a name="remove-entity-or-intent-filter"></a>Távolítsa el az entitás vagy leképezési szűrő
A minta listában vannak, amikor az entitás vagy a leképezési neve jelenik meg az eszköztár alatt. A szűrő eltávolításához jelölje ki a nevét.

![Az entitás szűrt minták képernyőképe](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

A szűrő törlődik, és minden mintát jelenít meg. 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>Minta hozzáadása a meglévő utterance leképezés vagy entitás az oldalon
A minta egy meglévő utterance akár hozhat létre a **leképezés** vagy **entitás** lap. Minden utterances bármely leképezés vagy entitás lapján jelennek meg listáját a jobb oldali oszlopban hozzá lehet férni utterance szintű beállításokat, mint a **szerkesztése**, **törlése**, és **Mintázat,Hozzáadás**.

1. A kijelölt sorban található a utterance, a három pont (…) jobb oldalán a utterance válassza ki és **mintázat, Hozzáadás**.

    [![](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "Képernyőkép a beállítások menü kiemelve Hozzáadás mintával utterances tábla")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. A minta a következők szerint módosítsa a [szintaxis szabályok](luis-concept-patterns.md#pattern-syntax). Ha a kiválasztott utterance az entitások megjelölése, a entitásokból a helyes szintaxis a mintát már szerepelnek.

    ![Az entitás szűrt minták képernyőképe](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Az alkalmazás train model mintákat módosítása után
Miután hozzáadása, szerkesztése, törlése vagy ismételt hozzárendelése egy olyan mintát, [betanítása](luis-how-to-train.md) és [közzététele](PublishApp.md) az alkalmazás a módosításokat az végpont lekérdezések. 

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [mintát build](luis-tutorial-pattern.md) egy pattern.any és szerepköröket.
* Megtudhatja, hogyan [betanítása](luis-how-to-train.md) az alkalmazást.