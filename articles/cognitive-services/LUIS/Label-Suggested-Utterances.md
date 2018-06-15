---
title: Javasolt utterances LUIS rendelkező címke |} Microsoft Docs
description: Nyelvi ismertetése (LUIS) segítségével javasolt utterances címke és a program aktív gépi tanulás érdekében.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2017
ms.author: v-geberr
ms.openlocfilehash: 91a2fe738743d359677392bfb79aac885702b440
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35350082"
---
# <a name="review-endpoint-utterances"></a>Tekintse át a végpont utterances

LUIS áttörést jelentő jellemzője a [koncepció](luis-concept-review-endpoint-utterances.md) aktív tanulás. Miután a LUIS végpont lekérdezések, LUIS használ active tanulási az eredmények minőségének javítása érdekében. Az aktív tanulási folyamatban LUIS megvizsgálja-e a végpont-utterances, és kiválasztja, hogy ez nem ismeri utterances. Ha ezek utterances, betanítását, és közzététele, majd LUIS utterances pontosabban azonosítja. 

## <a name="filter-utterances"></a>Szűrő utterances
1. Nyissa meg az alkalmazás (például TravelAgent) választva módosíthatja a nevet **saját alkalmazások** lapon, majd válasszon **Build** a felső sávon.

2. Az a **alkalmazás teljesítményének javítása**, jelölje be **tekintse át a végpont utterances**.

    ![Tekintse át a utterances](./media/label-suggested-utterances/review.png)

3. A a **tekintse át a végpont utterances** lapon jelölje be a a **szűrőlista leképezés vagy entitás** szövegmezőben. A legördülő lista tartalmazza az összes leképezések **LEKÉPEZÉSEK** és az összes entitás **entitások**.

    ![Utterances szűrő](./media/label-suggested-utterances/filter.png)

4. Válassza ki (leképezések vagy entitások) kategóriát a legördülő listában, és tekintse át a utterances.

    ![Leképezési utterances](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Címke entitások
LUIS entitás jogkivonatok (szavak) kiemeli a kék entitásnévnek lecseréli. Ha egy utterance entitások rendelkezik címkézetlen, lássa el a utterance. 

1. Jelölje be a utterance a keresett szavakat. 

2. Válassza ki a listából egy entitás.

    ![Címke entitás](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Egyetlen utterance igazítása

Minden egyes utterance rendelkezik a javasolt leképezés jelenik meg a **leképezés igazítva** oszlop. 

1. Ha elfogadja, hogy a javaslat, jelölje be a pipa jelre.

    ![Tartsa igazított leképezés](./media/label-suggested-utterances/align-intent-check.png)

2. Ha Ön nem ért a javaslat, válassza ki a megfelelő leképezést a igazított leképezési legördülő listából, majd jelölje be a pipa jelre a igazított leképezés jobb a. 

    ![Kísérlet történt igazítása](./media/label-suggested-utterances/align-intent.png)

3. Miután kiválasztotta a jelölőnégyzet be van jelölve, a utterance távolítva a listáról. 

## <a name="align-several-utterances"></a>Több utterances igazítása

Több utterances igazítása, jelölje be a jelölőnégyzetet a utterances balra, majd jelölje be a a **kijelölt Hozzáadás** gombra. 

![Több igazítása](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Ellenőrizze a igazított leképezés
A utterance megfelelő biztonsági mentés lett igazítva megnyitásával ellenőrizheti a **leképezések** lapon válassza ki a leképezési nevét, és a utterances áttekintése. A utterance **tekintse át a végpont utterances** szerepel a listán.

## <a name="delete-utterance"></a>Utterance törlése
Minden egyes utterance törölheti a felülvizsgálati listából. A törölt már nem jelenik a listában újra. Ez igaz, akkor is, ha a felhasználó megadja a azonos utterance a végpontról. 

Ha nem ismeri, ha a utterance törölni kell, vagy helyezze át a nincs leképezési, vagy hozzon létre egy új leképezés, például az "egyéb" és a utterance áthelyezése, hogy a leképezés. 

## <a name="delete-several-utterances"></a>Több utterances törlése
Több utterances törléséhez jelöljön be minden elemet, és jelölje be a jobb oldalán a is van a **kijelölt Hozzáadás** gombra.

![Több törlése](./media/label-suggested-utterances/delete-several.png)

## <a name="next-steps"></a>További lépések

Hogyan javítja a teljesítményt, akkor javasolt utterances címke után teszteléséhez elérheti a tesztelési konzol kiválasztásával **tesztelése** a az ablak tetején. A alkalmazás tesztelése a test-konzollal kapcsolatos útmutatásért lásd: [tanítási és az alkalmazás tesztelése a](Train-Test.md).
