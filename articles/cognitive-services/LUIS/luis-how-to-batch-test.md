---
title: Kötegelt teszt végrehajtása - LUIS
titleSuffix: Azure Cognitive Services
description: A Language Understanding (LUIS) kötegelt tesztelési készletek használatával helytelen szándékokkal és entitásokkal keresheti meg az utterances(language understanding) (LUIS) kötegelt tesztelési készleteket.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: bfef7eae7158a05b09a3534e8fb44335333d8cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904359"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Kötegelt tesztelés példakimondottszövegekkel

 Kötegelt tesztelés egy átfogó teszt a jelenlegi betanított modell a LUIS teljesítményének mérésére. A kötegelt teszteléshez használt adatkészletek nem tartalmazhatnak példa utterances a szándékok vagy kimondott szövegek az előrejelzési futásidejű végpont. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Adatkészletfájl importálása kötegelt teszteléshez

1. Válassza a felső sáv **Teszt elemét,** majd a **Kötegelt tesztelés panelt**.

    ![Kötegelt tesztelési hivatkozás](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Válassza **az Adatkészlet importálása**lehetőséget. Megjelenik **az Új adatkészlet importálása** párbeszédpanel. Válassza **a Fájl kiválasztása lehetőséget,** és keresse meg a megfelelő [JSON formátumú JSON-fájlt,](luis-concept-batch-test.md#batch-file-format) amely *legfeljebb 1000* tesztelési kimondott szöveget tartalmaz.

    Az importálási hibákat a böngésző tetején lévő piros értesítési sáv jelenti. Ha egy importálás hibákat tartalmaz, nem jön létre adatkészlet. További információ: [Gyakori hibák](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. Az **Adatkészlet neve** mezőben adja meg az adatkészletfájl nevét. Az adatkészletfájl egy **sor utterances,** beleértve a *címkézett szándék* és az *entitások.* Tekintse át a [példakötegfájl](luis-concept-batch-test.md#batch-file-format) szintaxisát. 

4. Válassza a **Done** (Kész) lehetőséget. A program hozzáadja az adatkészletfájlt.

## <a name="run-rename-export-or-delete-dataset"></a>Adatkészlet futtatása, átnevezése, exportálása vagy törlése

Az adatkészlet futtatásához, átnevezéséhez, exportálásához vagy törléséhez használja az adatkészletsor végén található három pont (***...***) gombot.

![Adatkészlet-műveletek](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Kötegelt teszt futtatása a betanított alkalmazáson

A teszt futtatásához jelölje ki az adatkészlet nevét. Amikor a teszt befejeződik, ez a sor megjeleníti az adatkészlet teszteredményét.

![Kötegvizsgálati eredmény](./media/luis-how-to-batch-test/run-test.png)

A letölthető adatkészlet ugyanaz a fájl, amelyet kötegelt tesztelésre töltöttek fel.

|Állapot|Jelentés|
|--|--|
|![Sikeres teszt zöld kör ikon](./media/luis-how-to-batch-test/batch-test-result-green.png)|Minden kimondott szöveg sikeres.|
|![Sikertelen teszt piros x ikon](./media/luis-how-to-batch-test/batch-test-result-red.png)|Legalább egy utterance (kifejezés) szándék nem felelt meg az előrejelzésnek.|
|![Készen áll a tesztelésre ikon](./media/luis-how-to-batch-test/batch-test-result-blue.png)|A teszt készen áll a futásra.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Kötegvizsgálati eredmények megtekintése 

A kötegelt vizsgálati eredmények áttekintéséhez válassza az **Eredmények megtekintése**lehetőséget.

![Tételvizsgálati eredmények](./media/luis-how-to-batch-test/run-test-results.png)

<a name="filter-chart-results-by-intent-or-entity"></a>  

## <a name="filter-chart-results"></a>Diagram eredmények szűrése

Ha a diagramot egy adott szándék vagy entitás szerint szeretné szűrni, jelölje ki a szándékot vagy az entitást a jobb oldali szűrési panelen. Az adatpontok és azok eloszlása a kiválasztottnak megfelelően frissülnek a grafikonon. 
 
![Láthatóvá tételes teszt eredménye](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Egypontos utterance (kifejezés) kimondott szöveg adatok megtekintése

A diagramon vigye az egérmutatót egy adatpontra az előrejelzés bizonyossági pontszámának megtekintéséhez. Válasszon ki egy adatpontot a megfelelő utterances a kimondott szöveg lista alján az oldal. 

![Kijelölt utterance (kifejezés)](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Szakaszadatok megtekintése

A négyszakaszos diagramon jelölje ki a szakasz nevét, például a Diagram jobb felső részén található **Hamis pozitív.** A diagram alatt az adott szakaszban lévő összes kimondott szöveg a lista diagramja alatt jelenik meg. 

![Kijelölt kimondott szöveg szakaszok szerint](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Ebben az előző képen `switch on` az utterance (kifejezés) a TurnAllOn szándékkal van címkézve, de a Nincs szándék előrejelzését kapta. Ez azt jelzi, hogy a TurnAllOn szándék további példa utterances annak érdekében, hogy a várt előrejelzést. 

A diagram két szakasza piros színben jelzi a kimondott szövegeket, amelyek nem feleltek meg a várt előrejelzésnek. Ezek azt jelzik, kimondott, amely LUIS további képzésre van szüksége. 

A diagram két zöld szakasza megfelelt a várt előrejelzésnek.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>További lépések

Ha a tesztelés azt jelzi, hogy a LUIS-alkalmazás nem ismeri fel a megfelelő szándékokat és entitásokat, további kimondott szövegcímkézéssel vagy funkciók hozzáadásával javíthatja a LUIS-alkalmazás teljesítményét. 

* [Címke javasolt kimondott szöveg a LUIS-szal](luis-how-to-review-endpoint-utterances.md) 
* [A LUIS-alkalmazás teljesítményének javítására szolgáló funkciók használata](luis-how-to-add-features.md) 
* [A kötegelt tesztelés ismertetése ezzel az oktatóanyaggal](luis-tutorial-batch-testing.md)
* [Ismerje meg a kötegelt tesztelési fogalmakat.](luis-concept-batch-test.md)
