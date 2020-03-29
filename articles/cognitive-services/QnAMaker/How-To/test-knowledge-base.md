---
title: Tudásbázis tesztelése - QnA Maker
description: A QnA Maker tudásbázistesztelése fontos része egy iteratív folyamatnak a visszaadott válaszok pontosságának javítása érdekében. A tudásbázist egy továbbfejlesztett csevegőfelületen tesztelheti, amely lehetővé teszi a szerkesztést is.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927276"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Tesztelje tudásbázisát a QnA Maker programban

A QnA Maker tudásbázistesztelése fontos része egy iteratív folyamatnak a visszaadott válaszok pontosságának javítása érdekében. A tudásbázist egy továbbfejlesztett csevegőfelületen tesztelheti, amely lehetővé teszi a szerkesztést is.

## <a name="interactively-test-in-qna-maker-portal"></a>Interaktív tesztelés a QnA Maker portálon

1. A tudásbázis eléréséhez válassza ki a nevét a **Saját tudásbázisok** lapon.
1. A Teszt kicsúsztatáspanel eléréséhez válassza a **Teszt** lehetőséget az alkalmazás felső paneljén.
1. Írjon be egy lekérdezést a szövegmezőbe, és válassza az Enter lehetőséget.
1. A tudásbázis legjobban illeszkedő válasza válaszként kerül visszaadásra.

### <a name="clear-test-panel"></a>Tesztpanel törlése

Ha törölni szeretné az összes beírt tesztlekérdezést és azok eredményeit a tesztkonzolról, válassza az **Újrakezdés** lehetőséget a Teszt panel bal felső sarkában.

### <a name="close-test-panel"></a>Tesztpanel bezárása

A Teszt panel bezárásához kattintson ismét a **Teszt** gombra. Amíg a Teszt panel meg van nyitva, a Tudásbázis tartalma nem szerkeszthető.

### <a name="inspect-score"></a>Pontszám vizsgálata

A vizsgálati eredmény részleteit az Ellenőrzés panelen vizsgálja meg.

1.  Nyissa meg a Teszt kicsúsztatás panelt, és válassza a **Vizsgálat** lehetőséget, hogy további részleteket megtudjon a válaszról.

    ![Válaszok vizsgálata](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Megjelenik az ellenőrző panel. A panel tartalmazza a legmagasabb pontozási szándékot, valamint az azonosított entitásokat. A panel a kijelölt utterance (kifejezés) eredményét jeleníti meg.

### <a name="correct-the-top-scoring-answer"></a>A legmagasabb pontozási válasz javítása

Ha a legmagasabb pontozási válasz helytelen, válassza ki a helyes választ a listából, és válassza a **Mentés és betanítás**lehetőséget.

![A legmagasabb pontozási válasz javítása](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Egy adott válaszhoz alternatív űrlapokat is hozzáadhat. Írja be az alternatív válaszokat a szövegmezőbe, és kattintson a Enter gombra a hozzáadásukhoz. A frissítések tárolásához válassza a **Mentés és betanítás** lehetőséget.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Új válasz hozzáadása

Új választ adhat hozzá, ha az egyeztetett válaszok bármelyike helytelen, vagy a válasz nem létezik a tudásbázisban (a tudásbázisban nem található megfelelő egyezés).

A válaszlista alján a szövegmezősegítségével adjon meg egy új választ, és nyomja meg az Enter billentyűt a hozzáadáshoz.

Válassza a **Mentés és betanítás** lehetőséget a válasz megőrzéséhez. Egy új kérdés-válasz pár lett hozzáadva a tudásbázishoz.

> [!NOTE]
> A tudásbázis összes szerkesztése csak akkor kerül mentésre, ha megnyomja a Mentés és a **Betanítás** gombot.

### <a name="test-the-published-knowledge-base"></a>A közzétett tudásbázis tesztelése

A tudásbázis közzétett verzióját a tesztpanelen tesztelheti. A tudásbázis közzététele után jelölje be a **Közzétett tudásbázis** mezőt, és küldjön egy lekérdezést a közzétett kb-tól származó eredmények lekérdezéséhez.

![Teszt közzétett KB-val](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Tételvizsgálat szerszámmal

Akkor használja a kötegelt tesztelési eszközt, ha a következőket szeretné:

* meghatározni a legjobb választ és pontszám egy sor kérdést
* a várt válasz érvényesítése kérdéssorozatra

Olvassa el a kötegelt [tesztelési oktatóanyagot,](../Quickstarts/batch-testing.md) amely részletes útmutatást tartalmaz.

A kötegelt tesztelés a kötegelt tesztelési eszközzel együtt történik. Ez az eszköz letölthető [tömörítési végrehajtható fájlként](https://aka.ms/qnamakerbatchtestingtool) vagy [C# forráskódként](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)érhető el.

[Az eszközre vonatkozó referenciadokumentáció a](../reference-tsv-format-batch-testing.md) következőket tartalmazza:

* az eszköz parancssori példája
* A TSV bemeneti és outfile fájlok formátuma

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis közzététele](./publish-knowledge-base.md)
