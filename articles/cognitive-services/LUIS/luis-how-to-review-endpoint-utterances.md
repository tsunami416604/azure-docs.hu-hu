---
title: Felhasználói hosszúságú kimondott szöveg áttekintése – LUIS
titleSuffix: Azure Cognitive Services
description: Tekintse át az aktív tanulás által rögzített hosszúságú kimondott szöveg, hogy kiválassza a szándékot, és jelölje meg az entitásokat a Read-World hosszúságú kimondott szöveg; módosítások elfogadása, betanítás és közzététel.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 67953f552b5b2bcdd7d13253548227e57dab8548
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132677"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>A LUIS-alkalmazás fejlesztése a végpontok hosszúságú kimondott szöveg áttekintésével

A megfelelő előrejelzésekhez tartozó végpont hosszúságú kimondott szöveg áttekintését [aktív tanulási](luis-concept-review-endpoint-utterances.md)folyamatnak nevezzük. Az aktív tanulás rögzíti a végponti lekérdezéseket, és kiválasztja a felhasználó végpontjának hosszúságú kimondott szöveg, hogy az nem biztos benne. Ezeket a hosszúságú kimondott szöveg áttekintve kiválaszthatja a szándékot, és megjelölheti az entitásokat ezen olvasási-globális hosszúságú kimondott szöveg. Fogadja el ezeket a módosításokat a hosszúságú kimondott szöveg, majd végezze el a betanítást és a közzétételt. A LUIS ezután pontosabban azonosítja a hosszúságú kimondott szöveg.

Ha sok ember járul hozzá a LUIS-alkalmazáshoz, 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Aktív tanulás engedélyezése

Az aktív tanulás engedélyezéséhez naplóznia kell a felhasználói lekérdezéseket. Ezt úgy hajthatja végre, hogy meghívja a [végponti lekérdezést](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) a `log=true` querystring paraméterrel és értékkel.

## <a name="correct-intent-predictions-to-align-utterances"></a>A hosszúságú kimondott szöveg igazításához megfelelő leképezési előrejelzések

Minden kimondásakor rendelkezik egy javasolt szándékot, megjelenik a **szándékot igazítva** oszlop. 

> [!div class="mx-imgBorder"]
> [![tekintse át az Endpoint hosszúságú kimondott szöveg, hogy LUIS nem biztos](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Ha elfogadja ezt a szándékot, jelölje be a pipa. Ha Ön nem ért a javaslatot, válassza ki a megfelelő leképezést a igazított szándék legördülő listából, majd jelölje ki a jobbra igazított célja a pipa jelre a. Miután bejelölte a jelölőnégyzetet, a rendszer áthelyezi a kiválasztást a szándékba, és eltávolítja a **felülvizsgálati végpont hosszúságú kimondott szöveg** listájáról. 

> [!TIP]
> Fontos, hogy a következtetések részletei lapon tekintse át és javítsa ki az entitások előrejelzéseit az összes példa hosszúságú kimondott szöveg a **felülvizsgálati végpont hosszúságú kimondott szöveg** listájában.

## <a name="delete-utterance"></a>Törölje az utterance (kifejezés)

Minden kimondásakor a felülvizsgálati listából lehet törölni. A törölt, nem jelenik a lista újra. Ez igaz, akkor is, ha a felhasználó megadja az azonos utterance (kifejezés) a végpontról. 

Ha nem biztos abban, hogy törölni kell a kilépést, vagy helyezze át a none (nincs) célra, vagy hozzon létre egy új szándékot, például `miscellaneous`, és helyezze át a teljes értéket az adott szándékba. 

## <a name="disable-active-learning"></a>Aktív tanulás letiltása

Az aktív tanulás letiltásához ne naplózza a felhasználói lekérdezéseket. Ez úgy érhető el, hogy a [végponti lekérdezést](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) a `log=false` querystring paraméterrel és értékkel állítja be, vagy nem a querystring értéket használja, mert az alapértelmezett érték false (hamis).

## <a name="next-steps"></a>További lépések

Hogyan javítja a teljesítményt, akkor javasolt utterances címke után teszteléséhez elérheti a tesztelési konzol kiválasztásával **tesztelése** a az ablak tetején. Tesztelheti az alkalmazást a tesztelési konzollal kapcsolatos utasításokért lásd: [Train és tesztelje alkalmazását](luis-interactive-test.md).
