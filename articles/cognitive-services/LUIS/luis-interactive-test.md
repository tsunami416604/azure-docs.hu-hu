---
title: Alkalmazás tesztelése a LUIS Portalon
description: A Language Understanding (LUIS) használatával folyamatosan dolgozhat az alkalmazáson, és javíthatja annak nyelvi megismerését.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219818"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>A LUIS-alkalmazás tesztelése a LUIS-portálon

Az alkalmazások [tesztelése](luis-concept-test.md) egy iterációs folyamat. A LUIS-alkalmazás betanítása után tesztelje a mintát hosszúságú kimondott szöveg, és ellenőrizze, hogy a szándékok és entitások felismerése helyesen történt-e. Ha nem, akkor frissítse a LUIS alkalmazást, a betanítást és a tesztelést.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Tesztelés előtti tanítás

Ha tesztelni szeretné az aktív alkalmazás legújabb verzióját, a tesztelés előtt válassza a **betanítás** lehetőséget a felső menüben.

## <a name="test-an-utterance"></a>Kimondás tesztelése

A teszt kimondása nem egyezhet meg pontosan az alkalmazás egyik hosszúságú kimondott szöveg sem. A teszt kiírásának tartalmaznia kell a felhasználó számára várt szót, kifejezés hosszúságát és az entitások használatát.

1. Az alkalmazás eléréséhez válassza ki a saját **alkalmazások** lapján a saját nevét.

1. A **teszt** kivetítése panel eléréséhez válassza a **tesztelés** lehetőséget az alkalmazás felső paneljén.

    > [!div class="mx-imgBorder"]
    > ![& tesztelési alkalmazás betanítása oldal](./media/luis-how-to-interactive-test/test.png)

1. Adjon meg egy kiírást a szövegmezőben, és válassza az ENTER billentyűt. Annyi teszt hosszúságú kimondott szöveg is beírhat, amennyit csak szeretne a **tesztben**, de egyszerre csak egy Kimondás.

1. A Kimondás, a felső szándék és a pontszám a szövegmezőben a hosszúságú kimondott szöveg listájára kerül.

    ![Az interaktív tesztelés a helytelen szándékot azonosítja](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Pontszám vizsgálata

A teszt eredményének részleteit a **vizsgálat panelen** tekintheti meg.

1. Nyissa meg a test slide (tesztelés) **Inspect** panelt, és válassza a **vizsgálat** lehetőséget az összehasonlítani kívánt kifejezés kiválasztásához.

    ![A vizsgálati eredmények további részleteinek megtekintéséhez kattintson a vizsgálat gombra.](./media/luis-how-to-interactive-test/inspect.png)

1. Megjelenik a **vizsgálat** panel. A panel tartalmazza a leggyakoribb pontozási szándékot, valamint az azonosított entitásokat. A panel a kiválasztott kifejezés eredményét jeleníti meg.

    ![A panel tartalmazza a leggyakoribb pontozási szándékot, valamint az azonosított entitásokat. A panel a kiválasztott kifejezés eredményét jeleníti meg.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Legjobb pontozási szándék javítása

1. Ha a felső pontozási szándék helytelen, kattintson a **Szerkesztés** gombra.

1.  A legördülő listában válassza ki a megfelelő szándékot a teljes beállításhoz.

    ![Helyes leképezés kiválasztása](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>A hangulat eredményeinek megtekintése

Ha a találatok elemzése a **[közzétételi](luis-how-to-publish-app.md#enable-sentiment-analysis)** oldalon van konfigurálva, a teszt eredményei közé tartozik a teljes **kiértékelésben** talált hangulat.

![A teszt ablaktábla és a hangulat elemzésének képe](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Megfelelő egyezésű minta szándéka

Ha [mintázatokat](luis-concept-patterns.md) használ, és a kihagyás egy mintázatnak felel meg, de a rossz szándékot jósolta meg, válassza a minta alapján a **Szerkesztés** hivatkozást, majd válassza ki a megfelelő szándékot.

## <a name="compare-with-published-version"></a>Összehasonlítás a közzétett verzióval

Tesztelheti az alkalmazás aktív verzióját a közzétett [végpont](luis-glossary.md#endpoint) verziószámával. Az **ellenőrzés** panelen válassza a **összehasonlítás a közzétetttel**lehetőséget. A közzétett modellel kapcsolatos bármilyen vizsgálatot levonják az Azure-előfizetési kvóta egyenlegéről.

![Összehasonlítás közzétételsel](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Végpont JSON megtekintése a teszt panelen
Az összehasonlításhoz a **JSON-nézet megjelenítése**lehetőség kiválasztásával megtekintheti a végpont által VISSZAadott JSON-t.

![Közzétett JSON-válasz](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>További beállítások a teszt panelen

### <a name="luis-endpoint"></a>LUIS-végpont

Ha több LUIS-végponttal rendelkezik, a teszt közzétett paneljén a **További beállítások** hivatkozásra kattintva módosíthatja a teszteléshez használt végpontot. Ha nem biztos abban, hogy melyik végpontot szeretné használni, válassza ki az alapértelmezett **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![A teszt panel további beállítások hivatkozását kiemelve](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Kötegelt tesztelés
Tekintse meg a Batch-tesztelési [fogalmakat](luis-concept-batch-test.md) , és Ismerje meg, [Hogyan](luis-how-to-batch-test.md) tesztelheti a Batch of hosszúságú kimondott szöveg.

## <a name="next-steps"></a>További lépések

Ha a tesztelés azt jelzi, hogy a LUIS-alkalmazás nem ismeri fel a megfelelő szándékokat és entitásokat, a további hosszúságú kimondott szöveg címkézésével vagy funkciók hozzáadásával javíthatja a LUIS-alkalmazás pontosságát.

* [Címke javasolt hosszúságú kimondott szöveg a LUIS-vel](luis-how-to-review-endpoint-utterances.md)
* [Szolgáltatások használata a LUIS-alkalmazás teljesítményének növeléséhez](luis-how-to-add-features.md)
