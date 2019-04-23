---
title: Tekintse át a felhasználói kimondott szöveg
titleSuffix: Language Understanding - Azure Cognitive Services
description: Aktív tanulás végpont lekérdezések rögzíti, és kiválasztja, hogy nem tudja, hogy a felhasználó endpoint kimondott szöveg. Tekintse át ezeket utterances válassza ki a leképezést, és jelölje be az entitások esetében ezek olvasási vett kimondott szöveg. Fogadja el a példa utterances be ezeket a módosításokat, majd betanítás, közzététel. A LUIS majd azonosítja a kimondott szöveg pontosabb geokódolást.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: 8fac360682ef11c438cdec333fac21d6f8cfc117
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791051"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>A LUIS-portál aktív tanulás végpont utterances felülvizsgálata

[Aktív tanulás](luis-concept-review-endpoint-utterances.md) végpont lekérdezések rögzíti, és kiválasztja, hogy nem tudja, hogy a felhasználó endpoint kimondott szöveg. Tekintse át ezeket utterances válassza ki a leképezést, és jelölje be az entitások esetében ezek olvasási vett kimondott szöveg. Fogadja el a példa utterances be ezeket a módosításokat, majd betanítás, közzététel. A LUIS majd azonosítja a kimondott szöveg pontosabb geokódolást.


## <a name="enable-active-learning"></a>Aktív tanulás engedélyezése

Ahhoz, hogy az aktív tanulás, a felhasználói lekérdezések naplózását. Mindez azzal a [végpont lekérdezés](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) az a `log=true` lekérdezési karakterlánc paraméter és érték.

## <a name="disable-active-learning"></a>Aktív tanulás letiltása

Aktív tanulás letiltásához ne naplózza a felhasználói lekérdezések. Mindez azzal a [végpont lekérdezés](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) az a `log=false` lekérdezési karakterlánc paraméter és érték.

## <a name="filter-utterances"></a>Szűrő kimondott szöveg

1. Nyissa meg az alkalmazás (például TravelAgent) válassza a neve a **saját alkalmazások** lapon, majd válassza a **hozhat létre** a lap tetején található.

1. Alatt a **megnövelheti az alkalmazások teljesítményét**válassza **tekintse át a végpont utterances**.

1. A a **tekintse át a végpont utterances** lapon jelölje be a a **szűrőlista leképezés vagy entitás** szövegmező. A legördülő lista tartalmaz minden leképezések alapján **LEKÉPEZÉSEK** és az összes entitása **entitások**.

    ![Kimondott szöveg szűrése](./media/label-suggested-utterances/filter.png)

1. A legördülő listában válassza ki a kategóriát (szándékok és entitások), és tekintse át a kimondott szöveg.

    ![Leképezési kimondott szöveg](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Címke entitások
A LUIS kék színnel entitásnévnek entitás jogkivonatok (szavak) helyettesíti. Ha az utterance (kifejezés) entitások rendelkezik címkézetlen, lássa el az utterance (kifejezés). 

1. Jelölje be az utterance (kifejezés) a szavak. 

1. Entitás kiválasztása a listából.

    ![Címke entitás](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Egyetlen utterance (kifejezés) igazítása

Minden kimondásakor rendelkezik egy javasolt szándékot, megjelenik a **szándékot igazítva** oszlop. 

1. Ha elfogadja, hogy javaslatot, jelölje be a pipa jelre.

    ![Tartsa igazított leképezés](./media/label-suggested-utterances/align-intent-check.png)

1. Ha Ön nem ért a javaslatot, válassza ki a megfelelő leképezést a igazított szándék legördülő listából, majd jelölje ki a jobbra igazított célja a pipa jelre a. 

    ![Leképezés igazítása](./media/label-suggested-utterances/align-intent.png)

1. Miután kiválasztotta a pipa jelre, a rendszer eltávolítja az utterance (kifejezés) a listából. 

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
