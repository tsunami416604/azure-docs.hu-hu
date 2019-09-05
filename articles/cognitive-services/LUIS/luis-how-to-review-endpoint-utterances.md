---
title: Felhasználói hosszúságú kimondott szöveg áttekintése – LUIS
titleSuffix: Azure Cognitive Services
description: Az aktív tanulás rögzíti a végponti lekérdezéseket, és kiválasztja a felhasználó végpontjának hosszúságú kimondott szöveg, hogy az nem biztos benne. Ezeket a hosszúságú kimondott szöveg áttekintve kiválaszthatja a szándékot, és megjelölheti az entitásokat ezen olvasási-globális hosszúságú kimondott szöveg. Fogadja el ezeket a módosításokat a hosszúságú kimondott szöveg, majd végezze el a betanítást és a közzétételt. A LUIS ezután pontosabban azonosítja a hosszúságú kimondott szöveg.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: c617e4aa62ce2ff468545bef0b2ebe2c4d0e4f03
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382356"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Az Endpoint hosszúságú kimondott szöveg áttekintése a LUIS portálon az aktív tanuláshoz

Az [aktív tanulás](luis-concept-review-endpoint-utterances.md) rögzíti a végponti lekérdezéseket, és kiválasztja a felhasználó végpontjának hosszúságú kimondott szöveg, hogy az nem biztos benne. Ezeket a hosszúságú kimondott szöveg áttekintve kiválaszthatja a szándékot, és megjelölheti az entitásokat ezen olvasási-globális hosszúságú kimondott szöveg. Fogadja el ezeket a módosításokat a hosszúságú kimondott szöveg, majd végezze el a betanítást és a közzétételt. A LUIS ezután pontosabban azonosítja a hosszúságú kimondott szöveg.


## <a name="enable-active-learning"></a>Aktív tanulás engedélyezése

Az aktív tanulás engedélyezéséhez jelentkezzen be a felhasználói lekérdezésekre. Ezt úgy érheti el, ha a [végpont-lekérdezést](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) a `log=true` querystring paraméterrel és értékkel állítja be.

## <a name="disable-active-learning"></a>Aktív tanulás letiltása

Az aktív tanulás letiltásához ne naplózza a felhasználói lekérdezéseket. Ezt úgy érheti el, ha a [végpont-lekérdezést](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) a `log=false` querystring paraméterrel és értékkel állítja be.

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
