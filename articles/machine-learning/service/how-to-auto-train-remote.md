---
title: Automatizált machine learning – az Azure Machine Learning szolgáltatás távoli számítási céljainak beállítása
description: Ez a cikk azt ismerteti, hogyan hozhat létre automatizált gépi tanulás használatával az Azure Machine Learning szolgáltatással Data Science virtuális gép (DSVM) távoli számítási célt
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 80a227b57c8df157890337f0e207519c71ae5bd6
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034620"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>A felhőalapú automatikus machine learning-modellek

Az Azure Machine Learning betaníthatja a modell a különböző típusú számítási erőforrásokat, amelyeket Ön kezel. A számítási célnak lehet helyi számítógépen vagy egy számítógép, a felhőben. 

Egyszerűen vertikális vagy horizontális felskálázás a machine learning-kísérlet további számítási célnak, például az Ubuntu-alapú adatok adatelemzési virtuális gépet (DSVM) vagy az Azure Batch AI hozzáadásával. A dsvm-hez egy testre szabott Virtuálisgép-lemezkép a Microsoft Azure-felhőben, amelyet kifejezetten adatelemzésre. Számos népszerű adatelemzési és egyéb eszközök, előre telepítve és konfigurálva van.  

Ebből a cikkből megismerheti, hogyan hozhat létre automatizált gépi tanulás használatával a dsvm-hez a modell.  

## <a name="how-does-remote-differ-from-local"></a>Miben helyi távoli?

Az oktatóanyag "[ML automatikus osztályozási modell betanításához](tutorial-auto-train-models.md)" bemutatja, hogyan használja a helyi számítógép automatikus gépi Tanulási modell.  A munkafolyamat betanításakor helyileg is, valamint a távoli tárolókra vonatkozik. A távoli számítási automatikus gépi Tanulási kísérlet ismétléseinek végrehajtása aszinkron módon történik. Ez lehetővé teszi, hogy megszakítja a műveletet egy adott iterációhoz, tekintse meg a végrehajtási állapotát, folytatja a munkát a többi cella Jupyter notebookot. Távolról betanításához, először hozzon létre egy távoli számítási célnak, például egy Azure-DSVM, konfigurálja és küldje el a kódot.

Ez a cikk bemutatja, hogy a távoli DSVM kísérletezhet a gépi tanulás automatikus futtatásához szükséges további lépéseket.  A munkaterület objektum `ws`, az oktatóanyag használja Itt a kód egészében.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Erőforrás létrehozása

A dsvm-hez a munkaterület létrehozása (`ws`) Ha ezt még nem létezik. A DSVM korábban jött létre, ha ez a kód kihagyja a létrehozási folyamat, és betölti azokat a meglévő erőforrás részletei a `dsvm_compute` objektum.  

**Becsült időtartam**: a virtuális gép létrehozása körülbelül 5 percet vesz igénybe.

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
    dsvm_compute = DsvmCompute.create(ws, name = dsvm_name, provisioning_configuration = dsvm_config)
    dsvm_compute.wait_for_completion(show_output = True)
```

Most már használhatja a `dsvm_compute` a távoli számítási célnak az objektumot.

A DSVM alapján való korlátozások a következők:
+ 64 karakternél rövidebbnek kell lennie.  
+ Nem tartalmazza a következő karakterek egyikét sem: `\` ~! @ # $ % ^ & * () = + _ [] {} \\ \\ |};: \' \\", < > /?. `

>[!Warning]
>Ha létrehozása meghiúsul, és a piactér vásárlás jogosultsági szóló üzenetet:
>    1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
>    1. A DSVM létrehozásának megkezdéséhez 
>    1. Válassza ki "létrehozni kívánt programozott módon" programozott létrehozásának engedélyezése
>    1. Kilépés nélkül valójában a virtuális gép létrehozása
>    1. Futtassa újra a létrehozási kódot


## <a name="access-data-using-get-data-file"></a>Adatok lekérése fájl használatával érheti el adatait

Adja meg a betanítási adatok eléréséhez a távoli erőforrás. Az automatikus Machine Learning kísérleteket távoli számítási fut, az adatok használatával kéri le kell-e egy `get_data()` függvény.  

A hozzáférés biztosításához tegye a következőket:
+ Hozzon létre tartalmazó get_data.py fájlt egy `get_data()` függvény 
* Helyezze el a fájlt a gyökérkönyvtárban a szkripteket tartalmazó mappa 

Adatok beolvasása a blob storage vagy a helyi lemezre a get_data.py fájlban kódot is magába foglalja. Az alábbi kódmintában az adatokat az sklearn csomag származik.

>[!Warning]
>Ha távoli számítási használ, akkor kell használni `get_data()` az adatok csatlakoztatva átalakításokat hajthattak végre.


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

## <a name="configure-automated-machine-learning-experiment"></a>Automatizált machine learning-kísérlet konfigurálása

Adja meg a beállításokat a `AutoMLConfig`.  (Lásd a [paraméterek teljes listája]() és azok lehetséges értékei.)

A beállítások `run_configuration` értékre van állítva a `run_config` objektum, amely tartalmazza a dsvm-hez a konfiguráció.  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "max_time_sec": 600,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings
                            )
```

## <a name="submit-automated-machine-learning-training-experiment"></a>Automatizált a machine learning betanítási kísérlet elküldése

Most küldje el a konfiguráció ki automatikusan a algoritmus, a hyper-paraméterek, és a modell betanításához. (További [beállításaival kapcsolatos további információk]() számára a `submit` metódus.)

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```
Ehhez hasonló kimenetet fog látni:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0.0                      0.954     0.954
             7      Normalizer DT                         0.0                      0.161     0.954
             0      Scale MaxAbs 1 extra trees            0.0                      0.936     0.954
             4      Robust Scaler SGD classifier          0.0                      0.867     0.954
             1      Normalizer kNN                        0.0                      0.984     0.984
             9      Normalizer extra trees                0.0                      0.834     0.984
             5      Robust Scaler DT                      0.0                      0.736     0.984
             8      Standardize kNN                       0.0                      0.981     0.984
             6      Standardize SVM                       2.2                      0.984     0.984
            10      Scale MaxAbs 1 DT                     0.0                      0.077     0.984
            11      Standardize SGD classifier            0.0                      0.863     0.984
             3      Standardize gradient boosting         5.4                      0.971     0.984
            12      Robust Scaler logistic regression     2.0                      0.955     0.984
            14      Scale MaxAbs 1 SVM                    0.0                      0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      3.4                      0.971     0.989
            15      Robust Scaler kNN                     0.0                      0.904     0.989
            17      Standardize kNN                       0.0                      0.974     0.989
            16      Scale 0/1 gradient boosting           2.8                      0.968     0.989
            18      Scale 0/1 extra trees                 0.0                      0.828     0.989
            19      Robust Scaler kNN                     0.0                      0.983     0.989


## <a name="explore-results"></a>Ismerje meg az eredmények

Egy, használhatja ugyanazt a Jupyter-widget [az oktatóprogram](tutorial-auto-train-models.md#explore-the-results) , a graph és az eredmények táblázatát.

```python
from azureml.train.widgets import RunDetails
RunDetails(remote_run).show()
```
Ez a vezérlő statikus képe.  A notebook kattintva megtekintheti a Futtatás tulajdonságait, és kimeneti naplók futtató a táblázatban minden olyan sorban.   A legördülő listában, a diagram felett segítségével is megtekintheti minden elérhető minden egyes ismétléskor metrikát.

![widget tábla](./media/how-to-auto-train-remote/table.png)
![widget diagram](./media/how-to-auto-train-remote/plot.png)

A widget használatával tekintse meg, és ismerje meg a Futtatás részletei az egyes URL-címet jeleníti meg.
 
### <a name="view-logs"></a>Naplók megtekintése

A DSVM alatt ügynökszámítógépen/azureml_run / {iterationid} a naplók keresése és azureml-naplókat.

## <a name="example"></a>Példa

A `automl/03.auto-ml-remote-execution.ipynb` notebook mutatja be a jelen cikk fogalmait.  Ez a jegyzetfüzet lekérése:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg, [automatikus képzéshez beállításainak konfigurálása](how-to-configure-auto-train.md).
