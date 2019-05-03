---
title: 'Multiclass logisztikai regresszió: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogy a logisztikai regressziós osztályú modul használata az Azure Machine Learning szolgáltatás több értékeinek használható egy logisztikai regressziós modell létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ac4310e851808d6e6d89d1a2b506975eea3b1d69
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029325"
---
# <a name="multiclass-logistic-regression-module"></a>Multiclass logisztikai regressziós modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával hozzon létre egy logisztikai regressziós modellt, amely előre, több érték is használható.

Besorolás logisztikai regressziós módszer a tanítás és címkézett adatkészlet tehát igényel. A modell betanításához a modell és a címkézett adatkészlet egy modul bemeneteként biztosít például [Train Model](./train-model.md). A betanított modell használható új bemeneti példák értékeinek.

Az Azure Machine Learning is biztosít egy [Two-Class logisztikai regressziós](./two-class-logistic-regression.md) modult, amely megfelelő a bináris vagy dichotomous változók fájlbesorolási.

## <a name="about-multiclass-logistic-regression"></a>Tudnivalók a multiclass logisztikai regresszió

Logisztikai regressziós a jól ismert módszer statisztika, amely előre jelezni a valószínűsége annak az eredménye, szolgál, és általában esetén fájlosztályozási feladatokhoz. Az algoritmus igyekeznek az adatok egy logisztikai függvény által előre megbecsüli egy esemény előfordulásának valószínűségét. 

A multiclass logisztikai regressziós az osztályozó által igénybe vett több következmények előrejelzésére is használható.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurálja a multiclass logisztikai regresszió

1. Adja hozzá a **osztályú logisztikai regressziós** modult a kísérletvászonra.

2. Adja meg, hogyan a modellt úgy vélik a **létrehozási trainer módban** lehetőséget.

    + **Egyetlen paraméter**: Használja ezt a beállítást, ha tudja, hogyan szeretné konfigurálni a modell, és adjon meg egy meghatározott készletének argumentumokkal értékek.

    + **A paraméter tartomány**: Használja ezt a beállítást, ha nem biztos a legjobb paraméterek, és szeretné használni a paraméteres.

3. **Optimalizálás tolerancia**, adja meg az optimalizáló convergence tartozó küszöbérték. Ha az ismétlések között továbbfejlesztése éri el a küszöbértéket, az algoritmus leállítja, és adja vissza az aktuális modell.

4. **L1 regularizációs súly**, **L2 regularizációs súly**: Írja be a regularizációs paraméterek L1 és L2 használandó értéket. Egy nem nulla értéket is ajánlott.

    Regularizációs a módszer a overfitting által büntetése modellek rendkívül együttható értékekkel. Regularizációs működik, a társított együttható értékeket a feltételezést hiba büntetés hozzáadásával. Egy rendkívül együttható értékek pontos modellhez lenne több büntetik, de kevésbé pontos modell korlátozóbb értékekkel kevesebb lenne büntetik.

     L1 és L2 regularizációs különböző következményekkel járhat, és használja. L1 alkalmazhatók a ritka modellek, amelyek akkor hasznos, ha nagy dimenziós adatok használata. Ezzel szemben a L2 regularizációs célszerű a nem ritka adatok számára.  Ez az algoritmus támogat L1 és L2 regularizációs értékek lineáris kombinációját: azt jelenti, ha `x = L1` és `y = L2`, `ax + by = c` lineáris leforgása alatt a regularizációs feltételeket határozza meg.

     Lineáris különböző kombinációit L1 és L2 feltételek lett kidolgozták logisztikai regressziós modellek, mint például [rugalmas nettó regularizációs](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Véletlenszerű szám kezdőérték**: Írja be a használandó a kezdőérték az algoritmus Ha azt szeretné, hogy az eredmények futtatások megismételhető kell egész szám. Ellenkező esetben a rendszer órája értéket használja, a kezdőérték, amelyek az ugyanazon kísérletben, a futtatások némileg különböző eredményeket hozhatnak.

8. Csatlakozzon a címkézett adatkészletet, és a vonat modulok egyike:

    + Ha **létrehozási trainer módban** való **egyetlen paramétert**, használja a [tanítási modell](./train-model.md) modul.

9. Futtassa a kísérletet.

## <a name="results"></a>Results (Eredmények)

Képzési befejezése után is a modell paraméterek együtt a szolgáltatás súlyok származó képzési összefoglaló, kattintson a jobb gombbal a kimenete a [tanítási modell](./train-model.md) modult, majd válassza ki **Visualize**.


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 