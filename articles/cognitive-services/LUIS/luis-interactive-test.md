---
title: A LUIS-portál alkalmazást
titleSuffix: Azure Cognitive Services
description: Folyamatosan dolgozunk azon, pontosítsa és javítható a beszédfelismerés annak az alkalmazás a Language Understanding (LUIS) használatával.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 628547e8254bb0055cf1f09af50e79b68311a759
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221742"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>A LUIS-alkalmazás tesztelése a LUIS-portálon

Az alkalmazások [tesztelése](luis-concept-test.md) egy iterációs folyamat. A LUIS-alkalmazás képzésekről, után tesztelje azt az minta utterances annak ellenőrzéséhez, hogy a szándékok és entitások helyesen ismeri. Ha nem, a LUIS-alkalmazásokon, tanítási és tesztelési ellenőrizze újra frissítéseket. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Tesztelés előtti tanítás

Ha tesztelni szeretné az aktív alkalmazás legújabb verzióját, a tesztelés előtt válassza a **betanítás** lehetőséget a felső menüben. 

## <a name="test-an-utterance"></a>Tesztelje az utterance (kifejezés)

A teszt kimondása nem egyezhet meg pontosan az alkalmazás egyik hosszúságú kimondott szöveg sem. A teszt kiírásának tartalmaznia kell a felhasználó számára várt szót, kifejezés hosszúságát és az entitások használatát. 

1. Az alkalmazás eléréséhez válassza ki a saját **alkalmazások** lapján a saját nevét. 

1. A **teszt** kivetítése panel eléréséhez válassza a **tesztelés** lehetőséget az alkalmazás felső paneljén.

    > [!div class="mx-imgBorder"]
    > ![a Train & test app (alkalmazás tesztelése) lapot](./media/luis-how-to-interactive-test/test.png)

1. Írja be az utterance (kifejezés) a szövegmezőbe, és nyomja le az Enter. Annyi teszt hosszúságú kimondott szöveg is beírhat, amennyit csak szeretne a **tesztben**, de egyszerre csak egy Kimondás.

1. A szövegmező alatt utterances listáját az utterance (kifejezés), a felső szándékot és pontszám kerülnek.

    ![Interaktív vizsgálati azonosítja a nem megfelelő leképezés](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Vizsgálja meg a pontszám

A teszt eredményének részleteit a **vizsgálat panelen** tekintheti meg. 
 
1. Nyissa meg a test slide (tesztelés) panelt, és válassza a **vizsgálat** lehetőséget az összehasonlítani kívánt kifejezés kiválasztásához. 

    ![A vizsgálati eredmények további részleteinek megtekintéséhez kattintson a vizsgálat gombra.](./media/luis-how-to-interactive-test/inspect.png)

1. Megjelenik a **vizsgálat** panel. A panel felső pontozási szándékot, valamint a azonosított entitások tartalmazza. A panelen a kiválasztott utterance (kifejezés) eredménye látható.

    ![A panel felső pontozási szándékot, valamint a azonosított entitások tartalmazza. A panelen a kiválasztott utterance (kifejezés) eredménye látható.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Leképezés pontozási megfelelő felső

1. Ha a felső pontozási szándék helytelen, kattintson a **Szerkesztés** gombra.

1.  A legördülő listában válassza ki a megfelelő leképezésének az utterance (kifejezés).

    ![Válassza ki a megfelelő leképezés](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Vélemények eredmények megtekintése

Ha a találatok elemzése a **[közzétételi](luis-how-to-publish-app.md#enable-sentiment-analysis)** oldalon van konfigurálva, a teszt eredményei közé tartozik a teljes **kiértékelésben** talált hangulat. 

![Hangulatelemzés a teszt panel képe](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Javítsa ki az egyeztetett minta célja

Ha [mintázatokat](luis-concept-patterns.md) használ, és a kihagyás egy mintázatnak felel meg, de a rossz szándékot jósolta meg, válassza a minta alapján a **Szerkesztés** hivatkozást, majd válassza ki a megfelelő szándékot.

## <a name="compare-with-published-version"></a>Hasonlítsa össze a közzétett verzióra

Tesztelheti az alkalmazás aktív verzióját a közzétett [végpont](luis-glossary.md#endpoint) verziószámával. Az **ellenőrzés** panelen válassza a **összehasonlítás a közzétetttel**lehetőséget. Bármely a közzétett modell tesztelése az Azure-előfizetés kvótája egyenleg vonni. 

![Hasonlítsa össze a közzétett](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Teszt panel végpont JSON megtekintése
Az összehasonlításhoz a **JSON-nézet megjelenítése**lehetőség kiválasztásával megtekintheti a végpont által VISSZAadott JSON-t.

![Közzétett JSON-válasz](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Teszt panel további beállítások

### <a name="luis-endpoint"></a>A LUIS-végpont

Ha több LUIS-végponttal rendelkezik, a teszt közzétett paneljén a **További beállítások** hivatkozásra kattintva módosíthatja a teszteléshez használt végpontot. Ha nem biztos abban, hogy melyik végpontot szeretné használni, válassza ki az alapértelmezett **Starter_Key**. 

> [!div class="mx-imgBorder"]
> ![a teszt panel további beállítások hivatkozását kiemelve](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)

<!--
###  View Bing Spell Check corrections in test panel

Requirements to view the spelling corrections: 

* Published app
* Bing Spell Check [service key](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). The service key is not stored and needs to be reset for each browser session. 

Use the following procedure to include the [Bing Spell Check v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) service  in the Test pane results. 

1. In the **Test** pane, enter an utterance. When the utterance is predicted, select **[Inspect](#inspect-score)** underneath the utterance you entered. 

1. When the **Inspect** panel opens, select **[Compare with Published](#compare-with-published-version)**. 

1. When the **Published** panel opens, select **[Additional Settings](#additional-settings-in-test-panel)**.

1. In the pop-up dialog, check **Enable Bing Spell Check** and enter the key, then select **Done**. 
    ![Enter Bing Spell Check service key](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Enter a query with an incorrect spelling such as `book flite to seattle` and select enter. The incorrect spelling of the word `flite` is replaced in the query sent to LUIS and the resulting JSON shows both the original query, as `query`, and the corrected spelling in the query, as `alteredQuery`.

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
-->

## <a name="batch-testing"></a>Kötegelt tesztelés
Tekintse meg a Batch-tesztelési [fogalmakat](luis-concept-batch-test.md) , és Ismerje meg, [Hogyan](luis-how-to-batch-test.md) tesztelheti a Batch of hosszúságú kimondott szöveg.

## <a name="next-steps"></a>További lépések

Ha a tesztelés azt jelzi, hogy a LUIS-alkalmazás nem ismeri fel a megfelelő szándékokat és entitásokat, további utterances címkézés vagy szolgáltatások hozzáadására a LUIS-alkalmazás pontosságának növelése érdekében használhatja. 

* [Címke javasolt hosszúságú kimondott szöveg a LUIS-vel](luis-how-to-review-endpoint-utterances.md) 
* [Szolgáltatások használata a LUIS-alkalmazás teljesítményének növeléséhez](luis-how-to-add-features.md) 
