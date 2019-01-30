---
title: Batch-teszt
titleSuffix: Language Understanding - Azure Cognitive Services
description: Tesztelési Language Understanding (LUIS) batch használatával kereshet a helytelen szándékok és entitások kimondott szöveg.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: dcc5e463eab6c5de612df45963f160156315a34a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213225"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>A Batch példa utterances vannak beállítva tesztelése

 A LUIS a teljesítmény méréséhez az aktuális betanított modell egy átfogó teszt Batch tesztelés. A batch-tesztelési célokra használható adatkészletek nem tartalmazhatja a leképezések vagy utterances utterances kapott az előrejelzési futásidejű végpont példa. 

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Egy batch teszteléséhez adatkészletfájlt importálása

1. Válassza ki **teszt** felső sáv, és adja meg **Batch-tesztelési panel**.

    ![Batch-tesztelési hivatkozás](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Válassza ki **importálás adatkészlet**. A **importálása új adatkészlet** párbeszédpanel jelenik meg. Válassza ki **fájl kiválasztása** , és keresse meg a JSON-fájlt a megfelelő [JSON formátumban](luis-concept-batch-test.md#batch-file-format) tartalmazó *legfeljebb 1000* teszteléséhez kimondott szöveg.

    Az importálási hibák kell jelenteni, egy piros értesítési sáv felső részén a böngészőben. Ha az importálás hibákkal rendelkezik, egyetlen adatkészlet jön létre. További információkért lásd: [gyakran előforduló hibák](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. Az a **Adatkészletnevet** mezőben adja meg az adatkészlet-fájl nevét. Az adatkészlet-fájl tartalmaz egy **kimondott szöveg egy tömbjét** többek között a *szándékot feliratú* és *entitások*. Tekintse át a [példafájl](luis-concept-batch-test.md#batch-file-format) szintaxis. 

4. Válassza a **Done** (Kész) lehetőséget. Az adatkészlet fájlt adnak hozzá.

## <a name="run-rename-export-or-delete-dataset"></a>Futtassa, átnevezése, exportálhatja vagy adatkészlet törlése

Futtassa, átnevezése, exportálhatja vagy törli az adatkészletet, használja a három pontra (***...*** ) gombra az adatkészlet sor végén található.

![Adatkészlet műveletek](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Egy batch-teszt futtatása a betanított alkalmazásban

Futtassa a tesztet, jelölje be az adatkészlet nevét. A teszt befejezését követően a sor az adatkészlet teszt eredményét jeleníti meg.

![Batch-teszt eredménye](./media/luis-how-to-batch-test/run-test.png)

A letölthető adatkészlet ugyanazt a fájlt, amely a feltöltött batch tesztelési.

|Állapot|Jelentés|
|--|--|
|![A sikeres vizsgálat zöld kör ikon](./media/luis-how-to-batch-test/batch-test-result-green.png)|Az összes utterances is sikeres.|
|![Meghibásodott teszt piros x ikon](./media/luis-how-to-batch-test/batch-test-result-red.png)|Legalább egy utterance (kifejezés) célja nem felelt meg az előrejelzést.|
|![Készen áll a teszt ikonja](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Teszt készen áll a futásra.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Batch-teszt eredményeinek megtekintése 

Válassza ki a batch-vizsgálati eredmények áttekintéséhez **eredmények megtekintéséhez**.

![A Batch terhelésiteszt-eredményei](./media/luis-how-to-batch-test/run-test-results.png)

<a name="filter-chart-results-by-intent-or-entity"></a>  

## <a name="filter-chart-results"></a>Diagram eredmények szűréséhez

A diagram egy adott szándékot vagy egy entitás szűréséhez válassza a leképezés vagy entitás a szűrési jobb oldali panelen. Az adatok és a terjesztési frissítse a grafikon a kijelölés alapján. 
 
![A Batch teszi teszteredménye](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Pont utterance (kifejezés) adatok megtekintése

A diagram egy adatpontra, az előrejelzési bizonyossággal pontszám mutasson. Válasszon ki egy adatpontot beolvasni a megfelelő utterance (kifejezés) a kimondott szöveg lista az oldal alján. 

![Kiválasztott utterance (kifejezés)](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>A szakasz adatok megtekintése

A négy szakaszt tartalmazó diagram, válassza ki a szakasz nevét, például **hamis pozitív** , a jobb felső sarkában a diagramot. A diagram alatt található összes kimondott szöveg megjelenítése a diagram egy lista alatt. 

![A szakasz által kiválasztott kimondott szöveg](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

A fenti ábrán az utterance (kifejezés) `switch on` TurnAllOn készítésében feliratú, de nincs szándék előrejelzését kapott. Ez az azt jelzi, hogy kell-e a TurnAllOn célt további példa utterances annak érdekében, hogy a várt előrejelzést. 

A két szakasz a diagram vörös színnel, amely nem egyezik a várt előrejelzési kimondott szöveg jelzi. Ezek azt jelzik, hogy kimondott szöveg melyik LUIS további betanítási van szüksége. 

A két szakasz a diagram zöld színnel egyezik a várt előrejelzési.

## <a name="next-steps"></a>További lépések

Ha a tesztelés azt jelzi, hogy a LUIS-alkalmazás nem ismeri fel a megfelelő szándékokat és entitásokat, használhatja a LUIS-alkalmazás teljesítményének növelése további utterances címkézés vagy szolgáltatások hozzáadására. 

* [Az intelligens hangfelismerési szolgáltatással javasolt utterances felirat](luis-how-to-review-endoint-utt.md) 
* [A LUIS-alkalmazás a teljesítmény javítása szolgáltatások használata](luis-how-to-add-features.md) 
* [Ebben az oktatóanyagban tesztelés batch ismertetése](luis-tutorial-batch-testing.md)
* [Ismerje meg, a batch-fogalmak tesztelése](luis-concept-batch-test.md).
