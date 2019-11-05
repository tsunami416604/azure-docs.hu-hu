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
ms.openlocfilehash: 4276a713e62f96cc5340fc7be0e8391939d32342
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497320"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Modellek betanítása automatizált gépi tanulással a felhőben

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning a modellt a kezelt számítási erőforrások különböző típusaira tanítja. A számítási cél lehet helyi számítógép vagy erőforrás a felhőben.

A gépi tanulási kísérletet könnyedén bővítheti vagy bővítheti további számítási célok hozzáadásával, például Azure Machine Learning számítással (AmlCompute). A AmlCompute egy felügyelt számítási infrastruktúra, amely lehetővé teszi, hogy egyszerűen hozzon létre egy vagy több csomópontos számítási módszert.

Ebből a cikkből megtudhatja, hogyan hozhat létre modellt automatizált ML-vel a AmlCompute használatával.

## <a name="how-does-remote-differ-from-local"></a>Miben különbözik a távoli távolság a helyitől?

A "[besorolási modell automatikus gépi tanulással](tutorial-auto-train-models.md)" című oktatóanyaga bemutatja, hogyan használható egy helyi számítógép egy modell automatikus ml-vel való betanításához. A munkafolyamat helyi betanítás esetén is a távoli célokra is vonatkozik. A távoli számítások esetében azonban a rendszer aszinkron módon hajtja végre az automatikus ML-kísérletek ismétlését. Ezzel a funkcióval megszakíthat egy adott iterációt, megtekintheti a végrehajtás állapotát, vagy folytathatja a munkát a Jupyter-jegyzetfüzet más celláiban. A távoli tanításhoz először létre kell hoznia egy távoli számítási célt, például AmlCompute. Ezután konfigurálja a távoli erőforrást, és küldje el a kódot.

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

Definiált X és y `TabularDataset`s értékként, amelyet a rendszer a AutoMLConfig automatizált ML-re továbbít. `from_delimited_files` alapértelmezés szerint a `infer_column_types` igaz értékre állítja, amely automatikusan kikövetkezteti az oszlopok típusát. 

Ha manuálisan szeretné beállítani az oszlopok típusait, beállíthatja a `set_column_types` argumentumot az egyes oszlopok típusának manuális beállításához. Az alábbi mintakód az adatok a sklearn-csomagból származnak.

```python
# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd

data_train = datasets.load_digits()

pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

X = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/X_train.csv'))
y = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/y_train.csv'))

```

## <a name="create-run-configuration"></a>Futtatási konfiguráció létrehozása

Ahhoz, hogy a függőségek elérhetők legyenek a get_data. a parancsfájlban, Definiáljon egy `RunConfiguration` objektumot a megadott `CondaDependencies`. Ezt az objektumot a `AutoMLConfig``run_configuration` paraméteréhez használhatja.

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config = RunConfiguration(framework="python")
run_config.target = compute_target
run_config.environment.docker.enabled = True
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

dependencies = CondaDependencies.create(
    pip_packages=["scikit-learn", "scipy", "numpy"])
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
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             )
```

### <a name="enable-model-explanations"></a>Modell magyarázatának engedélyezése

Adja meg a választható `model_explainability` paramétert a `AutoMLConfig` konstruktorban. Emellett egy érvényesítési dataframe-objektumot is át kell adni paraméterként `X_valid` a modell magyarázati funkciójának használatához.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             model_explainability=True,
                             X_valid=X_test
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

### <a name="view-logs"></a>Naplók megtekintése

Keresse meg a DSVM a `/tmp/azureml_run/{iterationid}/azureml-logs`alatt található naplókat.

## <a name="explain"></a>A legjobb modell magyarázata

A modell magyarázati adatainak beolvasása lehetővé teszi a modellekkel kapcsolatos részletes információk megtekintését, amelyekkel növelheti az átláthatóságot a háttérbeli működésben. Ebben a példában a modell magyarázatait csak a legjobb illeszkedési modellhez futtatja. Ha a folyamat összes modelljét futtatja, a művelet jelentős futási időt eredményez. A modell magyarázatával kapcsolatos információk a következők:

* shap_values: az formáló lib által generált magyarázati információ.
* expected_values: az X_train-adathalmazra alkalmazott modell várt értéke.
* overall_summary: a modell szintjének fontossági értékeit csökkenő sorrendben rendezi a szolgáltatás.
* overall_imp: a szolgáltatások nevei ugyanabban a sorrendben vannak rendezve, mint a overall_summary.
* per_class_summary: az osztály szintjének fontossági értékei csökkenő sorrendben rendezve jelennek meg. Csak a besorolási esethez érhető el.
* per_class_imp: a szolgáltatások nevei ugyanabban a sorrendben vannak rendezve, mint a per_class_summary. Csak a besorolási esethez érhető el.

A következő kód használatával kiválaszthatja az iterációk legjobb folyamatát. A `get_output` metódus a legjobb futtatást és a beillesztett modellt adja vissza az utolsó illeszkedési híváshoz.

```python
best_run, fitted_model = remote_run.get_output()
```

Importálja a `retrieve_model_explanation` függvényt, és futtassa a parancsot a legjobb modellen.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

A megtekinteni kívánt `best_run` magyarázó változók eredményeinek nyomtatása.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

A `best_run` magyarázat összefoglaló változóinak kinyomtatása a következő kimenetet eredményezi.

![Modell-magyarázó konzol kimenete](./media/how-to-auto-train-remote/expl-print.png)

A funkciók fontosságát a widget felhasználói felületén vagy a munkaterületen, [Azure Machine learning Studióban](https://ml.azure.com)is megjelenítheti. 

![Modell-magyarázó KEZELŐFELÜLET](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Példa

A [How-to-use-azureml/Automated-Machine-learning/Remote-amlcompute/Auto-ml-Remote-amlcompute. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) jegyzetfüzet bemutatja a cikkben szereplő fogalmakat.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg [, hogyan konfigurálhatja az automatikus betanítás beállításait](how-to-configure-auto-train.md).
