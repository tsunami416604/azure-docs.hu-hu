---
title: Gépi tanulási feladat definiálása automatikus gépi tanulási futtatáshoz
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan definiálhat gépi tanulási feladatot egy automatizált gépi tanulási futtatáshoz
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: fef3f0347d963b8a7d5fe2f1ca771293e9c3d2a9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475497"
---
# <a name="how-to-define-a-machine-learning-task"></a>Gépi tanulási feladat definiálása 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megismerheti a támogatott gépi tanulási feladatokat, és hogyan definiálhatja őket egy automatizált gépi tanulási (automatikus ML) kísérlet futtatásához.


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>Mi az a gépi tanulási feladat?

A gépi tanulási feladat a megoldandó probléma típusát jelöli egy prediktív modell létrehozásával. Az automatikus rendszerhiba három különböző típusú feladatot támogat, beleértve a besorolást, a regressziót és az idősorozat-előrejelzést.

Tevékenység típusa| Leírás| Példa
----|----|----
Osztályozás | Feladat egy adott sor kategóriájának előrejelzésére egy adatkészletben. | Csalás felderítése hitelkártyán. A céloszlop az *Igaz* vagy *hamis*kategóriákkal **észlelt csalás** lesz. Ebben az esetben az adatok minden egyes sorát igaznak vagy hamisnak minősítjük.
Regresszió | Folyamatos mennyiségű kimenet előrejelzésének feladata. | Autó költsége alapján a funkciók, a cél oszlop lenne **az ár**.
Előrejelzések |A jövőbeli tendenciák irányának meghatározásában megalapozott becslések készítésének feladata.| Energiaszükséglést jósolunk a következő 48 órában. A céloszlop a **kereslet** lenne, és az előre jelzett értékeket használnák az energiaigény mintáinak megjelenítésére.

Az automatikus ml az automatizálási és hangolási folyamat során a következő algoritmusokat támogatja. Felhasználóként nincs szükség az algoritmus megadására.

Osztályozás | Regresszió | Idősoros előrejelzés
-- |-- |--
[Logisztikai regresszió](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rugalmas háló](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rugalmas háló](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Fény GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Fény GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Fény GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Színátmenet kiemelése](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Színátmenet kiemelése](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Színátmenet kiemelése](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)|[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Legközelebbi Szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Legközelebbi Szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Legközelebbi Szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineáris SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS-lasszó](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS-lasszó](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-támogatás vektor besorolás (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Sztochastikus gradiens (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Sztochastikus gradiens (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN osztályozó](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN regresszió](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN regresszió](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN lineáris osztályozó](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineáris regresszió](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineáris regresszió](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naiv Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Sztochastikus gradiens (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>A tevékenységtípus beállítása
Beállíthatja a feladat típusát az SDK-val vagy az Azure Machine Learning stúdióval végzett automatikus ml-kísérletekhez.

A `task` `AutoMLConfig` konstruktor ban lévő paraméter segítségével adja meg a kísérlet típusát.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Beállíthatja a feladatot az automatikus ml-es kísérlet létrehozása az Azure Machine Learning stúdióban. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Tevékenységtípus kijelölése](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>További lépések

+ További információ az [automatikus ml-ről](concept-automated-ml.md) az Azure Machine Learningben.
+ További információ [az idősorozat-előrejelzési modell automatikus betanításáról](how-to-auto-train-forecast.md) az Azure Machine Learningben
+ Próbálja ki az [automatikus gépi tanulási besorolás](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) oktatóanyagát.
+ Próbálja ki az [automatikus gépi tanulási regressziós](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb) mintanotebook.

