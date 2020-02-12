---
title: 'Többosztályos logisztikai regresszió: modul leírása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használható a többosztályos logisztikai regressziós modul a Azure Machine Learning egy logisztikai regressziós modell létrehozásához, amely több érték előrejelzésére is használható.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a697c1e43ccd486f8c98399ea9065902bd247f7d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152142"
---
# <a name="multiclass-logistic-regression-module"></a>Többosztályos logisztikai regressziós modul

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal létrehozhat egy logisztikai regressziós modellt, amely több érték előrejelzésére is használható.

A logisztikai regressziót használó besorolás felügyelt tanulási módszer, ezért címkével ellátott adatkészletet igényel. A modellt úgy kell betanítani, hogy a modellt és a címkézett adatkészletet bemenetként adja meg egy modulhoz, például a [betanítási modellhez](./train-model.md). A betanított modell ezután felhasználható az új bemeneti példák értékeinek előrejelzésére.

A Azure Machine Learning egy [kétosztályos logisztikai regressziós](./two-class-logistic-regression.md) modult is biztosít, amely a bináris vagy dichotóm változók besorolására alkalmas.

## <a name="about-multiclass-logistic-regression"></a>A többosztályos logisztikai regresszió ismertetése

A logisztikai regresszió egy jól ismert módszer a statisztikában, amely az eredmény valószínűségének előrejelzésére szolgál, és népszerű a besorolási feladatokhoz. Az algoritmus azt jelzi, hogy egy esemény előfordulásának valószínűsége egy logisztikai függvénynek megfelelő adat betöltésével várható. 

A többosztályos logisztikai regresszió esetében az osztályozó használatával több eredmény is kiszámítható.

## <a name="configure-a-multiclass-logistic-regression"></a>Többosztályos logisztikai regresszió konfigurálása

1. Adja hozzá a **többosztályos logisztikai regressziós** modult a folyamathoz.

2. Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.

    + **Egyetlen paraméter**: akkor használja ezt a beállítást, ha tudja, hogyan szeretné konfigurálni a modellt, és az értékek egy adott készletét argumentumként adja meg.

    + **Paraméter tartománya**: akkor használja ezt a beállítást, ha nem biztos benne, hogy a legjobb paramétereket használja, és szeretné használni a paramétert.

3. **Optimalizálási tolerancia**: az optimalizáló konvergenciájának küszöbértékét határozza meg. Ha az ismétlések közötti javulás kisebb a küszöbértéknél, az algoritmus leáll, és az aktuális modellt adja vissza.

4. **L1 regularizációs súlyozás**, **L2 regularizációs súlya**: adjon meg egy értéket, amelyet az L1 és az L2 regularizációs paraméterekhez kell használni. A nem nulla értékű érték mindkét esetében ajánlott.

    A regularizációs olyan módszer, amellyel megelőzhető a rendkívül hatékony értékekkel rendelkező modellek szankcionálása. A regularizációs úgy működik, hogy a hipotézis hibája alapján együttható értékekkel társított büntetést ad hozzá. A rendkívül hatékony értékekkel rendelkező pontos modell többek között büntethető, de a kevésbé pontos, konzervatív értékekkel rendelkező modellek kevesebbet büntetik.

     Az L1 és az L2 regularizációs különböző effektusokkal és felhasználással rendelkeznek. Az L1 a ritka modellekre is alkalmazható, amelyek hasznosak lehetnek a nagy dimenziós adattípusok használatakor. Ezzel szemben az L2 regularizációs a nem ritka adatértékekhez ajánlott.  Ez az algoritmus az L1 és az L2 regularizációs értékek lineáris kombinációját támogatja: vagyis ha `x = L1` és `y = L2`, `ax + by = c` meghatározza a regularizációs feltételeinek lineáris hosszát.

     Az L1 és a L2 kifejezések különböző lineáris kombinációit tervezték logisztikai regressziós modellekhez, például a [rugalmas háló regularizációs](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Véletlenszerű számú mag**: adjon meg egy egész számot, amelyet az algoritmus magjaként kíván használni, ha azt szeretné, hogy az eredmények a futtatások során ismételhetők legyenek. Ellenkező esetben a rendszer egy rendszeróra-értéket használ a magokként, amely némileg eltérő eredményeket eredményezhet ugyanazon folyamat futtatásában.

8. Egy címkézett adatkészlet és az egyik betanítási modul összekötése:

    + Ha az **oktatói módot** **egyetlen paraméterként**állítja be, használja a [Train Model](./train-model.md) modult.

9. A folyamat futtatása.



## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 