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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4a77ac26076fc1b1e4e94ee24dafb28a0e88c858
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669379"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Az Endpoint hosszúságú kimondott szöveg áttekintése a LUIS portálon az aktív tanuláshoz

Az [aktív tanulás](luis-concept-review-endpoint-utterances.md) rögzíti a végponti lekérdezéseket, és kiválasztja a felhasználó végpontjának hosszúságú kimondott szöveg, hogy az nem biztos benne. Ezeket a hosszúságú kimondott szöveg áttekintve kiválaszthatja a szándékot, és megjelölheti az entitásokat ezen olvasási-globális hosszúságú kimondott szöveg. Fogadja el ezeket a módosításokat a hosszúságú kimondott szöveg, majd végezze el a betanítást és a közzétételt. A LUIS ezután pontosabban azonosítja a hosszúságú kimondott szöveg.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="enable-active-learning"></a>Aktív tanulás engedélyezése

Az aktív tanulás engedélyezéséhez jelentkezzen be a felhasználói lekérdezésekre. Ezt úgy végezheti el, hogy a [végponti lekérdezést](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) a `log=true` querystring paraméterrel és értékkel állítja be.

## <a name="disable-active-learning"></a>Aktív tanulás letiltása

Az aktív tanulás letiltásához ne naplózza a felhasználói lekérdezéseket. Ezt úgy végezheti el, hogy a [végponti lekérdezést](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) a `log=false` querystring paraméterrel és értékkel állítja be.

## <a name="filter-utterances"></a>Hosszúságú kimondott szöveg szűrése

1. Nyissa meg az alkalmazást (például TravelAgent) a saját **alkalmazások** lapján lévő név kiválasztásával, majd válassza a **Létrehozás** lehetőséget a felső sávon.

1. Az **alkalmazások teljesítményének javítása**területen válassza az **Endpoint hosszúságú kimondott szöveg áttekintése**elemet.

1. A **végponti hosszúságú kimondott szöveg áttekintése** lapon válassza a **szűrés lista alapján a szándék vagy az entitás** szövegmezőt. Ez a legördülő lista az **összes leképezést tartalmazza, és** az **entitások**területen található összes entitást.

    ![Hosszúságú kimondott szöveg szűrő](./media/label-suggested-utterances/filter.png)

1. Válasszon ki egy kategóriát (szándékokat vagy entitásokat) a legördülő listából, és tekintse át a hosszúságú kimondott szöveg.

    ![Szándék hosszúságú kimondott szöveg](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Címke entitásai
LUIS helyettesíti az entitás-jogkivonatokat (szavakat) a kék színnel jelölt entitások nevével. Ha egy Kimondás címke nélküli entitásokat tartalmaz, akkor a címkét a rendszer megjelöli. 

1. Válassza ki a szót (ka) t a teljes szövegben. 

1. Válasszon ki egy entitást a listából.

    ![Címke entitása](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Egyetlen Kimondás igazítása

Minden Kimondás egy javasolt szándéktal rendelkezik, amely a **igazított leképezés** oszlopban jelenik meg. 

1. Ha elfogadja ezt a javaslatot, jelölje be a jelölőnégyzetet.

    ![Igazított leképezés megőrzése](./media/label-suggested-utterances/align-intent-check.png)

1. Ha nem fogadja el a javaslatot, válassza ki a megfelelő szándékot az illesztett szándék legördülő listából, majd jelölje be az igazított szándék jobb oldalán lévő pipa négyzetet. 

    ![Szándék igazítása](./media/label-suggested-utterances/align-intent.png)

1. Miután kiválasztotta a pipát, a rendszer eltávolítja a kiválasztást a listából. 

## <a name="align-several-utterances"></a>Több hosszúságú kimondott szöveg igazítása

Ha több hosszúságú kimondott szöveg szeretne igazítani, jelölje be a hosszúságú kimondott szöveg bal oldalán található jelölőnégyzetet, majd kattintson a **kijelöltek hozzáadása** gombra. 

![Több igazítás](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Igazított leképezés ellenőrzése

Ellenőrizheti, hogy a kiírás a megfelelő szándékkal van-e igazítva, ehhez nyissa **meg a szándékok lapot,** válassza ki a leképezés nevét, és tekintse át a hosszúságú kimondott szöveg. A **felülvizsgálati végpont hosszúságú kimondott szöveg** való Kimondás a listában található.

## <a name="delete-utterance"></a>Teljes törlés

Minden Kimondás törölhető a felülvizsgálati listáról. A törlés után nem jelenik meg többé a listában. Ez akkor is igaz, ha a felhasználó ugyanazt a Kimondás alatt áll, mint a végpont. 

Ha nem biztos abban, hogy törölni kell a kilépést, vagy helyezze át a none (nincs) célra, vagy hozzon létre egy új szándékot, például "vegyes" értéket, és helyezze át a teljes értéket az adott szándékra. 

## <a name="delete-several-utterances"></a>Több hosszúságú kimondott szöveg törlése

Több hosszúságú kimondott szöveg törléséhez jelölje ki az egyes elemeket, és válassza ki a kívánt elemet a Kuka raktárhelyen a **kijelölt hozzáadása** gomb jobb oldalán.

![Több törlése](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>További lépések

Annak ellenőrzéséhez, hogy a teljesítmény hogyan javul a javasolt hosszúságú kimondott szöveg címkézése után, a felső panelen **tesztelheti** a teszt konzolt. Az alkalmazás tesztelési konzollal történő tesztelésével kapcsolatos útmutatásért lásd: [az alkalmazás betanítása és tesztelése](luis-interactive-test.md).
