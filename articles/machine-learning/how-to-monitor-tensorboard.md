---
title: Kísérletek megjelenítése a TensorBoard használatával
titleSuffix: Azure Machine Learning
description: Indítsa el a TensorBoard ot a kísérletfuttatási előzmények megjelenítéséhez, és azonosítsa a hiperparaméter-hangolás és átképzés lehetséges területeit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78195379"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>A TensorBoard és az Azure Machine Learning segítségével vizualizálhatja a kísérletfuttatásokat és a mérőszámokat
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan tekintheti meg a kísérlet futtatások és metrikák tensorboard [a csomag `tensorboard` ](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) használatával a fő Azure Machine Learning SDK. Miután megvizsgálta a kísérlet fut, jobban hangolhatja és újrataníthatja a gépi tanulási modellek.

[A TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) egy webes alkalmazáscsomag a kísérlet szerkezetének és teljesítményének ellenőrzésére és megértésére.

A TensorBoard indítása az Azure Machine Learning-kísérletekkel a kísérlet típusától függ:
+ Ha a kísérlet natív módon olyan naplófájlokat ad ki, amelyek a TensorBoard által fogyaszthatóak, például a PyTorch, a Chainer és a TensorFlow kísérleteket, akkor [a TensorBoardot közvetlenül a](#direct) kísérlet futtatási előzményeiből indíthatja el. 

+ Olyan kísérletekhez, amelyek nem natív módon aTensorBoard fogyóeszközökfájljait, például a Scikit-learn vagy az Azure Machine Learning-kísérleteket, [a módszerrel `export_to_tensorboard()` ](#export) exportálhatja a futtatási előzményeket TensorBoard-naplókként, és onnan indíthatja el a TensorBoardot. 

> [!TIP]
> A jelen dokumentumban szereplő információk elsősorban adatszakértők és fejlesztők számára készült, akik a modell betanítási folyamatát szeretnék figyelni. Ha ön az Azure Machine Learning erőforrás-használatának és eseményeinek figyelése iránt érdeklődő rendszergazda, például kvóták, befejezett képzési futtatások vagy befejezett modelltelepítések, olvassa el [az Azure Machine Learning figyelése.](monitor-azure-machine-learning.md)

## <a name="prerequisites"></a>Előfeltételek

* A TensorBoard elindításához és a kísérletfuttatási előzmények megtekintéséhez a kísérleteknek korábban engedélyezniük kell a naplózást a mérőszámok és a teljesítmény nyomon követéséhez.  

* A dokumentumban lévő kód az alábbi környezetekben futtatható: 

    * Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

        * Töltse ki az [oktatóanyag: Telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) hozzon létre egy dedikált notebook-kiszolgáló előre betöltött az SDK és a mintatár.

        * A jegyzetfüzet-kiszolgáló mintamappájában két kitöltött és kibontott jegyzetfüzetet kereshet az alábbi könyvtárakra navigálva:
            * **how-to-use-azureml > képzés-with-deep-learning > export-run-history-to-tensorboard > export-run-history-to-tensorboard.ipynb**

            * **how-to-use-azureml > track-and-monitor-kísérletek > tensorboard.ipynb**

    * Saját Juptyer notebook szerver
       * [Telepítse az Azure Machine Learning SDK-t](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) az `tensorboard` extra
        * [Hozzon létre egy Azure Machine Learning-munkaterületet.](how-to-manage-workspace.md)  
        * [Munkaterület-konfigurációs fájl létrehozása](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>1. lehetőség: A futtatási előzmények közvetlen megtekintése a TensorBoardban

Ez a beállítás olyan kísérletekhez használható, amelyek natív módon a TensorBoard által elhasználható naplófájlokat, például a PyTorch, a Chainer és a TensorFlow kísérleteket adja ki. Ha nem ez a kísérlet esete, használja inkább [ `export_to_tensorboard()` a módszert.](#export)

A következő példakód a TensorFlow tárházából származó [MNIST bemutatókísérletet](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) használja egy távoli számítási célban, az Azure Machine Learning Compute-ban. Ezután betanítjuk a modellt az SDK egyéni [TensorFlow-becslésével,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)majd elindítjuk a TensorBoard-ot ezzel a TensorFlow kísérlettel, azaz egy kísérlettel, amely natív módon adja ki a TensorBoard eseményfájljait.

### <a name="set-experiment-name-and-create-project-folder"></a>Kísérlet nevének beállítása és projektmappa létrehozása

Itt nevezzük el a kísérletet, és hozzuk létre a mappát. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>A TensorFlow bemutatókísérlet kódjának letöltése

A TensorFlow adattára mnist demóval rendelkezik, kiterjedt TensorBoard műszerekkel. Nem módosítjuk és nem is kell módosítani a bemutató kódját ahhoz, hogy az Azure Machine Learningtel működjön. A következő kódban letöltjük az MNIST kódot, és az újonnan létrehozott kísérleti mappába mentjük.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Az Egész MNIST kód fájl, mnist_with_summaries.py, észre, `tf.summary.histogram()` `tf.summary.FileWriter()` hogy vannak vonalak, hogy hívja `tf.summary.scalar()`, , stb Ezek a módszerek csoportosítják, naplózzák és címkézik a kísérletek kulcsfontosságú mutatóit a futtatási előzményekbe. A `tf.summary.FileWriter()` különösen fontos, mivel szerializálja a naplózott kísérlet metrikáiból származó adatokat, ami lehetővé teszi a TensorBoard számára, hogy vizualizációkat hozzon létre róluk.

 ### <a name="configure-experiment"></a>Kísérlet konfigurálása

A következőkben konfiguráljuk a kísérletet, és könyvtárakat állítunk be a naplókhoz és az adatokhoz. Ezek a naplók lesznek feltöltve a műtermék szolgáltatás, amely TensorBoard később hozzáfér.

>[!Note]
> Ebben a TensorFlow-példában telepítenie kell a TensorFlow-t a helyi számítógépre. Továbbá a TensorBoard modulnak (azaz a TensorFlow-hoz mellékelt modulnak) elérhetőnek kell lennie a jegyzetfüzet kernele számára, mivel a helyi számítógép futtatja a TensorBoardot.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Fürt létrehozása a kísérlethez
Létrehozunk egy AmlCompute fürtet ehhez a kísérlethez, azonban a kísérletek bármilyen környezetben létrehozhatók, és továbbra is elindíthatja a TensorBoardot a kísérlet futtatási előzményei ellen. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>Futtatás beküldése a TensorFlow-becslővel

A TensorFlow-becslés egyszerű módot biztosít a TensorFlow képzési feladat elindítására egy számítási célon. Ez az általános [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztályon keresztül valósul meg, amely bármely keretrendszer támogatására használható. Az általános becslést használó betanítási modellekről további információt az [Azure Machine Learning használatával a becsléssel rendelkező modellek betanítása című](how-to-train-ml-models.md) témakörben talál.

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>A TensorBoard elindítása

A TensorBoard ot futás közben vagy befejezése után is elindíthatja. A következőkben létrehozunk egy TensorBoard `tb`objektumpéldányt, amely a `run`kísérlet futtatási előzményeit betölti a , majd elindítja a TensorBoard-ot a `start()` metódussal. 
  
A [TensorBoard konstruktor](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) vesz egy tömb fut, ezért győződjön meg róla, és adja át, mint egy elemtömb.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 Bár ebben a példában a TensorFlow-t használta, a TensorBoard ugyanolyan könnyen használható a PyTorch vagy Chainer modellekkel. A TensorFlow-nak elérhetőnek kell lennie a TensorBoard-ot futtató gépen, de nem szükséges a PyTorch vagy Chainer számításokat végző gépen. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>2. lehetőség: Exportálási előzmények naplóként a TensorBoard ban való megtekintéshez

A következő kód beállít egy mintakísérletet, megkezdi a naplózási folyamatot az Azure Machine Learning futtatási előzmények API-jai használatával, és exportálja a kísérlet futtatási előzményeit a TensorBoard által vizualizációra fogyasztható naplókba. 

### <a name="set-up-experiment"></a>Kísérlet beállítása

A következő kód új kísérletet állít `root_run`be, és megnevezi a futtatási könyvtárat. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Itt töltjük be a diabétesz adatkészletet - egy beépített kis adatkészletet, amely scikit-learn-el érkezik, és teszt- és edzéskészletekre osztja fel.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Kísérlet futtatása és naplómetrikák futtatása

Ehhez a kódhoz lineáris regressziós modellt és naplókulcs-mutatókat, az `alpha` `mse`alfa-együtthatót és az átlagos négyzetes hibát tanítunk be a futtatási előzményekben.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Futtatások exportálása a TensorBoard ba

Az SDK [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) módszerével exportálhatjuk az Azure gépi tanulási kísérletének futtatási előzményeit a TensorBoard naplóiba, így a TensorBoardon keresztül tekinthetjük meg őket.  

A következő kódban hozzuk `logdir` létre a mappát a jelenlegi munkakönyvtárunkban. Ez a mappa az a hely, ahol `root_run` exportálni fogjuk a kísérleti futtatási előzményeket és naplókat, majd megjelöljük, hogy a futtatás befejeződöttként fusson. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 Egy adott futtatást a TensorBoard-ba is exportálhat, ha megadja a futtatás nevét.`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>TensorBoard indítása és leállítása
Miután a kísérlet futtatási előzményeit exportáltuk, elindíthatjuk a TensorBoard-ot a [start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) módszerrel. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Ha elkészült, győződjön meg róla, hogy hívja meg a TensorBoard objektum [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) metódusát. Ellenkező esetben a TensorBoard addig fog futni, amíg le nem állítja a jegyzetfüzetkernelt. 

```python
tb.stop()
```

## <a name="next-steps"></a>További lépések

Ebben a how-to you, létrehozott két kísérletet, és megtanulta, hogyan kell indítani TensorBoard ellen fut történelem azonosítani területek potenciális tuning és átképzés. 

* Ha elégedett a modell, feje fölött a [mi Hogyan kell telepíteni a modell](how-to-deploy-and-where.md) cikket. 
* További információ a [hiperparaméter-hangolásról.](how-to-tune-hyperparameters.md) 
