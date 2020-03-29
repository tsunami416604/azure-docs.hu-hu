---
title: Felhasználói kimondott szöveg áttekintése – LUIS
titleSuffix: Azure Cognitive Services
description: Tekintse át az aktív tanulás által rögzített kimondott szövegeket a szándék kiválasztásához és az entitások megjelöléséhez az olvasási világ kimondott szövegéhez; módosításokat, betanítást és közzétételt.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219851"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>A LUIS-alkalmazás javítása a végpontkimondott szöveg áttekintésével

A végpontkimondott szöveg ek helyes előrejelzések felülvizsgálatának folyamatát [aktív tanulásnak](luis-concept-review-endpoint-utterances.md)nevezzük. Az aktív tanulás rögzíti a végpontlekérdezéseket, és kiválasztja a felhasználó végponti utterances, hogy nem biztos benne. Tekintse át ezeket a kimondott szövegeket, hogy válassza ki a szándékot, és jelölje meg az entitásokat ezek az olvasási világ utterances. Fogadja el ezeket a módosításokat a példa utterances majd a vonat és a közzététel. A LUIS ezután pontosabban azonosítja a kimondott szövegeket.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Aktív tanulás engedélyezése

Az aktív tanulás engedélyezéséhez naplóznia kell a felhasználói lekérdezéseket. Ez úgy érhető el, hogy meghívja a [végpontlekérdezést](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) a `log=true` querystring paraméterrel és értékkel.

A LUIS-portál segítségével hozza létre a megfelelő végpontlekérdezést.

1. Az [előzetes LUIS portálon](https://preview.luis.ai/)válassza ki az alkalmazást az alkalmazások listájából.
1. Nyissa meg a **Kezelés** szakaszt, majd válassza az **Azure-erőforrások lehetőséget.**
1. A hozzárendelt előrejelzési erőforrásesetén válassza a **Lekérdezési paraméterek módosítása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A LUIS-portál használatával naplók mentése, amely szükséges az aktív tanuláshoz.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. A **Naplók mentése,** majd a Mentés gombra a **Kész**gombra kattintva válthat be.

    > [!div class="mx-imgBorder"]
    > ![A LUIS-portál használatával naplók mentése, amely szükséges az aktív tanuláshoz.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Ez a művelet módosítja `log=true` a példa URL-címét a querystring paraméter hozzáadásával. Másolja és használja a módosított példa lekérdezési URL-cím, amikor előrejelzési lekérdezéseket a futásidejű végpontra.

## <a name="correct-intent-predictions-to-align-utterances"></a>A szándékkiképezési előrejelzések javítása az utterances igazításához

Minden utterance (kifejezés) egy javasolt szándék jelenik meg az **Igazított leképezés** oszlopban.

> [!div class="mx-imgBorder"]
> [![Tekintse át azokat a végpontkimondott szövegeket, amelyekben a LUIS nem biztos](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Ha egyetért ezzel a szándékkal, jelölje be a pipát. Ha nem ért egyet a javaslattal, válassza ki a megfelelő szándékot az igazított szándék legördülő listából, majd jelölje be az igazított leképezés jobb oldalán lévő pipát. Miután bejelöli a pipát, az utterance (kifejezés) átkerül a szándék, és törlődik a **felülvizsgálati végpont utterances** listából.

> [!TIP]
> Fontos, hogy a szándék részletei lapra lépjen, és tekintse át és javítsa ki az entitás-előrejelzéseket az összes példa utterances a **felülvizsgálati végpont utterances** listából.

## <a name="delete-utterance"></a>Utterance (kifejezés) törlése

Minden utterance (kifejezés) törölhető a felülvizsgálati listából. A törlés után nem jelenik meg újra a listában. Ez akkor is igaz, ha a felhasználó adja meg ugyanazt az utterance (kifejezés) a végpontról.

Ha nem biztos abban, hogy törölnie kell-e az utterance (kifejezés) parancsot, vagy helyezze át a Nincs szándékba, vagy hozzon létre egy új szándékot, például `miscellaneous` és helyezze át az utterance (utterance( utterance (kifejezés) a szándékot.

## <a name="disable-active-learning"></a>Aktív tanulás letiltása

Az aktív tanulás letiltásához ne naplózza a felhasználói lekérdezéseket. Ez úgy érhető el, hogy a `log=false` [végpontlekérdezést](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) a querystring paraméterrel és értékkel állítja be, vagy nem használja a querystring értéket, mert az alapértelmezett érték hamis.

## <a name="next-steps"></a>További lépések

Ha tesztelni szeretné, hogyan javul a teljesítmény a javasolt kimondott szövegek címkézése után, a felső panelen a **Tesztelés** lehetőség kiválasztásával érheti el a tesztkonzolt. Az alkalmazás tesztelésével kapcsolatos tudnivalókért olvassa el [az Alkalmazás betanítása és tesztelése](luis-interactive-test.md)című témakört.
