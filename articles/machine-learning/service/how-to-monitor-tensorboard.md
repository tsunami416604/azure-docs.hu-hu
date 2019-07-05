---
title: Megjelenítheti a kísérletek TensorBoard és az Azure Machine Learning szolgáltatással
description: Indítsa el a TensorBoard kísérlet futtatása előzmények megjelenítése, és a hiperparaméter finomhangolása és átképezési területek azonosítását.
services: machine-learning
author: maxluk
ms.author: maxluk
ms.reviewer: nibaccam
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: babd4cdf8b7ed9e04b4bd975d840688b27439c4f
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560853"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Megjelenítheti a Kísérletezési futtatások és TensorBoard és az Azure Machine Learning-metrikák

Ez a cikk ismerteti a Kísérletezési futtatások és a metrikák megtekintéséhez TensorBoard használatával [a `tensorboard` csomag](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) a fő Azure Machine Learning szolgáltatás SDK-t. Miután a Kísérletezési futtatások már megvizsgálni, jobban hangolása és a gépi tanulási modellek szoftveres átképezése.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) olyan webalkalmazások vizsgálatához és a kísérlet struktúra és a teljesítmény ismertetése.

Hogyan TensorBoard indítása az Azure Machine Learning-kísérletek kísérlet típusától függ:
+ A kísérlet natív módon adja vissza a naplófájlokat, amelyek például a PyTorch, a Chainer és TensorFlow kísérletek TensorBoard, fogyaszthatóvá, akkor is [TensorBoard indítása közvetlenül](#direct) kísérletből a futtatási előzmények. 

+ Kísérletek, amely nem natív módon kimeneti TensorBoard értelmezhető fájlok, például a Scikit-ismerje meg, vagy az Azure Machine Learning-példakísérleteket, például használja [a `export_to_tensorboard()` metódus](#export) , majd indítsa el a futtatási előzményeket TensorBoard naplók exportálása TensorBoard onnan. 

## <a name="prerequisites"></a>Előfeltételek

* TensorBoard és megtekintése a futtatási előzmények kísérlet indításához, a kísérletek kell korábban engedélyezte a naplózást a mérőszámokhoz és nyomon követéséhez.  

* Ebben az útmutatóban a kódot futtathatja a következő környezetekben egyikével: 

    * Az Azure Machine Learning Notebook Virtuálisgép - letöltések vagy nem szükséges telepítés

        * Végezze el a [felhőalapú notebook rövid](quickstart-run-cloud-notebook.md#create-notebook) hozhat létre egy dedikált notebook server előre betöltött az SDK-t és a mintaadattárat.

        * A notebook server minták mappájában található két befejeződött, és jegyzetfüzetek kibontva az ebben a könyvtárban: **útmutatóval-to-használat – azureml > képzés a deep learning**.
        * export-Run-history-to-Run-history.ipynb
        * tensorboard.ipynb

    * A saját Juptyer notebook server
      * Használja a [hozzon létre egy munkaterületet a cikk](setup-create-workspace.md) ,
          * [Telepítse az Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) együtt a `tensorboard` extra
          * Hozzon létre egy munkaterületet, és a konfigurációs fájl (config.json)
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Option 1: Közvetlenül a TensorBoard előzmények megtekintése

Ezt a beállítást, hogy natív módon kimenetek naplófájlok értelmezhető által TensorBoard, például a PyTorch, Chainer és TensorFlow kísérleteket kísérletek működik. Ha ez nem a kis-és is futtathatja a kísérletet, [a `export_to_tensorboard()` metódus](#export) helyette.

Használja az alábbi példakód a [MNIST bemutató kísérlet](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) egy távoli számítási célt, az Azure Machine Learning Compute a TensorFlow-adattárból. Ezután azt be a modell az SDK-t egyéni [TensorFlow estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), majd indítsa el a TensorBoard ehhez tensorflow-hoz a kísérlethez, kísérlet, amely natív módon TensorBoard esemény fájlok ellen.

### <a name="set-experiment-name-and-create-project-folder"></a>Kísérlet neve és a projekt mappa létrehozása

Itt nevet használjuk a kísérletet, hozza létre a mappát. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>TensorFlow bemutató kísérlet kód letöltése

Tensorflow-hoz a tárház egy kiterjedt TensorBoard instrumentation MNIST bemutató rendelkezik. Mi viszont nem, és nem kell, ez a bemutató kódot, hogy működjön az Azure Machine Learning szolgáltatással valamelyik alter. Az alábbi kódot hogy a MNIST kód letöltése, és mentse az újonnan létrehozott kísérlet mappában.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
A kódfájl MNIST egész mnist_with_summaries.py, figyelje meg, hogy van ez a meghívás vonalak `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` stb. Ezeket módszerek csoportot, a napló és a futtatási előzmények, a kísérletek címke főbb metrikáit. A `tf.summary.FileWriter()` különösen fontos akkor szerializálja a kísérletet a naplózott mérőszámok, amely lehetővé teszi, hogy nekik vizualizációkat hozhat létre TensorBoard származó adatokat.

 ### <a name="configure-experiment"></a>Kísérlet konfigurálása

Az alábbiakban azt konfigurálja a kísérletet, és állítsa be a könyvtárak naplókat és az adatok. Ezek a naplók lesz feltöltve az összetevő szolgáltatásnak, amely TensorBoard később fér hozzá.

>[!Note]
> TensorFlow például szüksége lesz a helyi gépen telepítse a tensorflow-hoz. További a TensorBoard modul (azaz a tensorflow-hoz mellékelt egy) elérhetőnek kell lennie, ez a jegyzetfüzet kernel, mert a helyi gép TensorBoard futtatható.

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

### <a name="create-a-cluster-for-your-experiment"></a>Hozzon létre egy fürtöt a kísérlethez
Azonban minden környezetben a kísérletek hozható létre, és továbbra is tudja majd elindítani TensorBoard ellen a futtatási előzmények kísérlet létrehozunk egy AmlCompute fürt ehhez a kísérlethez. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>Küldje el a Futtatás a TensorFlow estimator

A TensorFlow estimator, a TensorFlow betanítási feladat a számítási célt indítása egyszerű módszert kínál. Az általános keresztül megvalósított [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztály, amely bármely keretrendszer támogatásához használható. Az általános estimator használó modellek betanítása kapcsolatos további információkért lásd: [modelleket taníthat be az Azure Machine Learning estimator használatával](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Indítsa el a TensorBoard

A futtatás során vagy után, el is indíthatja TensorBoard. A következő hozunk létre egy TensorBoard objektumpéldányt `tb`, hogy az idő szükséges a kísérlet futtatásához előzmények betöltése a a `run`, majd elindítja a TensorBoard és a `start()` metódus. 
  
A [TensorBoard konstruktor](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) egy diagnosztikakonfigurációs tömböt futtatások, ezért győződjön meg, és adja át azt egyelemű tömb.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Option 2: Napló megtekintése TensorBoard előzmények exportálása

A következő kódot állít be egy mintakísérlet, megkezdi a naplózási folyamatot, az Azure Machine Learning futtatási előzmények API-k használatával, és exportálja a futtatási előzmények modellezésére a naplókban értelmezhető által TensorBoard vizualizációs kísérletet. 

### <a name="set-up-experiment"></a>Állítsa be a kísérlet

A következő kódot állít be egy új kísérlet, és a futtatási könyvtár neve `root_run`. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Itt nem betölteni a küzdő adatkészlet – beépített kisméretű adatkészlet, amely együttműködik a scikit-ismerje meg, és azt fel teszt- és csoportok betanítása.

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

### <a name="run-experiment-and-log-metrics"></a>Futtassa a kísérletet, és jelentkezzen metrikák

Ezt a kódot, az azt egy lineáris regressziós modell betanítását, és jelentkezzen alapvető metrikákat, a alfa együttható `alpha` és mean squared error, `mse`, a futtatási előzmények.

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

### <a name="export-runs-to-tensorboard"></a>Exportálás fut és TensorBoard

Az SDK-val [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) módszer, hogy exportálhatja TensorBoard naplókat, hogy az Azure machine learning kísérlettel futtatási előzményeit így tudjuk TensorBoard keresztül megtekintheti őket.  

Az alábbi kódban hozunk létre a mappa `logdir` az aktuális munkakönyvtárba. Ebben a mappában, ahol nem lesz a kísérlet futtatási előzmények exportálni, és a naplók `root_run` és majd megjelölése befejezettként futtató. 

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
 Emellett exportálhatja egy adott Futtatás TensorBoard, adja meg a Futtatás neve  `export_to_tensorboard(run_name, logdir)`

Indítás és leállítás TensorBoard egyszer ehhez a kísérlethez futtatási előzmények exportálása, azt is elindíthatja az TensorBoard a [start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) metódust. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Ha elkészült, győződjön meg arról, hívja meg a [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) TensorBoard metódusa. Ellenkező esetben TensorBoard tovább futnak, amíg nem állítja le a notebookot kernel. 

```python
tb.stop()
```

## <a name="next-steps"></a>További lépések

A jelen útmutató, létrehozott két kísérletet, és bemutattuk, hogyan indítsa el a TensorBoard elleni lehetséges finomhangolásához és átképezési területek azonosítására, a futtatási előzményeket. 

* Ha elégedett a modellel, látogasson el a [a modell üzembe](how-to-deploy-and-where.md) cikk. 
* Tudjon meg többet [hiperparaméter finomhangolása](how-to-tune-hyperparameters.md). 
