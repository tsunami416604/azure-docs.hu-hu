---
title: Tudásbázis – QnA Maker tesztelése
titlesuffix: Azure Cognitive Services
description: A QnA Maker Tudásbázis tesztelése egy, a visszaadott válaszokat pontosságának javítása iteratív folyamat fontos részét képezi. A Tudásbázis is lehetővé teszi a Szerkesztés továbbfejlesztett Csevegés felületen tesztelheti.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 12/17/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 2ad8d4fa04801996364755d1e0270a6509912679
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581946"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>A QnA Maker interaktív módon a Tudásbázis tesztelése

A QnA Maker Tudásbázis tesztelése egy, a visszaadott válaszokat pontosságának javítása iteratív folyamat fontos részét képezi. A Tudásbázis is lehetővé teszi a Szerkesztés továbbfejlesztett Csevegés felületen tesztelheti.

## <a name="test-answer-matching"></a>Válasz megfelelő tesztelése

1.  A Tudásbázis eléréséhez válassza a neve a a **saját tudásbázisok** lapot.
2.  A teszt dia kibővített panel eléréséhez, válassza ki a **teszt** az alkalmazás tetején található.

    ![Teszt panel eléréséhez](../media/qnamaker-how-to-test-kb/access-test.png)

3.  Adjon meg egy lekérdezést a szövegmezőbe, és nyomja le az Enter.

4.  A Tudásbázis a legjobban megfelelő választ ad vissza, a válaszban.

## <a name="clear-test-panel"></a>Egyértelmű teszt panel

Törölje az összes megadott teszt lekérdezés és a test-konzolról eredményeit, válassza ki a **újrakezdés** a teszt panel bal felső sarkában.

## <a name="close-test-panel"></a>Zárja be a teszt panel

A teszt panel bezárásához kattintson a **teszt** újra gombra. Miközben a teszt panel meg nyitva, már nem szerkesztheti a Tudásbázis-tartalmat.

## <a name="inspect-score"></a>Vizsgálja meg a pontszám

Nézze meg a teszt eredménye a vizsgálat panelen részleteit.

1.  A panelen nyissa meg a teszt dia kibővített, válassza ki a **vizsgálat** a válasszal kapcsolatos további részletekért.

    ![Vizsgálja meg a válaszok](../media/qnamaker-how-to-test-kb/inspect.png)

2.  A Hálózatvizsgáló panelen jelenik meg. A panel felső pontozási szándékot, valamint a azonosított entitások tartalmazza. A panelen a kiválasztott utterance (kifejezés) eredménye látható.

## <a name="correct-the-top-scoring-answer"></a>Javítsa ki az első válasz a pontozás

Ha pontozási válasz felső helytelen, jelölje be a megfelelő választ a listában, majd válassza a **mentéséhez és Betanítunk**.

![Javítsa ki az első válasz a pontozás](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Egy adott válasz egy kérdést alternatív formáját is hozzáadhat. Írja be a másodlagos válaszokat a szövegmezőbe, majd kattintson a adja meg, adja hozzá őket. Válassza ki **mentéséhez és a vonat** a frissítések tárolásához.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Adjon hozzá egy új válasz

Új válasz is hozzáadhat, ha minden meglévő választ egyezést mutató helytelen, vagy a válasz nem szerepel a Tudásbázis (nem jó egyezés található a KB-ban). A mezőben adja meg az új aktuális kérdésre adott válasz, és nyomja le az enter hozzáadáshoz. 

Válassza ki **mentéséhez és a vonat** segítségével őrzi meg a választ. Kérdés-válasz párokat már hozzá van adva a tudásbázist.

![Új kérdést és választ pár hozzáadása](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> A Tudásbázis minden módosítását csak mentette amikor lenyomja a **mentéséhez és a vonat** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis közzététele](./publish-knowledge-base.md)
