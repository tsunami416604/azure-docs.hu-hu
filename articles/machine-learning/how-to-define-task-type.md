---
title: Gépi tanulási feladat definiálása automatizált gépi tanulási futtatáshoz
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan határozhat meg gépi tanulási feladatot egy automatizált gépi tanulási futtatáshoz
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: cb2fbcda7f30db24b876a66df22071f14df74814
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653249"
---
# <a name="how-to-define-a-machine-learning-task"></a>Gépi tanulási feladat definiálása 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan határozhatja meg a gépi tanulás támogatott feladatait, és hogyan definiálhatja őket egy automatizált gépi tanulás (automatizált ML) kísérlet futtatásához.


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>Mi az a Machine learning-feladat?

A gépi tanulási tevékenység a prediktív modell létrehozásával megoldott probléma típusát jelöli. Az automatikus ML három különböző típusú feladatot támogat, többek között a besorolást, a regressziót és az idősorozat-előrejelzést.

Feladat típusa| Leírás| Példa
----|----|----
Osztályozás | Az adatkészletben lévő adott sor kategóriájának előrejelzésére szolgáló feladat. | Csalás észlelése bankkártyán. A cél oszlop **csalást észlelt** az *igaz* vagy *hamis*kategóriákkal. Ebben az esetben az adatsorokat igaz vagy hamis értékként osztályozjuk.
Regresszió | Folyamatos mennyiségű kimenet előrejelzésére szolgáló feladat. | Az autók költsége a funkciói alapján, a célként megadott oszlop **ára**.
Előrejelzések |A jövőbeli trendek irányának meghatározására szolgáló feladat a megalapozott becslések meghozatalához.| Előrejelzési energia iránti kereslet a következő 48 órában. A cél oszlop **igénybe** vehető, és a rendszer az előre jelzett értékeket használja az energiaszükséglet mintázatának megjelenítésére.

Az automatikus ML a következő algoritmusokat támogatja az automatizálási és hangolási folyamat során. Felhasználóként nem kell megadnia az algoritmust.

Osztályozás | Regresszió | Idősoros előrejelzés
-- |-- |--
[Logisztikai regresszió](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rugalmas háló](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rugalmas háló](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradiens fokozása](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradiens fokozása](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradiens fokozása](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)|[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)
[Legközelebbi szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Legközelebbi szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Legközelebbi szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineáris SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS-lasszó](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS-lasszó](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C – a vektoros besorolás (SVC) támogatása](https://scikit-learn.org/stable/modules/svm.html#classification)|[Sztochasztikus gradiens leereszkedés (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Sztochasztikus gradiens leereszkedés (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN osztályozó](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN lineáris osztályozó](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineáris Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineáris Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naiv Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Sztochasztikus gradiens leereszkedés (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>A feladat típusának beállítása
Az automatikus ML-kísérletek feladattípusát az SDK-val vagy a Azure Machine Learning Studióval is beállíthatja.

A `task` `AutoMLConfig` kísérlet típusának megadásához használja a konstruktor paraméterét.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

A feladatot a Azure Machine Learning Studióban állíthatja be az automatikus ML kísérlet futtatásának részeként. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Feladattípus kiválasztása](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>További lépések

+ További információ a Azure Machine Learning [automatizált ml](concept-automated-ml.md) -ről.
+ További információ az [idősorozat-előrejelzési modell automatikus képzéséről](how-to-auto-train-forecast.md) Azure Machine learning
+ Próbálja ki az [automatizált Machine learning besorolási](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) oktatóanyagot.
+ Próbálja ki az [automatizált Machine learning regressziós](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb) minta jegyzetfüzetet.

