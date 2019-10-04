---
title: 'Többosztályos logisztikai regresszió: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható a többosztályos logisztikai regressziós modul a Azure Machine Learning szolgáltatásban egy logisztikai regressziós modell létrehozásához, amely több érték előrejelzésére is használható.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: d51bc48944204b4c7c50790949927849869f26fc
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128632"
---
# <a name="multiclass-logistic-regression-module"></a>Többosztályos logisztikai regressziós modul

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal létrehozhat egy logisztikai regressziós modellt, amely több érték előrejelzésére is használható.

A logisztikai regressziót használó besorolás felügyelt tanulási módszer, ezért címkével ellátott adatkészletet igényel. A modellt úgy kell betanítani, hogy a modellt és a címkézett adatkészletet bemenetként adja meg [](./train-model.md)egy modulhoz, például a betanítási modellhez. A betanított modell ezután felhasználható az új bemeneti példák értékeinek előrejelzésére.

A Azure Machine Learning egy kétosztályos [logisztikai regressziós](./two-class-logistic-regression.md) modult is biztosít, amely a bináris vagy dichotóm változók besorolására alkalmas.

## <a name="about-multiclass-logistic-regression"></a>A többosztályos logisztikai regresszió ismertetése

A logisztikai regresszió egy jól ismert módszer a statisztikában, amely az eredmény valószínűségének előrejelzésére szolgál, és népszerű a besorolási feladatokhoz. Az algoritmus azt jelzi, hogy egy esemény előfordulásának valószínűsége egy logisztikai függvénynek megfelelő adat betöltésével várható. 

A többosztályos logisztikai regresszió esetében az osztályozó használatával több eredmény is kiszámítható.

## <a name="configure-a-multiclass-logistic-regression"></a>Többosztályos logisztikai regresszió konfigurálása

1. Adja hozzá a többosztályos logisztikai regressziós modult a kísérlethez.

2. Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.

    + **Egyetlen paraméter**: Akkor használja ezt a beállítást, ha tudja, hogyan szeretné konfigurálni a modellt, és az értékek egy adott halmazát adja meg argumentumként.

    + **Paraméter tartománya**: Akkor használja ezt a beállítást, ha nem biztos benne, hogy a legjobb paramétereket használja, és szeretné használni a paramétert.

3. **Optimalizálási tolerancia**: az optimalizáló konvergenciájának küszöbértékét határozza meg. Ha az ismétlések közötti javulás kisebb a küszöbértéknél, az algoritmus leáll, és az aktuális modellt adja vissza.

4. **L1 regularizációs súlyozás**, **L2 regularizációs súlyozás**: Adja meg az L1 és az L2 regularizációs paraméterekhez használandó értéket. A nem nulla értékű érték mindkét esetében ajánlott.

    A regularizációs olyan módszer, amellyel megelőzhető a rendkívül hatékony értékekkel rendelkező modellek szankcionálása. A regularizációs úgy működik, hogy a hipotézis hibája alapján együttható értékekkel társított büntetést ad hozzá. A rendkívül hatékony értékekkel rendelkező pontos modell többek között büntethető, de a kevésbé pontos, konzervatív értékekkel rendelkező modellek kevesebbet büntetik.

     Az L1 és az L2 regularizációs különböző effektusokkal és felhasználással rendelkeznek. Az L1 a ritka modellekre is alkalmazható, amelyek hasznosak lehetnek a nagy dimenziós adattípusok használatakor. Ezzel szemben az L2 regularizációs a nem ritka adatértékekhez ajánlott.  Ez az algoritmus az L1 és az L2 regularizációs értékek lineáris kombinációját támogatja: Ez a `x = L1` ( `y = L2`z `ax + by = c` ), ha és, meghatározza a regularizációs-kifejezések lineáris kiterjedését.

     Az L1 és a L2 kifejezések különböző lineáris kombinációit tervezték logisztikai regressziós modellekhez, például a [rugalmas háló regularizációs](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Véletlenszerű számú mag**: Adjon meg egy egész számot, amelyet az algoritmus magjaként kíván használni, ha azt szeretné, hogy az eredmények a futtatások során ismételhetőek legyenek. Ellenkező esetben a rendszer órajel-értéket használ a magokként, ami némileg eltérő eredményeket eredményezhet ugyanazon kísérlet futtatásában.

8. Egy címkézett adatkészlet és az egyik betanítási modul összekötése:

    + Ha az **oktatói módot** **egyetlen paraméterként**állítja be, használja a [Train Model](./train-model.md) modult.

9. Futtassa a kísérletet.

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után megtekintheti a modell paramétereinek összegzését, valamint a képzésből beszerzett funkciók súlyozását, a jobb gombbal kattintson a [Train Model](./train-model.md) modul kimenetére, majd válassza a **Megjelenítés**lehetőséget.


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 