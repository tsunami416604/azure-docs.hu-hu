---
title: 'Oktatóanyag: a Batch tesztelése a hibák kereséséhez – LUIS'
description: Ez az oktatóanyag azt mutatja be, hogyan használható a Batch Testing a Language Understanding (LUIS) alkalmazás minőségének ellenőrzéséhez.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2020
ms.openlocfilehash: 7dd181f8cd398dd683296b446028b398a9800b53
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298314"
---
# <a name="tutorial-batch-test-data-sets"></a>Oktatóanyag: batch test adatkészletek

Ez az oktatóanyag azt mutatja be, hogyan használható a Batch Testing a Language Understanding (LUIS) alkalmazás minőségének ellenőrzéséhez.

A Batch Testing lehetővé teszi az aktív, betanított modell állapotának érvényesítését a címkézett hosszúságú kimondott szöveg és entitások ismert készletével. A JSON-formátumú batch-fájlban adja hozzá a hosszúságú kimondott szöveg, és állítsa be az entitások feliratait, amelyeknek a teljes kifejezésen belül kell lenniük.

A Batch-tesztelésre vonatkozó követelmények:

* Legfeljebb 1000 hosszúságú kimondott szöveg.
* Nincsenek ismétlődések.
* Engedélyezett entitások típusai: csak a géppel megtanult entitások használhatók.

Ha az oktatóanyagtól eltérő *alkalmazást használ, ne használja az* alkalmazáshoz már hozzáadott hosszúságú kimondott szöveg.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Alkalmazás importálása – példa
> * Batch-teszt fájl létrehozása
> * Batch-teszt futtatása
> * Tesztek eredményeinek áttekintése

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Alkalmazás importálása – példa

Importáljon egy olyan alkalmazást, amely pizzás sorrendet (például) vesz igénybe `1 pepperoni pizza on thin crust` .

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Importálja a JSON-t egy új alkalmazásba, nevezze el az alkalmazást `Pizza app` .


1. A navigáció jobb felső sarkában válassza a **vonat** lehetőséget az alkalmazás betanításához.

## <a name="what-should-the-batch-file-utterances-include"></a>A batch-fájl hosszúságú kimondott szöveg belefoglalása

A Batch-fájlnak tartalmaznia kell a legfelső szintű gépi tanulási entitások hosszúságú kimondott szöveg, beleértve a kezdő és a záró pozíciót is. A hosszúságú kimondott szöveg nem lehet az alkalmazásban már szereplő példák része. Érdemes hosszúságú kimondott szöveg, ha pozitívan szeretné megjósolni a szándékot és az entitásokat.

Elkülönítheti a teszteket szándék és/vagy entitás alapján, vagy az összes tesztet (akár 1000 hosszúságú kimondott szöveg) ugyanabban a fájlban.

## <a name="batch-file"></a>Batch-fájl

A JSON-példa tartalmaz egy címkével ellátott entitást, amely bemutatja, hogyan néz ki egy adott teszt fájl. A saját tesztek során sok hosszúságú kimondott szöveg kell rendelkeznie, és a megfelelő szándékú és gépi tanulási entitást kell megcímkézni.

1. Hozzon létre `pizza-with-machine-learned-entity-test.json` egy szövegszerkesztőben, vagy [töltse le](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) .

2. A JSON-formátumú batch-fájlban adjon hozzá egy, a tesztben előre jelezni kívánt **szándékot** .

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>A köteg futtatása

1. Válassza a **teszt** lehetőséget a felső navigációs sávon.

2. Válassza a **Batch-tesztelés panelt** a jobb oldali panelen.

3. Válassza az **adatkészlet importálása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a LUIS-alkalmazásról a Kiemelt importálási adatkészlettel](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Válassza ki a fájl helyét `pizza-with-machine-learned-entity-test.json` .

5. Nevezze el az adatkészletet `pizza test` , majd válassza a **kész**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Fájl kiválasztása](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Kattintson a **Futtatás** gombra.

7. Válassza az **eredmények megtekintése**lehetőséget.

8. Tekintse át az eredményeket a gráfban és a jelmagyarázatban.

## <a name="review-batch-results-for-intents"></a>A szándékok kötegelt eredményeinek áttekintése

A teszt eredményei grafikusan mutatják be, hogy a teszt hosszúságú kimondott szöveg hogyan lettek előre jelezve az aktív verziónál.

A Batch-diagram az eredmények négy negyedét jeleníti meg. A diagram jobb oldalán egy szűrő látható. A szűrő leképezéseket és entitásokat tartalmaz. Amikor kijelöl egy [szakaszt](luis-concept-batch-test.md#batch-test-results) a diagramon, vagy egy pontot a diagramon belül, a társított kimaradás (ek) a diagram alatt látható.

Amikor a diagram fölé viszi a kurzort, az egér kereke megnövelheti vagy csökkentheti a diagram megjelenítését. Ez akkor hasznos, ha a diagramon több pont is van, és szorosan össze van kapcsolva.

A diagram négy részből áll, és a piros színnel megjelenő két részből áll.

1. Válassza ki a **ModifyOrder** szándékot a szűrők listájában.

    > [!div class="mx-imgBorder"]
    > ![Válassza ki a ModifyOrder szándékát a szűrőlista listából](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    A Kimondás értéke **igaz pozitív** , ami azt jelenti, hogy a Kimondás sikeresen megfelelt a Batch-fájlban felsorolt pozitív előrejelzésnek.

    > [!div class="mx-imgBorder"]
    > ![A Kimondás sikeresen megegyezett a pozitív előrejelzéssel](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    A szűrők listában a zöld pipa jelzi az egyes szándékok tesztelésének sikerességét is. Az összes többi szándék 1/1 pozitív pontszámmal van felsorolva, mivel a teljes ellenőrzés az egyes szándékok alapján lett tesztelve, a Batch-tesztben nem szereplő szándékok negatív vizsgálataként.

1. Válassza ki a **megerősítő** szándékot. Ez a szándék nem szerepel a Batch-tesztben, így ez a Batch-tesztben szereplő teljes érték negatív tesztje.

    > [!div class="mx-imgBorder"]
    > ![A kizáró eredmény sikeresen előre jelezve lett a nem listázott szándékhoz a Batch-fájlban](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    A negatív teszt sikeres volt, ahogy az a szűrőben lévő zöld szöveggel és a rácsmal is megjegyezve volt.

## <a name="review-batch-test-results-for-entities"></a>Entitások tesztelési eredményeinek áttekintése

A ModifyOrder entitás, amely alentitásokkal rendelkező gépi entitásként jelenik meg, megjeleníti, hogy a legfelső szintű entitás egyezik-e, és megjeleníti az alentitások előrejelzésének módját.

1. Válassza ki a **ModifyOrder** entitást a szűrő listában, majd válassza ki a rácsban található kört.

1. Az entitás előrejelzése a diagram alatt jelenik meg. A kijelző tartalmazza azokat az előrejelzéseket tartalmazó folytonos vonalakat, amelyek megfelelnek a vártnak nem megfelelő előrejelzések és pontozott soroknak.

    > [!div class="mx-imgBorder"]
    > ![Az entitás szülőjének sikeresen megjósolva a Batch-fájlban](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

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

