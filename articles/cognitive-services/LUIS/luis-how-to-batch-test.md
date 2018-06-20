---
title: Kötegelt a alkalmazás tesztelése a LUIS - Azure |} Microsoft Docs
description: Helytelen leképezések és entitásokkal utterances kereséséhez használja a nyelvi ismertetése (LUIS) kötegelt tesztelése.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 822fb1e2d5b13941527d242e8501b423bd6b81cb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265513"
---
# <a name="batch-testing"></a>Kötegelt tesztelése
 A LUIS teljesítményének méréséhez az aktuális betanított modell egy átfogó vizsgálat kötegelt tesztelés. 

<a name="batch-testing"></a>
## <a name="import-a-dataset-file-for-batch-testing"></a>Kötegelt tesztelési dataset fájl importálása

1. Válassza ki **teszt** a felső menüsoron, és jelölje ki a **tesztelési panel kötegelt**.

    ![Kötegelt tesztelés hivatkozás](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Válassza ki **importálási dataset**. A **importálási új adatkészlet** párbeszédpanel jelenik meg. Válassza ki **Choose File** , és keresse meg a [JSON](luis-concept-batch-test.md#batch-file-format) tartalmazó fájl *legfeljebb 1000* utterances teszteléséhez.

    ![Adatkészlet-fájl importálása](./media/luis-how-to-batch-test/batchtest-importset.png)

    A böngésző tetején a vörös értesítési sávon jelez importálás hibát. Ha az importálás hibákkal rendelkezik, az adatkészlet jön létre. További információkért lásd: [gyakori hibák](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. Az a **Adatkészletnevet** mezőbe írja be a dataset fájl nevét. A dataset fájl tartalmaz egy **utterances tömbje** többek között a *leképezés feliratú* és *entitások*. Tekintse át a [példafájl](luis-concept-batch-test.md#batch-file-format) szintaxis. 

4. Válassza ki **végzett**. A dataset fájl kerül.

## <a name="run-rename-export-or-delete-dataset"></a>Futtassa, nevezze át, exportálhatja vagy adatkészlet törlése
Futtassa, nevezze át, exportálása, vagy törölje az adathalmazt, használja a három pont (**...** ) a dataset sor végén.

![A DataSet műveletek](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>A kötegelt teszt futtatása a betanított alkalmazásában

A teszt futtatása, válassza a DataSet adatkészlet neve. A vizsgálat befejezésekor a sor a dataset teszt eredményét jeleníti meg.

![Kötegelt tesztelési eredménye](./media/luis-how-to-batch-test/run-test.png)

A letölthető dataset ugyanazt a fájlt töltött kötegelt tesztelési.

|Állapot|Jelentés|
|--|--|
|![A sikeres vizsgálat zöld kör ikon](./media/luis-how-to-batch-test/batch-test-result-green.png)|Minden utterances is sikeres.|
|![Meghibásodott teszt piros x ikon](./media/luis-how-to-batch-test/batch-test-result-red.png)|Legalább egy utterance leképezés nem felelt meg az előrejelzés.|
|![Készen áll a teszt ikonja](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Teszt készen áll a futtatásra.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>
## <a name="view-batch-test-results"></a>Kötegelt vizsgálat eredményeinek megtekintése 
A kötegelt vizsgálati eredmények, jelölje ki **eredményeket**.

![Kötegelt teszt eredményei](./media/luis-how-to-batch-test/run-test-results.png)

<!--
 Select the **See results** link that appears after you run the test. A scatter graph known as an error matrix displays. The data points represent the utterances in the dataset. 

Green points indicate correct prediction, and red ones indicate incorrect prediction.

The filtering panel on the right side of the screen displays a list of all intents and entities in the app, with a green point for intents/entities that were predicted correctly in all dataset utterances, and a red point for those items with errors. Also, for each intent/entity, you can see the number of correct predictions out of the total utterances.

-->


<a name="filter-chart-results-by-intent-or-entity"></a> ## A diagram eredmény szűrése

A diagram által egy adott leképezés vagy entitás szűréséhez válassza a leképezés vagy entitás a szűrési jobb oldali panelen. Az adatok és a terjesztési frissítése a diagramon, a beállítás alapján. 
 
![Feladatkonfigurációkat kötegelt tesztelési eredménye](./media/luis-how-to-batch-test/filter-by-entity.png) 

<!--
## Investigate false sections
Data points on the **[False Positive][false-positive]** and **[False Negative][false-negative]** sections indicate errors, which should be investigated. If all data points are on the **[True Positive][true-positive]** and **[True Negative][true-negative]** sections, then your application's performance is perfect on this dataset.


The graph indicates [F-measure][f-measure], [recall][recall], and [precision][precision].  
-->
## <a name="view-single-point-utterance-data"></a>Egyetlen pont utterance adatok megtekintése
A diagramon mutasson az előrejelzés biztonságot eredményének megtekintéséhez adatpont. Válassza ki a utterances lista az oldal alján a megfelelő utterance beolvasása adatpont. 

![Kijelölt utterance](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
## <a name="view-section-data"></a>A szakasz adatok megtekintése
A négy-szakasz diagram, válassza ki a szakasz nevét, például a **téves pozitív** , a jobb felső részén a diagramon. A diagram alá minden utterances ezen részében jeleníti meg, a diagram lista alatt. 

![Kijelölt utterances szakaszhoz](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Az előző ábrán a utterance `switch on` TurnAllOn biztonsági mentés lett címkézve, de nincs leképezési előrejelzését kapott. Ez az arra utal, hogy kell-e a TurnAllOn célt további példa utterances annak érdekében, hogy a várt előrejelzését. 

A két szakasz piros a diagram, amely nem egyezik a várt előrejelzés utterances jelzi. Ezek határozzák meg, hogy utterances mely LUIS kell további képzési. 

A diagram zöld két szakasza egyezett a várt előrejelzését.

## <a name="next-steps"></a>További lépések

Ha tesztelési azt jelzi, hogy az LUIS alkalmazás nem ismeri fel a megfelelő leképezések és entitások, használhat LUIS alkalmazás javítása további utterances címkézési vagy szolgáltatások hozzáadására. 

* [Javasolt utterances LUIS rendelkező címke](Label-Suggested-Utterances.md) 
* [A szolgáltatások használata a LUIS alkalmazás teljesítmény javítása érdekében](luis-how-to-add-features.md) 
* [Ez az oktatóanyag tesztelték kötegelt ismertetése](luis-tutorial-batch-testing.md)
* [Ismerje meg a tesztelési fogalmak kötegelt](luis-concept-batch-test.md).

[true-positive]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#true-positive
[true-negative]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#true-negative
[false-positive]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#false-positive
[false-negative]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#false-negative
[f-measure]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#f-measure
[recall]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#recall
[precision]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#precision

