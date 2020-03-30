---
title: 'Többosztályos logisztikai regresszió: modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a többosztályos logisztikai regressziós modult az Azure Machine Learningben egy logisztikai regressziós modell létrehozásához, amely több érték előrejelzésére használható.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ca446b0ab67a8a202c1f4d505262660ac55f42db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456165"
---
# <a name="multiclass-logistic-regression-module"></a>Többosztályos logisztikai regressziós modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal hozzon létre egy logisztikai regressziós modellt, amely több érték előrejelzésére használható.

A logisztikai regresszió használatával történő besorolás felügyelt tanulási módszer, ezért egy címkézett adatkészletet igényel. A modell betanításával adja meg a modell és a címkézett adatkészlet bemenetként egy modul, például [a train modell.](./train-model.md) A betanított modell ezután új bemeneti példák értékeinek előrejelzésére használható.

Az Azure Machine Learning egy [kétosztályos logisztikai regressziós](./two-class-logistic-regression.md) modult is biztosít, amely bináris vagy dichotomous változók besorolására alkalmas.

## <a name="about-multiclass-logistic-regression"></a>Többosztályos logisztikai regresszió –

A logisztikai regresszió egy jól ismert módszer a statisztikákban, amely az eredmény valószínűségének előrejelzésére szolgál, és népszerű a besorolási feladatokban. Az algoritmus előre jelzi egy esemény előfordulásának valószínűségét azáltal, hogy adatokat illeszt egy logisztikai függvényhez. 

A többosztályos logisztikai regresszió, az osztályozó több kimenetel előrejelzésére használható.

## <a name="configure-a-multiclass-logistic-regression"></a>Többosztályos logisztikai regresszió konfigurálása

1. Adja hozzá a **többosztályos logisztikai regressziós modult** a folyamathoz.

2. Adja meg, hogyan szeretné betanítani a modellt az **Oktatói mód létrehozása** beállítás beállításával.

    + **Egyetlen paraméter**: Akkor használja ezt a beállítást, ha tudja, hogyan szeretné konfigurálni a modellt, és argumentumként adjon meg egy adott értékkészletet.

    + **Paramétertartomány**: Akkor válassza ezt a lehetőséget, ha nem biztos a legjobb paraméterekben, és paraméterkeresést szeretne futtatni. Válasszon ki egy értéktartományt, amely felett iterálni, és a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterálja az összes lehetséges kombinációa a megadott beállítások meghatározásához a hiperparaméterek, amelyek az optimális eredményt.  

3. **Optimalizálási tolerancia**, adja meg az optimalizáló konvergenciájának küszöbértékét. Ha az ismétlések közötti javulás kisebb, mint a küszöbérték, az algoritmus leáll, és az aktuális modellt adja vissza.

4. **L1 regularizációs súly**, **L2 regularizációs súly**: Írja be az L1 és L2 regularizációs paraméterekhez használandó értéket. Mindkettőhez nem nulla érték ajánlott.

    A szabályosítás olyan módszer, amely megakadályozza a túlszerelést a szélsőséges együtthatóértékű modellek szankcionálásával. A szabályosítás úgy működik, hogy az együttható értékekhez kapcsolódó büntetést hozzáadja a hipotézis hibájához. Egy szélsőséges együtthatóértékű pontos modellt jobban büntetnének, de egy kevésbé pontos, konzervatívabb értékekkel rendelkező modellt kevesebbbüntetéssel sújtanának.

     L1 és L2 regularization különböző hatások és felhasználások. Az L1 ritka modellekre alkalmazható, ami akkor hasznos, ha nagy dimenziós adatokkal dolgozik. Ezzel szemben az L2-es regisztálás előnyösebb a nem ritka adatok esetében.  Ez az algoritmus támogatja az L1 és L2 regularizációs `x = L1` `y = L2`értékek `ax + by = c` lineáris kombinációját: azaz, ha és , meghatározza a regisztizálási kifejezések lineáris tartományát.

     Az L1 és L2 kifejezések különböző lineáris kombinációit dolgozták ki a logisztikai regressziós modellekhez, mint például a [rugalmas hálós regularizáció.](https://wikipedia.org/wiki/Elastic_net_regularization)

6. **Véletlenszámmag:** Írjon be egy egész értéket, amelyet az algoritmus magjaként használ, ha azt szeretné, hogy az eredmények megismételhetők legyenek a futtatások on keresztül. Ellenkező esetben a rendszeróra értéke a vetőmag, amely képes némileg eltérő eredményeket fut az azonos folyamat.

8. Láncolt adatkészlet és a vonatmodulok egyikének csatlakoztatása:

    + Ha az **Oktató létrehozása módot** egy **paraméterre állítja**be, használja a [Betanítási modell](./train-model.md) modult.

9. Küldje el a folyamatot.



## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 