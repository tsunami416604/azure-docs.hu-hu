---
title: Batch-teszt elvégzése – LUIS
titleSuffix: Azure Cognitive Services
description: Használjon Language Understanding (LUIS) batch-tesztelési készleteket a helytelen szándékokkal és entitásokkal rendelkező hosszúságú kimondott szöveg kereséséhez.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: ee15d08c820492cfa2c0701dfe3b682d57e00187
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344407"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Batch-tesztelés példaként megadott hosszúságú kimondott szöveg

 A Batch Testing egy átfogó teszt a jelenlegi betanított modellen a teljesítményének méréséhez a LUIS-ben. A Batch-teszteléshez használt adatkészletek nem tartalmazhatnak példaként megadott hosszúságú kimondott szöveg az előrejelzési futtatókörnyezet végpontján kapott szándékokban vagy hosszúságú kimondott szöveg.

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Adatkészlet-fájl importálása batch-teszteléshez

1. Válassza a **teszt** elemet a felső sávon, majd válassza a **Batch Testing (tesztelés) panel**elemet.

    ![Batch-tesztelési hivatkozás](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Válassza az **adatkészlet importálása**lehetőséget. Megjelenik az **új adatkészlet importálása** párbeszédpanel. Válassza a **fájl kiválasztása** lehetőséget, és keresse meg a megfelelő [JSON-formátumú](luis-concept-batch-test.md#batch-file-format) JSON-formátumot, amely *nem tartalmaz több mint 1 000* hosszúságú kimondott szöveg a teszteléshez.

    Az importálási hibák jelentése a böngésző tetején található piros értesítési sávon történik. Ha egy importálás hibákat tartalmaz, a rendszer nem hoz létre adatkészletet. További információ: [gyakori hibák](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. Az **adatkészlet neve** mezőben adja meg az adatkészlet fájljának nevét. Az adatkészlet-fájl a **hosszúságú kimondott szöveg tömbjét tartalmazza,** beleértve a *címkézett szándékot* és az *entitásokat*. Tekintse át a [példaként szolgáló batch-fájlt](luis-concept-batch-test.md#batch-file-format) szintaxisként.

4. Válassza a **Done** (Kész) lehetőséget. Az adatkészlet fájlja hozzá van adva.

## <a name="run-rename-export-or-delete-dataset"></a>Adatkészlet futtatása, átnevezése, exportálása vagy törlése

Az adatkészlet futtatásához, átnevezéséhez, exportálásához vagy törléséhez használja az adatkészlet sor végén lévő három pont (***..***.) gombot.

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel a Batch-tesztek listájáról a lehetőségekkel](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Batch-teszt futtatása a betanított alkalmazáson

A teszt futtatásához válassza ki az adatkészlet nevét, majd válassza a **Futtatás** lehetőséget a környezetfüggő eszköztáron. Ha a teszt befejeződött, ez a sor az adatkészlet tesztelési eredményét jeleníti meg.

A letölthető adatkészlet ugyanaz a fájl, amelyet a Batch-teszteléshez töltöttek fel.

|Állam|Értelmezés|
|--|--|
|![Sikeres teszt zöld kör ikon](./media/luis-how-to-batch-test/batch-test-result-green.png)|Minden hosszúságú kimondott szöveg sikeres.|
|![Nem sikerült tesztelni a piros x ikont](./media/luis-how-to-batch-test/batch-test-result-red.png)|Legalább egy Kimondás nem egyezik meg az előrejelzéssel.|
|![Készen áll a teszt ikonra](./media/luis-how-to-batch-test/batch-test-result-blue.png)|A teszt készen áll a futtatásra.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>A Batch-tesztek eredményeinek megtekintése

A Batch-tesztek eredményeinek áttekintéséhez válassza az **eredmények megtekintése**lehetőséget.

<a name="filter-chart-results-by-intent-or-entity"></a>

## <a name="filter-chart-results"></a>Diagram eredményeinek szűrése

Ha egy adott szándék vagy entitás alapján szeretné szűrni a diagramot, válassza ki a szándékot vagy az entitást a jobb oldali szűrés panelen. Az adatpontok és a terjesztési frissítésük a diagramon a választott módon történik.

![Vizualizációs batch-teszt eredménye](./media/luis-how-to-batch-test/filter-by-entity.png)

## <a name="view-single-point-utterance-data"></a>Egypontos Kimondás-adatelemek megtekintése

A diagramon vigye az egérmutatót egy adatpontra, és tekintse meg az előrejelzésének bizonyossági pontszámát. A lap alján található hosszúságú kimondott szöveg listában válasszon ki egy adatpontot a megfelelő kifejezés beolvasásához.

![Kijelölt Kimondás](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Szakaszra vonatkozó adatnézet

A négy szakaszból álló diagramon válassza ki a szakasz nevét, például a **hamis pozitív értéket** a diagram jobb felső sarkában. A diagram alatt az adott szakaszban található összes hosszúságú kimondott szöveg megjelenik a diagram alatt a listában.

![Kijelölt hosszúságú kimondott szöveg szakasz szerint](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Ebben az előző képen a Kimondás a `switch on` TurnAllOn szándékkal van megjelölve, de a nincs szándék előrejelzését kapta. Ez azt jelzi, hogy a TurnAllOn szándéknak több példát kell hosszúságú kimondott szöveg a várható előrejelzés elvégzéséhez.

A diagram két része piros színnel jelöli azt a hosszúságú kimondott szöveg, amely nem felel meg a várt előrejelzésnek. Ezek azt jelzik, hosszúságú kimondott szöveg, hogy a LUIS több betanításra van szüksége.

A diagram két része zöld színnel egyezik a várt előrejelzéssel.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Következő lépések

Ha a tesztelés azt jelzi, hogy a LUIS-alkalmazás nem ismeri fel a megfelelő szándékokat és entitásokat, a további hosszúságú kimondott szöveg címkézésével vagy funkciók hozzáadásával javíthatja a LUIS-alkalmazás teljesítményét.

* [Címke javasolt hosszúságú kimondott szöveg a LUIS-vel](luis-how-to-review-endpoint-utterances.md)
* [Szolgáltatások használata a LUIS-alkalmazás teljesítményének növeléséhez](luis-how-to-add-features.md)
* [A Batch tesztelésének megismerése az Oktatóanyaggal](luis-tutorial-batch-testing.md)
* A [Batch tesztelési koncepcióinak megismerése](luis-concept-batch-test.md).
