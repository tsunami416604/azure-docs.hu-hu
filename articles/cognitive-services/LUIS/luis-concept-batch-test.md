---
title: Kötegelt a alkalmazás tesztelése a LUIS - Azure |} Microsoft Docs
description: Kötegelt tesztelési célra használható az alkalmazás számára, pontosítsa és a nyelvi megértése folyamatosan dolgozunk.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 3803df32d6431b8413e8df0837ed62b2e4344cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348183"
---
# <a name="batch-testing-in-luis"></a>Kötegelt LUIS tesztelése

Kötegelt tesztelés érvényesíti a [aktív](luis-concept-version.md#active-version) betanított modell méréséhez az előrejelzés pontosságát. A kötegelt teszt segít minden leképezés és az entitás pontosságát tekintse meg a diagramot a jelenlegi betanított modell. Ellenőrizze a kötegelt vizsgálati eredményeket hajtsa végre a megfelelő műveletet, például további példa utterances hozzáadása megjelölésű, ha az alkalmazás gyakran nem tudja azonosítani a helyes leképezés pontosság növeléséhez.

## <a name="group-data-for-batch-test"></a>A kötegelt teszteléséhez csoport adatai
Fontos, hogy kötegelt teszteléshez utterances még nem használta a LUIS. Ha a utterances dataset, ossza három be utterances: utterances megjelölésű hozzá, a közzétett végpont kapott utterances és utterances használt kötegelt tesztelési LUIS után be van tanítva. 

## <a name="a-dataset-of-utterances"></a>A utterances adatkészlet
Küldje el a utterances néven kötegelt fájl egy *adatkészlet*, kötegelt teszteléséhez. Az adatkészlet egy olyan JSON-formátumú fájl legfeljebb 1000 feliratú tartalmazó **nem ismétlődő** utterances. Legfeljebb 10 adatkészletek alkalmazáson belüli tesztelheti. Több tesztelni kell, ha törli egy adatkészletet, és adja meg egy új.

|**Szabályok**|
|--|
|* Nem ismétlődő utterances|
|Nincs a hierarchikus gyermeke|
|1000 utterances vagy kevesebb|

* Ismétlődések pontos karakterlánc megfelel, nem megfelelő találat először tokenekre bontott minősülnek. 

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>
## <a name="batch-file-format"></a>Kötegelt fájlformátum
A kötegfájl utterances áll. Minden egyes utterance rendelkeznie kell egy várt leképezési előrejelzés együtt [gép megtanulta entitások](luis-concept-entity-types.md#types-of-entities) várhatóan észlelhető. 

A következő egy példa kötegfájl:

   [!code-json[Valid batch test](~/samples-luis/documentation-samples/batch-testing/travel-agent-1.json)]


## <a name="common-errors-importing-a-batch"></a>Gyakori hibák a kötegelt importálása
Gyakori hibák a következők: 

> * Több mint 1000 utterances
> * Egy entitás tulajdonsággal nem rendelkező utterance JSON objektum

## <a name="batch-test-state"></a>Kötegelt teszt állapota
LUIS minden adatkészlet utolsó vizsgálat állapotát követi nyomon. Tartalmazzák a legutóbbi futtatás méretét (a kötegben utterances száma), dátum és az utolsó eredményét (sikeres előre jelzett utterances száma).

<a name="sections-of-the-results-chart"></a>
## <a name="batch-test-results"></a>Kötegelt teszt eredményei
A kötegelt tesztelési eredménye pont grafikon, egy hiba mátrix néven ismert. Ez a diagram a fájl és az aktuális modell előre jelzett leképezés és entitások utterances 4 – kétutas összehasonlítása. 

Az adatpontok a **téves pozitív** és **hamis negatív** szakaszok jelzi a hibákat, amelynek meg kell vizsgálni. Ha minden adatpontok a **igaz pozitív** és **igaz negatív** szakaszban, akkor az alkalmazás pontossága tökéletes megoldás az adatkészlethez.

![Diagram négy részből áll](./media/luis-concept-batch-test/chart-sections.png)

Ez a diagram segít az aktuális képzési helytelenül alapján, amely LUIS előrejelzi utterances található. Az eredmények a diagram régiónként jelennek meg. Válassza ki a diagramot úgy, hogy tekintse át a utterance adatokat, vagy jelölje ki a régió nevét, az adott régióban utterance eredményeinek áttekintése egyes pontjait.

![Kötegelt tesztelése](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Eredmények hibái
A kötegelt teszt utalnak, hogy a rendszer nem jelzett másolóeszközt leírtaknak megfelelően leképezések. Hibák a diagram a két piros szakaszban szerepelnek. 

A téves pozitív szakasz azt jelzi, hogy egy utterance megfelelő egy leképezés vagy entitás pedig nem kellett volna. A hamis negatív azt jelzi, hogy egy utterance nem egyezik a leképezés vagy entitás kell volna. 

## <a name="fixing-batch-errors"></a>Kötegelt hibák elhárítása
Ha nincsenek hibák, a kötegelt teszteléshez, akkor is további utterances hozzáadása megjelölésű, és/vagy címke további utterances érdekében ellenőrizze a leképezések megkülönböztetését LUIS entitással. Ha hozzáadta utterances, és címkével őket, és továbbra is get előrejelzés hibáinak kötegelt tesztelése, vegyen fel egy [kifejezéslista](luis-concept-feature.md) tartományspecifikus szóhasználatának gyorsabban további LUIS segítségével a szolgáltatás. 

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [egy kötegelt tesztelése](luis-how-to-batch-test.md)