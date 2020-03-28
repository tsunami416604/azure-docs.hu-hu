---
title: 'Oktatóanyag: Kötegelt tesztelés problémák kereséséhez - LUIS'
description: Ez az oktatóanyag bemutatja, hogyan használhatja a kötegelt tesztelést a language understanding (LUIS) alkalmazás minőségének ellenőrzéséhez.
ms.topic: tutorial
ms.date: 03/02/2020
ms.openlocfilehash: c276f0b52f83937fbe3b6fd9e0b7c1a66f665095
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78250463"
---
# <a name="tutorial-batch-test-data-sets"></a>Oktatóanyag: Kötegelt tesztadatkészletek

Ez az oktatóanyag bemutatja, hogyan használhatja a kötegelt tesztelést a language understanding (LUIS) alkalmazás minőségének ellenőrzéséhez.

Kötegelt tesztelés lehetővé teszi, hogy érvényesítse az aktív, betanított modell állapotát egy ismert sor címkézett utterances és entitások. A JSON-formázott kötegfájlban adja hozzá az utterances, és állítsa be az entitás címkéket, amelyeket előre kell az utterance (kifejezés) belül.

A tételvizsgálatra vonatkozó követelmények:

* Tesztenként legfeljebb 1000 kimondott szöveg.
* Nincsenek másolatok.
* Entitástípusok engedélyezettek: csak gépelt megtanult entitások.

Ha nem ez az oktatóanyag-alkalmazást használ, *ne* használja az alkalmazáshoz már hozzáadott példa kimondott szövegeket.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Példaalkalmazás importálása
> * Kötegelt tesztfájl létrehozása
> * Kötegelt vizsgálat futtatása
> * A vizsgálati eredmények áttekintése

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Példaalkalmazás importálása

Importáljon egy olyan alkalmazást, `1 pepperoni pizza on thin crust`amely pizzarendelést vesz igénybe, például .

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Használja az [előzetes LUIS portált,](https://preview.luis.ai/)importálja a JSON-t egy új alkalmazásba, és nevezze el az alkalmazást. `Pizza app`

1. Válassza a **Vonat** lehetőséget a navigációs jobb felső sarokban az alkalmazás betanításához.

## <a name="what-should-the-batch-file-utterances-include"></a>Mit kell a batch fájl utterances tartalmazza

A kötegelt fájl tartalmaznia kell a kimondott szöveget a legfelső szintű gép által megtanult entitások címkével ellátott, beleértve a kezdő és a záró pozíciót. A kimondott szöveg nem lehet része a példák már az alkalmazásban. A szándékés az entitások esetében pozitívan előre jelezni kívánt kimondott szövegeket kell tenni.

A teszteket elválaszthatja szándék és/vagy entitás szerint, vagy az összes tesztet (legfeljebb 1000 utterances) ugyanabban a fájlban.

## <a name="batch-file"></a>Kötegfájl

A json példa tartalmaz egy utterance (kifejezés) egy címkézett entitás, amely bemutatja, hogyan néz ki egy tesztfájl. A saját tesztek, rendelkeznie kell a megfelelő szándékkal és a gép által megtanult entitás címkével ellátott sok kimondott szöveget.

1. Hozzon létre `pizza-with-machine-learned-entity-test.json` egy szövegszerkesztőben, vagy [töltse le.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)

2. A JSON-formátumú kötegfájlban adjon hozzá egy utterance (kifejezés) a **kívánt szándékkal** a tesztben.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>A köteg futtatása

1. Válassza a Felső navigációs sáv **Teszt elemét.**

2. Válassza a **Kötegelt tesztelés panelt** a jobb oldali panelen.

3. Válassza **az Adatkészlet importálása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a LUIS alkalmazásról, amelyen kiemelt adatkészlet importálása látható](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Adja meg a `pizza-with-machine-learned-entity-test.json` fájl helyét.

5. Nevezze el `pizza test` az adatkészletet, és válassza **a Kész gombot.**

    > [!div class="mx-imgBorder"]
    > ![Fájl kijelölése](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Kattintson a **Futtatás** gombra.

7. Válassza **az Eredmények megtekintése**lehetőséget.

8. Tekintse át az eredményeket a grafikonon és a jelmagyarázatban.

## <a name="review-batch-results-for-intents"></a>Leképezések kötegelt eredményeinek áttekintése

A teszteredmények grafikusan mutatják, hogyan előre jelzett a teszt kimondott szövegaz aktív verzióval.

A kötegdiagram az eredmények négy negyedét jeleníti meg. A diagramtól jobbra egy szűrő található. A szűrő leképezéseket és entitásokat tartalmaz. Amikor kijelöl [egy szakaszt a diagramból](luis-concept-batch-test.md#batch-test-results) vagy egy pontot a diagramon belül, a társított kimondott szöveg(ek) a diagram alatt jelennek meg.

Miközben az egér mutatója a diagram fölé mutat, az egérkerék nagyíthatja vagy csökkentheti a diagram megjelenítését. Ez akkor hasznos, ha a diagramon sok pont van szorosan csoportosítva.

A diagram négy kvadránsban, két szakasz jelenik meg piros.

1. Válassza ki a **ModifyOrder** leképezést a szűrőlistában.

    > [!div class="mx-imgBorder"]
    > ![Válassza a ModifyOrder leképezést a szűrőlistából](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    Az utterance (kifejezés) egy **igaz pozitív,** vagyis az utterance (kifejezés) sikeresen megfelelt a kötegfájlban felsorolt pozitív előrejelzés.

    > [!div class="mx-imgBorder"]
    > ![Az utterance (kifejezés) sikeresen megfelelt a pozitív előrejelzésnek](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    A szűrők listájában lévő zöld pipák az egyes leképezések tesztjének sikeresét is jelzik. Az összes többi szándékok vannak felsorolva egy 1/1 pozitív pontszám, mert az utterance (kifejezés) minden szándékkal szemben tesztelték, mint egy negatív teszt minden szándékok nem szerepel a kötegelt tesztben.

1. Válassza ki a **megerősítési** szándékot. Ez a szándék nem szerepel a kötegelt tesztben, így ez a kötegtesztben felsorolt utterance (kifejezés) negatív tesztje.

    > [!div class="mx-imgBorder"]
    > ![Az utterance (kifejezés) sikeresen megjósolta a nem listázott szándék negatívját a kötegfájlban](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    A negatív teszt sikeres volt, amint azt a zöld szöveg a szűrőben és a rácsban megjegyezte.

## <a name="review-batch-test-results-for-entities"></a>Entitások kötegvizsgálati eredményeinek áttekintése

A ModifyOrder entitás alentitásokkal rendelkező gépentitásként megjeleníti, hogy a legfelső szintű entitás megfelelt-e, és megjeleníti-e az alentitások előrejelzésének módját.

1. Jelölje ki a **ModifyOrder** entitást a szűrőlistában, majd jelölje ki a kört a rácsban.

1. Az entitás-előrejelzés a diagram alatt jelenik meg. A kijelző szilárd vonalakat tartalmaz az elvárásoknak megfelelő előrejelzésekhez, és pontozott sorokat az elvárásoknak nem megfelelő előrejelzésekhez.

    > [!div class="mx-imgBorder"]
    > ![Az entitás szülője sikeresen előre jelzett a kötegfájlban](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Hibák keresése kötegelt vizsgálattal

Ez a bemutató megmutatta, hogyan kell futtatni egy tesztet, és értelmezni az eredményeket. Nem terjed ki a tesztfilozófiára, vagy arra, hogy hogyan reagáljunk a sikertelen tesztekre.

* Győződjön meg arról, hogy a teszt pozitív és negatív utterances, beleértve a kimondott szöveg, amely előre jelezhető egy másik, de a kapcsolódó szándékot.
* Sikertelen kimondott szöveg esetén hajtsa végre a következő feladatokat, majd futtassa újra a teszteket:
    * Tekintse át a leképezések és entitások aktuális példáit, ellenőrizze az aktív verzió példautterances helyesek mind a szándék, mind az entitás címkézése szempontjából.
    * Olyan funkciók hozzáadása, amelyek segítenek az alkalmazásnak megjósolni a szándékokat és az entitásokat
    * További pozitív példakimondott szöveg hozzáadása
    * A példakimondott szöveg ek egyenlegének áttekintése a leképezések között

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Következő lépés

Az oktatóanyag kötegelt tesztet használt az aktuális modell érvényesítéséhez.

> [!div class="nextstepaction"]
> [További információ a mintákról](luis-tutorial-pattern.md)

