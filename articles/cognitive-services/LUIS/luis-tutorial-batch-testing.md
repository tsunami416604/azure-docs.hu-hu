---
title: 'Oktatóanyag: a Batch tesztelése a hibák kereséséhez – LUIS'
description: Ez az oktatóanyag azt mutatja be, hogyan használható a Batch Testing a Language Understanding (LUIS) alkalmazás minőségének ellenőrzéséhez.
ms.topic: tutorial
ms.date: 03/02/2020
ms.openlocfilehash: c276f0b52f83937fbe3b6fd9e0b7c1a66f665095
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250463"
---
# <a name="tutorial-batch-test-data-sets"></a>Oktatóanyag: batch test adatkészletek

Ez az oktatóanyag azt mutatja be, hogyan használható a Batch Testing a Language Understanding (LUIS) alkalmazás minőségének ellenőrzéséhez.

Batch tesztelés lehetővé teszi, hogy ellenőrizze az aktív, betanított modell állapotának ismert vannak beállítva címkézett utterances és entitásokat. A JSON-formátumú parancsfájlba a beszédmódok hozzáadása, és állítsa be az entitás címkék van szüksége, előre meghatározott az utterance (kifejezés) belül.

Batch-tesztelés vonatkozó követelmények:

* Legfeljebb 1000 utterances tesztenként.
* Nincsenek ismétlődések.
* Engedélyezett entitások típusai: csak a géppel megtanult entitások használhatók.

Ha az oktatóanyagtól eltérő *alkalmazást használ, ne használja az* alkalmazáshoz már hozzáadott hosszúságú kimondott szöveg.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Alkalmazás importálása – példa
> * Hozzon létre egy kötegfájlt teszt
> * Egy batch-teszt futtatása
> * Vizsgálati eredmények áttekintése

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Alkalmazás importálása – példa

Importáljon egy olyan alkalmazást, amely pizzás sorrendet (például `1 pepperoni pizza on thin crust`) vesz igénybe.

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. A [Luis-portál előzetes](https://preview.luis.ai/)verziójának használatával importálja a JSON-t egy új alkalmazásba, nevezze el az alkalmazást `Pizza app`.

1. A navigáció jobb felső sarkában válassza a **vonat** lehetőséget az alkalmazás betanításához.

## <a name="what-should-the-batch-file-utterances-include"></a>A batch-fájl hosszúságú kimondott szöveg belefoglalása

A Batch-fájlnak tartalmaznia kell a legfelső szintű gépi megtanult entitások hosszúságú kimondott szöveg, beleértve a kezdő és a befejező pozíciót is. A hosszúságú kimondott szöveg nem lehet az alkalmazásban már szereplő példák része. Érdemes hosszúságú kimondott szöveg, ha pozitívan szeretné megjósolni a szándékot és az entitásokat.

Elkülönítheti a teszteket szándék és/vagy entitás alapján, vagy az összes tesztet (akár 1000 hosszúságú kimondott szöveg) ugyanabban a fájlban.

## <a name="batch-file"></a>Batch-fájl

A JSON-példa tartalmaz egy címkével ellátott entitást, amely bemutatja, hogyan néz ki egy adott teszt fájl. A saját tesztek során számos hosszúságú kimondott szöveg kell lennie, amely a megfelelő szándékkal és a gépi megtanult entitással van megjelölve.

1. Hozzon létre `pizza-with-machine-learned-entity-test.json` egy szövegszerkesztőben, vagy [töltse le](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) .

2. A JSON-formátumú batch-fájlban adjon hozzá egy, a tesztben előre jelezni kívánt **szándékot** .

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Futtassa a köteget

1. Válassza a **teszt** lehetőséget a felső navigációs sávon.

2. Válassza a **Batch-tesztelés panelt** a jobb oldali panelen.

3. Válassza az **adatkészlet importálása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![a LUIS alkalmazás képernyőképét az importálási adatkészlet kiemelésével](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Válassza ki a `pizza-with-machine-learned-entity-test.json` fájljának helyét.

5. Nevezze el az adathalmazt `pizza test` majd válassza a **kész**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![a fájl kiválasztása](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Kattintson a **Futtatás** gombra.

7. Válassza az **eredmények megtekintése**lehetőséget.

8. Tekintse át az eredményeket a graph és jelmagyarázat.

## <a name="review-batch-results-for-intents"></a>A szándékok kötegelt eredményeinek áttekintése

A teszt eredményei grafikusan mutatják be, hogy a teszt hosszúságú kimondott szöveg hogyan lettek előre jelezve az aktív verziónál.

A batch diagram jeleníti meg az eredmények négy elemzésében. A diagram jobb szűrő van. A szűrő leképezéseket és entitásokat tartalmaz. Amikor kijelöl egy [szakaszt](luis-concept-batch-test.md#batch-test-results) a diagramon, vagy egy pontot a diagramon belül, a társított kimaradás (ek) a diagram alatt látható.

A diagram rámutatáskor egérkereket nagyítása vagy csökkentheti a megjelenített a diagramon. Ez akkor hasznos, ha a fürtözött szorosan együtt diagramra hány pontot.

A diagram van négy elemzésében, és két a szakaszok vörös színnel jelenik meg.

1. Válassza ki a **ModifyOrder** szándékot a szűrők listájában.

    > [!div class="mx-imgBorder"]
    > ![válassza ki a ModifyOrder leképezést a szűrőlista listából](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    A Kimondás értéke **igaz pozitív** , ami azt jelenti, hogy a Kimondás sikeresen megfelelt a Batch-fájlban felsorolt pozitív előrejelzésnek.

    > [!div class="mx-imgBorder"]
    > ![a Kimondás sikeresen megegyezett a pozitív előrejelzéssel](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    A szűrők listában a zöld pipa jelzi az egyes szándékok tesztelésének sikerességét is. Az összes többi szándék 1/1 pozitív pontszámmal van felsorolva, mivel a teljes ellenőrzés az egyes szándékok alapján lett tesztelve, a Batch-tesztben nem szereplő szándékok negatív vizsgálataként.

1. Válassza ki a **megerősítő** szándékot. Ez a szándék nem szerepel a Batch-tesztben, így ez a Batch-tesztben szereplő teljes érték negatív tesztje.

    > [!div class="mx-imgBorder"]
    > ![a Kimondás sikeresen megjósolta, hogy a batch-fájl nem listázza a nem listázott szándékot](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    A negatív teszt sikeres volt, ahogy az a szűrőben lévő zöld szöveggel és a rácsmal is megjegyezve volt.

## <a name="review-batch-test-results-for-entities"></a>Entitások tesztelési eredményeinek áttekintése

A ModifyOrder entitás, amely alentitásokkal rendelkező gépi entitásként jelenik meg, megjeleníti, hogy a legfelső szintű entitás egyezik-e, és megjeleníti az alentitások előrejelzésének módját.

1. Válassza ki a **ModifyOrder** entitást a szűrő listában, majd válassza ki a rácsban található kört.

1. Az entitás előrejelzése a diagram alatt jelenik meg. A kijelző tartalmazza azokat az előrejelzéseket tartalmazó folytonos vonalakat, amelyek megfelelnek a vártnak nem megfelelő előrejelzések és pontozott soroknak.

    > [!div class="mx-imgBorder"]
    > ![entitás szülője sikeresen megjósolva a Batch-fájlban](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Hibák keresése batch-teszttel

Ebből az oktatóanyagból megtudhatta, hogyan futtathat egy tesztet, és hogyan értelmezheti az eredményeket. Nem fedi le a tesztelési filozófiát, illetve a sikertelen tesztekre való reagálás módját.

* Ügyeljen arra, hogy a teszt pozitív és negatív hosszúságú kimondott szöveg is vonatkozzon, beleértve a hosszúságú kimondott szöveg is, amelyek egy másik, de kapcsolódó szándék alapján várhatóak.
* A sikertelen hosszúságú kimondott szöveg hajtsa végre az alábbi feladatokat, majd futtassa újra a teszteket:
    * Tekintse át a szándékok és entitások aktuális példáit, és ellenőrizze, hogy az aktív verzió hosszúságú kimondott szöveg helyesek-e a szándék és az entitások címkézése szempontjából.
    * Az alkalmazás előrejelzési szándékait és entitásait segítő funkciók hozzáadása
    * További pozitív példa hosszúságú kimondott szöveg hozzáadása
    * Példa hosszúságú kimondott szöveg egyenlegének áttekintése a leképezések között

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Következő lépés

Az oktatóanyag egy batch-tesztet használt az aktuális modell érvényesítéséhez.

> [!div class="nextstepaction"]
> [A minták ismertetése](luis-tutorial-pattern.md)

