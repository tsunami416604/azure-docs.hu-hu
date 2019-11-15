---
title: Tudásbázis – QnA Maker tesztelése
titleSuffix: Azure Cognitive Services
description: A QnA Maker Tudásbázis tesztelése egy, a visszaadott válaszokat pontosságának javítása iteratív folyamat fontos részét képezi. A Tudásbázis is lehetővé teszi a Szerkesztés továbbfejlesztett Csevegés felületen tesztelheti.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c139d3a740067e3cecaff90d3171d7b0cb3d52c7
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091757"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>A Tudásbázis tesztelése QnA Maker

A QnA Maker Tudásbázis tesztelése egy, a visszaadott válaszokat pontosságának javítása iteratív folyamat fontos részét képezi. A Tudásbázis is lehetővé teszi a Szerkesztés továbbfejlesztett Csevegés felületen tesztelheti.

## <a name="interactively-test-in-qna-maker-portal"></a>Interaktív tesztelés QnA Maker portálon

1. A Tudásbázisban a saját **Tudásbázis** lapján megjelenő név kiválasztásával férhet hozzá.
1. A teszt kivetítése panel eléréséhez válassza a **tesztelés** lehetőséget az alkalmazás felső paneljén.
1. Adjon meg egy lekérdezést a szövegmezőbe, és nyomja le az Enter.
1. A Tudásbázis a legjobban megfelelő választ ad vissza, a válaszban.

### <a name="clear-test-panel"></a>Egyértelmű teszt panel

Ha törölni szeretné az összes megadott tesztelési lekérdezést és azok eredményeit a tesztelési konzolról, válassza az **Indítás** lehetőséget a teszt panel bal felső sarkában.

### <a name="close-test-panel"></a>Zárja be a teszt panel

A teszt panel bezárásához kattintson újra a **teszt** gombra. Miközben a teszt panel meg nyitva, már nem szerkesztheti a Tudásbázis-tartalmat.

### <a name="inspect-score"></a>Vizsgálja meg a pontszám

A teszt eredményének részleteit a vizsgálat panelen tekintheti meg.

1.  Nyissa meg a teszt kivetítése panelt, **és válassza a vizsgálat lehetőséget** a válasz további részleteinek megtekintéséhez.

    ![Vizsgálja meg a válaszok](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Megjelenik a vizsgálat panel. A panel felső pontozási szándékot, valamint a azonosított entitások tartalmazza. A panelen a kiválasztott utterance (kifejezés) eredménye látható.

### <a name="correct-the-top-scoring-answer"></a>Javítsa ki az első válasz a pontozás

Ha pontozási válasz felső helytelen, jelölje be a megfelelő választ a listában, majd válassza a **mentéséhez és Betanítunk**.

![Javítsa ki az első válasz a pontozás](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Egy adott válasz egy kérdést alternatív formáját is hozzáadhat. Írja be a másodlagos válaszokat a szövegmezőbe, majd kattintson a adja meg, adja hozzá őket. Válassza ki **mentéséhez és a vonat** a frissítések tárolásához.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Adjon hozzá egy új válasz

Új válasz is hozzáadhat, ha minden meglévő választ egyezést mutató helytelen, vagy a válasz nem szerepel a Tudásbázis (nem jó egyezés található a KB-ban). 

A válaszok lista alján található szövegmezővel adjon meg egy új választ, és az ENTER billentyű lenyomásával adja hozzá. 

Válassza ki **mentéséhez és a vonat** segítségével őrzi meg a választ. Kérdés-válasz párokat már hozzá van adva a tudásbázist. 

> [!NOTE]
> A Tudásbázis minden módosítását csak mentette amikor lenyomja a **mentéséhez és a vonat** gombra.

### <a name="test-the-published-knowledge-base"></a>A közzétett Tudásbázis tesztelése

A Tudásbázis közzétett verzióját tesztelheti a teszt ablaktáblán. Miután közzétette a KB-ot, válassza ki a **közzétett tudásbázist** , és küldjön egy lekérdezést a közzétett Tudásbázis eredményeinek lekéréséhez.

![Tesztelés egy közzétett TUDÁSBÁZISban](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Batch-teszt eszközzel

A Batch Testing eszközt a következő esetekben használhatja:

* a legfontosabb válasz és a pontszám meghatározása a kérdésekre
* a várt válasz ellenőrzése a kérdéses csoportra vonatkozóan

A Batch Testing eszközt a Batch-tesztelési eszközzel együtt kell megadnia. Ez az eszköz letölthető vagy [ C# forráskódként](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)használható [tömörített végrehajtható fájlként](https://aka.ms/qnamakerbatchtestingtool) . 

[Az eszközre vonatkozó dokumentáció az](../reference-tsv-format-batch-testing.md) alábbiakat tartalmazza:

* az eszköz parancssori példája
* a TSV bemeneti és kimeneti fájljainak formátuma 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis közzététele](./publish-knowledge-base.md)
