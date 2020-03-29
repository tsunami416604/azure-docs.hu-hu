---
title: Automatizált ML távoli számítási célok
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre modelleket automatizált gépi tanulással egy Azure Machine Learning távszámítási célon az Azure Machine Learning segítségével
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 9e499d609a3f78dc5f422b9ed90df09be30f2e7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080411"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Modellek betanítása automatizált gépi tanulással a felhőben

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure Machine Learningben a modellt különböző típusú, ön által kezelt számítási erőforrásokra taníthatja be. A számítási cél lehet egy helyi számítógép vagy egy erőforrás a felhőben.

A gépi tanulási kísérlet et egyszerűen skálázhatja vagy kicsinyítheti további számítási célok, például az Azure Machine Learning Compute (AmlCompute) hozzáadásával. Az AmlCompute egy felügyelt számítási infrastruktúra, amellyel egyszerűen hozhat létre egy vagy több csomópontos számításokat.

Ebben a cikkben megtudhatja, hogyan hozhat létre egy modellt az AmlCompute automatikus ml használatával.

## <a name="how-does-remote-differ-from-local"></a>Miben különbözik a távoli a helyitől?

A "[Besorolási modell betanítása automatizált gépi tanulással](tutorial-auto-train-models.md)" című oktatóanyag bemutatja, hogyan használhatja a helyi számítógépet egy modell automatikus ml-rel történő betanításához. A munkafolyamat helyi betanítása esetén a távoli célokra is vonatkozik. A távoli számítással azonban az automatikus gépi tanulási kísérletek iterációi aszinkron módon történnek. Ez a funkció lehetővé teszi egy adott iteráció megszakítását, a végrehajtás állapotának figyelését, vagy a Jupyter-jegyzetfüzet más celláin végzett munkát. A távolból történő betanításhoz először hozzon létre egy távoli számítási célt, például az AmlCompute-t. Ezután konfigurálja a távoli erőforrást, és küldje el a kódot ott.

Ez a cikk bemutatja az automatikus ml-kísérlet távoli AmlCompute célon való futtatásához szükséges további lépéseket. A munkaterület-objektum `ws`, az oktatóanyagból a kódban itt használatos.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Erőforrás létrehozása

Hozza [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py) létre a célt`ws`a munkaterületen ( ) , ha még nem létezik.

**Becsült idő**: Az AmlCompute cél létrehozása körülbelül 5 percet vesz igénybe.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Most már használhatja az `compute_target` objektumot távoli számítási célként.

A fürtnév-korlátozások a következők:
+ 64 karakternél rövidebbnek kell lennie.
+ A következő karakterek egyike `\` sem tartalmazható: ~ ! @ # $ % ^ & * ( \\ \\ ) = + _ [ ] { } | : \' \\" ,  < > / ?. `

## <a name="access-data-using-tabulardataset-function"></a>Adatok elérése a TáblázatosAdatkészlet függvénnyel

A megadott [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) training_data a címkénél és a [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)címkén, amelyek et a rendszer automatikus hibajelzőnek ad át a . A `TabularDataset` `from_delimited_files`metódus alapértelmezés szerint `infer_column_types` igaz értékre állítja a beállítást, amely automatikusan következtet az oszlopok típusára. 

Ha manuálisan szeretné beállítani az oszloptípusokat, `set_column_types` beállíthatja az argumentumot úgy, hogy manuálisan állítsa be az egyes oszlopok típusát. A következő kódmintában az adatok a sklearn csomagból származnak.

```python
from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd
import os

# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists('project_folder'):
    os.makedirs('project_folder')

X = pd.DataFrame(data_train.data[100:,:])
y = pd.DataFrame(data_train.target[100:])

# merge X and y
label = "digit"
X[label] = y

training_data = X

training_data.to_csv('data/digits.csv')
ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

training_data = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/digits.csv'))
```

## <a name="configure-experiment"></a>Kísérlet konfigurálása
Adja meg `AutoMLConfig`a beállításait.  (Lásd [a paraméterek teljes listáját](how-to-configure-auto-train.md#configure-experiment) és lehetséges értékeiket.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "experiment_timeout_minutes" : 20,
    "enable_early_stopping" : True,
    "iteration_timeout_minutes": 10,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "max_concurrent_iterations": 10,
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             training_data=training_data,
                             label_column_name=label,
                             **automl_settings,
                             )
```

## <a name="submit-training-experiment"></a>Benyújtási betanítási kísérlet

Most küldje el a konfigurációt, hogy automatikusan válassza ki az algoritmust, a hyper paramétereket, és betanítsa a modellt.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

A következő példához hasonló kimenet jelenik meg:

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


## <a name="explore-results"></a>Az eredmények megismerése

Használhatja ugyanazt a [Jupyter widget,](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) ahogy [az a képzési bemutató,](tutorial-auto-train-models.md#explore-the-results) hogy egy grafikont és táblázatot az eredmények.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Itt látható a vezérlő statikus képe.  A jegyzetfüzetben a táblázat bármely sorára kattintva megtekintheti a futtatási tulajdonságokés kimeneti naplók futtatását.   A diagram feletti legördülő menüsegítségével megtekintheti az egyes iterációkhoz rendelkezésre álló metrikák grafikonját is.

![vezérlő táblázata](./media/how-to-auto-train-remote/table.png)
![vezérlő ábrázolása](./media/how-to-auto-train-remote/plot.png)

A widget egy URL-t jelenít meg, amelynek segítségével megtekintheti és felfedezheti az egyes futtatási részleteket.  

Ha nem jupyter-jegyzetfüzetben van, megjelenítheti az URL-címet a futtatásból:

```
remote_run.get_portal_url()
```

Ugyanezaz információk érhetők el a munkaterületen.  Ha többet szeretne megtudni ezekről az eredményekről, [olvassa el Az automatikus gépi tanulási eredmények ismertetése című témakört.](how-to-understand-automated-ml.md)

## <a name="example"></a>Példa

A következő [jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) ismerteti a cikkben szereplő fogalmakat.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* További információ [az automatikus betanítás beállításainak konfigurálásáról.](how-to-configure-auto-train.md)
* Tekintse meg a modellértelmezhetőségi funkciók [engedélyezésének útmutatóját](how-to-machine-learning-interpretability-automl.md) az automatizált gépi tanulási kísérletekben.
