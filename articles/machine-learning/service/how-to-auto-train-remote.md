---
title: Automatikus ML távoli számítási célok
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre modelleket automatizált gépi tanulással egy Azure Machine Learning távoli számítási célra a Azure Machine Learning használatával
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8c50a1ba79fc07f62e319c6dceb75c32a9e8609f
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287108"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>A felhőalapú automatikus machine learning-modellek

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure Machine Learning a számítási erőforrásokat, különböző típusú modellje betanításához. A számítási cél lehet helyi számítógép vagy erőforrás a felhőben.

A gépi tanulási kísérletet könnyedén bővítheti vagy bővítheti további számítási célok hozzáadásával, például Azure Machine Learning számítással (AmlCompute). A AmlCompute egy felügyelt számítási infrastruktúra, amely lehetővé teszi, hogy egyszerűen hozzon létre egy vagy több csomópontos számítási módszert.

Ebből a cikkből megtudhatja, hogyan hozhat létre modellt automatizált ML-vel a AmlCompute használatával.

## <a name="how-does-remote-differ-from-local"></a>Miben helyi távoli?

A "[besorolási modell automatikus gépi tanulással](tutorial-auto-train-models.md)" című oktatóanyaga bemutatja, hogyan használható egy helyi számítógép egy modell automatikus ml-vel való betanításához. A munkafolyamat betanításakor helyileg is, valamint a távoli tárolókra vonatkozik. Azonban a távoli számítási automatizált gépi Tanulási kísérlet ismétléseinek végrehajtása aszinkron módon történik. Ez a funkció lehetővé teszi, hogy egy adott iterációhoz visszavonja, tekintse meg a végrehajtási állapotát, vagy folytatja a munkát a többi cella Jupyter notebookot. A távoli tanításhoz először létre kell hoznia egy távoli számítási célt, például AmlCompute. Ezután konfigurálja a távoli erőforrás, és küldje el a kódot.

Ez a cikk azokat az extra lépéseket ismerteti, amelyekkel egy automatizált ML-kísérletet futtathat egy távoli AmlCompute-tárolón. Az oktatóanyagban szereplő munkaterület-objektum `ws`a kód egészében használatos.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Erőforrás létrehozása

Ha még nem létezik, hozza létre a AmlCompute célját a munkaterületen (`ws`).

**Becsült idő**: a AmlCompute cél létrehozása körülbelül 5 percet vesz igénybe.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl"  # Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            # for GPU, use "STANDARD_NC6"
                                                            # vm_priority = 'lowpriority', # optional
                                                            max_nodes=6)
compute_target = ComputeTarget.create(
    ws, amlcompute_cluster_name, provisioning_config)

# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(
    show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Mostantól a `compute_target` objektumot is használhatja távoli számítási célként.

A fürt nevének korlátozásai a következők:
+ 64 karakternél rövidebbnek kell lennie.
+ Nem szerepelhetnek a következő karakterek: `\` ~! @ # $% ^ & * () = + _ [] {} \\\\ |; : \' \\", < >/?. `

## <a name="access-data-using-tabulardataset-function"></a>Hozzáférés az TabularDataset függvénnyel

A training_data a `TabularDataset`és a címkét adja meg, amelyek a AutoMLConfig automatizált ML-re lesznek átadva. `from_delimited_files` alapértelmezés szerint a `infer_column_types` igaz értékre állítja, amely automatikusan kikövetkezteti az oszlopok típusát. 

Ha manuálisan szeretné beállítani az oszlopok típusait, beállíthatja a `set_column_types` argumentumot az egyes oszlopok típusának manuális beállításához. Az alábbi kódmintában az adatokat az sklearn csomag származik.

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
    
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

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

## <a name="create-run-configuration"></a>Futtatási konfiguráció létrehozása

Ahhoz, hogy a függőségek elérhetők legyenek a get_data. a parancsfájlhoz, Definiáljon egy `RunConfiguration` objektumot definiált `CondaDependencies`. Ezt az objektumot a `AutoMLConfig``run_configuration` paraméteréhez használhatja.

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config = RunConfiguration(framework="python")
run_config.target = compute_target
run_config.environment.docker.enabled = True
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

dependencies = CondaDependencies.create(
    pip_packages=["scikit-learn", "scipy", "numpy==1.16.2"])
run_config.environment.python.conda_dependencies = dependencies
```

Tekintse meg ezt a [minta jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) a kialakítási minta további példájának megtekintéséhez.

## <a name="configure-experiment"></a>Kísérlet konfigurálása
`AutoMLConfig`beállításainak megadása.  (Lásd a [paraméterek teljes listáját](how-to-configure-auto-train.md#configure-experiment) és a lehetséges értékeket.)

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

## <a name="submit-training-experiment"></a>Betanítási kísérlet elküldése

Most küldje el a konfiguráció ki automatikusan a algoritmus, a hyper-paraméterek, és a modell betanításához.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
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

Használhatja ugyanazt a [Jupyter widgetet](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) , ahogy az [oktatóanyagban](tutorial-auto-train-models.md#explore-the-results) is látható, és megtekintheti az eredmények gráfját és táblázatát.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Itt látható a vezérlő statikus képe.  A notebook kattintva megtekintheti a Futtatás tulajdonságait, és kimeneti naplók futtató a táblázatban minden olyan sorban.   A legördülő listában, a diagram felett segítségével is megtekintheti minden elérhető minden egyes ismétléskor metrikát.

![vezérlő táblázata](./media/how-to-auto-train-remote/table.png)
![vezérlő ábrázolása](./media/how-to-auto-train-remote/plot.png)

A widget használatával tekintse meg, és ismerje meg a Futtatás részletei az egyes URL-címet jeleníti meg.  

Ha nem Jupyter jegyzetfüzetben található, az URL-címet a futtatásból is megjelenítheti:

```
remote_run.get_portal_url()
```

Ugyanezek az információk a munkaterületen is elérhetők.  Ha többet szeretne megtudni ezekről az eredményekről, olvassa el az [automatizált gépi tanulási eredmények megismerése](how-to-understand-automated-ml.md)című témakört.

## <a name="example"></a>Példa

A következő [Jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) a cikkben szereplő fogalmakat mutatja be.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* Ismerje meg [, hogyan konfigurálhatja az automatikus betanítás beállításait](how-to-configure-auto-train.md).
* Lásd: [útmutató](how-to-machine-learning-interpretability-automl.md) a modell-értelmező funkcióinak engedélyezéséhez az automatikus ml-kísérleteknél.
