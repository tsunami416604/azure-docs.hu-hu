---
title: Tekintse át a felhasználói kimondott szöveg
titleSuffix: Language Understanding - Azure Cognitive Services
description: A LUIS áttörést jelentő funkció az aktív tanulás fogalmát. Miután a LUIS végpont lekérdezések rendelkezik, aktív tanulás növeli az eredmények minőségének választja ki, hogy pontosan kimondott szöveg. Ha ezek a kimondott szöveg, betanítását és közzététele, majd a LUIS pontosabb azonosítja a kimondott szöveg.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 13a9fba996ea2929bb7165c395176fbfb8c051c3
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208057"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal"></a>A LUIS-portál végpontja utterances felülvizsgálata

A LUIS átütő jellemzője a [fogalom](luis-concept-review-endpoint-utterances.md) , aktív tanulás. Miután a LUIS végpont lekérdezéseket tartalmaz, a LUIS használja aktív tanulás az eredmények minőségének javítása érdekében. Az aktív tanulás folyamatban LUIS megvizsgálja az összes endpoint megcímkézzen, és kiválasztja, hogy pontosan kimondott szöveg. Ha ezek a kimondott szöveg, betanítását és közzététele, majd a LUIS pontosabb azonosítja a kimondott szöveg. 

## <a name="filter-utterances"></a>Szűrő kimondott szöveg
1. Nyissa meg az alkalmazás (például TravelAgent) válassza a neve a **saját alkalmazások** lapon, majd válassza a **hozhat létre** a lap tetején található.

2. Alatt a **megnövelheti az alkalmazások teljesítményét**válassza **tekintse át a végpont utterances**.

3. A a **tekintse át a végpont utterances** lapon jelölje be a a **szűrőlista leképezés vagy entitás** szövegmező. A legördülő lista tartalmaz minden leképezések alapján **LEKÉPEZÉSEK** és az összes entitása **entitások**.

    ![Kimondott szöveg szűrése](./media/label-suggested-utterances/filter.png)

4. A legördülő listában válassza ki a kategóriát (szándékok és entitások), és tekintse át a kimondott szöveg.

    ![Leképezési kimondott szöveg](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Címke entitások
A LUIS kék színnel entitásnévnek entitás jogkivonatok (szavak) helyettesíti. Ha az utterance (kifejezés) entitások rendelkezik címkézetlen, lássa el az utterance (kifejezés). 

1. Jelölje be az utterance (kifejezés) a szavak. 

2. Entitás kiválasztása a listából.

    ![Címke entitás](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Egyetlen utterance (kifejezés) igazítása

Minden kimondásakor rendelkezik egy javasolt szándékot, megjelenik a **szándékot igazítva** oszlop. 

1. Ha elfogadja, hogy javaslatot, jelölje be a pipa jelre.

    ![Tartsa igazított leképezés](./media/label-suggested-utterances/align-intent-check.png)

2. Ha Ön nem ért a javaslatot, válassza ki a megfelelő leképezést a igazított szándék legördülő listából, majd jelölje ki a jobbra igazított célja a pipa jelre a. 

    ![Leképezés igazítása](./media/label-suggested-utterances/align-intent.png)

3. Miután kiválasztotta a pipa jelre, a rendszer eltávolítja az utterance (kifejezés) a listából. 

## <a name="align-several-utterances"></a>Több kimondott szöveg igazítása

Több utterances igazításához megcímkézzen balra a jelölőnégyzetet, majd válassza ki a a **kiválasztott Hozzáadás** gombra. 

![Több igazítása](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Ellenőrizze a igazított leképezés
Ellenőrizheti az utterance (kifejezés) a megfelelő készítésében lett igazítva a **leképezések** lapon válassza ki a leképezés nevét, és tekintse át a kimondott szöveg. Az utterance (kifejezés) **tekintse át a végpont utterances** szerepel a listán.

## <a name="delete-utterance"></a>Törölje az utterance (kifejezés)
Minden kimondásakor a felülvizsgálati listából lehet törölni. A törölt, nem jelenik a lista újra. Ez igaz, akkor is, ha a felhasználó megadja az azonos utterance (kifejezés) a végpontról. 

Ha bizonytalan, ha az utterance (kifejezés) törölni kell, vagy helyezze át a nincs szándék, vagy hozzon létre egy új szándékot, például az "egyéb" és az utterance (kifejezés) át, hogy a leképezés. 

## <a name="delete-several-utterances"></a>Több kimondott szöveg törlése
Több kimondott szöveg törléséhez jelöljön be minden elemet, és válassza a jobb oldalán a szemetes a **kiválasztott Hozzáadás** gombra.

![Több törlése](./media/label-suggested-utterances/delete-several.png)

## <a name="next-steps"></a>További lépések

Hogyan javítja a teljesítményt, akkor javasolt utterances címke után teszteléséhez elérheti a tesztelési konzol kiválasztásával **tesztelése** a az ablak tetején. Tesztelheti az alkalmazást a tesztelési konzollal kapcsolatos utasításokért lásd: [Train és tesztelje alkalmazását](luis-interactive-test.md).
