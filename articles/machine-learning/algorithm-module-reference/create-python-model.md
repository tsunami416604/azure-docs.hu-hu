---
title: 'Python-modell létrehozása: Modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre egy egyéni modellezési vagy adatfeldolgozási modult a Python modell létrehozása modul használatával az Azure Machine Learningben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c8be0882452dc120f538394a5481769e26e3fa15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682804"
---
# <a name="create-python-model-module"></a>Pythonmodell-modul létrehozása

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ismerje meg, hogyan hozhat létre egy nem képzetlen modellt egy Python-parancsfájlból a Python-modell létrehozása segítségével. A modell talapzatára alapozhatja bármely tanuló, amely szerepel egy Python-csomag az Azure Machine Learning designer környezetben. 

A modell létrehozása után a [Betanítási modell](train-model.md) segítségével betaníthatja a modellt egy adatkészleten, mint bármely más tanuló az Azure Machine Learningben. A betanított modell átadható [a score modell](score-model.md) előrejelzéseket. Ezután mentheti a betanított modellt, és közzéteheti a pontozási munkafolyamatot webszolgáltatásként.

> [!WARNING]
> Jelenleg nem lehet átadni a Python-modell pontozott eredményeit [a Modell kiértékelése című modellnek.](evaluate-model.md) Ha ki kell értékelnie egy modellt, írhat egy egyéni Python-parancsfájlt, és futtathatja azt a [Python-parancsfájl végrehajtása](execute-python-script.md) modul használatával.  


## <a name="configure-the-module"></a>A modul konfigurálása

A modul használatához a Python köztes vagy szakértői ismerete szükséges. A modul támogatja az Azure Machine Learningben már telepített Python-csomagokban szereplő tanulók használatát. Tekintse meg az előtelepített Python-csomaglistát a [Python-parancsfájl végrehajtása című témakörben.](execute-python-script.md)

> [!NOTE]
> Kérjük, legyen nagyon óvatos a parancsfájl írásakor, és győződjön meg arról, hogy nincs szintaktikai hiba, például nem deklarált objektum vagy nem importált modul használata.

> [!NOTE]
Fordítson különös figyelmet az előre telepített modulok listájára is a [Python-parancsfájl végrehajtása listában.](execute-python-script.md) Csak előre telepített modulok importálása. Kérjük, ne telepítsen további csomagokat, mint például a "pip install xgboost" ebben a szkriptben, különben hibák merülnek fel, amikor modelleket olvas down-stream modulokban.
  
Ez a cikk bemutatja, hogyan lehet használni a **Python-modell létrehozása** egy egyszerű folyamat. Íme egy diagram a csővezetékről:

![Python-modell létrehozása diagramja](./media/module/create-python-model.png)

1. Válassza **a Python-modell létrehozása**lehetőséget, és a modellezési vagy adatkezelési folyamat megvalósításához szerkesztheti a parancsfájlt. A modell alapján bármely tanuló, amely szerepel egy Python-csomag az Azure Machine Learning-környezetben.

> [!NOTE]
> Kérjük, fordítson különös figyelmet a megjegyzések minta kódot a forgatókönyvet, és győződjön meg róla, a szkript szigorúan követi a követelményt, beleértve az osztály nevét, módszerek, valamint a módszer aláírása. Az erőszak kivételeket von el. 

   A következő minta kód a két osztályú Naive Bayes osztályozó használja a népszerű *sklearn* csomag:

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

1. Csatlakoztassa az **imént** létrehozott Python modell létrehozása modult a modell és a score modell **betanításához.** **Train Model**

1. Ha ki kell értékelnie a modellt, adjon hozzá egy [Python-parancsfájl végrehajtása](execute-python-script.md) modult, és szerkesztheti a Python-parancsfájlt.

   A következő parancsfájl mintakiértékelési kód:

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

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 