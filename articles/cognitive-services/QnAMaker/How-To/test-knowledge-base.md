---
title: Tudásbázis tesztelése – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker Tudásbázis tesztelése egy iterációs folyamat fontos részét képezi, amellyel javítható a visszaadott válaszok pontossága. A tudásbázist egy bővített csevegési felületen keresztül tesztelheti, amely lehetővé teszi a szerkesztést is.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 64b4b9a4ad5ceb0b3c33ae022b34daeafda93a62
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491208"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>A Tudásbázis tesztelése QnA Maker

A QnA Maker Tudásbázis tesztelése egy iterációs folyamat fontos részét képezi, amellyel javítható a visszaadott válaszok pontossága. A tudásbázist egy bővített csevegési felületen keresztül tesztelheti, amely lehetővé teszi a szerkesztést is.

## <a name="interactively-test-in-qna-maker-portal"></a>Interaktív tesztelés QnA Maker portálon

1. A Tudásbázisban a saját **Tudásbázis** lapján megjelenő név kiválasztásával férhet hozzá.
1. A teszt kivetítése panel eléréséhez válassza a **tesztelés** lehetőséget az alkalmazás felső paneljén.
1. Írjon be egy lekérdezést a szövegmezőbe, majd válassza az ENTER billentyűt.
1. A program válaszként a Tudásbázis legjobb egyezésű válaszát adja vissza.

### <a name="clear-test-panel"></a>Teszt panel törlése

Ha törölni szeretné az összes megadott tesztelési lekérdezést és azok eredményeit a tesztelési konzolról, válassza az **Indítás** lehetőséget a teszt panel bal felső sarkában.

### <a name="close-test-panel"></a>A teszt panel lezárása

A teszt panel bezárásához kattintson újra a **teszt** gombra. Amíg a teszt panel meg van nyitva, nem szerkesztheti a Tudásbázis tartalmát.

### <a name="inspect-score"></a>Pontszám vizsgálata

A teszt eredményének részleteit a vizsgálat panelen tekintheti meg.

1.  Nyissa meg a teszt kivetítése panelt, **és válassza a vizsgálat lehetőséget** a válasz további részleteinek megtekintéséhez.

    ![Válaszok vizsgálata](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Megjelenik a vizsgálat panel. A panel tartalmazza a leggyakoribb pontozási szándékot, valamint az azonosított entitásokat. A panel a kiválasztott kifejezés eredményét jeleníti meg.

### <a name="correct-the-top-scoring-answer"></a>A legfontosabb pontozásos válasz javítása

Ha a felső pontozási válasz helytelen, válassza ki a megfelelő választ a listából, és válassza a **Mentés és a betanítás**lehetőséget.

![A legfontosabb pontozásos válasz javítása](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Adott válaszhoz hozzáadhat alternatív űrlapokat is. Írja be a helyettesítő válaszokat a szövegmezőbe, majd kattintson az ENTER gombra a hozzáadásához. A frissítések mentéséhez válassza a **Mentés és a betanítás** lehetőséget.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Új válasz hozzáadása

Új választ adhat hozzá, ha a meglévő válaszok bármelyike helytelen, vagy a válasz nem létezik a Tudásbázisban (nem található megfelelő egyezés a KB-ban). 

A válaszok lista alján található szövegmezővel adjon meg egy új választ, és az ENTER billentyű lenyomásával adja hozzá. 

Válassza a **Mentés és a tanítás** lehetőséget a válasz megőrzéséhez. Mostantól új kérdés-válasz pár lett hozzáadva a tudásbázishoz. 

> [!NOTE]
> A Tudásbázis összes módosítása csak a **Mentés és a betanítás** gomb megnyomása után menthető.

### <a name="test-the-published-knowledge-base"></a>A közzétett Tudásbázis tesztelése

A Tudásbázis közzétett verzióját tesztelheti a teszt ablaktáblán. Miután közzétette a KB-ot, válassza ki a **közzétett tudásbázist** , és küldjön egy lekérdezést a közzétett Tudásbázis eredményeinek lekéréséhez.

![Tesztelés egy közzétett TUDÁSBÁZISban](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Batch-teszt eszközzel

A Batch Testing eszközt a következő esetekben használhatja:

* a legfontosabb válasz és a pontszám meghatározása a kérdésekre
* a várt válasz ellenőrzése a kérdéses csoportra vonatkozóan

A Batch Testing eszközt a Batch-tesztelési eszközzel együtt kell megadnia. Ez az eszköz letölthető vagy [ C# forráskódként](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)használható [tömörített végrehajtható fájlként](https://qnamakerstore.blob.core.windows.net/qnamakerdata/batchtesting/bt.zip) . 

[Az eszközre vonatkozó dokumentáció az](../reference-tsv-format-batch-testing.md) alábbiakat tartalmazza:

* az eszköz parancssori példája
* a TSV bemeneti és kimeneti fájljainak formátuma 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis közzététele](./publish-knowledge-base.md)
