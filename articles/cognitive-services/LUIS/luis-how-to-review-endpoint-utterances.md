---
title: Felhasználói hosszúságú kimondott szöveg áttekintése – LUIS
titleSuffix: Azure Cognitive Services
description: Tekintse át az aktív tanulás által rögzített hosszúságú kimondott szöveg, hogy kiválassza a szándékot, és jelölje meg az entitásokat a Read-World hosszúságú kimondott szöveg; elfogadhatja a módosításokat, a betanítást és a közzétételt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219851"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>A LUIS-alkalmazás fejlesztése a végpontok hosszúságú kimondott szöveg áttekintésével

A megfelelő előrejelzésekhez tartozó végpont hosszúságú kimondott szöveg áttekintését [aktív tanulási](luis-concept-review-endpoint-utterances.md)folyamatnak nevezzük. Az aktív tanulás rögzíti a végponti lekérdezéseket, és kiválasztja a felhasználó végpontjának hosszúságú kimondott szöveg, hogy az nem biztos benne. Ezeket a hosszúságú kimondott szöveg áttekintve kiválaszthatja a szándékot, és megjelölheti az entitásokat ezen olvasási-globális hosszúságú kimondott szöveg. Fogadja el ezeket a módosításokat a hosszúságú kimondott szöveg, majd végezze el a betanítást és a közzétételt. A LUIS ezután pontosabban azonosítja a hosszúságú kimondott szöveg.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Aktív tanulás engedélyezése

Az aktív tanulás engedélyezéséhez naplóznia kell a felhasználói lekérdezéseket. Ezt úgy érheti el, ha meghívja a [végponti lekérdezést](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) a `log=true` querystring paraméterrel és értékkel.

A LUIS portál használatával hozza létre a megfelelő végpont-lekérdezést.

1. A [Luis-portálon](https://preview.luis.ai/)válassza ki az alkalmazást az alkalmazások listájából.
1. Lépjen a **kezelés** szakaszra, majd válassza az **Azure-erőforrások**lehetőséget.
1. A hozzárendelt előrejelzési erőforráshoz válassza a **lekérdezési paraméterek módosítása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A LUIS Portal használatával mentheti a naplókat, amelyek az aktív tanuláshoz szükségesek.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. A **mentési naplók** váltásához válassza a **kész**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A LUIS Portal használatával mentheti a naplókat, amelyek az aktív tanuláshoz szükségesek.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Ez a művelet megváltoztatja a példa URL-címét `log=true` a querystring paraméter hozzáadásával. Másolja ki és használja a megváltozott példa lekérdezési URL-címét, amikor előrejelzési lekérdezéseket készít a futásidejű végpontra.

## <a name="correct-intent-predictions-to-align-utterances"></a>A hosszúságú kimondott szöveg igazításához megfelelő leképezési előrejelzések

Minden Kimondás egy javasolt szándéktal rendelkezik, amely a **igazított leképezés** oszlopban jelenik meg.

> [!div class="mx-imgBorder"]
> [![Ellenőrizze, hogy a LUIS nem biztos-e az Endpoint hosszúságú kimondott szöveg](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Ha elfogadja ezt a szándékot, jelölje be a pipa. Ha nem fogadja el a javaslatot, válassza ki a megfelelő szándékot az illesztett szándék legördülő listából, majd jelölje be az igazított szándék jobb oldalán lévő pipa négyzetet. Miután bejelölte a jelölőnégyzetet, a rendszer áthelyezi a kiválasztást a szándékba, és eltávolítja a **felülvizsgálati végpont hosszúságú kimondott szöveg** listájáról.

> [!TIP]
> Fontos, hogy a következtetések részletei lapon tekintse át és javítsa ki az entitások előrejelzéseit az összes példa hosszúságú kimondott szöveg a **felülvizsgálati végpont hosszúságú kimondott szöveg** listájában.

## <a name="delete-utterance"></a>Teljes törlés

Minden Kimondás törölhető a felülvizsgálati listáról. A törlés után nem jelenik meg többé a listában. Ez akkor is igaz, ha a felhasználó ugyanazt a Kimondás alatt áll, mint a végpont.

Ha nem biztos abban, hogy törölni kell a kilépést, vagy helyezze át a none (nincs) értékre, vagy hozzon létre egy új `miscellaneous` szándékot, például az értéket, és helyezze át a teljes értéket az adott szándékba.

## <a name="disable-active-learning"></a>Aktív tanulás letiltása

Az aktív tanulás letiltásához ne naplózza a felhasználói lekérdezéseket. Ez úgy érhető el, hogy a [végponti lekérdezést](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) a `log=false` querystring paraméterrel és értékkel állítja be, vagy nem a querystring értéket használja, mert az alapértelmezett érték hamis.

## <a name="next-steps"></a>További lépések

Annak ellenőrzéséhez, hogy a teljesítmény hogyan javul a javasolt hosszúságú kimondott szöveg címkézése után, a felső panelen **tesztelheti** a teszt konzolt. Az alkalmazás tesztelési konzollal történő tesztelésével kapcsolatos útmutatásért lásd: [az alkalmazás betanítása és tesztelése](luis-interactive-test.md).
