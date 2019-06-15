---
title: Automatizált ML távoli számítási célnak
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan hozhat létre automatizált gépi tanulás használatával az Azure Machine Learning szolgáltatás egy Azure Machine Learning távoli számítási célnak
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6a18bdf3a2a1ccd60ff20d21ebd99f4f6e15e38f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65551341"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>A felhőalapú automatikus machine learning-modellek

Az Azure Machine Learning a számítási erőforrásokat, különböző típusú modellje betanításához. A számítási célnak lehet helyi számítógépen vagy egy számítógép, a felhőben.

Egyszerűen vertikális vagy horizontális felskálázáshoz a machine learning-kísérlet adjon hozzá további számítási célokhoz, például az Azure Machine Learning Compute (AmlCompute). AmlCompute egy felügyelt számítási infrastruktúra, amely lehetővé teszi, hogy egyszerűen hozzon létre egy egyetlen vagy több csomópontos számítási.

Ebből a cikkből megismerheti, hogyan hozhat létre automatizált gépi tanulás használatával AmlCompute modell.

## <a name="how-does-remote-differ-from-local"></a>Miben helyi távoli?

Az oktatóanyag "[egy automatizált gépi tanulással osztályozási modell betanításához](tutorial-auto-train-models.md)" bemutatja, hogyan használja a helyi számítógép automatikus gépi Tanulási modell.  A munkafolyamat betanításakor helyileg is, valamint a távoli tárolókra vonatkozik. Azonban a távoli számítási automatizált gépi Tanulási kísérlet ismétléseinek végrehajtása aszinkron módon történik. Ez a funkció lehetővé teszi, hogy egy adott iterációhoz visszavonja, tekintse meg a végrehajtási állapotát, vagy folytatja a munkát a többi cella Jupyter notebookot. Távolról betanítást, először hozzon létre egy távoli számítási célnak, például a AmlCompute. Ezután konfigurálja a távoli erőforrás, és küldje el a kódot.

Ez a cikk bemutatja az automatizált gépi Tanulási kísérlet futtatása egy távoli AmlCompute cél szükséges további lépéseket. A munkaterület objektum `ws`, az oktatóanyag használja Itt a kód egészében.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Erőforrás létrehozása

Hozza létre a AmlCompute cél a munkaterület (`ws`) Ha még nem létezik.  

**Becsült időtartam**: A AmlCompute cél létrehozása körülbelül 5 percet vesz igénybe.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl" #Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size = "STANDARD_D2_V2", 
                                                            # for GPU, use "STANDARD_NC6"
                                                            #vm_priority = 'lowpriority', # optional
                                                            max_nodes = 6)

compute_target = ComputeTarget.create(ws, amlcompute_cluster_name, provisioning_config)
    
# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(show_output = True, min_node_count = None, timeout_in_minutes = 20)
```

Most már használhatja a `compute_target` a távoli számítási célnak az objektumot.

Fürt neve korlátozások a következők:
+ 64 karakternél rövidebbnek kell lennie.  
+ Nem tartalmazza a következő karakterek egyikét sem: `\` ~! @ # $ % ^ & * () = + _ [] {} \\ \\ |};: \' \\", < > /?. `

## <a name="access-data-using-getdata-file"></a>Get_data fájl használatával érheti el adatait

Adja meg a betanítási adatok eléréséhez a távoli erőforrás. Automatizált machine learning-kísérletek távoli számítási fut, az adatok helyobjektum használatával kell egy `get_data()` függvény.  

A hozzáférés biztosításához tegye a következőket:
+ Hozzon létre tartalmazó get_data.py fájlt egy `get_data()` függvény 
+ Helyezze a fájlt a könyvtár abszolút elérési útnak elérhetőnek 

Adatok beolvasása a blob storage vagy a helyi lemezre a get_data.py fájlban kódot is magába foglalja. Az alábbi kódmintában az adatokat az sklearn csomag származik.

>[!Warning]
>Ha távoli számítási használ, akkor kell használni `get_data()` ahol az adatátalakítások történik. Az adatátalakítások további kódtárak telepítése get_data() részeként van szüksége, van-e további lépéseket kell követni. Tekintse meg a [automatikus – Machine Learning-adatelőkészítés minta notebook](https://aka.ms/aml-auto-ml-data-prep ) részleteiről.


```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-experiment"></a>Kísérlet konfigurálása

Adja meg a beállításokat a `AutoMLConfig`.  (Lásd a [paraméterek teljes listája](how-to-configure-auto-train.md#configure-experiment) és azok lehetséges értékei.)

A beállítások `run_configuration` értékre van állítva a `run_config` objektum, amely tartalmazza a dsvm-hez a konfiguráció.  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = compute_target,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                            )
```

### <a name="enable-model-explanations"></a>Modell magyarázatok engedélyezése

Állítsa be a választható `model_explainability` paramétert a `AutoMLConfig` konstruktor. Ezenkívül egy érvényesítési dataframe-objektumot kell átadni paraméterként `X_valid` modell explainability funkcióját.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = compute_target,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                             model_explainability=True,
                             X_valid = X_test
                            )
```

## <a name="submit-training-experiment"></a>Betanítási kísérlet elküldése

Most küldje el a konfiguráció ki automatikusan a algoritmus, a hyper-paraméterek, és a modell betanításához.

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Az alábbi példához hasonló kimenetet fog látni:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>Ismerje meg az eredmények

Egy, használhatja ugyanazt a Jupyter-widget [az oktatóprogram](tutorial-auto-train-models.md#explore-the-results) , a graph és az eredmények táblázatát.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```
Itt látható a vezérlő statikus képe.  A notebook kattintva megtekintheti a Futtatás tulajdonságait, és kimeneti naplók futtató a táblázatban minden olyan sorban.   A legördülő listában, a diagram felett segítségével is megtekintheti minden elérhető minden egyes ismétléskor metrikát.

![vezérlő táblázata](./media/how-to-auto-train-remote/table.png)
![vezérlő ábrázolása](./media/how-to-auto-train-remote/plot.png)

A widget használatával tekintse meg, és ismerje meg a Futtatás részletei az egyes URL-címet jeleníti meg.
 
### <a name="view-logs"></a>Naplók megtekintése

A DSVM alatt található naplók `/tmp/azureml_run/{iterationid}/azureml-logs`.

## <a name="best-model-explanation"></a>Ajánlott modell ismertetése

Modell magyarázata adatok beolvasása lehetővé teszi a modellek átlátható képet adnak a háttér-futó növelése részletes információinak megtekintéséhez. Ebben a példában csak a legjobb regressziós modellt a modell magyarázatok fogja futtatni. Ha a folyamat minden modell futtat, azt jelentős futási idő eredményez. Modell magyarázata adatokat tartalmazza:

* shap_values: A létrejövő Alakzatadatok lib magyarázata adatokat.
* expected_values: A modell X_train adatok beállítása a alkalmazni a várt értékkel.
* overall_summary: A modell szintű funkció fontossági értékek csökkenő sorrendbe rendezve.
* overall_imp: A szolgáltatások neveit, ugyanabban a sorrendben, ahogy overall_summary rendezve.
* per_class_summary: Az osztály szintű funkció fontossági értékek csökkenő sorrendbe rendezve. Csak a besorolási esethez érhető el.
* per_class_imp: A szolgáltatások neveit, ugyanabban a sorrendben, ahogy per_class_summary rendezve. Csak a besorolási esethez érhető el.

A következő kód használatával válassza ki a legjobb folyamatot, az ismétlések. A `get_output` metódus visszaadja a legjobb Futtatás és illesztett modell az utolsó hívás szélességhez tartozó.

```python
best_run, fitted_model = remote_run.get_output()
```

Importálás a `retrieve_model_explanation` funkciót, és futtassa a legjobb modellt.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Nyomtassa ki az eredményeket a `best_run` magyarázata változók meg szeretné jeleníteni.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

Nyomtatás a `best_run` magyarázata összefoglaló változók eredmények a következő kimenet.

![Modell explainability konzolkimenet](./media/how-to-auto-train-remote/expl-print.png)

A munkaterületen belül is a widget felhasználói felület, valamint a webes felhasználói felület az Azure Portalon keresztül a szolgáltatás fontosság jelenítheti meg.

![Modell explainability felhasználói felület](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Példa

A [how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) notebook mutatja be a jelen cikk fogalmait. 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg, [automatikus képzéshez beállításainak konfigurálása](how-to-configure-auto-train.md).
