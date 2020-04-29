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
ms.date: 03/09/2020
ms.openlocfilehash: 9e499d609a3f78dc5f422b9ed90df09be30f2e7c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79080411"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Modellek betanítása automatizált gépi tanulással a felhőben

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning a modellt a kezelt számítási erőforrások különböző típusaira tanítja. A számítási cél lehet helyi számítógép vagy erőforrás a felhőben.

A gépi tanulási kísérletet könnyedén bővítheti vagy bővítheti további számítási célok hozzáadásával, például Azure Machine Learning számítással (AmlCompute). Az AmlCompute egy felügyelt számítási infrastruktúra, amellyel egyszerűen hozhat létre egy vagy több csomópontos számításokat.

Ebből a cikkből megtudhatja, hogyan hozhat létre modellt automatizált ML-vel a AmlCompute használatával.

## <a name="how-does-remote-differ-from-local"></a>Miben különbözik a távoli távolság a helyitől?

A "[besorolási modell automatikus gépi tanulással](tutorial-auto-train-models.md)" című oktatóanyaga bemutatja, hogyan használható egy helyi számítógép egy modell automatikus ml-vel való betanításához. A munkafolyamat helyi betanítás esetén is a távoli célokra is vonatkozik. A távoli számítások esetében azonban a rendszer aszinkron módon hajtja végre az automatikus ML-kísérletek ismétlését. Ezzel a funkcióval megszakíthat egy adott iterációt, megtekintheti a végrehajtás állapotát, vagy folytathatja a munkát a Jupyter-jegyzetfüzet más celláiban. A távoli tanításhoz először létre kell hoznia egy távoli számítási célt, például AmlCompute. Ezután konfigurálja a távoli erőforrást, és küldje el a kódot.

Ez a cikk azokat az extra lépéseket ismerteti, amelyekkel egy automatizált ML-kísérletet futtathat egy távoli AmlCompute-tárolón. Az oktatóanyagban szereplő `ws`munkaterület-objektum itt található a kódban.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Erőforrás létrehozása

Ha még [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py) nem létezik, hozza létre`ws`a célt a munkaterületen ().

**Becsült idő**: a AmlCompute cél létrehozása körülbelül 5 percet vesz igénybe.

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

Mostantól a távoli számítási `compute_target` célként is használhatja az objektumot.

A fürt nevének korlátozásai a következők:
+ 64 karakternél rövidebbnek kell lennie.
+ Nem szerepelhetnek a következő karakterek: `\` ~! @ # $% ^ & * () = + _ [] {} \\ \\ |; : \' \\",  < > /?. `

## <a name="access-data-using-tabulardataset-function"></a>Hozzáférés az TabularDataset függvénnyel

Definiált training_data [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) a és a címke alapján, amelyek a [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)következő automatizált ml-re lesznek átadva:. Alapértelmezés `TabularDataset` szerint `from_delimited_files`a metódus a igaz értéket állítja `infer_column_types` be, amely automatikusan kikövetkezteti az oszlopok típusát. 

Ha manuálisan szeretné beállítani az oszlopok típusát, beállíthatja az `set_column_types` argumentumot úgy, hogy manuálisan állítsa be az egyes oszlopok típusát. Az alábbi mintakód az adatok a sklearn-csomagból származnak.

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
Határozza meg a beállításait `AutoMLConfig`.  (Lásd a [paraméterek teljes listáját](how-to-configure-auto-train.md#configure-experiment) és a lehetséges értékeket.)

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

## <a name="submit-training-experiment"></a>Betanítási kísérlet beküldése

Most küldje el a konfigurációt az algoritmus, a Hyper-paraméterek és a modell betanítása automatikus kiválasztásához.

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


## <a name="explore-results"></a>Eredmények feltárása

Használhatja ugyanazt a [Jupyter widgetet](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) , ahogy az [oktatóanyagban](tutorial-auto-train-models.md#explore-the-results) is látható, és megtekintheti az eredmények gráfját és táblázatát.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Itt látható a vezérlő statikus képe.  A jegyzetfüzetben a táblázat bármelyik sorára kattintva megjelenítheti a futtatási tulajdonságokat és a kimeneti naplókat.   A gráf fölötti legördülő lista segítségével megtekintheti az egyes iterációk összes elérhető metrikájának gráfját.

![vezérlő táblázata](./media/how-to-auto-train-remote/table.png)
![vezérlő ábrázolása](./media/how-to-auto-train-remote/plot.png)

A widget megjelenít egy URL-címet, amellyel megtekintheti és feltárhatja az egyes futtatások részleteit.  

Ha nem Jupyter jegyzetfüzetben található, az URL-címet a futtatásból is megjelenítheti:

```
remote_run.get_portal_url()
```

Ugyanezek az információk a munkaterületen is elérhetők.  Ha többet szeretne megtudni ezekről az eredményekről, olvassa el az [automatizált gépi tanulási eredmények megismerése](how-to-understand-automated-ml.md)című témakört.

## <a name="example"></a>Példa

A következő [Jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) a cikkben szereplő fogalmakat mutatja be.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* Ismerje meg [, hogyan konfigurálhatja az automatikus betanítás beállításait](how-to-configure-auto-train.md).
* Lásd: [útmutató](how-to-machine-learning-interpretability-automl.md) a modell-értelmező funkcióinak engedélyezéséhez az automatikus ml-kísérleteknél.
