---
title: A LUIS-portál alkalmazást
titleSuffix: Language Understanding - Azure Cognitive Services
description: Folyamatosan dolgozunk azon, pontosítsa és javítható a beszédfelismerés annak az alkalmazás a Language Understanding (LUIS) használatával.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 51c6a58567b35c9b8486d8634b0bed1af7218994
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893623"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>A LUIS-alkalmazás tesztelése a LUIS-portálon
<a name="train-your-app"></a>
[Tesztelés](luis-concept-test.md) az alkalmazás az iteratív folyamat. A LUIS-alkalmazás képzésekről, után tesztelje azt az minta utterances annak ellenőrzéséhez, hogy a szándékok és entitások helyesen ismeri. Ha nem, a LUIS-alkalmazásokon, tanítási és tesztelési ellenőrizze újra frissítéseket. 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="test-an-utterance"></a>Tesztelje az utterance (kifejezés)

1. Az alkalmazás eléréséhez annak nevét választva a **saját alkalmazások** lapot. 

2. Hozzáférés a **teszt** dia kibővített panelen kattintson **teszt** az alkalmazás tetején található.

    ![Tanítási és tesztelési lap](./media/luis-how-to-interactive-test/test.png)

3. Írja be az utterance (kifejezés) a szövegmezőbe, és nyomja le az Enter. Számos teszt a kimondott szöveg, amelyet szeretne beírhatja a **tesztelése**, de egyszerre csak egy utterance (kifejezés).

4. A szövegmező alatt utterances listáját az utterance (kifejezés), a felső szándékot és pontszám kerülnek.

    ![Interaktív vizsgálati azonosítja a nem megfelelő leképezés](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Vizsgálja meg a pontszám

A teszteredmények részletek nézze meg a **vizsgálat** panel. 
 
1. Az a **teszt** dia kibővített panel meg van nyitva, válassza ki **vizsgálat** az utterance (kifejezés) kívánja. 

    ![Válassza ki a vizsgálat gombra kattintva további részleteket tudhat a vizsgálati eredmények](./media/luis-how-to-interactive-test/inspect.png)

2. A **ellenőrzési** panel jelenik meg. A panel felső pontozási szándékot, valamint a azonosított entitások tartalmazza. A panelen a kiválasztott utterance (kifejezés) eredménye látható.

    ![A panel felső pontozási szándékot, valamint a azonosított entitások tartalmazza. A panelen a kiválasztott utterance (kifejezés) eredménye látható.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Leképezés pontozási megfelelő felső

1. Ha szándékot pontozási felső helytelen, válassza ki a **szerkesztése** gombra.

2.  A legördülő listában válassza ki a megfelelő leképezésének az utterance (kifejezés).

    ![Válassza ki a megfelelő leképezés](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Vélemények eredmények megtekintése

Ha **hangulatelemzés** konfigurálva van a **[közzététel](luis-how-to-publish-app.md#enable-sentiment-analysis)** oldal, a vizsgálati eredmények belefoglalása az utterance (kifejezés) található a róluk szóló véleményeket. 

![Hangulatelemzés a teszt panel képe](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Javítsa ki az egyeztetett minta célja

Ha használ [minták](luis-concept-patterns.md) és az utterance (kifejezés) egyező mintát, de a nem megfelelő leképezés volt előre jelzett, jelölje be a **szerkesztése** a minta szerint, majd válassza ki a megfelelő leképezést.

## <a name="compare-with-published-version"></a>Hasonlítsa össze a közzétett verzióra

Tesztelheti az alkalmazást a közzétett aktív verziója [végpont](luis-glossary.md#endpoint) verzió. Az a **vizsgálat** panelen kattintson **hasonlítsa össze a közzétett**. Bármely a közzétett modell tesztelése az Azure-előfizetés kvótája egyenleg vonni. 

![Hasonlítsa össze a közzétett](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Teszt panel végpont JSON megtekintése
A végpont JSON adja vissza az összehasonlításhoz kiválasztásával megtekintheti a **megjelenítése JSON-nézet**.

![Közzétett JSON-válasz](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Teszt panel további beállítások

### <a name="luis-endpoint"></a>A LUIS-végpont

Ha több LUIS-végpontokat, használja a **további beállításokat** a teszt hivatkozása közzétételének panelen módosíthatja a teszteléshez használt végpont. Ha nem biztos abban, hogy melyik végponthoz használatára, válassza ki az alapértelmezett **Starter_Key**. 

![Teszt panel a további beállításokat hivatkozás kiemelésével](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>A Bing Spell Check javításokat megtekintése a teszt panel

Megtekintheti a helyesírási javításokkal vonatkozó követelmények: 

* Közzétett alkalmazás
* A Bing Spell Check [Szolgáltatáskulcs](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). A kulcs nem tárolja, és minden böngésző-munkamenet alaphelyzetbe kell állítani. 

Tartalmazza az alábbi eljárással a [Bing helyesírás-ellenőrzés 7-es](https://azure.microsoft.com/services/cognitive-services/spell-check/) szolgáltatást a teszteredmények ablaktáblán. 

1. Az a **teszt** panelen írja be az utterance (kifejezés). Amikor az utterance (kifejezés) előrejelzésének megtekintéséhez válassza ki a **[vizsgálat](#inspect-score)** alá az utterance (kifejezés) adott meg. 

2. Ha a **vizsgálat** panel megnyílik, válassza ki  **[közzétett összehasonlítandó](#compare-with-published-version)**. 

3. Ha a **közzétett** panel megnyílik, válassza ki  **[további beállítás](#additional-settings-in-test-panel)**.

4. Az előugró párbeszédpanelen adja meg a **a Bing Spell Check** Szolgáltatáskulcs. 
    ![Adja meg a Bing Spell Check-kulcsot](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)

5. Adjon meg egy lekérdezést, és a egy helytelen helyesírás-ellenőrzés például `book flite to seattle` , és válassza ki, adja meg. A szó helyesírási `flite` váltja fel a lekérdezésben, LUIS küldött és az eredményül kapott JSON állapota is az eredeti lekérdezés `query`, és a javított helyesírás-ellenőrzés a lekérdezésben, `alteredQuery`.

    ![Kijavított helyesírás-ellenőrzés JSON](./media/luis-how-to-interactive-test/interactive-with-spell-check-results.png)

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

## <a name="batch-testing"></a>Kötegelt tesztelés
Tekintse meg a batch-tesztelés [fogalmak](luis-concept-batch-test.md) , és ismerje meg [hogyan](luis-how-to-batch-test.md) utterances kötegelt teszteléséhez.

## <a name="next-steps"></a>További lépések

Ha a tesztelés azt jelzi, hogy a LUIS-alkalmazás nem ismeri fel a megfelelő szándékokat és entitásokat, további utterances címkézés vagy szolgáltatások hozzáadására a LUIS-alkalmazás pontosságának növelése érdekében használhatja. 

* [Az intelligens hangfelismerési szolgáltatással javasolt utterances felirat](luis-how-to-review-endpoint-utterances.md) 
* [A LUIS-alkalmazás a teljesítmény javítása szolgáltatások használata](luis-how-to-add-features.md) 
