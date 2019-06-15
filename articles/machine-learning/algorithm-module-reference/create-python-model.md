---
title: 'Python-modell létrehozása: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Útmutató az Azure Machine Learning szolgáltatás a Python-modell létrehozása modell használatával hozzon létre egyéni modellezési vagy adatfeldolgozási modult.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ea9b50cede3e2d264ca0476b6a987ca6896c3c79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029745"
---
# <a name="create-python-model"></a>Python-modell létrehozása

Ez a cikk ismerteti, hogyan használható a **létrehozása Python-modell** modul egy kellő modell létrehozása a Python-szkriptet. 

Minden tanuló, amely egy Python-csomag az Azure Machine Learning-környezetben megtalálható is a modell alapján. 

Miután létrehozta a modellt, használhat [Train Model](train-model.md) egy adatkészleten a modell betanításához, mint bármely más learner az Azure Machine Learning. A betanított modell átadható [Score Model](score-model.md) előrejelzéseket a modell használatával. A betanított modell menthető, és a pontozási munkafolyamat közzétehetők webszolgáltatásként.

> [!WARNING]
> Jelenleg nem lehet átadni egy Python-modell, pontozott eredményeit [Evaluate Model](evaluate-model.md). A modell értékelése van szüksége, ha egyéni Python szkriptet, és futtathatja írhat a [Python-szkript végrehajtására](execute-python-script.md) modul.  


## <a name="how-to-configure-create-python-model"></a>Python-modell létrehozása konfigurálása

Ez a modul használatához Python közbenső vagy szakértői ismerete. A modul minden tanuló, amely megtalálható a Python-csomagok már telepítve van az Azure Machine Learning használatát támogatja. Tekintse meg az előre telepített Python csomaglista [Python-szkript végrehajtására](execute-python-script.md).
  

A cikkből megtudhatja, hogyan használható a **létrehozása Python-modell** egy egyszerű kísérlet a. Az alábbi, a grafikon a kísérlet.

![create-python-model](./media/module/aml-create-python-model.png)

1.  Kattintson a **létrehozása Python-modell**, szerkessze a szkriptet a modellezés megvalósításához, vagy az adatkezelést feldolgozni. Minden tanuló, amely az Azure Machine Learning-környezetben a Python-csomag része is a modell alapján.


    Az alábbiakban a két osztályú Naiv Bayes osztályozó mintakód használatával a népszerű *sklearn* csomagot.

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


2. Csatlakozás a **létrehozása Python-modell** modul az imént létrehozott egy **tanítási modell** és **pontszám modell**

3. Ha a modell kiértékelésére van szüksége, adjon hozzá egy [Python-szkript végrehajtására](execute-python-script.md) és szerkesztheti az értékelés végrehajtására, a Python-szkriptet.

Az alábbi, a kiértékelési mintakódot.

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
