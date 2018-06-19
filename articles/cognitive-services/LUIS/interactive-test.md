---
title: Betanítása és a alkalmazás tesztelése a LUIS - Azure |} Microsoft Docs
description: Nyelvi ismertetése (LUIS) segítségével az alkalmazás számára, pontosítsa és a nyelvi megértése folyamatosan működik.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 760434253a3ece14352154a22cc68142ec2b0531
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35760226"
---
# <a name="test-your-luis-app"></a>A LUIS alkalmazás tesztelése
<a name="train-your-app"></a>
[Képzési](luis-how-to-train.md) és [tesztelés](luis-concept-test.md) egy alkalmazás áll iteratív folyamat. Után az LUIS alkalmazás képzési és tesztelte minta utterances megjelenítéséhez, ha a leképezések és entitások megfelelően a rendszer felismeri. Ha még nem, a LUIS app, tanítási és tesztelési végezze újra frissítéseket. 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="interactive-testing"></a>
## <a name="test-an-utterance"></a>Egy utterance tesztelése

1. Az alkalmazás eléréséhez választva módosíthatja a nevet a **saját alkalmazások** lap. 

2. Hozzáférés a **teszt** menüpontban húzással állíthatja be a kibővített panelen, jelölje be **teszt** a az alkalmazás felső panelje.

    ![Tanítási és tesztelési App lapon](./media/luis-how-to-interactive-test/test.png)

3. A mezőben adja meg egy utterance, és válassza ki a billentyűt. Adhatja meg kívánt utterances számos teszt a **tesztelése**, de egyszerre csak egy utterance.

4. A utterance, a felső leképezés és pontszám hozzáadódnak a szövegmező alatt utterances listája.

    ![A nem megfelelő leképezés interaktív tesztelés azonosítja.](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="clear-test-panel"></a>Egyszerű vizsgálati panel
Törli az összes megadott teszt utterances és a vizsgálati konzolról eredményeit, válassza ki a **kezdje újra a folyamatot** a bal felső sarkában a **teszt panel**. 

## <a name="close-test-panel"></a>Bezárás teszt panel
Bezárja a **teszt** panelen, jelölje be a **teszt** újra gombra.

## <a name="inspect-score"></a>Vizsgálja meg a pontszám
A vizsgálati eredmények a részleteket nézze meg a **vizsgálat** panel. 
 
1. Az a **teszt** menüpontban húzással állíthatja be a kibővített panel megnyitása, jelölje be **vizsgálat** az összehasonlítani kívánt egy utterance. 

    ![Vizsgálja meg a gomb](./media/luis-how-to-interactive-test/inspect.png)

2. A **ellenőrző** panel jelenik meg. A panel leképezés, valamint a azonosított entitások pontozási felső tartalmazza. A panel a kijelölt utterance eredményének megjelenítése.

    ![Vizsgálja meg a gomb](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Leképezés pontozási megfelelő felső

1. Ha leképezés pontozási felső helytelen, válassza ki a **szerkesztése** gombra.

2.  A legördülő listában válassza ki a megfelelő leképezésének a utterance.

    ![Válassza ki a megfelelő cél](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Véleményeket eredmények megtekintése

Ha **véleményeket elemzés** konfigurálva van a **[közzététel](publishapp.md#enable-sentiment-analysis)** lap, a vizsgálati eredmények tartalmazzák a céggel kapcsolatos véleményeket a utterance található. 

![Teszt ablaktábla véleményeket elemzés képe](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Javítsa ki a megfelelő mintát leképezés
Ha használ [minták](luis-concept-patterns.md) és a utterance egyező mintát, de a nem megfelelő leképezés lett előre jelezni, válassza ki a **szerkesztése** által a minta hivatkozásra, majd válassza ki a megfelelő leképezést.

## <a name="compare-with-published-version"></a>Hasonlítsa össze a közzétett verzióra
Az aktív verzió az alkalmazás a közzétett tesztelheti [végpont](luis-glossary.md#endpoint) verziója. Az a **vizsgálat** panelen, jelölje be **hasonlítsa össze a közzétett**. Bármely közzétett modellre tesztelése az Azure-előfizetés kvótájának egyenleg vonni. 

![Hasonlítsa össze a közzétett](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>A teszt panelen végpont JSON megtekintése
A végpont JSON adott vissza az összehasonlításhoz kiválasztásával megtekintheti a **megjelenítése JSON nézet**.

![Közzétett JSON-válasz](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>A teszt panelen további beállítások

### <a name="luis-endpoint"></a>LUIS végpont
Ha több LUIS végpontok, használja a **további beállításokat** hivatkozás a teszteléshez használt közzétett módosítása a teszteléshez használt végpont ablaktáblán. Ha nem biztos abban, hogy mely végpontot kell használni, jelölje be az alapértelmezett **Starter_Key**. 

![További beállítások hivatkozás a kijelölt panelen tesztelése](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Bing helyesírás-ellenőrző javításokat teszt panelen megtekintése
Megtekintheti a helyesírás javításokat által biztosított [Bing helyesírás-ellenőrzés v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) API JSON nézetében a **közzétett** panelje a ablaktáblán. 

A szolgáltatás használatához, akkor közzé kell tennie az alkalmazást, és a Bing helyesírás-ellenőrzés [szolgáltatás kulcs](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). A szolgáltatás kulcs nem tárolja, és minden böngésző-munkamenet alaphelyzetbe kell állítani. 

A következő segítségével billentyűvel a Bing helyesírás-ellenőrzés v7 szolgáltatás a ablaktáblán. 

1. A teszt panelen a a **közzétett** ablaktáblán válassza előbb **további beállításokat**.

2. Az előugró párbeszédpanelen adja meg a **Bing helyesírás-ellenőrzés** szolgáltatásának kulcsát. 
    ![Bing helyesírás-ellenőrzés szolgáltatási kulcs megadása](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)

3. Adjon meg egy lekérdezést egy helytelen helyesírás, mint a `book flite to seattle` , és válassza ki, írja be. A helytelen helyesen adta-e szó `flite` váltja fel a lekérdezésben LUIS küldött és az eredményül kapott JSON jeleníti meg mindkét az eredeti lekérdezés, mint `query`, és a javított helyesen adta-e a lekérdezés, mint `alteredQuery`.

    ![Helyesírás-ellenőrző JSON javítani](./media/luis-how-to-interactive-test/interactive-with-spell-check-results.png)

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
## <a name="batch-testing"></a>Kötegelt tesztelése
Tekintse meg a kötegelt tesztelése [fogalmak](luis-concept-batch-test.md) és további [hogyan](luis-how-to-batch-test.md) tesztelése utterances egy tranzakcióköteghez.

## <a name="next-steps"></a>További lépések

Ha tesztelési azt jelzi, hogy az LUIS alkalmazás nem ismeri fel a megfelelő leképezések és entitások, használhat LUIS alkalmazás pontosságának javítása a címkézés további utterances vagy szolgáltatások hozzáadására. 

* [Javasolt utterances LUIS rendelkező címke](Label-Suggested-Utterances.md) 
* [A szolgáltatások használata a LUIS alkalmazás teljesítmény javítása érdekében](luis-how-to-add-features.md) 
