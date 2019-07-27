---
title: Tudásbázis – QnA Maker tesztelése
titleSuffix: Azure Cognitive Services
description: A QnA Maker Tudásbázis tesztelése egy, a visszaadott válaszokat pontosságának javítása iteratív folyamat fontos részét képezi. A Tudásbázis is lehetővé teszi a Szerkesztés továbbfejlesztett Csevegés felületen tesztelheti.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6a512098d5dfda47b7755e24b286aabf83aa7e69
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563070"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>A QnA Maker interaktív módon a Tudásbázis tesztelése

A QnA Maker Tudásbázis tesztelése egy, a visszaadott válaszokat pontosságának javítása iteratív folyamat fontos részét képezi. A Tudásbázis is lehetővé teszi a Szerkesztés továbbfejlesztett Csevegés felületen tesztelheti.

## <a name="test-answer-matching"></a>Válasz megfelelő tesztelése

1. A Tudásbázisban a saját **Tudásbázis** lapján megjelenő név kiválasztásával férhet hozzá.
1. A teszt kivetítése panel eléréséhez válassza a **tesztelés** lehetőséget az alkalmazás felső paneljén.
1. Adjon meg egy lekérdezést a szövegmezőbe, és nyomja le az Enter.
1. A Tudásbázis a legjobban megfelelő választ ad vissza, a válaszban.

## <a name="clear-test-panel"></a>Egyértelmű teszt panel

Ha törölni szeretné az összes megadott tesztelési lekérdezést és azok eredményeit a tesztelési konzolról, válassza az **Indítás** lehetőséget a teszt panel bal felső sarkában.

## <a name="close-test-panel"></a>Zárja be a teszt panel

A teszt panel bezárásához kattintson újra a **teszt** gombra. Miközben a teszt panel meg nyitva, már nem szerkesztheti a Tudásbázis-tartalmat.

## <a name="inspect-score"></a>Vizsgálja meg a pontszám

A teszt eredményének részleteit a vizsgálat panelen tekintheti meg.

1.  Nyissa meg a teszt kivetítése panelt, **és válassza a** vizsgálat lehetőséget a válasz további részleteinek megtekintéséhez.

    ![Vizsgálja meg a válaszok](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Megjelenik a vizsgálat panel. A panel felső pontozási szándékot, valamint a azonosított entitások tartalmazza. A panelen a kiválasztott utterance (kifejezés) eredménye látható.

## <a name="correct-the-top-scoring-answer"></a>Javítsa ki az első válasz a pontozás

Ha pontozási válasz felső helytelen, jelölje be a megfelelő választ a listában, majd válassza a **mentéséhez és Betanítunk**.

![Javítsa ki az első válasz a pontozás](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Egy adott válasz egy kérdést alternatív formáját is hozzáadhat. Írja be a másodlagos válaszokat a szövegmezőbe, majd kattintson a adja meg, adja hozzá őket. Válassza ki **mentéséhez és a vonat** a frissítések tárolásához.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Adjon hozzá egy új válasz

Új válasz is hozzáadhat, ha minden meglévő választ egyezést mutató helytelen, vagy a válasz nem szerepel a Tudásbázis (nem jó egyezés található a KB-ban). 

A válaszok lista alján található szövegmezővel adjon meg egy új választ, és az ENTER billentyű lenyomásával adja hozzá. 

Válassza ki **mentéséhez és a vonat** segítségével őrzi meg a választ. Kérdés-válasz párokat már hozzá van adva a tudásbázist. 

> [!NOTE]
> A Tudásbázis minden módosítását csak mentette amikor lenyomja a **mentéséhez és a vonat** gombra.

## <a name="test-the-published-knowledge-base"></a>A közzétett Tudásbázis tesztelése

A Tudásbázis közzétett verzióját tesztelheti a teszt ablaktáblán. Miután közzétette a KB-ot, válassza ki a **közzétett tudásbázist** , és küldjön egy lekérdezést a közzétett Tudásbázis eredményeinek lekéréséhez.

![Tesztelés egy közzétett TUDÁSBÁZISban](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis közzététele](./publish-knowledge-base.md)
