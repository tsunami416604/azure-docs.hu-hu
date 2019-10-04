---
title: 'Python-modell létrehozása: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan hozhat létre egyéni modellezési vagy adatfeldolgozási modult a Azure Machine Learning szolgáltatásban a Python modell létrehozása című témakörben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: c6d7aabd41e9d0e872926adbbcb2d18332cb7d5e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128924"
---
# <a name="create-python-model"></a>Python-modell létrehozása

Ez a cikk bemutatja, hogyan hozhat létre egy nem betanított modellt egy Python-szkriptből a **Python-modell létrehozása** modul használatával. 

A modellt a Azure Machine Learning-környezetben található Python-csomagban szereplő bármelyik tanulón alapozhatja. 

A modell létrehozása után a betanítási [modell](train-model.md) segítségével betaníthatja a modellt egy adatkészleten, például a Azure Machine learning bármely más tanulója számára. A betanított modell átadható a [pontszám modellnek](score-model.md) , hogy a modell segítségével előrejelzéseket lehessen készíteni. A betanított modell így menthető, és a pontozási munkafolyamat webszolgáltatásként is közzétehető.

> [!WARNING]
> Jelenleg nem lehet átadni a Python-modell eredményes eredményeit a [modell](evaluate-model.md)kiértékeléséhez. Ha ki kell értékelnie egy modellt, írhat egyéni Python-szkriptet, és futtathatja azt a [Python-parancsfájl végrehajtása](execute-python-script.md) modullal.  


## <a name="how-to-configure-create-python-model"></a>A Python-modell létrehozásának beállítása

A modul használatához a Python közbenső vagy szakértői ismerete szükséges. A modul támogatja a Azure Machine Learning-ban már telepített Python-csomagok részét képező tanulók használatát. Lásd: előre telepített Python-csomagok listája a [Python-szkript végrehajtásához](execute-python-script.md).
  

Ez a cikk bemutatja, hogyan használható a **Python-modell létrehozása** egy egyszerű kísérlettel. Alább látható a kísérlet gráfja.

![create-python-model](./media/module/aml-create-python-model.png)

1.  Kattintson a **Python-modell létrehozása**lehetőségre, szerkessze a szkriptet a modellezés vagy az adatkezelési folyamat megvalósításához. A modellt a Azure Machine Learning környezetben található Python-csomagban található bármely tanulón alapozhatja.


    Az alábbi példa a kétosztályos naiv Bayes-osztályozó minta kódját mutatja be a népszerű *sklearn* -csomag használatával.

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. Csatlakoztassa az imént létrehozott **Python Model** -modult a betanítási **modellhez** és a **pontszám modellhez**

3. Ha ki kell értékelnie a modellt, vegyen fel egy [Python](execute-python-script.md) -szkriptet, és szerkessze a Python-szkriptet a kiértékelés megvalósításához.

Az alábbiakban a minta kiértékelési kódja látható.

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
