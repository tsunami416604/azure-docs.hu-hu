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
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: b04a17c893f748670eb1c79f0ada879c0d5c401f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381715"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>A LUIS-alkalmazás fejlesztése a végpontok hosszúságú kimondott szöveg áttekintésével

A megfelelő előrejelzésekhez tartozó végpont hosszúságú kimondott szöveg áttekintését [aktív tanulási](luis-concept-review-endpoint-utterances.md)folyamatnak nevezzük. Az aktív tanulás rögzíti a végponti lekérdezéseket, és kiválasztja a felhasználó végpontjának hosszúságú kimondott szöveg, hogy az nem biztos benne. Ezeket a hosszúságú kimondott szöveg áttekintve kiválaszthatja a szándékot, és megjelölheti az entitásokat ezen olvasási-globális hosszúságú kimondott szöveg. Fogadja el ezeket a módosításokat a hosszúságú kimondott szöveg, majd végezze el a betanítást és a közzétételt. A LUIS ezután pontosabban azonosítja a hosszúságú kimondott szöveg.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Aktív tanulás engedélyezése

Az aktív tanulás engedélyezéséhez naplóznia kell a felhasználói lekérdezéseket. Ezt úgy hajthatja végre, hogy meghívja a [végponti lekérdezést](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) a `log=true` querystring paraméterrel és értékkel.

## <a name="correct-intent-predictions-to-align-utterances"></a>A hosszúságú kimondott szöveg igazításához megfelelő leképezési előrejelzések

Minden Kimondás egy javasolt szándéktal rendelkezik, amely a **igazított leképezés** oszlopban jelenik meg.

> [!div class="mx-imgBorder"]
> [![tekintse át az Endpoint hosszúságú kimondott szöveg, hogy LUIS nem biztos](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Ha elfogadja ezt a szándékot, jelölje be a pipa. Ha nem fogadja el a javaslatot, válassza ki a megfelelő szándékot az illesztett szándék legördülő listából, majd jelölje be az igazított szándék jobb oldalán lévő pipa négyzetet. Miután bejelölte a jelölőnégyzetet, a rendszer áthelyezi a kiválasztást a szándékba, és eltávolítja a **felülvizsgálati végpont hosszúságú kimondott szöveg** listájáról.

> [!TIP]
> Fontos, hogy a következtetések részletei lapon tekintse át és javítsa ki az entitások előrejelzéseit az összes példa hosszúságú kimondott szöveg a **felülvizsgálati végpont hosszúságú kimondott szöveg** listájában.

## <a name="delete-utterance"></a>Teljes törlés

Minden Kimondás törölhető a felülvizsgálati listáról. A törlés után nem jelenik meg többé a listában. Ez akkor is igaz, ha a felhasználó ugyanazt a Kimondás alatt áll, mint a végpont.

Ha nem biztos abban, hogy törölni kell a kilépést, vagy helyezze át a none (nincs) célra, vagy hozzon létre egy új szándékot, például `miscellaneous`, és helyezze át a teljes értéket az adott szándékba.

## <a name="disable-active-learning"></a>Aktív tanulás letiltása

Az aktív tanulás letiltásához ne naplózza a felhasználói lekérdezéseket. Ez úgy érhető el, hogy a [végponti lekérdezést](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) a `log=false` querystring paraméterrel és értékkel állítja be, vagy nem a querystring értéket használja, mert az alapértelmezett érték false (hamis).

## <a name="next-steps"></a>Következő lépések

Annak ellenőrzéséhez, hogy a teljesítmény hogyan javul a javasolt hosszúságú kimondott szöveg címkézése után, a felső panelen **tesztelheti** a teszt konzolt. Az alkalmazás tesztelési konzollal történő tesztelésével kapcsolatos útmutatásért lásd: [az alkalmazás betanítása és tesztelése](luis-interactive-test.md).
