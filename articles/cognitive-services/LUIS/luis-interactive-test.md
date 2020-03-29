---
title: Tesztalkalmazás a LUIS portálon
description: A Language Understanding (LUIS) segítségével folyamatosan dolgozhat az alkalmazáson, hogy finomítsa és javítsa a nyelvi megértést.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219818"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>A LUIS-alkalmazás tesztelése a LUIS portálon

Az alkalmazás [tesztelése](luis-concept-test.md) iteratív folyamat. A LUIS-alkalmazás betanítása után tesztelje azt a minta utterances, hogy lássa, a szándékok és az entitások megfelelően ismeri fel. Ha nem, a LUIS-alkalmazás frissítései, a vonat és a tesztelés újra.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Vonat a vizsgálat előtt

Annak érdekében, hogy tesztelje az aktív alkalmazás legújabb verzióját, a tesztelés előtt válassza a **Vonat** lehetőséget a felső menüből.

## <a name="test-an-utterance"></a>Kimondott szöveg tesztelése

A teszt utterance (teszt utterance ( teszt utterance s in the app) kifejezésnem azonos lehet. A teszt utterance (teszt utterance ( teszt utterance (kifejezés) szóválasztás, kifejezés hossza és entitáshasználat, amely et vár egy felhasználó.

1. Az alkalmazás eléréséhez válassza ki a nevét a **Saját alkalmazások** lapon.

1. A **Teszt** kicsúsztatáspanel eléréséhez válassza a **Teszt** lehetőséget az alkalmazás felső paneljén.

    > [!div class="mx-imgBorder"]
    > ![A & tesztalkalmazás oldalának betanítása](./media/luis-how-to-interactive-test/test.png)

1. Írjon be egy kimondott szöveget a szövegmezőbe, és válassza az Enter lehetőséget. A **tesztben**a kívánt számú tesztkimondott szöveget is beírhatja, de egyszerre csak egy utterance (kifejezés) beírhatja.

1. Az utterance (kifejezés), a felső szándék és a pontszám hozzáadódnak a szövegmező alatti utterances listához.

    ![Az interaktív tesztelés helytelen szándékot azonosít](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Pontszám vizsgálata

A vizsgálati eredmény részleteit az Ellenőrzés panelen vizsgálja **meg.**

1. Ha a **Teszt** kicsúsztatás panel meg van nyitva, válassza **az Összehasonlítás** lehetőséget az összehasonlítani kívánt kimondott szövegért.

    ![A Vizsgálat gombra kattintva további részleteket tekinthet meg a teszteredményekről.](./media/luis-how-to-interactive-test/inspect.png)

1. Megjelenik **az ellenőrző** panel. A panel tartalmazza a legmagasabb pontozási szándékot, valamint az azonosított entitásokat. A panel a kijelölt utterance (kifejezés) eredményét jeleníti meg.

    ![A panel tartalmazza a legmagasabb pontozási szándékot, valamint az azonosított entitásokat. A panel a kijelölt utterance (kifejezés) eredményét jeleníti meg.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>A legmagasabb pontozási szándék javítása

1. Ha a legfelső pontozási szándék helytelen, válassza a **Szerkesztés** gombot.

1.  A legördülő listában válassza ki a megfelelő leképezést az utterance (kifejezés).

    ![Válassza ki a megfelelő szándékot](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Hangulateredmények megtekintése

Ha **a véleményelemzés** konfigurálva van a **[Közzététel](luis-how-to-publish-app.md#enable-sentiment-analysis)** lapon, a teszt eredmények tartalmazzák az utterance (kifejezés) található vélemény.

![A Teszt ablaktábla hangulatelemzéssel](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Helyes egyező minta szándéka

Ha minták [at használ,](luis-concept-patterns.md) és az utterance (kifejezés) egy minta egyezik, de a rossz szándék előre jelzett, válassza ki a **szerkesztési** hivatkozást a minta, majd válassza ki a megfelelő szándékot.

## <a name="compare-with-published-version"></a>Összehasonlítás a közzétett verzióval

Tesztelheti az alkalmazás aktív verzióját a [endpoint](luis-glossary.md#endpoint) közzétett végpontverzióval. A **Vizsgálat** panelen válassza az **Összehasonlítás a közzétetttel**lehetőséget. A közzétett modellen végzett bármilyen tesztelés levonásra kerül az Azure-előfizetési kvótaegyenlegéből.

![Összehasonlítás a közzététellel](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>A JSON végpont megtekintése a tesztpanelen
Az összehasonlításhoz visszaadott JSON végpontot a **JSON-nézet megjelenítése**lehetőség kiválasztásával tekintheti meg.

![Közzétett JSON-válasz](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>További beállítások a tesztpanelen

### <a name="luis-endpoint"></a>LUIS-végpont

Ha több LUIS-végponttal rendelkezik, a Teszt közzétett ablaktábláján található **További beállítások** hivatkozással módosítsa a teszteléshez használt végpontot. Ha nem biztos abban, hogy melyik végpontot használja, válassza ki az alapértelmezett **Starter_Key.**

> [!div class="mx-imgBorder"]
> ![Tesztpanel kiemelt További beállítások hivatkozással](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Kötegelt tesztelés
Tekintse meg a kötegelt [tesztelési fogalmakat,](luis-concept-batch-test.md) és ismerje [meg, hogyan](luis-how-to-batch-test.md) tesztelhatoaz utterances kötegek.

## <a name="next-steps"></a>További lépések

Ha a tesztelés azt jelzi, hogy a LUIS-alkalmazás nem ismeri fel a megfelelő szándékokat és entitásokat, további kimondott szövegek címkézésével vagy funkciók hozzáadásával javíthatja a LUIS-alkalmazás pontosságát.

* [Címke javasolt kimondott szöveg a LUIS-szal](luis-how-to-review-endpoint-utterances.md)
* [A LUIS-alkalmazás teljesítményének javítására szolgáló funkciók használata](luis-how-to-add-features.md)
