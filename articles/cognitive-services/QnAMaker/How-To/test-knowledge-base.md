---
title: A Tudásbázis - kérdések és válaszok készítő - kognitív Azure-szolgáltatások tesztelése |} Microsoft Docs
description: Tesztelje a Tudásbázis közzététel előtt.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: cffb63666edab25e1b3b0739d0e0f2f828600f3a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348682"
---
# <a name="test-your-knowledge-base"></a>A Tudásbázis tesztelése

A kérdések és válaszok készítő Tudásbázis vizsgálat nem ad vissza a válaszok pontosságának javítása érdekében iteratív folyamat fontos részét képezik. A Tudásbázis is lehetővé teszi a Szerkesztés továbbfejlesztett Csevegés felületen tesztelheti.

## <a name="test-answer-matching"></a>Válasz megfelelő tesztelése

1.  A Tudásbázis hozzáférési választva módosíthatja a nevet a **a Tudásbázis körrel** lap.
2.  A teszt menüpontban húzással állíthatja be a kibővített panel megnyitásához válassza ki a **teszt** az alkalmazás felső panel.

    ![Hozzáférés tesztelése](../media/qnamaker-how-to-test-kb/access-test.png)

3.  Adjon meg egy lekérdezést a szövegmezőbe, és válassza ki a billentyűt.

4.  A Tudásbázis a legjobban illeszkedő választ ad vissza, a válaszban.

## <a name="clear-test-panel"></a>Egyszerű vizsgálati panel

Törölje a megadott teszt lekérdezések és a vizsgálati konzolról eredményeit, válassza ki a **kezdje újra a folyamatot** a teszt panel bal felső sarkában.

## <a name="close-test-panel"></a>Bezárás teszt panel

Zárja be a teszt panelen, jelölje be a **teszt** újra gombra. A teszt panel meg nyitva, amíg a Tudásbázis tartalma nem szerkeszthetők.

## <a name="inspect-score"></a>Vizsgálja meg a pontszám

A vizsgálat panelen a vizsgálat eredményének részletei vizsgálhatja meg.

1.  A panel nyissa meg a teszt menüpontban húzással állíthatja be a kibővített, válassza ki a **vizsgálat** kapcsolatban további részleteket a választ.

    ![Vizsgálja meg a válaszok](../media/qnamaker-how-to-test-kb/inspect.png)

2.  A hálózatfelügyeleti panelen jelenik meg. A panel leképezés, valamint a azonosított entitások pontozási felső tartalmazza. A panel a kijelölt utterance eredményének megjelenítése.

## <a name="correct-the-top-scoring-answer"></a>Javítsa ki a pontozási válasz felső

Ha pontozási válasz felső helytelen, jelölje be a helyes választ csoportot a listából, és válassza ki **mentéséhez és a vonat**.

![Hozzáférés tesztelése](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

A kérdés alternatív űrlapok adhat hozzá egy adott válasz. A helyettesítő ad választ, a szövegmezőbe, majd kattintson a típus felveheti azokat adja meg. Válassza ki **mentéséhez és a vonat** a frissítések tárolásához.

![Hozzáférés tesztelése](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Egy másik választ a hozzáadása

Egy másik választ adhat hozzá, minden egyező volt meglévő választ, ha a válasz nem szerepel a Tudásbázis (nincs helyes egyezés a KB-ban). A mezőben adja meg az új aktuális kérdésre adott válasz, és nyomja le az enter veheti fel. 

Válassza ki **mentéséhez és a vonat** megőrizni ezt a választ. Egy új kérdés-válasz pár már fel lett véve a Tudásbázis.

![Hozzáférés tesztelése](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> Minden módosítását a Tudásbázis csak get menteni, amikor a **mentéséhez és a vonat** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis közzététele](./publish-knowledge-base.md)
