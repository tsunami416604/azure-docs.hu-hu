---
title: 'Python-modell létrehozása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre egyéni modellezési vagy adatfeldolgozási modult a Azure Machine Learning Python modell létrehozása moduljában.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 929938bba9c9512ecfd663a540cf4a7ebbf68e2b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371817"
---
# <a name="create-python-model-module"></a>Python-modell modul létrehozása

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ismerje meg, hogyan hozhat létre egy nem betanított modellt egy Python-szkriptből a Python-modell létrehozása modul használatával. A modellt a Azure Machine Learning Designer-környezetben található Python-csomagban szereplő bármelyik tanulón alapozhatja. 

A modell létrehozása után a [betanítási modell](train-model.md) segítségével betaníthatja a modellt egy adatkészleten, például a Azure Machine learning bármely más tanulója számára. A betanított modell átadható a [pontszám modellnek](score-model.md) az előrejelzések készítéséhez. Ezután mentheti a betanított modellt, és közzéteheti a pontozási munkafolyamatot webszolgáltatásként.

> [!WARNING]
> Jelenleg nem lehet átadni egy Python-modell pontozásos eredményeit a [modell kiértékeléséhez](evaluate-model.md). Ha ki kell értékelnie egy modellt, írhat egy egyéni Python-szkriptet, és futtathatja azt a [Python-szkript végrehajtása](execute-python-script.md) modul használatával.  


## <a name="configure-the-module"></a>A modul konfigurálása

A modul használatához a Python közbenső vagy szakértői ismerete szükséges. A modul támogatja a Azure Machine Learning-ban már telepített Python-csomagokban található bármely tanuló használatát. Tekintse meg az előre telepített Python-csomagok listáját a [Python-szkript végrehajtása](execute-python-script.md)című témakörben.
  

Ez a cikk bemutatja, hogyan használható a **Python-modell létrehozása** egyszerű folyamattal. Itt látható a folyamat ábrája:

![Python-modell létrehozási diagramja](./media/module/create-python-model.png)

1. Válassza a **Python-modell létrehozása**lehetőséget, és szerkessze a szkriptet a modellezés vagy az adatkezelési folyamat megvalósításához. A modellt a Azure Machine Learning-környezetben található Python-csomagban szereplő bármelyik tanulón alapozhatja.

   A kétosztályos naiv Bayes-osztályozó következő mintakód a népszerű *sklearn* -csomagot használja:

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

1. Csatlakoztassa a **Python modell létrehozása** modult, amelyet az imént hozott létre a modell és a **pontszám modell** **betanításához** .

1. Ha ki kell értékelnie a modellt, vegyen fel egy [Python-parancsfájlt](execute-python-script.md) , és szerkessze a Python-szkriptet.

   A következő parancsfájl minta-értékelési kód:

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

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 